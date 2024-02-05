+++
title = "Bliss OS and why you may actually care about it."
date = 2023-11-03
template = "page.html"

[taxonomies]
tags = ["Linux", "Android", "Operating Systems"]
categories = ["Tech Showcase"]
+++

Why on earth would someone want to install android on their PC? It's a question I get asked a lot lately. To be fair, it's not hard to understand why it would be asked, it's not as if people have a lot of exposure to android outside of the small spy device they lug around daily. There are actually a good few reasons to use android apps, and hopefully this will be able to showcase some of the benefits of ax86's continued development efforts. 

<!-- more -->


## Tablet and "Touch Primary" devices.

Probably the most common sale would be tablet PCs. While not so common now, 2-in-1 tablets and convertibles actually held some degree of popularity, with windows 8 trying to cash in on it hard. They fall under a category I call "Touch Primary" devices. While Laptops and Desktops are primarily used with mouse and keyboard, Tablets and phones would be devices that you primarily use via touch screen. 

Android's ecosystem for touch friendly apps dwarfs any other operating system you could install on a PC, nearly all available apps you have access to out of box on either Foss or Gapps versions will be optimized for touchscreens. I personally use a Chuwi hi10x that was gifted to me for helping testing and application development for the foss ecosystem. 

Bliss OS works excellently on it, allowing me to rarely need to tether myself to the admittedly fairly clunky keyboard a stark contrast to linux and windows which can often struggle at the best of times with their buggy OSK handling. 

## Low end systems 

If you find yourself with a very old and low end system Bliss offers an optimized version of android called Bliss GO. Android GO is a set of configurations a rom can compile with that will optimize android for low speced machines, allowing even the oldest of potatoes you have buried in the garden to be usable devices. 

This is not unlike Linux with DEs like xfce or sway. However compared to those Bliss poses a large benefit, Android applications are still optimized for lower performance applications too. While linux itself may be nice and responsive on these low spec machines, sometimes as low as 2GB It's not uncommon for the devices to slow to a crawl when trying to do actual work such as use a browser, watch a youtube video.  It may be qemu, but getting a smooth experience on 2gb of ram is not to be understated

![qemu](https://files.catbox.moe/4dnc9f.png)

this may not be the fastest machine you have ever used, but on 2gb, it certainly is impressive

<video src="https://files.catbox.moe/4n17z3.mp4" controls></video>
<!--iframe src="https://files.catbox.moe/4n17z3.mp4" frameBorder="0" allow="autoplay; picture-in-picture" allowfullscreen></iframe-->


Thanks to android applications being catered to a much lower preforming class of hardware, this means that using an android browser, albeit possibly clunky on mouse and keyboard, can be a much better experience then using Linux or Windows on these low and usually very old devices. 

## Android Applications themselves

It may come as a surprise to many, but a lot of people actually care about specific android apps, possibly the largest use case in the Bliss OS telegram where users ask for support is, probably not surprising, games. Android games are actually fairly popular use for Bliss OS, as it can often fill the same roll that applications like WSA and Bluestacks fill. (Waydroid is a sort of sister project, which also fills the roll nicely for linux users).  

However, Games are not the only use. Companies looking to use their own hardware may also be interested in Bliss too, as many companies are now using android applications for field work, such as ISP technicians that do house calls. A company could see value in Bliss since it would allow them to have their in house IT teams to run and maintain their own hardware instead of getting locked into whatever junky tablet they can buy at scale. 

## Android Boxes

The sheer amount of custom hardware running android is not small, if you search any major tech supplier for "Android Box" you are bound to come up with at least a couple of results, if not hundreds. Android is one of, if not the most common OS for set-top-boxes, and it's easy to see why.  

With the access to the healthy ecosystem of remote optimized applications developed for Android TVs if you are looking for an HTPC setup, or an emulation box that can do more then just emulation, Bliss happens to be an excellent choice for that too.  

## Cloud and Virtual Android Solutions

Another benefit to Bliss is it's ability to be installed in Qemu with ease. Being x86_64 with legacy bios and UEFI support, it has great support in Qemu for both passed through graphics as well as Virgl. This means you can load Bliss OS into Proxmox, oVirt, and other qemu or crosvm based virtualization solutions. If you are an individual looking to get started with Qemu, BlissOS has a good guide on getting started with qemu CLI.  

I personally plan on looking into xen based solutions with the work being done with virtio venus, I am quite excited about the possibility of Bliss working as a first class citizen within the xen ecosystem when that work starts to land in projects like XCP-NG.  


## Cars, Arcades, Casinos

To likely no one's surprise. Cars are another great hobbyist use for Bliss, There have been a couple of bliss users toying with car projects using bliss. Here is a small demo I had made using Bliss and Qemu, emulating one of the smart displays new cars are shipping with  

![wide screen](https://files.catbox.moe/dx26y7.jpg)

there are also some more traditional uses that some users have had to accessorize their vehicles

![car tablet](https://files.catbox.moe/vgrzev.jpg)

However it doesn't end there, Arcades and Casinos implement a lot of custom work often based on android, so Bliss can be a good fit here too. as it can be cheap for them to repair and service their machines, but also being flexible too as they can use their own custom purpose hardware for the job.  

## Miscellaneous projects

These are only the ideas I have had myself over the years, a couple other notable contenders I have played around with are using android with devices on super small screens or low resolution screens, Android is actually uniquely suited for this. Below is a picture of what BlissOS would look like on a 320x320 screen. 

![square](https://files.catbox.moe/hx8rpu.jpg)

Bliss has a LOT of potential uses, and the community is always looking forwards to hearing more about them. If you want to get involved in the community, feel free to join the community at the URLs linked below

 
[BlissOS website](https://blissos.org/)
[Telegram](https://t.me/blissx86)
[Matrix](https://matrix.to/#/#blissos:matrix.org)
[Discord](https://discord.com/invite/F9n5gbdNy2)


