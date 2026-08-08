> 大家好，我是 iDao。10 年全栈开发，做过架构、运维，也在落地 AI 工程化。这里不搞虚的，只分享能直接跑、能直接用的代码、方案和经验。内容包括：全栈开发实战、系统搭建、可视化大屏、自动化部署、AI 应用、私有化部署等。关注我，一起写能落地的代码，做能上线的项目。

很多人已经能用 AI 生成代码，但做不到“可持续交付”：没有 PRD 结构、任务拆分混乱、TDD 不落地、回测无标准、任务状态不可追踪。本文给出一套完整协同流程：用 OpenSpec 产出 PRD+技术规格，用 Superpowers 执行批量改造与质量守卫，用 oh-my-opencode 串联任务状态与自动回测，最终交付可运行的用户管理系统。你可以按模板完成一次端到端开发。

这版把流程拉直：**PRD 输出 → 任务拆分 → TDD 开发 → 回测 → task 标记 → 交付归档**。按步骤执行即可跑通。

---

## 1）先做文档基线：OpenSpec 产出 PRD + 技术规格 + 验收标准

### 问题现象
代码先行，后面不断返工：字段改名、接口漂移、页面逻辑冲突。

### 根因分析
缺少统一规格源，团队和 AI 都在“猜需求”。

### 解决步骤
在项目根目录建立 `docs/`，先让 OpenSpec 生成三份文件：

- `docs/prd.md`（业务需求）
- `docs/spec-api.yaml`（接口契约）
- `docs/acceptance.md`（验收清单）

可直接给 OpenCode / OpenSpec 的提示词：

```text
请基于“用户管理系统（登录、列表、新增、编辑、删除）”输出三份文档：

1) PRD（docs/prd.md）
- 背景、目标、范围（In/Out）
- 用户角色（管理员）
- 用户故事（Given/When/Then）
- 非功能要求（性能、安全、可维护性）
- 风险与依赖

2) API Spec（docs/spec-api.yaml）
- POST /api/login
- GET /api/users
- POST /api/users
- PUT /api/users/{id}
- DELETE /api/users/{id}
- 统一响应结构：{ code, message, data, traceId }

3) 验收标准（docs/acceptance.md）
- 功能验收（CRUD、分页、搜索）
- 接口验收（状态码、字段、错误码）
- 回归验收（关键路径）
```

### 验证方式
- PRD 中必须有 **In Scope / Out of Scope**
- API Spec 字段名与 PRD 一致（如 `role`）
- 验收文档至少 10 条可执行项

---

## 2）任务拆分：从 PRD 自动生成 Task Board（可追踪）

### 问题现象
开发中途不知道“现在做到哪一步”，AI 输出与人工修改互相覆盖。

### 根因分析
没有任务粒度和状态机制，缺少单一事实来源。

### 解决步骤
建立 `tasks/tasks.yaml`，按“模块-子任务-状态”管理。
状态建议：`todo -> doing -> review -> test -> done -> blocked`

示例（可复制）：
```yaml
epic: user-management-mvp
tasks:
  - id: API-LOGIN-001
    title: 实现登录接口与token签发
    owner: ai
    status: todo
    depends_on: []
    dod:
      - 返回 code/message/data/traceId
      - 错误密码返回 401
  - id: API-USER-002
    title: 用户列表分页与搜索
    owner: ai
    status: todo
    depends_on: [API-LOGIN-001]
    dod:
      - 支持 page/pageSize/keyword
  - id: WEB-USER-003
    title: 列表页与搜索栏
    owner: ai
    status: todo
    depends_on: [API-USER-002]
  - id: TEST-API-004
    title: 用户接口集成测试
    owner: ai
    status: todo
    depends_on: [API-USER-002]
```

给 AI 的任务推进提示词：
```text
请按 tasks/tasks.yaml 执行，只处理 status=todo 且依赖完成的任务。
每完成一个任务：
1) 更新状态为 review
2) 输出变更文件清单
3) 输出测试结果
禁止跨任务修改未声明文件。
```

### 验证方式
- 每次提交只对应 1-2 个 task id
- `depends_on` 未满足时不能执行
- 任务状态流转可追踪

---

## 3）TDD 落地：先写测试，再让 AI 实现

### 问题现象
功能看似可用，但一改字段就崩，回归成本高。

### 根因分析
AI 默认先写实现，测试滞后，导致“假稳定”。

### 解决步骤
后端用 Jest/Vitest + Supertest（任选其一），先建失败测试：

```bash
cd server
npm i -D vitest supertest @types/supertest
```

