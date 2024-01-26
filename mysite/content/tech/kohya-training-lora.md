---
title: kohya-training-lora
slug: kohya-training-lora
author: [jianchengwang]
date: 2023-06-02
excerpt: "kohya training lora"
draft: true
tags: [aigc]
---

最近简单用stable-difusson简单玩一下，并用kohya训练一下lora模型，这边简单记录下。

## 常见术语

### 模型种类

#### 大模型

大模型特指标准的`latent-diffusion`模型。拥有完整的`TextEncoder`、`U-Net`、`VAE`。

由于想要训练一个大模型非常困难，需要极高的显卡算力，所以更多的人选择去训练小型模型。

##### CKPT

CKPT格式的全称为`CheckPoint`（检查点），完整模型的常见格式，模型体积较大，一般单个模型的大小在7GB左右。

文件位置：该模型一般放置在`*\stable-diffusion-webui\models\Stable-diffusion`目录内。

### 小模型

小模型一般都是截取大模型的某一特定部分，虽然不如大模型能力那样完整，但是小而精，因为训练的方向各为明确，所以在生成特定内容的情况下，效果更佳。

常见微调模型：`Textual inversion (Embedding)`、`Hypernetwork`、`VAE`、`LoRA`等，下面一一进行介绍。

#### VAE

全称：VAE全称`Variational autoencoder`。变分自编码器，负责将潜空间的数据转换为正常图像。

后缀格式：后缀一般为.pt格式。

功能描述：类似于滤镜一样的东西，他会影响出图的画面的色彩和某些极其微小的细节。大模型本身里面自带 VAE ，但是并不是所有大模型都适合使用VAE，VAE最好搭配指定的模型，避免出现反效果，降低生成质量。

使用方法：`设置 -> Stable-Diffusion -> 模型的 VAE (SD VAE)`，在该选项框内选择VAE模型。

文件位置：该模型一般放置在`*\stable-diffusion-webui\models\VAE`目录内。

#### Embedding

常见格式为`pt`、`png`、`webp`格式，文件体积一般只有几KB。

风格模型，即只针对一个风格或一个主题，并将其作为一个模块在生成画作时使用对应TAG在Prompt进行调用。

使用方法：例如本站用数百张`海绵宝宝`训练了一个Embedding模型，然后将该模型命名为`HMBaby`，在使用AI绘图时加载名称为`HMBaby`的Embedding模型，在使用Promat时加入`HMBaby`的Tag关键字，SD将会自动调用该模型参与AI创作。

文件位置：该模型一般放置在`*\stable-diffusion-webui\embeddings`目录内。

#### Hypernetwork

一般为.pt后缀格式，大小一般在几十兆左右。这种模型的可自定义的参数非常之多。

使用方法：使用方法：在SD的文生图或图生图界面内的生成按钮下，可以看到一个红色的图标，该图标名为`Show extra networks（显示额外网络）`，点击该红色图标将会在本页弹出一个面板，在该面板中可以看到`Hypernetwork选项卡`。

文件位置：该模型一般放置在`*\stable-diffusion-webui\models\hypernetworks`目录内。

#### LoRA

LoRA的模型分两种，一种是基础模型，一种是变体。

目前最新版本的Stable-diffusion-WebUI原生支持Lora模型库，非常方便使用。

##### 基础模型

名称一般为`chilloutmix*`，后缀可能为safetensors或CKPT。

基础模型存放位置：`*\stable-diffusion-webui\models\Stable-diffusion`目录内。

##### 变体模型

变体模型存放位置：`*\stable-diffusion-webui\models\Lora`目录内。

是放在extensions下的，sd-webui-additional-networks文件夹下的models文件夹里的lora！！

不是主文件夹下的models，别放错了！！！

### 模型后缀解析

#### Safetensors格式

-   Safetensors格式所生成的内容与ckpt等格式完全一致（包括NFSW）。
-   Safetensors格式拥有更高的安全性，
-   Safetensors比ckpt格式加载速度更快
-   该格式必须在2023年之后的Stable Diffusion内才可以使用，在此之间的SD版本内使用将无法识别。
-   Safetensors格式由Huggingface推出，将会逐渐取代ckpt、pt、pth等格式，使用方法上与其它格式完全一致。

#### Pickle反序列化攻击

可以将字节流转换为一个对象，但是当我们程序接受任意输入时，如果用户的输入包含一些恶意的序列化数据，然后这些数据在服务器上被反序列化，服务器是在将用户的输入转换为一个对象，之后服务器就会被任意代码执行。

