+++
title = "Why I'm in no rush to sell Linux PCs."
date = 2023-11-02
template = "page.html"
description = "The TLDR of this article is linux is still jank, and probably will be for a long time."

[taxonomies]
tags = ["Linux"]
categories = ["Information"]
+++

For those who know me, it may come as some surprise that I don't sell, nor recommend selling linux PCs to the general consumer market. I as a large linux fan don't recommend linux for the general audience for a couple of reasons. 

<!-- more -->
## Applications and the no average pilot problem.
One of the things I hear the most when talking about the suitability of linux for the average user is that "It does 90% of what a users need". I think this is without a doubt true. Linux has good office programs, web browsers, and even a plethora of games now. There really is no shortage when it comes to app library for the linux desktop.  

This is where the music stops. Yes, Linux covers 90% of what the average user needs. However I highly recommend reading [this article.](https://worldwarwings.com/no-such-thing-as-an-average-pilot-1950s-study-suggests/) The same issues apply here that did then. Many users have individual needs that fall outside of the 90% of applications that are touted. 

From my personal life alone, I have 3 anecdotes of such issues. My mother is an avid crocheter. The application she is looking to use has no support at all for linux. Wine may be a viable option here, However managing and maintaining wine for the average user is simply out of the question. Handling dependencies alone is enough of an issue that I don't see this viable. The argument "Just user another program" doesn't really apply here. I have yet to find a single application good enough for her needs that supports linux. 

My father is another story too. My father uses this CNC machine that needs a windows application to run. We did get a little lucky here as an open source project that adds support to Inkscape does exist. However the difficulty of using Inkscape in comparison is quite high. While my father is rather technologically literate, and could probably figure it out, the time investment into doing so would be far to high. 

And of course there are other applications and hardware too. There are still a lot of Elgato products that are missing support. Yes, the solution of buying a different product does exist, but it's not always a great deal. As I have already said, I don't see wine as a valid solution to this problem yet. Until dependency management is handled almost as well as it is on windows, I don't see it happening any time soon. 

## Accessibility, Flexibility, and Fragmentation. 
Linux has never been particularly great in the fragmentation realm. The two largest DEs have often had their own applications uniquely tailored and supported by their own DEs. However outside of that, fragmentation wasn't really *That bad*. This was the case until wayland came along. 

Wayland solves a lot of problems, but it also creates them too. Mainly fragmentation, and fragmentation is bad for flexibility and accessibility. For good accessibility support. One needs to be able to pick and choose the components they need. Screen readers, OSKs, Overlays, Attention management software, etc. 

A11y needs a LOT to have a good time. many OSKs are now DE specific. Overlay software is too, with some overlay stuff not working on gnome due to relying on stuff like wlr_layers. And then you have stuff like Activity Watch, which some users rely on to help manage time. [This issue was filed in 2019 and it's still an issue today](https://github.com/flatpak/xdg-desktop-portal/issues/304). The fragmentation that has come from Wayland is a massive net loss in terms of usability for many people. 

Some times it feels like the problem with fragmentation isn't being taken seriously. And this is even worse from a flexibility perspective. I used to use background managers since I like having a really customizable background. The tool I currently use for this on sway is mpvpaper. It allows me to use MPV for the background which is really nice for me. It only works on wlroots and smithay compositors at the moment. It relies on wlr_layers protocol and kwin's background service seems to override it whenever focus is lost. 

Since gnome does not support the protocol no luck here. There is a proposal in the wayland protocols gitlab repo for `ext-layer-shell` which implements it mostly, but comes with the caveat that it is a privileged protocol. Meaning that applications may not be able to rely on being able to use it. 

We are relying on wayland and xdg for implementing features we need to interact with other apps and the DE. However these protocols seem to put more stock into security then flexibility, with this quote being taken from the issue above. 

> I'm still of the opinion that we don't want sandboxed apps to get into managing foreign toplevels. That is fundamentally a privileged operation

I am of the opinion that one of the benefits of a sandbox is being able to pick and choose the permissions we want to expose to it. So what do I do? We used to be able to say, "Well you should go and implement it yourself!" This no longer holds water, as we have to either get XDG to agree, or the wayland protocol maintainers to agree to accepting such a protocol. and if they don't want it, you are kind of SOL. [But even if they accept it](https://gitlab.gnome.org/GNOME/mutter/-/issues/217) as you can see, There is no guarantee for support. 

## Needs more then a spit shine.
Linux usage in general has a significant lack of polish. If you open youtube video in one firefox tab, and then another video in a separate tab, Firefox will show twice in the audio panel. This is a minor thing, but there are a LOT of minor things, this just happens to be one that's very common now. Each DE is chalk full of small issues. Kwin is a little buggy, the settings menu can be complicated to navigate, and has plenty of small issues like design inconsistencies, and has some really bizarre names sometimes. 

Gnome manages to some how chug on lower end hardware when doing not much else then opening firefox, needs 3 separate applications for settings, Doesn't support server side decor, and more. Xfce has issues, Cinnamon has issues, so on and so forth. You can never escape these small issues that can really have a tendency to grate on you after a while. 

Linux has a lot of these issues. While in isolation they are fine, when you combine the other issues it really does become a bad experience. Thankfully I do actually have a lot of hopes for [System76's Cosmic DE](https://github.com/pop-os/cosmic-epoch) when it comes to polish. I have been testing what they have already I have high hopes for it. 

## Support. 
When I sell a computer, I commit to supporting the said machine. With windows or OSX, there is a degree of reliable support for the OS and applications. With linux this puts a lot of burden onto me compared to something like OSX or Windows. Every time a customer has a question or an issue needing fixed, That's time I need to spend that I could be doing other work. 

With linux because of the issue above, I need to spend a lot more time on supporting these users, Helping out with A11y setup. Answering questions, trying to help setup applications or find alternatives. I don't really have an issue with these in isolation, However when these issues happen on too often of a basis, it becomes simply too much of a time sink to support it. 

I of course would be willing to invest time in being an early adopter. However I simply can't do it when it means that other aspects will suffer. Being able to support both gnome and KDE users will be simply too much of a time investment, and as I have already stated, I cannot support just any one of these, since that simply isn't flexible enough.

## So how do we get to the year of the linux desktop?
We need to chip away at these issues. The lowest hanging fruit is wine. By having good wine support and integration in a way that can make using wine as seamless as it would on windows, That would be one major problem solved. This would single handedly over night fix the "No average pilot problem". This of course seems like the largest hurdle, and it's something people have been working on for a long time now. We need at least one distro to fully commit on supporting wine OOB to the best extent possible, and some tooling to make dependency management a problem of the past.

The next stop would be a fragmentation. The fragmentation occurring in linux right now is a very aggravating problem. You have video recording applications that are DE specific. You have Docs and output management tools that are DE specific. OSKs that are DE specific. It has become a large enough problem that asking for DE has become a knee-jerk response when someone asks "What's the best tool for doing x thing". 

Accessibility is a large problem on linux. It's not one that cannot be solved, but it needs a lot of thought put into it, and a lot of flexibility. People underestimate the large variety of needs people for accessibility. Screen readers, Magnifying glasses, Specialized OSKs, Attention management tools, Overlays, Wye tracking, Live Captions and Dictation etc. And this is only the tip of the iceberg. 

Without the appropriate levels of flexibility in how an application interacts with a DE and other applications, It can be very hard to appropriately develop apps that accommodate these needs. I'm not sure how best to fix these issues, It's not something any single company can do, In the end, you either get XDG or Wayland protocols to support it, Or create a new protocol group and try to get the existing DE's to accept them, good luck with that. 

The other section I talked about was polish, This is the most feasible to tackle as this is something a single group can feasibly do. In fact I really do have high hopes for S76's Cosmic DE as they are directly working on this specifically. While you will never escape all applications, as long as the core DE itself is good, then it will be a major improvement. 

There are other smaller issues, but these are the major ones I have that prevent me from selling Linux PCs. Some issues like hardware support are simply not going to be resolved, But thankfully, in isolation these should be a small minority of problems.

## So what will I be trying?
I do have hope for the future, but I don't think it will come any time soon, I will be testing Pop OS with predominately flatpak to see how that works out. I think pairing that with a wine manager that can handle dependancies a bit better then what we currently have could be a really good setup as S76 seems to focusing on making a good desktop experience. 

Ubuntu I have had nothing but issue after issue after issue. It's honestly not something I will be investing any more time into trying, for everyone who likes ubuntu, that's great. I will not be reccomending at any point going forwards. 

Fedora and derivatives were at one point on my radar, particularly nobara. But recent stuff with the fedora community and RHEL has pushed me away from them. One thing that irked my in general was when they had made the proposal for telemetry that has an "on by default at installation time" setup. Where they specifically acknowledge, if you give the user a choice, even if they can't skip over it, that users will opt out. The RHEL team that made the proposal, explicitly acknowldged that they were hoping that users would skip over it, even if it's something they don't want.

I have never, and doubt I ever will, reccomend an arch derivative, that isn't making a large amount of effort to become their own OS. To explain what this means, SteamOS is very much it's own thing. Valve has put a lot of effort into making sure SteamOS remains a stable and good experience. This is in contrast to operating systems like Manjaro, who's selling point is being an arch derivative. They still greatly rely on the AUR, and their experience suffers because of this. You often run into bugs that you simple don't usually experience when using other operating systems. 

Arch itself I do still reccomend to people explicitly looking, and willing to put the effort into learning and maintaing their own OS. Arch can be very rewarding for users, even new ones, who are willing to invest in it. This makes it a decent choice for a very specific type of new linux user.