+++
title = "Waydroid, What is it, and How does it work?"
date = 2024-01-17
template = "page.html"
description = "Waydroid, the not android emulator for linux. How it works, and why it's neat."

[taxonomies]
categories = ["Tech Showcase"]
tags = ["Linux", "Windows"]
+++

For those who read my Bliss article, This can be thought of a sort of companion peice to it, Both Waydroid and Bliss OS are Bliss Labs projects, a sort of family when it comes to android development, Waydroid and Bliss share a lot of the same technical aspects both being generic android x86 projects. Waydroid is becomming increasingly popular within the linux community but it seems to not be well understood. Hopefully this blog will shine some light on some of the topics that make waydroid unique to android ecosystem, without diving too far into technicals.
<!-- more -->
### Prelude

For those familiar with collabora's past endeavors, Waydroid builds upon Collabora's [SPURV](https://www.collabora.com/news-and-blog/blog/2019/04/01/running-android-next-to-wayland/) and is quite related with a lot of how the technology works. This means that those familiar with that project will have a fairly good understanding of how waydroid works already.

## So what IS waydroid?

Waydroid is widely regarded as an android emulator for android, And if you have ready my technical term tomfoolery article, You would know that I agree with the explanation, but for the general audience, "It's not emulation, it's containerization". That's right, cringe aside, Waydroid works by running android natively within a LXC container environment. 

This means waydroid shares almost all of its resources with the host it runs on. Applications run natively on the host kernel without any need for translation like wine, It directly commuicates with hardware like the GPU. Network is setup using native linux networking techniques like bridges and taps, Ram is shared, and more.

But waydroid is a bit more then just "running android in a container." so lets talk about it.

## Native Integration

Many people are probably aware of this already, but waydroid strives to feel "native" to the system. It supports a native feeling "multi window mode". A variety of input and interaction methods, and much more. But how does waydroid accomplish this?

First and foremost, Waydroid has a hard requirement on Wayland, Hence the name Wayland + Android = Waydroid. This is how Waydroid is able to achieve the "Native" feeling. By directly integrating with the host's wayland compositor. 

### The display

The first thing we need to do is have a quick overview on how android display stack works. Without getting to indepth, There are three main components in the android display stack, The app itself, SurfaceFlinger, and Hardware Composer. The apps get rendered by surface flinger onto their own respective layers. This is an opengl/vulkan process. The layers then get sent to the Hardware Composer, This takes the indivdual layers and arranges them into a sort of sandwhich, making sure every layer is in the right position, This layer sandwhich then gets sent to the GPU so the GPU's own functions can handle this outside of vulkan/opengl. 

So where does waydroid fit into this? Waydroid more or less replaces the final part where Hardware composer sends the sandwhich to the GPU, instead Waydroid packages it up into a wayland compatible window and send that to the host via waydroid socket. Doing it like this means waydroid should always render windows correctly as android intended them to be rendered. Waydroid itself is still handling the compositing of the window, we just need to make sure that composited window makes it to the host's compositor intact. 

### Audio

Those who have used waydroid for a while now may have noticed that audio is mostly a set and forget kind of thing. Those who are familiar with Linux will feel more or less right at home here. Android audio is actually handled quite similar to your standard linux distro at a low level. You use a high level audio server. In android's case the Audio Flinger, Which then sends audio to a low level interface for communication with the hardware. This can be a varieity of sound servers like OSS, or in our case. Alsa. Thanks to Alsa being well understood, It is fairly easy enough to make Alsa write directly to a pulse server, which is exactly what waydroid does. Because of this, all of waydroid's audio is mixed within android itself and and sent to the host. 

This sounds detrimental at first glance, and for sure does come with it's own detriments, However the are great benefits to this. Again all android apps behave normally. If an android app wants to control audio, it works just as it would on a regular android app. 

It could be possible to implement pipewire support, Or perhaps modify Audio Flinger so we could control app audio directly, but no effort nor interest has been made towards this.

So we do trade off some "Native Integration" here but the app compatibility is excellent, and the solution has proven to be quite robust, albiet perhaps less flexible then some would like, as it does require a pulseaudio implementation on the host. It also has some known issues with latency, but those may be config related.

### Inputs

Waydroid's input support is where the wayland integration really shines. Waydroid directly uses wayland protocols to support capturing inputs into android. Waydroid recieves the input events like any other wayland app, Takes the input events, and sends them to the android side. This means we have excellent integration with wayland input events nearly perfectly so far. Mouse, Keyboard, Multitouch, and even stylus with pressure support all works out of box. Artisic types rejoyce, Ibis paint for instance works perfectly. Any artistic android app you rely on should have out of box for things like pressure sensitivity, Tablet buttons, and more.

This is sadly a double edged sword. Im sure many readers are aware, Wayland is both great and terrible at the same time. Due to this tight integration with wayland, this makes inputs outside of wayland... not great. While waydroid can discover devices by uevent, which is off by default for very good reasons, We don't have a real method for discovering other devices, nor locking usage of them. This means peripherals that don't have wayland support like controllers have pretty poor support. 

They do still work with uevent, but uevent works by listening to hotplugging, and waydroid cannot lock control of these devices. This means things like keyboards if they are connected after waydroid starts will get input from both Wayland, and the kernel interface itself, effectively double inputting everything.

To get controllers working you actually need to disconnect the device and reconnect it. Sadly there is no real way to work around this currently with wayland. We are stuck waiting for upstream wayland protocol to support the devices. If you want support for them, Perhaps follow the threads in the wayland protocols repo, or make a new issue ticket if one doesn't exist.

### Sensors

Sensors is probably the "Least integrated" solution, but surprisngly it might just be the most flexible. Waydroid sensor integration is mostly done through an auxiliar service, called `waydroid-sensors`. This will gather sensor data from the host and send it to waydroid over the binder interface, and then back to the host if needed. It's a simple solution but quite robust, It supports things like GPS for locations, Gravity and Sccelerometers work too. But like all things, it can't support everything out of the box, so if you run into issues make sure to report them.

## What does waydroid not do?

First and foremost, Waydroid does not provide emulation solutions and so long as an open source one doesn't exist, will never do so. These things are usually licence encumbered and pose licence issues that waydroid cannot eat up. Thankfully it doesn't make a lot of sense for waydroid to ship these anyways since the two options that exist currently, make sense for different people, so letting the user set it up themselves actually works better in most cases.

Waydroid doesn't currently ship a currated set of applications, This can be seen as a pro and a con by many, but by default, Waydroid ships a fairly stock AOSP experience, leaving the user to install their own app suite. This is worth taking note of, since while there are many people who much prefer this, There of course are some would rather have the "Full Ecosystem". This is fairly easy to setup using [wpm, the waydroid package manager.](https://github.com/waydroid/waydroid-package-manager) There are of course issues with this approach one being that it only supports fdroid repos, However considering the nature of Linux as a whole, I don't think that is a major cause for concern.

### Houston, we have bugs.

There are also things waydroid doesn't do, but it should do. In many of these cases it is due to bugs or regressions, but in others, It's just effort that hasn't yet been made. One of the more requested features that waydroid actually used to have is camera support. Unforunately camera support was lost with the android 11 migration. There have been talks of ways around this, but in the end nothing concrete has been done.

Also on the topic of cameras is libcamera. Waydroid never had support for libcamera but it has been requested in the past. While android does support libcamera, it's infranstructure is still pretty immature. [While there has been some work on getting libcamera working](https://github.com/waydroid/waydroid-package-manager), it has unforunately stalled.

It doesn't stop with camera however, Security is a place where waydroid is for sure lacking as well. While waydroid isn't "unsafe" There hasn't been a lot of consideration made for making it safe, The container itself runs as root, and while android does have fairly good internal sandboxing, It could still be possible for a rogue app to gain root permissions in some method, and when that happens, It doesn't matter what container tech you use, [Root is Root unless it isn't.](https://wiki.archlinux.org/title/Security#Sandboxing_applications) `CONFIG_USER_NS_UNPRIVILEGED` can be used to make a "Fake Rootful container" however this **needs** to be paired with additional permissions system. Without a system like SELinux or apparmor in place, this, while more secure then a root container, Is still insecure, and could bring up security issues in other places.

## So why is waydroid so important?

### Linux Mobile users rejoyce.
This is the part where I sell waydroid to potential investors or contributors. And I don't think it will be too hard to do so. First let me address linux mobile space. Linux mobile apps suck. There is just no nice way to put it. The design is all over the place, While the freedom is a good thing there is a significant lack of quality applications with a consisitent design and usage paradigm. On android it's pretty standard, Nearly all apps support the "Back Button" as an input and it behaves predictably across the board. You have the standard locations, Top left for a "Menu button" or a "Back button" if a menu button isn't needed, Swipe from side to open drawers etc. This consistent user design is something that most people have come to expect, maybe not specifically android's usage paradigm, but at least it *is* a paradigm.

### Android is easier to deploy then ever before.
And how about just specifically android users and distributors? Perhaps you need your android device to last a long time, and you want the flexibility and ease of deploying it anywhere. Waydroid has a very minimal set of dependencies. Python, A somewhat modern kernel with binder enabled, LXC and Wayland. That is pretty much it for the major dependencies. You do have some smaller libraries you need but those are easy and minimal enough to deploy anywhere. As long as it can run vanilla linux, you can easily port android to it in a consitent and easy manor. Waydroid is very generic, Most of the work done to arm, applies broadly to nearly all arm devices, and the exact same can be said with x86.

There are still some more specific stuff. We rely on mesa for graphics with the generic stack when possible. This means if your device doesn't support mesa it might wind up being a problem, and you may need to use software rendering. It may be possible at some time in the future to use the host's GPU drivers, but currently waydroid will use it's internal mesa to talk directly with the gpu kernel driver.

Another benefit of this is that we can support devices much longer then traditional android does. Waydroid is actively working on devices older then 2011 with good preformance and reliability. The entirely open nature of Waydroid's AOSP stack means upgrading in the future will be extremely little effort which, as to what I said before, nearly all thhe work is shared across devices. If you are a company working on custom android solutions, by working with waydroid you can have a much easier time supporting your legacy devices, and porting to your new devices.

For independant contributors who just like foss software, getting android working on your custom arm box or your little x86 nuc is as often as making sure it can utilize foss software. You can buy just about any SBC that has good mesa support, install linux on it, and have waydroid up and running in short time and have full and consistent accsess to android's vast library of useful applications.

### Games

It's hard to talk about waydroid without talking about games. As one would think, Android games are actually quite popular. Waydroid can boast good support for the majority of games. Sadly there are games out there which will not work even with a lot of configuration. There are some games which are arm only. While a lot of these games can work when you load up libhoudini or libndk. There are still plenty of games which will actively block out solutions like waydroid. There are also games which require specific features that don't work well with mesa too. Some games actively require some features only commonly found in the GPUs and Drivers of android devices. And while this is fairly rare, it can for sure still happen. 

There are also other games which require you to spoof the device you are using. Waydroid can play these games but you will require a script to set up the spoofing for these applications, and while it is a hassle, it does work. Another configuration issue you may run into is mouse/touch. There are games out there which will require you to set up touch emulation since they wont respond to mouse. Waydroid does have touch emulation baked into it however so this is a fairly easy config setting to change.

Even with all this said, Many users will find that more often then not, games will just work once you have houdini or ndk installed. You can load up google play games, or Aurora store and have a large library of games at your disposal. Gaming is a fairly popular use for waydroid and any contributions to make it better, whether it be to waydroid itself, or better integrating and contributing to open source tools like [XTMapper](https://github.com/Xtr126/XtMapper) will go a long way, and be greatly appreciated by many users.