### 模型训练

#### Embedding (Textual inversion)

可训练：画风√ 人物√ | 推荐训练：人物

配置要求：显存6GB以上。

训练速度：中等 | 训练难度：中等

综合评价：☆☆☆

#### Hypernetwork

可训练：画风√ 人物√ | 推荐训练：画风

配置要求：显存6GB以上。

训练速度：中等 | 训练难度：难

综合评价：☆☆

评价：非常强大的一种模型，但是想训练好很难，不推荐训练。

#### LoRA

可训练：画风? 人物√  概念√ | 推荐训练：人物

配置要求：显存8GB以上。

训练速度：快 | 训练难度：简单

综合评价：☆☆☆☆

评价：非常好训练 好出效果的人物训练，配置要求低，图要求少。

备注：LoRA 本身也应该归类到 Dreambooth，但是这里还是分开讲。

#### Dreambooth / Native Train

可训练：画风√ 人物√ 概念√ | 推荐训练：Dreambooth 推荐人物，Native Train 推荐画风

配置要求：显存12GB以上。

训练速度：慢 | 训练难度：可以简单可以很难

综合评价：☆☆☆☆☆

评价：微调大模型，非常强大的训练方式，但是使用上会不那么灵活，推荐训练画风用，人物使用 LoRA 训练。

#### DreamArtist

显存要求6GB（4GB应该也可以），只需要（也只能）使用一张图完成训练，一般用于训练人物（画风没法抓住主次），优点是训练要求极低，成功率高，缺点是容易过拟合，并且不像Embedding可以跨模型应用，这个训练时使用什么模型应用时就要用什么，哪怕调一下CLIP参数生成结果都会完全跑飞。推荐每250步保存模型，后期用X/Y图脚本进行挑选。

### 模型后缀

仓库内一般存在多个模型文件，文件名后缀各不相同，这里简单介绍下文件名常见后缀及其含义：

#### ControlNet

ControlNet比之前的img2img要更加的精准和有效，可以直接提取画面的构图，人物的姿势和画  
面的深度信息等等。有了它的帮助，就不用频繁的用提示词来碰运气，抽卡式的创作了。

#### instruct-pix2pix

在 stable-diffusion-webui 中的img2img专用模型 自然语言指导图像编辑 生成速度极快 ，仅需要几秒的时间。

#### FP16、FP32

代表着精度不同，精度越高所需显存越大，效果也会有所提升。

#### 512｜768

代表着默认训练分辨率时512X512还是768X768，理论上默认分辨率高生成效果也会相应更好。

#### inpaint

代表着是专门为imgtoimg中的inpaint功能训练的模型，在做inpaint时效果会相对来说较好。

#### depth

代表此模型是能包含处理图片深度信息并进行inpainting和img2img的

#### EMA

模型文件名中带EMA一般意味着这是个用来继续训练的模型,文件大小相对较大

与之相比,正常的、大小相当较小的那个模型文件是为了做推理生成的

对于那些有兴趣真正理解发生了什么的人来说，应该使用EMA模型来进行推理

小模型实际上有EMA权重。而大模型是一个 “完整版”，既有EMA权重，也有标准权重。因此，如果你想训练这个模型，你应该加载完整的模型，并使用use_ema=False。

#### EMA权重

就像你作为一个学生在接受训练时，也许你会在最后一次考试表现较差，或者决定作弊并记住答案。所以一般来说，通过使用考试分数的平均值，你可以更好地了解到学生的表现，

由于你不关心幼儿园时的分数，如果你只考虑去年的分数(即只用一组最近的实际数据值来预测)，你会得到`MA`（moving average 移动平均数). 而如果你保留整个历史，但给最近的分数以更大的权重，则会得到`EMA`(exponential moving average 指数移动平均数)。

这对具有不稳定训练动态的GANs来说是一个非常重要的技巧，但对扩散模型来说，它其实并不是那么重要。

#### VAE

VAE模型文件并不能和正常模型文件一样独立完成图片生成。

## stable-diffusion-webui

参考 https://github.com/AUTOMATIC1111/stable-diffusion-webui