示例测试（`server/tests/users.test.ts`）：
```ts
import request from "supertest";
import { app } from "../src/app";

describe("User API", () => {
  it("GET /api/users should return paginated list", async () => {
    const res = await request(app).get("/api/users?page=1&pageSize=10");
    expect(res.status).toBe(200);
    expect(res.body.code).toBe(0);
    expect(Array.isArray(res.body.data.list)).toBe(true);
  });
});
```

给 OpenCode 的 TDD 提示词：
```text
你必须采用 TDD：
1) 先补全 tests，确保当前失败（红灯）
2) 再实现最小代码使测试通过（绿灯）
3) 最后重构并保持测试全绿
输出：测试前后结果、覆盖率、变更文件。
```

**关键参数说明**
`pageSize=10`：分页压测与接口一致性基准值，前后端默认值必须统一。

### 验证方式
- 看到“先失败再通过”的测试日志
- 覆盖率至少覆盖核心路由（login/users）

---

## 4）Superpowers 执行批处理：统一风格与跨文件改造

### 问题现象
代码能跑但质量参差：错误处理不统一、响应结构不一致。

### 根因分析
多轮 AI 生成后缺少“全局收口”步骤。

### 解决步骤
用 Superpowers 做三类批处理：

1. **统一响应结构**：所有接口返回 `{ code, message, data, traceId }`
2. **统一错误中间件**：`400/401/500` 落同一出口
3. **统一 API 客户端**：前端 axios 拦截器注入 token 与 traceId

可下达命令：
```text
执行跨文件重构：
- 后端所有 route/controller 返回结构统一为 ApiResponse<T>
- 新增 error middleware，未捕获异常返回 code!=0
- 前端 axios 统一处理 401 跳转登录
- 输出批量修改清单（文件+修改点）
```

### 验证方式
- 随机抽 3 个接口，响应结构一致
- 手动制造异常，确认 error middleware 生效
- 前端 token 过期自动回登录页

---

## 5）oh-my-opencode 串联工作流：回测、标记、归档

### 问题现象
功能完成后没有标准回测，线上前最后一步经常漏。

### 根因分析
缺少“自动化回测 + 任务闭环标记”。

### 解决步骤
结合 oh-my-opencode / openagent 生态做一个最小流水：

- `workflow/dev`：按 task 执行开发
- `workflow/test`：自动跑单测、集成测试、lint
- `workflow/report`：生成回测报告并更新 task 状态

建议输出文件：
- `reports/test-report.md`
- `reports/regression-checklist.md`
- `tasks/tasks.yaml`（更新状态）

回测提示词：
```text
请执行回测流程：
1) 运行后端测试、前端构建、lint
2) 按 docs/acceptance.md 逐条验收并给出结论（pass/fail）
3) 将通过任务标记为 done，失败标记为 blocked 并写明原因
4) 输出 reports/test-report.md
```

### 验证方式
- 有明确 pass/fail 列表，不是口头“基本可用”
- `blocked` 任务带原因和修复建议
- 可直接作为 PR 附件

---

## 常见坑（至少 3 条）
1. **PRD 不写 Out of Scope**：AI 会私自扩功能。
2. **Task 粒度过粗**：一个 task 同时改前后端+测试，难 review。
3. **TDD 只写 happy path**：异常路径没覆盖，回归必炸。
4. **回测不落文档**：下次迭代无法复盘。

---

## 快速自检清单
- [ ] `docs/prd.md`、`docs/spec-api.yaml`、`docs/acceptance.md` 已齐全
- [ ] `tasks/tasks.yaml` 状态流转规范（todo→doing→review→test→done）
- [ ] 至少 1 条 API 测试经历“先红后绿”
- [ ] 统一响应结构和错误处理中间件已落地
- [ ] 已生成 `reports/test-report.md` 回测报告
- [ ] blocked 任务有原因、有下一步动作

---

## 行动建议：今天就做这 3 件事
1. 先把当前项目补齐三份文档：PRD、API Spec、验收清单。
2. 建 `tasks.yaml`，所有 AI 改动必须绑定 task id。
3. 选一个接口（如 `/api/users`）严格按 TDD 走一遍，再扩到全模块。

这套流程的价值，不在“写得快”，而在“每次都能稳定交付”。按这个模板沉淀后，后续做订单、角色权限、审计日志都能复用同一协同框架，效率和质量会一起上升。

关注 【iDao技术魔方】，获取更多全栈到AI可落地的实战干货。

4. 发布补全
- 所属栏目（5 选 1）：**AI应用**
- 关键词 4-6 个：**OpenSpec、Superpowers、oh-my-opencode、TDD、任务拆分、自动回测**
- 标签 3 个：**AI工程化、全栈开发、交付流程**
- 封面大字文案 8-14 字：**PRD到回测闭环实战**
- 建议发布时间：**20:00-21:00**
  
> 原创技术博客 · 开源项目分享 · AI全栈创作社区  [idao.fun](https://idao.fun)