## 模型与定位

MPT（Mosaic Pretrained Transformer）由 Databricks MosaicML 团队研发，完全开源且商业可用。核心特性包括 Flash Attention 高效注意力、ALiBi 位置编码支持上下文长度外推，以及稳定性改进消除损失尖峰。MPT-30B 在多项基准上超越同规模模型。

## 参数速览

| 模型 | 参数量 | 上下文 | 开源权重 | 商业可用 | 适用场景 |
| --- | --- | --- | --- | --- | --- |
| MPT-7B | 7B | 2K | ✅ | ✅ | 轻量部署、实验 |
| MPT-7B-8K | 7B | 8K | ✅ | ✅ | 长文档理解 |
| MPT-7B-StoryWriter | 7B | 65K | ✅ | ✅ | 长文创作、小说 |
| MPT-30B | 30B | 8K | ✅ | ✅ | 通用对话、代码 |
| MPT-30B-Instruct | 30B | 8K | ✅ | ✅ | 指令遵循、Agent |

## 平台接入

| 平台 | 说明 |
| --- | --- |
| [Databricks](https://www.databricks.com/) | 企业级训练与部署 |
| [HuggingFace](https://huggingface.co/mosaicml) | 模型权重下载 |
| [MosaicML](https://www.mosaicml.com/mpt) | 官方文档 |

## 选型建议

长文档/小说创作首选 StoryWriter-65K；通用场景选 MPT-30B；轻量部署选 MPT-7B-8K；企业用户可结合 Databricks 平台使用。