1.  Install [Python 3.10.6](https://www.python.org/downloads/release/python-3106/) (Newer version of Python does not support torch), checking "Add Python to PATH".
2.  Install [git](https://git-scm.com/download/win).
3.  Download the stable-diffusion-webui repository, for example by running `git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git`.
4.  Run `webui-user.bat` from Windows Explorer as normal, non-administrator, user. or `.\webui.bat --no-gradio-queue`
6. anaconda https://www.anaconda.com/
7. cuda https://developer.nvidia.com/cuda-downloads
8. modules download https://huggingface.co/runwayml/stable-diffusion-v1-5 https://civitai.com/

## kohya

### 安装

参考 https://github.com/bmaltais/kohya_ss
https://www.youtube.com/watch?v=70H03cv57-o

-   Install [Python 3.10](https://www.python.org/ftp/python/3.10.9/python-3.10.9-amd64.exe)
    -   make sure to tick the box to add Python to the 'PATH' environment variable
-   Install [Git](https://git-scm.com/download/win)
-   Install [Visual Studio 2015, 2017, 2019, and 2022 redistributable](https://aka.ms/vs/17/release/vc_redist.x64.exe)

In the terminal, run:

```
git clone https://github.com/bmaltais/kohya_ss.git
cd kohya_ss
.\setup.bat
```

add optional CUDNN 8.6

This step is optional but can improve the learning speed for NVIDIA 30X0/40X0 owners. It allows for larger training batch size and faster training speed.

Due to the file size, I can't host the DLLs needed for CUDNN 8.6 on Github. I strongly advise you download them for a speed boost in sample generation (almost 50% on 4090 GPU) you can download them [here](https://b1.thefileditch.ch/mwxKTEtelILoIbMbruuM.zip).

To install, simply unzip the directory and place the `cudnn_windows` folder in the root of the this repo.

Run the following commands to install:

```
.\venv\Scripts\activate

python .\tools\cudann_1.8_install.py
```

upgrade 

```
upgrade.bat
```

gui

```
gui.bat --listen 127.0.0.1 --server_port 7860
```

### 准备数据

1. google image
2. https://www.birme.net/ crop image
3. `gui.bat` kohya-gui
4. utils-BLIP Captioning 处理图片，注意图片目录一般以`100_`数字代表迭代步数，一般至少要1500，比如你只有10张图片，图片目录名称要以`150_`开始

### 训练

Dreambooth-LoRA，选择你的模型，跟文件目录，训练的参数可以参考如下两个配置文件。

LoraBasicSettings.json

```json
{
  "pretrained_model_name_or_path": "runwayml/stable-diffusion-v1-5",
  "v2": false,
  "v_parameterization": false,
  "logging_dir": "C:/Users/ohmni/AI/SAMPLE IMAGES/ADAMS/LORA OUTPUT/log",
  "train_data_dir": "C:/Users/ohmni/AI/SAMPLE IMAGES/ADAMS/LORA OUTPUT/img",
  "reg_data_dir": "",
  "output_dir": "C:/Users/ohmni/AI/SAMPLE IMAGES/ADAMS/LORA OUTPUT/model",
  "max_resolution": "512,512",
  "learning_rate": "0.0001",
  "lr_scheduler": "constant",
  "lr_warmup": "0",
  "train_batch_size": 2,
  "epoch": "1",
  "save_every_n_epochs": "1",
  "mixed_precision": "bf16",
  "save_precision": "bf16",
  "seed": "1234",
  "num_cpu_threads_per_process": 2,
  "cache_latents": true,
  "caption_extension": ".txt",
  "enable_bucket": false,
  "gradient_checkpointing": false,
  "full_fp16": false,
  "no_token_padding": false,
  "stop_text_encoder_training": 0,
  "use_8bit_adam": true,
  "xformers": true,
  "save_model_as": "safetensors",
  "shuffle_caption": false,
  "save_state": false,
  "resume": "",
  "prior_loss_weight": 1.0,
  "text_encoder_lr": "5e-5",
  "unet_lr": "0.0001",
  "network_dim": 128,
  "lora_network_weights": "",
  "color_aug": false,
  "flip_aug": false,
  "clip_skip": 2,
  "gradient_accumulation_steps": 1.0,
  "mem_eff_attn": false,
  "output_name": "Addams",
  "model_list": "runwayml/stable-diffusion-v1-5",
  "max_token_length": "75",
  "max_train_epochs": "",
  "max_data_loader_n_workers": "1",
  "network_alpha": 128,
  "training_comment": "",
  "keep_tokens": "0",
  "lr_scheduler_num_cycles": "",
  "lr_scheduler_power": ""
}
```

LoraLowVRAMSettings.json

```json
{
  "pretrained_model_name_or_path": "runwayml/stable-diffusion-v1-5",
  "v2": false,
  "v_parameterization": false,
  "logging_dir": "C:/Users/ohmni/AI/SAMPLE IMAGES/ADAMS/LORA OUTPUT/log",
  "train_data_dir": "C:/Users/ohmni/AI/SAMPLE IMAGES/ADAMS/LORA OUTPUT/img",
  "reg_data_dir": "",
  "output_dir": "C:/Users/ohmni/AI/SAMPLE IMAGES/ADAMS/LORA OUTPUT/model",
  "max_resolution": "512,512",
  "learning_rate": "0.0001",
  "lr_scheduler": "constant",
  "lr_warmup": "0",
  "train_batch_size": 1,
  "epoch": "1",
  "save_every_n_epochs": "1",
  "mixed_precision": "fp16",
  "save_precision": "fp16",
  "seed": "1234",
  "num_cpu_threads_per_process": 2,
  "cache_latents": true,
  "caption_extension": ".txt",
  "enable_bucket": false,
  "gradient_checkpointing": true,
  "full_fp16": false,
  "no_token_padding": false,
  "stop_text_encoder_training": 0,
  "use_8bit_adam": true,
  "xformers": true,
  "save_model_as": "safetensors",
  "shuffle_caption": false,
  "save_state": false,
  "resume": "",
  "prior_loss_weight": 1.0,
  "text_encoder_lr": "5e-5",
  "unet_lr": "0.0001",
  "network_dim": 128,
  "lora_network_weights": "",
  "color_aug": false,
  "flip_aug": false,
  "clip_skip": 2,
  "gradient_accumulation_steps": 1.0,
  "mem_eff_attn": true,
  "output_name": "Addams",
  "model_list": "runwayml/stable-diffusion-v1-5",
  "max_token_length": "75",
  "max_train_epochs": "",
  "max_data_loader_n_workers": "1",
  "network_alpha": 128,
  "training_comment": "",
  "keep_tokens": "0",
  "lr_scheduler_num_cycles": "",
  "lr_scheduler_power": ""
}
```

### 参数说明
| 参数                 | 中文                     | 参数影响            | 参数值        | 备注                                       |
|----------------------|--------------------------|----------------------|---------------|--------------------------------------------|
| Repeat               | 学习次数                 | 次数多，学习效果好。 | 30            | 本次训练素材文件夹名为30_Zenny，代表Repeat的次数为30次。 |
| Epoch                | 循环                     | 循环多，学习效果好。 | 1             | 设置为1，只循环训练1次，生成1个模型。               |
| Batch_size           | GPU并行数量              | 越高，训练越快。    | 1             | 站长电脑较差，GPU并行任务数设置为1。                |
| Learning rate        | 学习率                   | 学习速度            | 0.00003       | 填写我们之前推算出的3e-5转换为10进制。                 |
| Unet learning rate   | 学习率                   | 学习速度            | 0.00003       | 填写我们之前推算出的3e-5转换为10进制。                 |
| Text Encoder learning rate | 学习文本编码器的学习率  | 对Tag更敏感         | 0.000003      | 填写我们之前推算出的3e-5转换为10进制除以10。            |
| Network Rank (Dimension) | 学习精度              | 数值越高，细节越好。 | 128           | 因为我们训练的是人物，所以推荐填写128。                  |
| Network Alpha        | 权重缩放                 | LoRA权重缩放的Alpha | 64            | LoRA权重缩放的Alpha，推荐填写为64即可。                |
| LR Scheduler         | 学习率调度器             | 训练时的算法        | constant_with_warmup | 无法选择哪种训练方法，都推荐使用该参数。        |
| Optimizer            | 优化器类型               | 训练时的关键算法    | AdamW8bit     | 不要更改，否则不适配上面的所有参数。                   |
| LR warmup (% of steps) | 逐渐改变学习率          | 定预热调度程序的步数 | 0             | 我们已经找到了最佳参数，所以关闭该选项。                 |


## 相关链接

https://github.com/bmaltais/kohya_ss
https://github.com/AUTOMATIC1111/stable-diffusion-webui
https://huggingface.co/
https://civitai.com/
https://www.youtube.com/watch?v=70H03cv57-o
https://aituts.com/stable-diffusion-lora/
https://openai.wiki/painting/sd
https://ai-edu.openai.wiki/index.html
https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/10793
