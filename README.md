# 轻度本地部署看这一篇就够（NSFW 向）

![轻度本地部署看这一篇就够（NSFW 向）](images/cover.png)

以成本为5000元 RTX 4060 Laptop 8GB 为背景的 ComfyUI 入门级本地简易部署生成：包含模型选择、LoRA 分工、安装顺序与工作流排错。

**更新日期：2026-10-22** · [完整模型与 LoRA 表格](docs/models.md) · [工作流下载与使用说明](workflows/README.md) · [常见问题](docs/troubleshooting.md)

> 适合有 Windows 电脑、愿意按步骤配置本地环境的读者。“轻度”指先跑通一条图片路线；文件体积不等于显存占用，本文不承诺特定速度。模型名称含成人向标签的条目以技术说明为主，示例工作流采用普通人像或场景提示词。


想在自己电脑上跑成人向图片，最容易走弯路的地方不是“哪张显卡最强”，而是把**底模、文本编码器、VAE、LoRA 和工作流**混成一锅。下面是我按一台 **RTX 4060 Laptop 8GB**、Windows、ComfyUI 0.42.0 的实际模型目录整理的入门路线。它是安装与选型指南，不是出图速度或画质排行榜。

## 先只选一个底模家族

| 想做什么 | 起步组合 | 本机这套权重约占磁盘 | 适合谁 |
|---|---|---:|---|
| 文生图、中文文字与布局 | MOSS-Image-SFT INT8 + Qwen3.5-9B 编码器 + FLUX.2 small VAE | 13.31 GiB | 想用自然语言写场景，或做中文文字/版面控制 |
| 文生图 + 参考图改图 | Qwen-Image 2.1 INT8 + Qwen3-VL-8B 编码器 + Qwen 2.1 VAE | 16.10 GiB | 想从一张图继续编辑、换场景、修细节 |
| 写实人物风格 | 用 NoctQ V4 替换上行的 Qwen 主模型，编码器和 VAE 继续复用 | 额外 6.76 GiB | 更偏向写实肖像，愿意比较不同底模效果 |

这里的数字是**文件体积之和，不是最低显存要求**。8GB 显存下，我用 ComfyUI 的 `--lowvram` 加载量化权重；实际速度会受内存卸载、分辨率、采样步数和其他程序占用影响。先用一张小图确认整条链路能出图，再提高尺寸。

模型出处：[MOSS-Image-SFT](https://huggingface.co/OpenMOSS-Team/MOSS-Image-SFT)、[Qwen-Image 2.1](https://huggingface.co/Qwen/Qwen-Image-2.1)、[NoctQ V4](https://huggingface.co/Noctaluna/Noct-Q-Uncensored-Qwen-Image-2.1?not-for-all-audiences=true)。这台机器上的 MOSS 主模型和编码器是本地 INT8 `unfused_modelscope` 文件；能确认其上游项目，但没有核实精确量化发布页。

## LoRA 按任务选，别一开始全叠

**MOSS 路线**：想做成人向风格，可以试 [axiomofmind 的 MOSS LoRA](https://huggingface.co/axiomofmind/Moss-image-nsfw-uncensored-lora?not-for-all-audiences=true)，本机装的是 ComfyUI 原生转换文件；作者给出的触发词是 `moss_xvstyle`，建议强度 0.7。其训练配置写的是 MOSS-Image-Base，用在本机 SFT 上的效果应单独测试。想缩短采样时间，换用 [lodestones 的 MOSS 8 步 LoRA](https://huggingface.co/lodestones/MOSS-Image-SFT-8-steps-dmd)，其 `lora.safetensors` 建议强度 1.0、8 步、CFG 1。它是另一种采样路线，不要把“8 步”理解成任何工作流都能直接改成 8 步；两个 LoRA 叠加也尚未在这份盘点中验证。

**Qwen 路线**：成人向人像可从一个 LoRA 开始测试，例如 TheseAlpacas、FemaledVaginus 或姿势类；先固定底模、种子和提示词，只换一个 LoRA 做 A/B 比较。追求速度时，Pruna 有 [5 步与 8 步](https://huggingface.co/PrunaAI/Pruna-Q-Image-2.1) 两个版本，作者明确说 8 步画质优先、5 步速度优先，而且各自需要对应的 sigma 与 CFG 设置。另一条 6 步路线是 [Viggle Turbo](https://huggingface.co/Viggle/Qwen-Image-2.1-viggle-turbo)。提速 LoRA 一次先选一个；混搭要单独验证。

**编辑和控制**：参考图换人有 [BFS Head/Body](https://huggingface.co/Alissonerdx/BFS-Best-Face-Swap)，照片保真修复有 [AusBoss Restore](https://huggingface.co/ausboss/Qwen-Image-2.1-Restore-LoRA)，需要精确姿势可以用 [VNCCS PoseStudio](https://huggingface.co/MIUProject/VNCCS_PoseStudio_QI2.1) 加对应 3D 姿势工作流。它们解决的是不同问题，不会因为同在 `loras` 文件夹就自动协同。
再次提醒：严禁利用任何未经授权的真人图片制作任何内容！！！
再次提醒：严禁利用任何未经授权的真人图片制作任何内容！！！
再次提醒：严禁利用任何未经授权的真人图片制作任何内容！！！

## 实际安装顺序

1. 从 [ComfyUI 官方项目](https://github.com/Comfy-Org/ComfyUI)安装 Windows 便携版。先启动并确认浏览器能打开本机服务。
2. 只下载上表中的**一个家族**：主模型放 `models/diffusion_models`，文本编码器放 `models/text_encoders`，VAE 放 `models/vae`，LoRA 放 `models/loras`。文件名要与工作流加载节点一致。
3. 先跑官方或简化工作流，确认底模、编码器、VAE 能出图；再加一个对应家族的 LoRA。出现“缺失模型/节点”时，先看当前工作流加载的**具体文件名**，不要盲目下载整个合集。
4. 保存每次成功的工作流 JSON、种子与参数。成人向图片如果要分享，检查 PNG 元数据是否附带提示词、工作流和私人路径。

本机目录里还装了 [SeedVR2](https://huggingface.co/Comfy-Org/SeedVR2)、[Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN) 和 [MiniMax H3](https://huggingface.co/Comfy-Org/MiniMax-H3)。前两者负责后期修复/放大；H3 是视频路线。这些都不是第一张成人向图片的必装项，尤其 H3 的模型文件很大，8GB 显存下运行成本高。

**最后一句**：底模决定能力边界，LoRA 决定专项倾向，工作流决定它们能否正确接起来。只创作明确成年的内容；不要制作未成年人、未经同意的真人性化图像，也别把私人的原图与带元数据的成图随手公开。

---

完整的本机 37 个文件、各 LoRA 的对应仓库与来源核实状态，见[完整模型与 LoRA 清单](docs/models.md)。工作流示例见[下载与使用说明](workflows/README.md)。

## 来源与更新
本文仅作收集与归纳，全部模型及工作流来源于文中链接，不做任何商业性质
模型、LoRA 和节点项目的作者与仓库链接列在表格中，使用和再分发请遵守各项目许可。这里的本机文件名、大小与安装状态是盘点快照，不能替代上游模型卡和安装说明。

封面为 AI 生成的虚构成年女性时尚肖像。示例工作流采用普通场景提示词；仓库没有附带模型权重或私人参考图。

发现链接失效、版本变化或说明错误，可以提交 Issue，附上模型版本、节点版本和错误信息；提交前请去掉访问凭据与私人路径。

