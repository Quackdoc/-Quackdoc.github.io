+++
title = "Computer repairman's cheap tricks and tips for a better time."
date = 2023-11-02
template = "page.html"

[taxonomies]

tags = ["Linux", "Windows", "Virtualization"]
categories = ["Tutorial"]
+++

Repairing computers can be long and tedious processes, these tips and tricks can make doing it just a little better on the provider. 
<!-- more -->
## Qemu
Probably the single most valuable tool in my toolkit is Qemu. Qemu allows you to emulate a PC. This has so many benefits. Primarily I can repair 10-20 PCs all at the same time using a single PC set up right. A simple HBA, plenty of ram, and a decent CPU is all you need. This also has the benefit of being significantly faster than many old systems that can be ram or CPU limited. However it also allows you to clone and clean drives in preparation for an upgrade as I will talk about more in the next segment. For users looking to try it themselves, here is a simple command line you can use to try this yourselves assuming you have a linux server for working with. 

```sh
sudo -E qemu-system-x86_64 -accel kvm \
    -m 4096 -smp 4 -cpu host -bios /usr/share/OVMF/x64/OVMF.fd \
    -display gtk -device qxl -drive file=/dev/sdd,if=none,id=drive-disk0,format=raw \
    -device ahci -device ide-hd,drive=drive-disk0,id=virtio-disk0 \
    -net nic,model=e1000 -net user -boot menu=on -usb -device usb-tablet
```

breaking this down, let's see what we have:

* `sudo -E` : This command allows us to run the program as root, the -E part "preserves environment" which is needed on wayland so we can actually have the display connect to the machine.

* `qemu-system-x86_64` : This is the application name

* `-accel kvm -m 4096 -smp 4 -cpu host -bios /usr/share/OVMF/x64/OVMF.fd` : These set up the machine, Basically use 4GiB of ram, 4 cores, and pretend the emulated CPU is the hosts, this can have small preformance benefits

* `-display gtk -device qxl` : This is setting up the display, it will emulate a graphics card called qxl "a very simple thing since we only need basic graphics support" and create a window to show the VM on using a toolkit called gtk, other backends exist, but this isn't important to us, as long as the window opens up, we are good. 

* `-drive file=/dev/sdd,if=none,id=drive-disk0,format=raw -device ahci -device ide-hd,drive=drive-disk0,id=virtio-disk0` : These are what allows us to boot from the disk drive. the /dev/sdd is the drive we wish to boot from. 

* `-net nic,model=e1000 -net user` : This allows us to connect to internet, It's not always a good assumption that you should connect, Whether or not to add this is up to your discretion. 

* `-boot menu=on` : This allows us to choose a boot menu, this can be helpful as qemu doesn't always like to boot from the drive we pass through
 
* `-usb -device usb-tablet` : This defines how the mouse behaves, I don't like the VM grabbing the mouse, so when you add this the VM behaves more like a normal application. 



### Windows qemu
While you can use qemu on windows (simply use `-accel whpx,kernel-irqchip=off` and find the drive name and path using `wmic diskdrive list brief ` and enter like so `file=\\.\PHYSICALDRIVE4,if=none,` instead. Qemu on windows when doing this can be quite fragile (though seemingly faster when it DOES work) as whpx can often crash in a variety of ways and haxm is discontinued. 

it could be possible that using [usb-uas instead (usb scsi) guide here](https://android.googlesource.com/platform/external/qemu/+/emu-master-dev/docs/usb-storage.txt) could lead to better preformance and reliability when running under windows

So instead you can also use Hyper-v on windows, It can be a bit slower then qemu, but at least it reliably works, it's still fast, and most importantly still free. I wont give the entire run down as this is not a hyper-v guide, however the key checklist, In order, is as follows

* the disk is off line
* create a new vm - attach disk later
* checkpoints are disabled
* add new scsi - hdd - physical disk
* set boot from hdd higher priorty in Firmware tab
* Disable checkpoints

If at any point, something goes wrong (like reporting checkpoint error or drive not showing up), close the entire hyper-v window and try again. You may have done something out of order, and hyper-v is terrible at updating itself live.


## Local server
Every repair technician should have a 10+Tb array for short term data storage. Not only does this allow you to save your tools, any media related stuff and other business related information, It can be used to help repair computers to a much faster degree and much more safely.  

Being able to clone a customers drive is great for many reasons, First one is obvious, It gives you a safety net in case you accidentally lose data, or the drive dies in your care. Having a backup of that data will never be a bad idea. 

Secondly you have the qemu use I talked about above. If you are preparing to migrate your customer to another drive, you can clone the old drive to your storage array and boot from that instead, This can be exponentially faster then an HDD, and I'm sure many of you know that working from an HDD can actually be an out right costly endeavor as it takes up valuable time that could be spent elsewhere. This also allows you to work around potential drive faults. If the customers hard drive is failing, and you need to clone it to a new device, doing a clone directly to your storage array can help recover any data that fails, or in the case of a premature failure causing the clone to fail, there is a higher chance of being able to rescue what data was there in the first place. 

There are other benefits too, One of the services you could provide given a sufficient storage server size, would be short term data retention and backup services. This is something I personally do with customers who wish to upgrade computers or hard drives for free, as well as a paid service by itself. 

## Boot tools.
Boot tools are an invaluable part of any repair technician's toolkit, These can boot on a PC that are otherwise completely unusable due to a corrupt or bad boot device, Virus preventing boot, and more issues.

* Hirens
* aio srt
* winpe tools

## This is incomplete, More will be added in the future