# mpv config

![mpv logo](https://raw.githubusercontent.com/mpv-player/mpv.io/master/source/images/mpv-logo-128.png)

## Overview

**mpv** is a free (as in freedom and free beer), open-source, and cross-platform media player. It supports
a wide variety of media file formats, audio and video codecs, and subtitle types.

These configuration and script files are meant to be put in the `config` or `portable_config` folder next to `mpv.exe`. The advantage of using `portable_config` folder is that all configurations will be loaded from said directory only. Please refer to the 
[mpv manual](https://mpv.io/manual/master/) for further reading about these configuration files, especially
for the options in `mpv.conf` and `input.conf`.

## Official links

* [mpv homepage](https://mpv.io/)  
* [mpv wiki](https://github.com/mpv-player/mpv/wiki)
* [mpv FAQ](https://github.com/mpv-player/mpv/wiki/FAQ)
* [mpv manual](https://mpv.io/manual/master/)

## Scripts

The scripts used:
* [audio-visualizer](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/audio_visualizer.lua) —
  [source](https://github.com/mfcc64/mpv-scripts#visualizerlua)\
  Various audio visualization. The default keybind to cycle visualizer is `c`. Cycling audio visualizer by pressing `c` only works if you open audio files. If it's a video file, however, it triggers the [mpv_crop_script](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/mpv_crop_script.lua).
  
* [autoload](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/autoload.lua) —
  [source](https://github.com/mpv-player/mpv/blob/master/TOOLS/lua/autoload.lua)\
  Automatically load playlist entries before and after the currently playing file, by scanning the directory.

* [cycle-commands](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/cycle-commands.lua) —
  [source](https://github.com/CogentRedTester/mpv-scripts#cycle-commands)\
  Cycles through a series of commands on a keypress. Each iteration of the cycle can contain as many commands as one wants. Syntax details are at the top of the file.

* [cycle-profile](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/cycle-profile.lua) —
  [source](https://github.com/CogentRedTester/mpv-scripts#cycle-profile)\
  Cycles through a list of profiles sent via a script message and prints the profile-desc to the OSD. More details at the top of the file.

* [mordenx](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/mordenx.lua) —
  [source](https://github.com/cyl0/mpv-osc-morden-x)\
  A modern OSC UI replacement for MPV that retains the functionality of the default OSC.

* [mpv_crop_script](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/mpv_crop_script.lua) —
  [source](https://github.com/TheAMM/mpv_crop_script)\
  Make cropped screenshots from within mpv, without any external dependencies, cross-platform-ly.

* [playlistmanager](https://github.com/noelsimbolon/mpv-config/blob/main/scripts/playlistmanager.lua) —
  [source](https://github.com/jonniek/mpv-playlistmanager)\
  Allows you to see and interact with your paylist in an intuitive way.

Please note that some of the scripts that I use are modified from the source, while some are not.

## Shaders

The shaders present in the `shaders` folder:

* [FSRCNNX_x2_16-0-4-1](https://github.com/noelsimbolon/mpv-config/blob/main/shaders/FSRCNNX_x2_16-0-4-1.glsl) — [source](https://github.com/igv/FSRCNN-TensorFlow/releases)
  
* [nnedi3-nns256-win8x4](https://github.com/noelsimbolon/mpv-config/blob/main/shaders/nnedi3-nns256-win8x4.hook) — [source](https://github.com/bjin/mpv-prescalers/tree/master)
  
* [SSimDownscaler](https://github.com/noelsimbolon/mpv-config/blob/main/shaders/SSimDownscaler.glsl) —
  [source](https://gist.github.com/igv)

* [KrigBilateral](https://github.com/noelsimbolon/mpv-config/blob/main/shaders/KrigBilateral.glsl) —
  [source](https://gist.github.com/igv)

Use shaders based on your system capabilities. For more info about shaders, check the useful links below.

## Usage

To use these configuration files, just download this repo as a ZIP file and place everything except the `README.md` file to your `portable_config` or your `config` folder. You can modify the configuration based on your personal needs.

Useful links:
* [mpv manual](https://mpv.io/manual/master/)
* [mpv.conf guide](https://iamscum.wordpress.com/guides/videoplayback-guide/mpv-conf/) by iamscum
* [mpv Configuration Guide for Watching Videos](https://kokomins.wordpress.com/2019/10/14/mpv-config-guide/) by Kokomins
* [Mathematically Evaluating mpv's Upscaling Algorithms](https://artoriuz.github.io/blog/mpv_upscaling.html) by João Vitor Chrisóstomo

## Downloads

For semi-official builds and third-party packages of mpv, please see
[mpv.io/installation](https://mpv.io/installation/).
