+++
title = "Scrcpy as a good webcam."
date = 2023-11-02
template = "page.html"

[taxonomies]

tags = ["Linux", "Windows", "Video"]
categories = ["Tutorial"]

#url_name = "scrcpycamera"
+++


Scrcpy for a long time has been the best way to record On linux it is pretty simple since scrcpy supports V4L2, however windows is a bit of a different beast, this blog post will cover windows primairly because anyone running linux should be able to trivially figureout the parts they need via v4l2loopback. 
<!-- more -->
## Phone setup

Scrcpy now supports recording the camera itself. However due to this being an A12+ only feature, it will not be focused on here, though keep in mind the setup will be the same, just without the need for opencam or filmic.

There are a couple ways of setting up the phone for this, however when it comes to quality, two methods by far are the best. OpenCam and Filmic, this post will focus on opencam. The quality is still great, even if not quite as good as filmic. However opencam is free and will be the most accsessible, and quality is still great.

The first thing to do is install opencam, I typically use fdroid opencam, but downloading the apk or using google play store should work fine too. In opencam settings, Click on `Onscreen GUI...` and sert `Immersive mode` to `Hide everything` this will be very important as we will be doing screen recording. 

Now when you back out to the main camera screen and wait a bit, the screen should disappear showing only the camera itself. as far as android stuff goes, we are already done, plug your phone into USB, prop it up on a mount pointed at whatever you please and you are good to go. 

## Scrcpy recording

Scrcpy does allow us to record to a file directly using;

```
scrcpy -N --stay-awake --turn-screen-off --record-format=mkv --record=filelocation
```

However because it is using ffmpeg under the hood, it also allows us to use other forms of arbitrary recording. So a brief run down of common protocols we could use to achieve this.

It's important to keep in mind that scrcpy only supports mp4 and mkv containers, so ideally we would work with just one of those. 

* named pipes:  sadly these won't work as windows named pipe support is not great at best, and at worst completely unusable
* sockets: also sadly, not an option as windows doesn't really support the type of sockets we can easily use. this isn't a great option
* file descriptors: not really an option here, but for more custom solutions it could be
* http streaming: this could be a potential solution, however without `-listen 1`, this won't be a very viable solution  as we wont have an http server to work with 
* Rtmp: could work with some minimal work on scrcpy's side. it would need us to add FLV streaming support.
* rtp/rtsp: possible solution however muxing other formats into rtp can sometimes cause quality degreddation, so it's not highly reccomended.

So this leaves us with three real contenders;

* udp: this can work, but has high latency, investigate this
* Rist: A possible solution
* srt: A possible solution

SRT is one of the most promising solutions, it is both low latency as the video shows, and sadly, the video I record suffers from compression artifacts so I cannot showcase the quality of the stream too, however it is just about excellent. not quite as good as a direct feed. but on windows, we will take what we can get.

##### INSERT VIDEO HERE (sorry haven't gotten around to recording it)

Some other benefits that using SRT offers is fairly easy setup, and expanded codec support, while it won't be beneficial here. keep in mind that SRT + MKV will support all the formats MKV supports. This includes formats like AV1 and Flac. 

So the scrcpy command to record the video and stream it over SRT is below this. There are a couple optimizations we can do such as setting bitrate from the phone to the PC. it's important to set the bitrate to something you can reliably do, go too low and you will get artifacts, go too high and you won't be able to sustain the connection.

```ps
scrcpy -b100M --stay-awake --turn-screen-off --record-format=mkv --record="srt://127.0.0.1:9990?mode=caller&transtype=live&latency=0&recv_buffer_size=0"
```

After that inside inside of OBS simply add a new media source, uncheck file and add `srt://127.0.0.1:9990?mode=listener&transtype=live&latency=0&recv_buffer_size=0` to it. 


## Conclusion and extra tidbits

With this you should have a fairly low latency, but still high quality feed to OBS. better then droidcam. It is possible this could be better in the future. 

* Windows does support named pipes, however they are quite the hassle to deal with. However an avid scripter or programmer could probably get this to work.
* Optimizations inside of scrcpy for quality and latency could be done
* Supporting something like dshow could be a viable solution for scrcpy, as it does support v4l2loopback on linux. 
* OBS could support scrcpy as a plugin

