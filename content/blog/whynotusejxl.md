+++
title = "Why aren't we using JXL?"
date = 2024-01-14
template = "page.html"

[taxonomies]
tags = ["Images"]
categories = ["Tech Showcase"]
+++

This is a mini part two in something I didn't think was going to become a series. but I wanted to test this specifically anyways. So here we go.

<!-- more -->

## I downloaded 20k images from danbooru.

I decided to do a real world test case for JXL. I haven't seen many of these done. I've seen a lot of smaller scale tests which showcase sometimes JXL does great, and sometimes not so much. But I haven't really seen anything actually indicative of how JXL can actually benefit service providers. 

Firstly, why 20k images? I decided this would give a pretty good representation, without being a complete dick. The 20k or so (a little less due to errors) images came out to 34.69 GiB total.

It's really not good etiquete to do something like this, and any more images, say 60k I would rather get an OK for someone first, since I had already downloaded 34Gb of data over a couple minutes. I decided roughly 20k was enough images. Throttling my download speed would be fine too but I still would rather show good ettiqute.

So how did I do this? It's really simple to replicate. Below is a script you can use to test this out for yourself.

```sh
#!/bin/bash
## Tools Needed;
## imageboard_downloader https://github.com/FerrahWolfeh/imageboard-downloader-rs
### If cargo is installed, compile and install with cargo install --git https://github.com/FerrahWolfeh/imageboard-downloader-rs
## GNU Parallel https://www.gnu.org/software/parallel/

mkdir -p jpg
mkdir -p jpg-jxl
mkdir -p png
mkdir -p png-jxl

## Download Images
## "-animated" to remove gifs and videos

imageboard_downloader search -i danbooru -d 10 -l 20000 -o ./dls --id -- "-animated"

mv dls/*.jpg jpg/
mv dls/*.jpeg jpg/
mv dls/*.png png/

## change -j ${JOBS}
ls png/  | parallel -j 6 cjxl -e 4 -d 0 --num_threads 1 png/{} png-jxl/{.}.jxl
ls jpg/ | parallel -j 8 cjxl -e 6 -d 0 --num_threads 1 jpg/{} jpg-jxl/{.}.jxl
```

A nice simple script. We don't want to download any animated pictures like gifs, or any video's since it doesn't really help out what we need for this. I use `-e 4` for encoding the PNGs since I want my ryzen 2600 to have a fighting chance at encoding these at any reasonable speeds. I also only have 16g of ram and need to use my PC for doing other things at the same time. You can get better results then I did by increasing the effort, but for lossless I think `-e 4` is a good benchmark.

When encoding the Jpeg images we can afford to use a much higher effort since we are by default using lossless jpeg transcoding which is extremely fast and efficient. Any more then `-e 6` however and the gains you see are minimal at best so it's best to just leave it here IMO. So, how did we come out? Below is the before and after of the roughly 20k images.

```sh
➜  booru-test dua png jpg   
  15.69 GiB png
  18.97 GiB jpg
  34.66 GiB total
➜  booru-test dua png-jxl jpg-jxl
   9.63 GiB png-jxl
  15.10 GiB jpg-jxl
  24.73 GiB total
 ```
 
It's also important to check how many errors we ran into, Not all files can always be decoded, sometimes it will error out so we can quickly check the file counts, and yup, not enough errors to be a massive issues.
```sh
➜  booru-test ls png-jxl | wc -l && ls png | wc -l                                         
4774
4776
➜  booru-test ls jpg-jxl | wc -l && ls jpg | wc -l
14559
14560
```

This may not be the most impressive numbers we have ever seen. We can clearly see PNG gave us a great size savings, while JPG didn't give us too much. PNG saved us around `39%` of filesize at `e4` and jpg at `e6` saved us about `15%` of filesize. This is slightly more impressive when we realize we lost pretty much nothing (besides browser support which is the topic of this post) in exchange. We still have fast decode times, we still have progressive decoding (unlike some other image formats) and this is *entirely lossless*. Not a single image (except those that failed to decode for one reason or another) was a lossy encode.

