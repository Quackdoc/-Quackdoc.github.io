+++
title = "Easy video tips for CLI chads"
date = 2023-11-02
template = "page.html"
description = "Some quick tips on manipulating and encoding video from terminal and scripts"

[taxonomies]
tags = ["Linux", "Video", "CLI"]
categories = ["Tidbits"]
+++

When it comes to encoding videos, FFmpeg is a ubiquitous tool, it's supported on nearly every modern platform, and many retro platforms, includes a plethora of encoders and decoders, and targets most currently used architectures.

for decoding videos and watching them MPV is easily king here, with a great deal of flexibility and customization, you can do a LOT to tune your viewing experience perfectly.

One of the downsides to using FFmpeg and other CLI tools is how many options they can sometimes have and scripting these options can sometimes be a complicated mess. This post will cover a few tips and tricks to make it just a little bit easier. 

<!-- more -->

## Flexible scripting.

This will focus on bash scripts however these can easily be implemented on windows via python or powershell scripting too. 

Taking a look at the given example it is small and concise, however it is hard to read and is prone to spelling mistakes that can be hard to point out. 

```sh
ffmpeg -init_hw_device vulkan -i Pacific-rim.webm \
-vf hwupload,libplacebo=tonemapping=st2094-40:peak_detect=false:contrast_recovery=0.5:colorspace=bt2020nc:color_primaries=bt2020:color_trc=arib-std-b67:range=tv:format=yuv420p10le,hwdownload,format=yuv420p10le \
 -c:v rawvideo -f nut - | mpv --cache=no -
```
instead of dealing with this mess, we can actually instead use an array instead, this lets us easily format the arguments, add comments, and as implied by the previous point, comment out arguments. and example of the exact same script is given below, going by character count alone the script is longer, however it is much easier to read and work with. 

```sh
#!/bin/bash

args=(
###set this block as needed
tonemapping=st2094-40
:peak_detect=false
:gamut_mode=perceptual
:contrast_recovery=0.5
###
#:deband=true
#:deband_iterations=4
#:deband_threshold=8
###set HLG
:colorspace=bt2020nc
:color_primaries=bt2020
:color_trc=arib-std-b67
###
:range=tv
:format=yuv420p10le,hwdownload,format=yuv420p10le
)

formated=$(echo ${args[@]} | tr -d " ")

ffmpeg -init_hw_device vulkan -i Pacific-rim.webm -vf hwupload,libplacebo=$formated -c:v rawvideo -f nut - | mpv --cache=no -
```

## Comparing videos with MPV

Comparing videos can be one of the worst experiences from proprietary tools that are lacking in features, to open source tools that are really janky, My prefered way of comparing videos is actually using MPV. 

[this is a link to my mpv config](https://gist.github.com/Quackdoc/bacd7f5eb78df5fffdca08c0e9720563) if you scroll to the bottom I have a couple profiles created to help in comparing videos `2sidesplit` and the various `diff` profiles will most likely be the most useful for visualizing changes. while dedicated tools can be more helpful, MPV is more then enough for simple comparisons. 

to use MPV in this manor, simply launch `mpv file1.mkv --external-file=file2.mkv` then you can use `--profile=diff` to use one of the diff profiles. if you instead want to be able to switch between videos do not specify a profile and instead use `set vid 1` `set vid 2` to swap between videos, this can scale to as many videos as you have. you can set keybinds by adding the below to your input.conf. the key buttons will then be `ctrl + shift + 1` etc. 

```
Ctrl+! set vid 1
Ctrl+@ set vid 2
Ctrl+# set vid 3
Ctrl+$ set vid 4
```

## Comparing MPV settings

[This is a link to a script](https://gist.github.com/Quackdoc/efd1b93e12cf915a4d2deeb9b4107cf6) I modify and use to collect screenshots to use this script, I highly recommend setting a profile like so in your mpv.conf and launching with it. it's important to set `input-ipc-server` as this is what the script I made uses to interact with mpv. 

```sh
[comp]
#input-ipc-server=\\.\pipe\mpvsocket  ### for windows
input-ipc-server=/tmp/mpvsocket           ### for linux

##set these as you please
screenshot-format=jxl
screenshot-directory=~/Pictures/mpv
screenshot-jxl-distance=0.0
screenshot-high-bit-depth=yes
screenshot-jxl-effort=3

##set this to yes if you want to retain the original colorspace
screenshot-tag-colorspace=no
```

## Generating images to share

while you really *shouldn't* use keyframes to showcase your new av1 encodes, we already know you will anyways, so here is an easy way to do so. 

this specifically is an example to create animated AVIF images for each keyframe in your encode. this is great since you can use an entire GOP for showcasing your encodes.
`ffmpeg -i .\nyan-av1.webm -c copy -f segment .\avif-keys\out-%02d.avif`

this on the otherhand will generate a png of every keyframe you have.
`ffmpeg -i ..\nyan-av1.webm -c:v copy -an -bsf:v "noise=drop=not(key)" -f nut - | ffmpeg -vsync 0 -i - out/image-%03d.png`


### This is incomplete, and will be added to in the future