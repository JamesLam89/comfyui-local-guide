# ComfyUI 本机模型与 LoRA 清单

盘点时间：2026-10-22。盘点范围：作者 ComfyUI 的 `models/` 目录。当前服务为 ComfyUI 0.42.0，GPU 为 RTX 4060 Laptop 8GB，启动参数包含 `--lowvram`。盘点到 **37 个权重文件，约 77.19 GB（71.89 GiB）**：5 个主模型、14 个 LoRA、18 个配套文件。`checkpoints`、`controlnet` 目录当前没有权重。

**口径**：以下“已安装”只表示本机有文件；“工作流引用”表示保存的 JSON 中写有文件名，不代表逐个完成出图测试。链接优先给原作者/官方仓库；如只能确认上游项目而无法确认这个本机量化文件的精确发布仓库，会标明。大小为本机文件的 GiB，四舍五入。

## 主模型（5 个）

| 本机文件 | 大小 | 优势和功能 | 需要搭配 | 仓库/来源 |
|---|---:|---|---|---|
| `moss_image_sft_int8_unfused_modelscope.safetensors` | 4.831 | MOSS-Image-SFT 文生图；自然语言、中文文字和 JSON 布局控制。INT8 版省磁盘/显存。 | `qwen3.5_9b_int8_unfused_modelscope` + `flux2-vae_small`；MOSS 专用 LoRA | [OpenMOSS 官方 SFT](https://huggingface.co/OpenMOSS-Team/MOSS-Image-SFT)；本机这份 `unfused_modelscope` 的精确量化发布页未核实 |
| `qwen_image_2.1_int8_convrot.safetensors` | 6.758 | Qwen-Image 2.1 通用文生图和参考图编辑；Qwen 系 LoRA 的基础。 | `qwen3vl_8b_int8_convrot` + `qwen_image_2.1_vae_bf16` | [Qwen 原版](https://huggingface.co/Qwen/Qwen-Image-2.1) · [ComfyUI 单文件版](https://huggingface.co/Comfy-Org/Qwen-Image-2.1) |
| `NoctQ_V4_int8_convrot.safetensors` | 6.758 | 基于 Qwen-Image 2.1 的写实人物/成人向微调底模；适合写实肖像。 | 复用 Qwen 2.1 的 8B 编码器与 VAE；LoRA 须验证兼容性 | [Noctaluna 原仓库](https://huggingface.co/Noctaluna/Noct-Q-Uncensored-Qwen-Image-2.1?not-for-all-audiences=true) |
| `seedvr2_7b_sharp_int8_convrot.safetensors` | 7.762 | 图像/视频修复与放大，偏细节重建；是后处理模型，不是文生图底模。 | `seedvr2_ema_vae_fp16` | [Comfy-Org SeedVR2](https://huggingface.co/Comfy-Org/SeedVR2) |
| `minimax_h3_fl2va_pruned_w6a8.safetensors` | 14.886 | MiniMax H3 首尾帧到有声视频；W6A8 剪枝量化版。8GB 显存需卸载/分块，速度不能按轻量图像模型预期。 | H3 视频/音频 VAE；本机工作流用 4B 编码器替代方案 | [MiniMax 原版](https://huggingface.co/MiniMaxAI/MiniMax-H3) · [Comfy-Org 单文件版](https://huggingface.co/Comfy-Org/MiniMax-H3) |

## LoRA（14 个）

| 本机文件 | 适配底模 | 优势和功能 | 本机工作流引用 | 仓库/来源 |
|---|---|---|---|---|
| `MOSS-Image-NSFW-Uncensored-LoRA-ComfyUI.safetensors` | MOSS-Image；训练配置写的是 Base | 成人向风格适配；ComfyUI 原生命名空间版本，触发词 `moss_xvstyle`，作者建议强度 0.7。本机 SFT 上的效果须实测。 | 是 | [axiomofmind 原仓库](https://huggingface.co/axiomofmind/Moss-image-nsfw-uncensored-lora?not-for-all-audiences=true) |
| `MOSS-Image-SFT-8-steps-dmd.safetensors` | MOSS-Image-SFT | 8 步蒸馏提速，作者建议 `lora.safetensors` 强度 1.0、CFG 1；与普通 50 步设置分开用。 | 是 | [lodestones 原仓库](https://huggingface.co/lodestones/MOSS-Image-SFT-8-steps-dmd) |
| `bfs_head_v1.1_qwen_2.1.safetensors` | Qwen-Image 2.1 | 双参考图换头/身份迁移；v1.1 针对皮肤过度磨平做了调整。 | 是 | [BFS 原仓库](https://huggingface.co/Alissonerdx/BFS-Best-Face-Swap) |
| `bfs_body_swap_v1.0_qwen_2.1.safetensors` | Qwen-Image 2.1 | 参考图整个人物替换，保留场景姿势、构图与光照；需按作者规定的图序输入。 | 是 | [BFS 原仓库与说明](https://huggingface.co/Alissonerdx/BFS-Best-Face-Swap/blob/main/docs/qwen-image-2.1-body.md) |
| `nicegirls_v2_qwen21.safetensors` | Qwen-Image 2.1 | 写实人像/美感风格；人物外观可能偏向训练风格。 | 是 | [Danrisi 原仓库](https://huggingface.co/Danrisi/nicegirls2_qwen2.1) |
| `nsfw-posture-v3.safetensors` | Qwen-Image 2.1 | 成人向姿势/身体表现增强；具体强度需自行对比。 | 否 | [Civitai 模型页](https://civitai.com/models/2988215)；此站当前无法由检索工具直接打开，出处据现有安装说明 |
| `p_qwen_image_2.1_5step_v0.1-comfyui-T8.safetensors` | Qwen-Image 2.1 | Pruna 5 步提速，速度优先，官方说明画质低于 8 步。须配套 5 步 sigma/CFG 1。 | 否 | [Pruna 原仓库](https://huggingface.co/PrunaAI/Pruna-Q-Image-2.1)；本机 `-comfyui-T8` 为转换版 |
| `p_qwen_image_2.1_8step_v0.1-comfyui-T8.safetensors` | Qwen-Image 2.1 | Pruna 8 步提速，质量优先于其 5 步版。须配套 8 步 sigma/CFG 1。 | 是 | [Pruna 原仓库](https://huggingface.co/PrunaAI/Pruna-Q-Image-2.1)；本机 `-comfyui-T8` 为转换版 |
| `qwen-image-2.1-restore.safetensors` | Qwen-Image 2.1 | 旧照/低清图修复，降低重绘时的构图、身份与颜色漂移；属于图像编辑路线。 | 是 | [AusBoss 原仓库](https://huggingface.co/ausboss/Qwen-Image-2.1-Restore-LoRA) |
| `Qwen-Image-2.1-viggle-turbo-v0.3-6step-lora-r128.safetensors` | Qwen-Image 2.1 | 6 步提速 LoRA；需使用作者配套采样设置/节点。 | 是 | [Viggle 原仓库](https://huggingface.co/Viggle/Qwen-Image-2.1-viggle-turbo) |
| `qwen2_1-femaledVaginus-nsfw-v1_3-fine_L22-31.safetensors` | Qwen-Image 2.1 | 成人向人体细节/姿势适配。 | 否 | [Civitai 模型页](https://civitai.com/models/2986844)；[文件镜像](https://huggingface.co/mpasila/Qwen-Image-2.1-LoRAs/blob/main/qwen2_1-femaledVaginus-nsfw-v1_3-fine_L22-31.safetensors) |
| `qwen21-nsfw-image-edit.safetensors` | Qwen-Image 2.1 编辑路线 | 成人向图片编辑 LoRA；与普通文生图 LoRA 的用法不同。 | 否 | 安装记录指向 `RunningHubAI/Qwen-Image-2.1-NSFW-Image-Edit`，公开页面目前无法核实；[安装记录](https://github.com/Arekejoker/qwen21-nsfw-pose-skill/blob/main/install/setup.md) |
| `thesealpacas-nsfw-v2.safetensors` | Qwen-Image 2.1 | 成人向人像风格；可能改变面部特征，适合单独做 A/B 比较。 | 是 | [Civitai 模型页](https://civitai.com/models/2958918)；此站当前无法由检索工具直接打开，出处据现有安装说明 |
| `VNCCS_QI2_PoseStudioV1.1.safetensors` | Qwen-Image 2.1 | 搭配 VNCCS 3D 人偶/姿势工作流做精确姿势控制；单独加载 LoRA 不等于完成控姿。 | 是 | [MIUProject 原仓库](https://huggingface.co/MIUProject/VNCCS_PoseStudio_QI2.1) |

**搭配原则**：MOSS LoRA 不直接用于 Qwen/NoctQ；MOSS 的成人向 LoRA 与 SFT、8 步 LoRA 同时叠加的表现未经本清单验证。Qwen 的提速 LoRA 一次先选一个，并使用该版本规定的步数与 sigma。BFS 换人像仅用于有授权的素材，成人向创作只使用明确成年的虚构人物或已获许可的人像。

## 编码器、VAE、放大与预览权重（18 个）

| 本机文件 | 功能/归属 | 仓库/来源 |
|---|---|---|
| `text_encoders/qwen3.5_9b_int8_unfused_modelscope.safetensors` | MOSS 提示词编码器；本机量化文件精确发布页未核实。 | [OpenMOSS SFT 上游](https://huggingface.co/OpenMOSS-Team/MOSS-Image-SFT) |
| `text_encoders/qwen3vl_8b_int8_convrot.safetensors` | Qwen-Image 2.1 / NoctQ 的文本和参考图理解编码器。 | [Comfy-Org Qwen-Image-2.1](https://huggingface.co/Comfy-Org/Qwen-Image-2.1) |
| `text_encoders/qwen3vl_4b_fp8_scaled.safetensors` | 本机 H3 工作流用的轻量 4B 替代编码器；原文件出自 Krea-2，非 MiniMax 官方 32B 编码器。 | [Comfy-Org Krea-2 文件](https://huggingface.co/Comfy-Org/Krea-2/blob/main/text_encoders/qwen3vl_4b_fp8_scaled.safetensors) · [H3 轻量方案](https://github.com/Comfy-Org/comfystreamerh3) |
| `clip_projections/mmh3-4b-ClipProj-v3.1.safetensors` | 4B 编码器接 H3 的投影适配。 | [NicoLab28 原仓库](https://huggingface.co/NicoLab28/ClipProj-MiniMax-H3) |
| `vae/flux2-vae_small.safetensors` | MOSS 编码/解码；本机 MOSS 工作流使用。 | [Comfy-Org MOSS-Image](https://huggingface.co/Comfy-Org/MOSS-Image) |
| `vae/qwen_image_2.1_vae_bf16.safetensors` | Qwen-Image 2.1 / NoctQ 编码/解码。 | [Comfy-Org Qwen-Image-2.1](https://huggingface.co/Comfy-Org/Qwen-Image-2.1) |
| `vae/seedvr2_ema_vae_fp16.safetensors` | SeedVR2 修复/放大解码。 | [Comfy-Org SeedVR2](https://huggingface.co/Comfy-Org/SeedVR2) |
| `vae/minimax_h3_video_vae_int8_convrot.safetensors` | H3 视频 VAE。 | [Comfy-Org MiniMax-H3](https://huggingface.co/Comfy-Org/MiniMax-H3) |
| `vae/minimax_h3_audio_vae_fp32.safetensors` | H3 音频 VAE。 | [Comfy-Org MiniMax-H3](https://huggingface.co/Comfy-Org/MiniMax-H3) |
| `vae/ae.safetensors` | FLUX.1 系自动编码器；当前五个主模型的已保存工作流未见使用。 | [Black Forest Labs FLUX.1-dev 上游](https://huggingface.co/black-forest-labs/FLUX.1-dev)；本机文件精确来源未核实 |
| `upscale_models/RealESRGAN_x2plus.pth` | 通用图片 2× 放大。 | [Real-ESRGAN 官方模型表](https://github.com/xinntao/Real-ESRGAN/blob/master/docs/model_zoo.md) |
| `upscale_models/RealESRGAN_x4plus.safetensors` | 通用图片 4× 放大；本机为 safetensors 格式，精确转换来源未核实。 | [Real-ESRGAN 官方模型表](https://github.com/xinntao/Real-ESRGAN/blob/master/docs/model_zoo.md) |
| `vae_approx/taef1_encoder.safetensors`、`taef1_decoder.safetensors` | FLUX 系快速预览；不是最终 VAE 输出。 | [ComfyUI 项目](https://github.com/Comfy-Org/ComfyUI) |
| `vae_approx/taesd_encoder.safetensors`、`taesd_decoder.safetensors` | SD 系快速预览；不是最终 VAE 输出。 | [TAESD 项目](https://github.com/madebyollin/taesd) |
| `vae_approx/taesdxl_encoder.safetensors`、`taesdxl_decoder.safetensors` | SDXL 系快速预览；不是最终 VAE 输出。 | [TAESD 项目](https://github.com/madebyollin/taesd) |

> 下表中的工作流名称为作者本机记录，不代表全部随仓库分发；实际提供的文件以[工作流目录](../workflows/README.md)为准。

## 当前工作流的实用分组

| 需求 | 推荐本机组合 | 本机保存的工作流示例 |
|---|---|---|
| 文生图、中文文字/布局 | MOSS SFT + MOSS 编码器 + small VAE；可选 NSFW LoRA 或 8 步 LoRA | `MOSS_原工作流_补全版_8_25_50步.json` |
| 文生图和图生图编辑 | Qwen-Image 2.1 + Qwen 8B 编码器 + Qwen VAE | `换样换图.json`、`Qwen+Image+2.1+多合一工作流小志版.json` |
| 写实人物 | NoctQ V4 + Qwen 编码器/VAE | `Noct-Q-V4-参考图-文生图.json`、`Noct-Q-V4-参考图-改图.json` |
| 图像修复/放大 | SeedVR2 或 RealESRGAN；两者属于后处理 | `MOSS_原工作流_补全版_8_25_50步.json`、`4K_竖图放大_保真与AI增强.json` |
| 首尾帧有声视频 | MiniMax H3 + H3 的 VAE 与本机 4B 适配组合 | `MiniMax_H3_竖屏有声视频.json` 等 |

**未安装但旧工作流仍引用**：原始 MOSS 75 节点文件中的 `moss_image_sft_bf16.safetensors`、`qwen3.5_9b_bf16.safetensors`、`flux2-vae.safetensors` 与可选 `Moss监控视角pinholecamview.safetensors` 并不在本机目录中。应使用补全版工作流所指向的本机量化文件，不要把旧文件名当成已经安装。

