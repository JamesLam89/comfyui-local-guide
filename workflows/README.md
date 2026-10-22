# 工作流下载与使用说明

[返回教程首页](../README.md)

这两个 JSON 来自作者本地保存的工作流，已整理为可分享的示例。结构检查通过不等于已在你的电脑上完成出图测试；模型、ComfyUI 版本和节点实现仍须匹配。

| 工作流 | 用途 | 依赖 |
|---|---|---|
| [MOSS 文生图](moss-text-to-image.json) | 用文字生成图片，普通婚礼场景示例；25 步、CFG 2.5，未加载 LoRA | MOSS SFT INT8、Qwen3.5-9B 编码器、FLUX.2 small VAE；支持 `moss_image`、`EmptyFlux2LatentImage`、`SkipLayerGuidanceDiTSimple` 的 ComfyUI |
| [NoctQ 参考图生成](noctq-reference-image.json) | 将输入图片交给 Qwen 参考图编码节点，再从空 latent 采样；25 步、CFG 3 | NoctQ V4、Qwen3-VL-8B 编码器、Qwen 2.1 VAE；`TextEncodeQwenImage21` 节点 |

## 导入步骤

1. 打开文件后选择 GitHub 的 **Raw / Download raw file**，保存为 `.json`；不要把网页 HTML 当成工作流下载。
2. 将 JSON 拖入 ComfyUI，或使用工作流菜单打开。
3. 按[模型清单](../docs/models.md)准备所需权重，并在各加载节点重新选择本机文件。文件名不同不一定是模型缺失，但不能只改名字冒充兼容版本。
4. 使用 NoctQ 示例时，在 `LoadImage` 节点上传你有权使用的参考图，替换 `your-reference.png`。该文件是占位名称，仓库没有附带私人原图。
5. 先以单张、较低分辨率试运行。成功后保存自己的参数版本。

## 两种“图生图”不要混淆

这里的 NoctQ 示例将参考图用于条件编码，起始 latent 是空白，并非传统的 `VAE Encode → KSampler（denoise < 1）` 图生图。它不保证逐像素保留原图，修改 denoise 也不能把这条链路自动变成传统图生图。

## 模型与节点

- 本仓库不包含模型权重；下载地址见模型清单。精确量化来源尚未核实的条目已标注。
- 找不到 `TextEncodeQwenImage21` 时，检查 [Comfy-Org/Qwen-Image-2.1](https://huggingface.co/Comfy-Org/Qwen-Image-2.1) 的当前 ComfyUI 集成说明，安装对应支持后再导入。
- 工作流不依赖成人向提示词。添加 LoRA 前先确认基础链路可用，再按作者说明加载与底模匹配的版本。
- 本仓库没有对所有硬件、节点版本和 LoRA 组合进行测试。