Even judging just the jpeg benefits. If you are a provider, this is 15% filesize and bandwidth savings. 15% file size may not sound like a lot, but 15% bandwidth is quite the savings for a lot of services. For services that self host and have bandwidth speed cap, this is 15% less traffic clogging your pipes. A direct benefit to the total speed your users can experience. If you pay for bandwidth. Well it speaks for itself.

## Well that's neat. Why aren't we using this?

This is a great question. You may have noticed I didn't even touch on lossy JXL. Why? In my opinion, Lossless JXL itself is a large enough benefit to warrent inclusion in the modern browser landscape. We don't need any flawed metrics to compare visual fidelity. These are 1:1 comparisons with no data loss.

However the team working on chromium preformed a massively flawed test in where they compared AVIF to JXL using a massively at the time out of date libjxl where avif got better decode preformance. When comparing image fidelity, they choose to showcase MS-SSIM which regularly outpreformed JXL vs other metrics they had which often preformed in favour of JXL but usually more balanced weighing JXL and AVIF similarly.

They often compared single thread and eight threaded workloads, but nothing inbetween. 

Despite this flawed testing methodology Apple has integrated JXL support in their products, and a significant amount of internet traffic advertises JXL support. This is from Jon Sneyers on the JXL discord 

> November 21:
    13.92% of Cloudinary image requests came from a user agent supporting jxl (2.8 billion out of 20.1 billion images served that day)

> December 5: 
    19.52% of Cloudinary image requests came from a user agent supporting jxl (3.1 billion out of 15.7 billion images served that day)

As we can see nearly 20% of their traffic now is advertises JXL support. This is roughly in line with exptectations as gs.statcounter reports IOS to account for a total around 28% of total mobile market share. And with how significant mobile internet usage is on the global scale now, this means JXL compatible devices account for a significant amount of marketshare.

Recently we were even shared a small chart showing JXL enabled traffic from cloudinary, You can see the chart below, where it currently sits about 20% traffic. Shopify actively serves JXL to supported clients. If they have similar statistics that would mean about 20% of their mobile traffic gets JXL images.

![cloudinary chart](https://files.catbox.moe/tcmd4f.png)

For android devices, we do at least have freedom, we can install a JXL compatible browser, and a JXL compatible gallery app. However that will only go so far as being compatible with those specific apps. It is no platform support sadly. Custom roms may implement support for JXL, but well... That's still just custom roms.

In the end, Chrome and Android support are the final barriers. Firefox... well does anyone actually care what they do anymore? Once chrome and Android support JXL. The vast majority of applications people at mass use will support it. How can you get involved? It's easy, Ask on the forums for your devices for JXL support. If you find webpages that use JXL like my previous entry, you can report those webpages as broken to chrome.

## Why not talk about lossy JXL?

I am very hesitant to talk about JXL, First and foremost, for those who really want to know, let me show you the results of running `cjxl -e6 -d 0.5` on the folder. These are the file sizes down below.

```
booru-test dua png png-jxl-loss 
   3.02 GiB png-jxl-loss
  15.69 GiB png
  18.71 GiB total
```

Now, Why don't I want to talk about lossy? I don't want to get caught in the inevitable trap of talking about metrics. Metrics are quite an intensly debated topic, and I could talk great about one metric, or talk crap about another one, and always upset someone. Each metric has it's flaws. and quite honestly i don't want to spend a week testing every single metric I can find. `-d 0.5` is a distance which is considered "Visually lossless" by many and this is the only reason why I am showcasing it here, it is fairly uncontroversial. I can't tell a difference, and this is how I encode all of my pictures, Camera shots, Downloaded pictures, Comics and manga. Doesn't matter, If I don't need lossless, I save it using `-e 0.5`. I typically bump the effort to `-e 8` for my own content, but `-e 6` might be considered a more safe estimate for what people would actually want to encode it with if you are running a service. As you can see, JXL is smaller, there really isn't any surprise here. You get your progressive decoding for web delivery, The benefits I talked about in the other article so on and so forth.

Without running a comprehensive comparison against AVIF, WEBP, and Jpeg. IMO there really isn't any point in talking about lossy jxl, And I believe the merits of lossless JXL to be more then enough to prove Jpeg-XL's worth in the modern market.
