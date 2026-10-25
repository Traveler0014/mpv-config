# mpv config

![mpv logo](https://raw.githubusercontent.com/mpv-player/mpv.io/master/source/images/mpv-logo-128.png)

## Overview

**mpv** is a free (as in freedom and free beer), open-source, and cross-platform media player. It supports
a wide variety of media file formats, audio and video codecs, and subtitle types.

This repo contains my personal mpv configurations and scripts that I use and are significantly better than default mpv, VLC, and MPC. Before installing, please take your time to read this whole README as common issues can be easily solved by simply reading carefully.

## Branches

| Branch | 适用场景 |
| --- | --- |
| `windows` | Windows（`portable_config`） |
| `linux` | 普通 Linux 桌面，独显/中高端集显，追求画质（`profile=high-quality` + `ewa_lanczossharp`） |
| `linux-lowend` | **弱 iGPU 的 Linux 设备**（Intel Gemini Lake / UHD 600 一类，无独显）。关掉昂贵缩放器与 HDR 动态峰值检测，4K 源自动降级，见下节 |

`linux-lowend` 是从 `linux` 拉出的独立分支，只针对弱硬件改 `mpv.conf`，脚本/字体/OSC 与 `linux` 分支保持一致。详见[低性能 Linux 设备](#低性能-linux-设备lowend-linux-分支)。


## Preview

[![preview.png](https://i.postimg.cc/8zNHHPHy/preview.png)](https://postimg.cc/VdZnsw2M)

## Installation

### Windows

Here are the steps to install mpv and to use my configuration files on Windows:
* Download the latest 64bit mpv Windows build by shinchiro from [mpv.io/installation](https://mpv.io/installation/) or directly from [here](https://sourceforge.net/projects/mpv-player-windows/files/) and extract it wherever you please. This is now your mpv folder
* Run `mpv-install.bat`, which is located in `installer` folder, with administrator priviledges by right-clicking and selecting Run as administrator
* Download this repository as a ZIP file (or you can clone it using git)
* Create a folder named `portable_config` (**this is important**), located at the same directory as `mpv.exe`
* Extract or copy the contents of this repository that you have downloaded to the `portable_config` folder
* To make some scripts work, you need to modify them from the release a little bit:
  * In order for the `mpv-gif.lua` script to work, it requires [FFmpeg](https://ffmpeg.org/) with libass enabled and accessible via terminal. See the [installation instructions](https://github.com/Scheliux/mpv-gif-generator#installation) from the script's source repository for further info.
  * **(Optional)** By default, the `mpv-gif.lua` script saves GIFs to `C:/Program Files/mpv/gifs`. To modify this, open `gif.conf`, which is located in `portable_config/script-opts` folder, with a text editor and specify the `dir`, which is output directory for GIFs, as you please. For example `dir="C:/Users/USERNAME/Pictures/mpv-gifs"`.
* **(Optional)** Make your own mpv configuration. You can do that by modifying my configuration files and/or making your own from scratch or modifying others' configurations. Check out the [useful links](#useful-links) section for mpv configuration guides.
* You're all set up.

### Linux

Here are the steps to install mpv and to use my configuration files on Linux:

* Install mpv and xclip (clipboard CLI interface) using the package manager that comes with your Linux distribution. xclip is needed for [copy-time.lua](https://github.com/noelsimbolon/mpv-config/blob/linux/scripts/copy-time.lua) and [seek-to.lua](https://github.com/noelsimbolon/mpv-config/blob/linux/scripts/seek-to.lua) scripts to work properly. The package name for mpv and xclip might also vary depending on your Linux distribution. Here, I will make Arch Linux, that comes with `pacman` as its package manager, as an example
  
  ```
  sudo pacman -S mpv xclip
  ```

  If you, for example, use Fedora Linux, that comes with `dnf` as its package manager, you can install mpv and xclip with the following command instead.
  ```
  sudo dnf install mpv xclip
  ```

  If you use other Linux distributions, please refer to the documentation of your Linux distribution's package manager on how to install packages.

* Download this repository as a ZIP file (or you can clone it using git) and extract/copy it to your standard mpv configuration directory which is `~/.config/mpv`
* Some things to highlight:
  * In order for the `mpv-gif.lua` script to work, it requires [FFmpeg](https://ffmpeg.org/) with libass enabled and accessible via terminal. See the [installation instructions](https://github.com/Scheliux/mpv-gif-generator#installation) from the script's source repository for further info.
  * **(Optional)** By default, the `mpv-gif.lua` script saves GIFs to `~/Videos/mpv-gifs`. To modify this, open `gif.conf`, which is located in `portable_config/script-opts` folder, with a text editor and specify the `dir`, which is output directory for GIFs, as you please. For example `dir="~/Videos"`.
* **(Optional)** Make your own mpv configuration. You can do that by modifying my configuration files and/or making your own from scratch or modifying others' configurations. Check out the [useful links](#useful-links) section for mpv configuration guides.
* You're all set up.

## 低性能 Linux 设备（`lowend-linux` 分支）

面向没有独显、iGPU 只有个位数 EU 的机器（测试机：Intel Celeron N4120 + UHD Graphics 600，12 EU / 700MHz，输出 2560x1440@60 SDR）。

### 改了什么

| 选项 | `linux` | `linux-lowend` | 原因（实测每帧渲染成本，24fps 预算 41.7ms） |
| --- | --- | --- | --- |
| `profile=high-quality` | 开 | **删掉** | `gpu-hq` 会带上 `ewa_lanczossharp` + 线性光缩放 + 抗振铃，1080p→1440p 实测 **48-56ms/帧（p95 176ms）** |
| `scale` / `cscale` / `dscale` | `ewa_lanczossharp` / `ewa_lanczossharp` / `mitchell` | `lanczos` / `bicubic` / `bilinear` | lanczos 只要 **1.9ms/帧** |
| `correct-downscaling` `linear-downscaling` `sigmoid-upscaling` | 默认开 | 全部 `no` | 三者合计是 17.5ms→3.2ms 的差距 |
| `scale-antiring` | `0.6` | `0` | 12EU 上性价比极低 |
| `hwdec` | `vulkan` | `auto-safe`（本机解析为 `vaapi`） | 弱 iGPU 基本没有 Vulkan video decode；`vaapi` dmabuf 直通 17% CPU，`vaapi-copy` 要 29% |
| `tone-mapping` / `hdr-compute-peak` | 默认 | `bt.2390` / **`no`** | 动态峰值检测多跑一遍直方图 pass：**72ms/帧**（4K HDR 掉到 14fps）；关掉后任何曲线都是 3.4-3.8ms |
| `gpu-api` | `vulkan` | `opengl` | 实测 4K HDR 全屏：20% vs 23% CPU |
| 4K 源 | — | `[4k-cheap]` 自动 profile 降级到 bilinear | 4K→1440p 默认 lanczos **120ms/帧**，bilinear 1.2ms |
| `gpu-shader-cache-dir` | `~~/shaders/cache` | `~/.cache/mpv/shader-cache` | `~~` 是配置目录，NixOS 下是只读 store 路径 → 缓存写不进去，每次启动重编 shader |
| `video-sync` | 默认 | 显式 `audio` | 4K 下 `display-resample` 会丢 15-20%（60Hz 的 16.7ms 窗口容不下 4K 的准备抖动） |

### 实测结果

| 素材 | 改之前 | 改之后 |
| --- | --- | --- |
| 1080p HEVC 10bit + 特效 ASS（23.976fps） | 丢 178/240 帧 | **0/1200 帧（50s）**，18% CPU |
| 4K HEVC 10bit HDR REMUX（DTS-HD MA 5.1） | 丢 207/240 帧 | **0/1200 帧（50s）**，21% CPU |
| 1080p AV1 10bit | — | 3/1200 帧，44% CPU（**无 AV1 硬解**，纯 dav1d 软解） |

### 能力边界

* 1080p H.264 / HEVC 8-10bit（含 60fps）：硬解轻松，余量很大。
* 1080p HEVC 10bit 即使纯软解也有 96-111fps（约 4x 实时）。
* 4K HEVC 10bit HDR：**必须硬解 + 本分支的缩放/HDR 设置**。硬解能力本身够（实测 4K HEVC Main10 解到 69fps），瓶颈是 GPU 着色器。
* 1080p AV1：能放，但吃 44-51% CPU；4K AV1 不可用（约 12fps）。
* 重特效字幕（大量 `\t`、`\clip`、`\blur`）是弱机另一个卡顿源：实测单行中文约 4ms/帧，带旋转/模糊的特效行 10-30ms/帧。

### 使用提示

* `gpu-shader-cache-dir` 指向的目录要存在（NixOS 下由 home-manager activation 建好）。
* 23.976fps 素材在 60Hz 屏上是 3:2 抖动，想完全顺滑就把输出切到 `1920x1080@23.976`（例如 `niri msg output HDMI-A-1 mode "1920x1080@23.976"`），比开插帧划算。
* 不要开 `interpolation=yes`：实测 1080p 已经会丢 14% 帧。
* 外接 exFAT/网络盘顺序读 366MB/s 就够 4K REMUX，不必额外加 `cache=yes`（实测无收益）。

## Scripts

Scripts from external sources:

* audio-visualizer.lua ([source](https://github.com/mfcc64/mpv-scripts#visualizerlua))\
  Various audio visualization. It only works if you open audio files.
  
* autoload.lua ([source](https://github.com/mpv-player/mpv/blob/master/TOOLS/lua/autoload.lua))\
  Automatically load playlist entries before and after the currently playing file, by scanning the directory.

* copy-time.lua ([source](https://github.com/linguisticmind/mpv-scripts/tree/master/copy-time))\
  Copies current timecode in HH:MM:SS.MS format to clipboard. Cross-platform (Mac, Windows, Linux).

* cycle-commands.lua ([source](https://github.com/CogentRedTester/mpv-scripts#cycle-commands))\
  Cycles through a series of commands on a keypress. Each iteration of the cycle can contain as many commands as one wants. Syntax details are at the top of the file.

* cycle-profile.lua ([source](https://github.com/CogentRedTester/mpv-scripts#cycle-profile))\
  Cycles through a list of profiles sent via a script message and prints the profile-desc to the OSD. More details at the top of the file.

* modernz.lua ([source](https://github.com/Samillion/ModernZ))\
  A modern OSC UI replacement for MPV that retains the functionality of the default OSC.

* mpv-gif.lua ([source](https://github.com/Scheliux/mpv-gif-generator))\
  Script to generate GIFs from video playback. Requires FFmpeg with libass enabled. The exporting GIFs with subtitled currently doesn't work properly.

* playlistmanager.lua ([source](https://github.com/jonniek/mpv-playlistmanager))\
  Allows you to see and interact with your playlist in an intuitive way.

* seek-to.lua ([source](https://github.com/dexeonify/mpv-config/blob/main/scripts/seek-to.lua))\
  Seek to an absolute timestamp specified via keyboard input or pasted from clipboard.

* sponsorblock-minimal.lua ([source](https://codeberg.org/jouni/mpv_sponsorblock_minimal))\
  Skip sponsor segments in YouTube videos.

* thumbfast.lua ([source](https://github.com/po5/thumbfast))\
  High-performance on-the-fly thumbnailer for mpv. **The script does not display thumbnails on its own**, it is meant to be used alongside a UI script that calls thumbfast.

Configuration files for these scripts can be found in the `script-opts` folder. I also modified some of these scripts' default keybindings. To see my modifications, look for script keybindings in `input.conf`.

## Shaders

The shaders included in the `shaders` folder:

* ArtCNN_C4F32 ([source](https://github.com/Artoriuz/ArtCNN/blob/main/GLSL/ArtCNN_C4F32.glsl))\
  Used for luma upscaling.

* nnedi3-nns128-win8x4 ([source](https://github.com/bjin/mpv-prescalers/tree/master))\
  Used for luma upscaling.

Use shaders based on your preference and system capabilities. For more info about shaders, read the resources in the [useful links](#useful-links) section.

## Useful Links

* [mpv tutorial](https://thewiki.moe/tutorials/mpv/) by The Wiki
* [mpv.conf guide](https://iamscum.wordpress.com/guides/videoplayback-guide/mpv-conf/) by iamscum
* [mpv Configuration Guide for Watching Videos](https://kokomins.wordpress.com/2019/10/14/mpv-config-guide/) by Kokomins
* [mpv Resampling](https://artoriuz.github.io/blog/mpv_upscaling.html) by João Vitor Chrisóstomo

## Official Links

* [mpv homepage](https://mpv.io/)  
* [mpv wiki](https://github.com/mpv-player/mpv/wiki)
* [mpv FAQ](https://github.com/mpv-player/mpv/wiki/FAQ)
* [mpv manual](https://mpv.io/manual/stable/)
* [mpv User Scripts](https://github.com/mpv-player/mpv/wiki/User-Scripts)
