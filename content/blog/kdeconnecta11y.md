+++
title = "Accsessibility using KDE connect can be quite nice."
date = 2024-02-06
template = "page.html"
description = "Using kde connect and whisper makes a very good accessibility input."

[taxonomies]
tags = ["Linux", "Android", "Accsesibility"]
categories = ["Tech Showcase"]
+++
#### This article is a DRAFT. Not yet complete but most stuff is here
KDE connect can actually be a very valuable accessibility tool. It allows us to use our android phones as keyboard and mice inputs for our desktop and laptop computers. Since we can use our optimized keyboards for our android devices on our desktop without any problem, including voice input devices, KDE connect makes accomdating specific input needs quite a bit easier. This post will be a short tutorial on how to do this.

<!-- more -->

## Before we start.

I want to preface this with the fact that I'm using KDE Connect and FUTO's voice input for typing this out almost entirely for this article. There are a couple ways to actually do this, however, I'm going to be using a notepad for this since it is a little bit more reliable. I will be using a keyboard to do minor corrections since voice detection is not perfect. However, with a clear enough enunciation and pronunciation, you can get a very good and accurate result.

Unfortunately with KDE connect currently there's a small issue where it does not do grammar for some reason. However, when I enter into a notepad on my phone and copy it over, it works perfectly fine so that's what I will be doing for this tutorial. It's worth noting if you don't need the grammar correction and autogrammer in general, then doing it straight through KDE Connect will work perfectly fine. I'm not sure why it doesn't work, but it doesn't. It would be nice if it did.

So why do this in the first place? I personally have developed a mild RSI. It starts to become quite painful when I try to do any moderate amount of typing for any prolonged period of time. This has made doing any kind of typing work infeasible for me because the pain just becomes too much too often for it to be of any value. So, by using KDE Connect for my input with voice input, it actually allows me to very accurately and fast talk out what I want to write instead of type it out. I also find that KDE Connect is much more reliable than using PC-based solutions for both Linux and Windows. 

Voice input uses something called whisper. Whisper is a speech-to-text engine that works quite well. It runs entirely locally so you don't need any internet to use it. You just talk into your phone and it dumps out the text. While there are methods you can use on desktop to do this, few of them actually work quite as well as just using your phone. They can be of spotty quality, they can have bad microphone inputs depending on your device, it can be all sorts of headache. I find that by using my phone it's far more convenient and reliable than using my desktop so this is the route forward I have elected to choose. The only thing I need between my phone and my desktop is a LAN connection. As long as they are both on the same router, they work perfectly fine together.

### Downloading the pre-requisites.

The first thing we need to do is install KDE Connect, which is easy enough to do. It's in both F-Droid and Google Play Store, so just download it from your favorite store and you're off to the races. Setting up KDE connect is fairly simple. So go ahead and do that. On Windows you need to download the KDE connect app from the store and on Linux. Well, I'll leave that up to you. GNOME, KDE, XFCE, all that stuff. They might have their own setup. If you're on Wayland, it can be a little bit more involved depending on your DE. If you're on X, it's probably just gonna work. Verify your virtual input works perfectly fine and we can move to the next step.

The next step is setting up your input method. I'm going to use FUTO voice input for this. It is an excellent voice input as I said earlier. It works great. So this is what we're going to use for this tutorial. You can use Google Speech to text. You can use a different keyboard. It doesn't matter since they will all wind up working. To install FUTO's voice input you can either get it from the Google Play Store, F-droid, or download the APK directly. To get it from the Google Play Store, install as you would any other app. To get it through F-droid, you're going to have to add the repo in your favorite F-droid store, which I will not walk you through. [However, the link to the website in which you can find the repo info is here](https://voiceinput.futo.org/). You can, as I already said, also download the APK directly to if that is more your style.

Once you have voice input installed, you're going to want to open the app for and set up your languages and model. The languages are obviously going to be the language you're going to speak into the device. Voice input supports a large variety of languages, and you can download multiple at a time. It's not recommended to mix languages in the same capture since it will try to automatically choose what model. So, you really want to speak one language at a time if you can help it. If you need to speak more languages at once, stop the input and restart it.

Now we need to setup the model. This is the sort of database it uses to match your voice to text. The larger the model, the more language it supports and the more data it contains. However, the slower it will also be. The larger models can also sometimes it can get caught up and use words you didn't mean. You will want to experiment and pick which one works best for you.

You may also want to visit the Advanced menu. This is especially true if the language you have is not available, or the model you have is not too great. There's a couple things in here that could be really nice. You may or may not wish sounds like coughs and other sounds to show up. These will show up in square brackets, so you may or may not want them. The other thing it allows you to do is select "undertrained models". This can help in some cases, but generally it's not recommended.

Finally we may want to do some tests with the testing menu that is available. Open the testing menu and test it as you please. Once you're satisfied, we can go to the last step, which is enabling the keyboard. In the Advanced section or the testing section, you will have an option that says Open Input Method Settings. This will take you to your Android settings where you can enable the voice input. You're going to want to do this step now.

Now we can actually use the application.

### Usage
##### Swapping input methods.

Now we have to actually be able to swap inputs. This is a little bit strange on some devices, but it's generally not too bad. While it does depend on the keyboard you are using a bit, for most phones it's nothing but a minor inconvience. If you are using the Google keyboard like I am, then pressing the microphone option is just going to open up Google speech text and not voice input. Instead, in the bottom right corner of my phone, I get a little keyboard icon when I open up a text prompt. I click that icon and a keyboard selection pops up. I click the voice input and then I can start talking. Once I stop talking, after a while it will automatically stop and start to figure out what I said. I can also just click "Tap to Stop", which will automatically stop the recording and start the voice detection. Some users may have a different icon, or may need to set up their keyboard to add a "select keyboard" function. I can't really help here.

##### Method 1: Notes app.

It may be prefered to use a notes application, then syncing the clipboard. This is probably the easiest and most reliable method. This is because when you use a notes app, whisper, or rather voice input from FUTO works lots better. It has proper grammar, and punctuation is normally excellent. You can also use your keyboard to properly edit and verify that the sentence or paragraph is proper incorrect before sending it through to the computer like this as well.

You can use most notes application, however the one I like is nice and simple and quite lightweight. [You can download it from the Google Play Store here.](https://play.google.com/store/apps/details?id=com.farmerbb.notepad) However if F-droid is more your style,[ you can grab it from the link here.](https://f-droid.org/packages/com.farmerbb.notepad/)

Once you've done that, copy all the selected text. Go into the KDE Connect app and click Sync Keyboard. After that, you'll be able to go to your desktop and simply paste, and all of your text will be there. I find this to in general be the most reliable method, though some people might find this a little bit tedious and may prefer to enter the text directly itself, and you can do that with method 2.

##### Method 2: Direct Input.

For direct input to work, you're going to have to click the input method option directly in KDE Connect. In the top right corner you will see a keyboard icon. All you have to do is click that icon, and your keyboard will pop up, swap to your voice input, and start talking. Once you're done talking, Futo voice input will finish up, and it will automatically enter the text directly into the computer. This is typically quite fast, but don't be surprised if the text chugs a little bit visibly as you can see it being input. This is quite normal in a lot of cases because voice input will be sending text so fast it will take a couple seconds for it to catch up. The hitches are quite normal.