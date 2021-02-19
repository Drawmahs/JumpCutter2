[中文](./README.md)　|　[English](./README_en.md) 

[Gitee](https://gitee.com/haujet/JumpCutter2)　|　[Github](https://github.com/HaujetZhao/JumpCutter2) 

# JumpCutter2

## ⭐ 简介

功能： 使用 spleeter 将视频中的人声提取出来（去除背景音），再对视频中的声音进行分析，分成静音部分和非静音部分，分别施加不同的速度，最后合成到一个新视频。

用途示例： 

* 录制教程、vlog 类视频，没有文字稿，就必然会有许多空隙片段，用于思考下一句说什么，这些片段多是没有信息含量的，加速剪掉，可以提升用户观看体验。
* 在许多种类的视频中，只有有声音的片段是人在说话的，其他安静的片段信息含量很少，就可以对静音部分加倍速，减少无用信息。比如将一个网课视频有声速度调为 1，静音速度调为 9999。

下载：

- 发行版下载地址： [Releases](../../releases) 

使用教程视频地址：https://www.bilibili.com/video/BV165411N7By/

## 📝 背景

原来 [JumpCutter](https://github.com/carykh/jumpcutter) 需要把视频所有帧都提取成照片，存储在硬盘上，一段100多兆的视频，就可能产生20多G的图片文件，对硬盘寿命影响很大。

改进后使用标准输入和标准输出，数据都是经过内存，不再频繁读写硬盘。

结合了 pyav 多进程读取视频、subprocess ffmpeg 写视频，速度是 JumpCutter 的 2 到 3 倍多。

Spleeter 可以将音频中的人声和背景声（音乐、风声、杂音）分开。如果你的视频的声音中，有大量杂音，影响自动分段，那就可以启用这个选项，脚本会先将音频声音中的背景音乐、杂音去掉，只留下纯净的人声，再进行分段变速。

再加上 spleeter 去除背景音，可以做到比 JumpCutter 更好的效果。

## ✨ 特性

两种运行方式：

* 直接运行，文字引导
* 命令行运行

## 🛠️ 安装

### 📦 发行版

需要提前安装上 FFmpeg、FFprobe，一般下载上 FFmpeg 后，包里就有 FFprobe

已为 Windows 64 位打包成可直接双击运行的包，请到 [本仓库的 Releases](../../releases) 界面下载。总共有两个包，一个是依赖包（里面包含了 tensorflow 库、spleeter 模型），依赖包有 1GB 大小，下载后就不用再更新依赖包了，另一个是源码包（更新的时候只要下载新的源码包覆盖解压即可），将两个 7z 压缩包解压放在同一个文件夹后，文件夹内有一个 exe 文件，双击即可运行。 

也可以从命令行运行：

```
JumpCutter2 -h
```

我没有其他系统（例如 Linux、MacOS）的电脑，所以无法为其他系统打包，这些系统的用户需要从源代码或 pip 安装使用。（参见下文）

### ⚙️ 用 pip 安装运行

需要提前安装上 FFmpeg、FFprobe 和 Python3

另外还需要从 [spleeter](https://github.com/deezer/spleeter/releases) 下载 [5stems.tar.gz](https://github.com/deezer/spleeter/releases/download/v1.4.0/5stems.tar.gz) （训练好的模型），将其中的内容文件夹解压到 `JumpCutter2/pretrained_models/5stems` 文件夹中。

#### 用 pip 从 pypi 安装

还未上传

#### 用 pip 从源代码安装

将仓库克隆下来，进入仓库文件夹，运行：

```
pip install .
```

就安装上了。然后就可以运行以下命令使用了：

```
JumpCutter2
```

### 📄 从源代码直接运行

将仓库克隆下来，进入仓库文件夹，先安装依赖库：

```
pip install -r requirements.txt
```

再从 [spleeter](https://github.com/deezer/spleeter/releases) 下载 [5stems.tar.gz](https://github.com/deezer/spleeter/releases/download/v1.4.0/5stems.tar.gz) （训练好的模型），将其中的内容文件夹解压到 `JumpCutter2/pretrained_models/5stems` 文件夹中。

然后就可以以模块的方式运行：

```
python -m JumpCutter2
```

在音频变速部分，优先使用 [soundstretch](http://www.surina.net/soundtouch/soundstretch.html) ，这是一个 c 实现的开源的音频变速软件，可以不改变声调，对音频进行伸缩，许多知名项目都是用着它来进行音频变速。

但是它没有 python 实现，所以我只能调用他的二进制包。这个 repository 已经包含了 Windows 和 MacOS 的 soundstretch 二进制包。如果你想在其他系统上也用它来处理音频，请自行安装。

当然如果你没有安装 [soundstretch](http://www.surina.net/soundtouch/soundstretch.html) 的话，会使用次选方案 audiotsm 进行变速。但是它的变速效果有些差。

## 💡 使用

```
python -m JumpCutter2
```

```shell
python -m JumpCutter2 -h
```

第一种方式是直接运行，会有文字提示引导你：

```
> python -m JumpCutter2

你没有输入任何文件，因此进入文字引导。
你可以在命令行加上 -h 参数运行此程序以获得
命令行运行的帮助。

程序的用处主要是对视频中的声音进行分析，
分成静音部分和非静音部分，
分别施加不同的速度，最后合成到一个新视频。


请输入要处理的视频或音频文件
请输入文件路径 或 直接拖入：
```

第二种方式是命令行传递参数运行：

```
> python -m JumpCutter2 -h

usage: __main__.py [-h] [--version] [--suffix Suffix] [--silence-speed Speed] [--sounded-speed Speed] [--buffer-frame Number] [--threshold Threshold] [--codec Codec] [--crf Number] [--only-audio] [--aux File] [--no-spleeter] File [File ...]

功能：对视频中的声音进行分析，分成静音部分和非静音部分，分别施加不同的速度，最后合成到一个新视频。

positional arguments:
  File                  要处理的音频或视频文件，可一次添加多个文件

optional arguments:
  -h, --help            show this help message and exit
  --version             show program's version number and exit
  --suffix Suffix       处理后生成的文件名后缀 (default: _JumpCut)
  --silence-speed Speed
                        静音片段速度 (default: 8.0)
  --sounded-speed Speed
                        有声片段速度 (default: 1.0)
  --buffer-frame Number
                        片段间缓冲帧数 (default: 4)
  --threshold Threshold
                        声音检测相对阈值 (default: 0.04)
  --codec Codec         视频编码器 (default: libx264)
  --crf Number          视频质量crf参数 (default: 23.0)
  --only-audio          只处理音频 (default: False)
  --aux File            辅助音频文件 (default: )
  --no-spleeter         不要使用 spleeter 生成辅助音频文件 (default: False)
```

## 🔋 打赏

本软件完全开源，用爱发电，如果你愿意，可以以打赏的方式为我充电：

![sponsor](assets/Sponsor.png)

## 😀 交流

如果有软件方面的反馈可以提交 issues，或者加入 [QQ 群：1146626791](https://qm.qq.com/cgi-bin/qm/qr?k=DgiFh5cclAElnELH4mOxqWUBxReyEVpm&jump_from=webapi) 