+++
title = "Emulation, Virtualization? An introduction to Technical Term Tomfoolery."
date = 2023-11-02
template = "page.html"

[taxonomies]
tags = ["Virtualization", "Emulation"]
categories = ["Information"]
#url_name = "TechnicalTermTomfoolery"
+++

When it comes to virtualization, there's so much that can be confusing when you start learning since there can be a lot of seemingly contradictory things. This is primarily due to what I call technical term tomfoolery, this post will serve as an introduction to emulation technologies, as well as learning how to navigate technical term tomfoolery. 

I want to preface this with stating that I am in no way, a hypervisor or VMM developer. I have hacked on them in the past, but my knowledge here is only of the fundamentals and usage of VMMs and hypervisor technologies, not developing them. 

The first thing to know when trying to learn computer related skills, is that the real world and the computer science world operate on two separate dictionaries. I'm not sure when this started, but it's been around for quite a long time. However instead of a history lesson this will primarily focus on virtualization.
<!-- more -->
### So what IS an emulator? 
Emulators are things which emulate. Simple enough, so what is to emulate? There are a few ways of saying it, but in the end it boils down to "intentionally imitating something". 

### Virtualization
To start things off, let's start off with the classic "it's virtualization, not emulation". This is a fairly common quote, so you may have heard it before. This is the equivalent of saying, "It's a Dodge Charger, not a Car". Virtualization is merely hardware accelerated CPU emulation. You are still imitating another computer environment. Using this method you typically take the CPU calls the kernel makes, and send them to the host CPU. However there are CPU calls and memory addresses that you really don't want a VM to to access. Hardware acceleration lets the computer emulate these calls instead of passing them directly to the host with very low overhead.

It's also with noting para-virtualisation, which is an interface which emulates the connections between hardware and other components or the components themselves. The most common implementation of these that users would actually be aware of is Qemu's Virtio devices. Qemu has a plethora of para-virt devices as well as fully emulated devices. Using para-virt or "Virtio" devices when possible can reduce overhead by a decent chunk due to instead of emulating devices, you are providing an emulated interface usually, to real devices or APIs, but this is not always the case.

### Wine too?
Wine is another classic example of this phenomena where technical dictionary and the real world dictionary are two completely separate things. Wine is an acronym actually for wine is not an emulator. As wine does provide an windows like environment when running on Unix, I would for sure consider this a class of emulation. In fact, they actually do address this... I recommend reading wine's FAQ on this in where they term it far more correct "Wine is not *just* and emulator". 

### Containers as well.
Wine and Virtualization are not alone in the technical term tomfoolery when it comes to emulation. Containerization programs such as LXC and Docker also do this. With virtualization, you are emulating the connection between hardware and kernel. However with containers you are instead emulating the connection between userland and kernel. This is a really interesting technique since it does allow you to, in theory share all devices on a machine. Of course this poses it's own issues, for instance when one container is running an application that want's exclusive access to a device or hardware and another container want's to do the same. Without proper mediation you could easily run into race conditions, however modern software stacks are pretty well battle tested at this point so it is unlikely to cause an issue now unless you go out of you way to do so. 

Containers do fit into an interesting situation. At what point does a container become a virtualized environment, and at what point does it not? Take LXC for instance, you can run multiple emulated environments using LXC, so a lot of people consider it virtualization. But by the same token, what about chroot? chroot is as the name implies, changing the root folder. Many people don't consider it to be virtualization or Emulation, but I personally do. Chroot is even rather close to a hypervisor itself. It allows you to run an emulated environment and allows you to share resources with the main environment. 

### Virtualization is just a branch of emulation
In the end, Virtualization, and Containerization if you consider them separate technologies, are both specific techniques Emulation. To say something is "Not emulation, it's Virtualization" is IMO wrong. Like wine it would perhaps be better to say "Not *just* Emulation". Perhaps that could solve the technical term tomfoolery that is present in this small segment of the tech world.
