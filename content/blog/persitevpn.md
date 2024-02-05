+++
title = "Per-site VPN using firefox and wireguard."
date = 2023-11-04
template = "page.html"

[taxonomies]
tags = ["Linux", "Windows", "VPN"]
categories = ["Tutorial"]
+++

### NOTE: THIS IS UNSAFE

It turns out that sometimes when firefox boots the multi account containers will not load up. If this happens and you use a website you don't want to be used over your normal network **THIS WILL COMPROMISE YOU** The websites will load through your normal network. You have been warned.

<!-- more -->

# Per-site VPN using firefox and wireguard (or other VPNs)
The basic Idea of this is pretty simple, Firefox has an offical addon called Multi-Account containers. This allows you to create and use pseudo profiles. These profiles allow you to isolate cookies, And use mozilla VPN. But "wait" you say, "Mozilla VPN sucks!" I agree! thankfully Mozilla may not allow us to use wireguard VPNs from within firefox let alone the extension, Socks5 proxies are supported.


### Why do we need wireguard at all?
Aside from the obvious "My VPN provider doesn't support socks5." Socks5 doesn't have inherent encryption, this means a misconfigured setup has a significant chance of de-anonymization occurring. There are many obvious reasons why this is bad. Due to this, It's often better to use a more reliable and easy to use VPN solution like Wireguard or OpenVPN. 

## What methods are available to us?
There are many ways of doing this. Their are main two ways we will focus on today. The first will be using ShadowSocks + a VPN, the other method will be to use a tool called wireproxy. While many other options do exist. These are what this guide will be focusing on.

### Wireproxy
Wireproxy is the easiest but comes with a sad disadvantage. Firstly however, Wireproxy handles both the VPN and the socks interface. You can set it up really easy by making a simple config file and launching it with a cmdline. This makes wireproxy really fast to setup, on linux you can easily do this via start configs, create a systemd service (my prefered method) or any init system autostart system you prefer. On windows, you can easily set this up using windows' task scheduler. Simply follow the instructions below for that. 

The unfortunate detriment that comes with wireproxy is that when inactive for a time, the VPN portion goes into a "sleep" mode. meaning when you open a website you will be greeted with a "Hmm. We’re having trouble finding that site." error from firefox. Simply wait a few seconds then refresh the page. It should then be working. 


#### Windows setup
Setup is quite nice and easy, First download wireproxy from their [Github repo.](https://github.com/pufferffish/wireproxy/release), (You may need to click "Show all assets" to download the windows version. you will want `wireproxy_windows_amd64.tar.gz`. Don't worry if you havent seen this file extension, Both windows file explorer and 7z can extract it perfectly fine, (though you will need to extract it twice). Copy the executable to somewhere you will remeber it (I choose C:\Tools). 

The next step is to go to your VPN provider and download the appropriate wireguard config file. I cannot help you here as this will vary in a per vpn basis. Place this file somewhere you will remember (for me I will do C:\Tools\VPN). 

Now that you have downloaded the wireguard config file, we need to create the file. No worries, this will be mostly copy and paste with some minor edits. Create a text file with the extension .conf. This is my file contents. 

Filename: C:\Tools\VPN\wireproxy-swiss.conf
```
WGConfig = C:\Tools\VPN\Swiss-vpn.conf

[Socks5]
BindAddress = 127.0.0.1:25344
```

Nice and simple right? Simply point `WGConfig` to the config you downloaded from your vpn provider. then for the `[Socks5] BindAddress` part `127.0.0.1` is the IP address for your local PC. this is often also called localhost. `:25344` is the important part, This is the part we will need to change for additional configs. This is called a `port` if you think of an IP address you can think of it as an Apartment building. The port is the room number inside of the building. 

Each network service uses ports. Without getting too technical, Every VPN instance we run will be it's own network service, as such each one needs a unique port number. For each subsequent VPN we run, we should +1 to that number. This will keep things organized and simple to remeber. 

Now that we have the file running, we are on the last part of the non firefox stuff. in your start menu, search `Task Scheduler` and launch the app that comes up. At the top of the rightmost bar you should see `Create basic task`, run it. Give it a good name and description! I'll do the below.

* Name: Swiss socks5 VPN
* Description : Swiss VPN using Socks5 for firefox


Click `Next >`, Tick "When I log on", Click `Next >`, "Start a program", Click `Next >`. and Finally we add the command to start the proxy and fill it as follows:

* Program/script: "C:\Tools\wireproxy.exe"
* Add Arguments: "-d -c C:\Tools\VPN\wireproxy-swiss.conf"

Click `Next >`, Tick "Open the properties dialog for this task", Click `Finish`. You now want to set "Run whether user is logged on or not". This is necessary because windows is a really stupid OS, and if it's not set, you will get a black window opening up running the program! not something we want at all. Finally click OK. After this you can either reboot your PC, or right click the task we made, and click `Run` You can now go to the firefox section at the bottom of the page.

#### Linux
The guide for this is mostly 1:1 with windows with the exception of scheduler and file paths. Making a systemd service is easy enough, Simply create a file with a .service extension, naming it whatever you please, Locate it in this path `$HOME/.config/systemd/user/SERVICENAME.service` and enter the appropriate contents. it may look something like this 

```toml
[Unit]
Description=Descrptive description
After=network.target

[Service]
Type=simple
ExecStartPre=/bin/sleep 2
ExecStart=wireproxy -d -c PATH/TO/Wireguard.conf"

[Install]
WantedBy=default.target
```

Other Init systems or autostart methods will not be covered here as systemd covers the vast majority of installs now, anyone not using systemd I will assume have the knowledge to set up an autostarting application themselves anyways. 

### Shadowsocks
Shadowsocks can be a really complicated program, however thankfully for us, Shadowsocks provides sslocal. sslocal is a CLI client that makes configuration really easy. The major detriment to shadowsocks is that it doesn't handle connecting to a VPN for us. 

Setting up a VPN to not route any default traffic unless the application explicitly wants it can be pretty hard sometimes. Sadly the official wireguard client on windows doesn't support split routing which could be used for this and on linux. well it's linux, well into DIY land. However I do have instructions on how to set this up for those savvy enough.

#### Windows VPN setup
An example dummy Windows conf is given below on how to setup a wireguard VPN to not preform routing of data by default. Note the postup and predown scripts. A brief description is as follows  

`Table = off` In networking, we call the connections from one place to another `Routes`. and the `Table` is a list of these potential routes and their priorities. Unfortunately for us. the official wireguard client isn't all THAT smart as it doesn't allow us to set `metric` which is the priority an interface has. But thankfully some powershell scripting can help us out. so we tell wireguard "Don't add the new routes to the routing table" This means nothing would be able to actually use this connection. which is where the next step comes in. 

`PostUp =` allows us to run commands on the host before the after the interface goes live. Since we turned off routing table, we need to manually add it ourselves

`PreDown =` Like wise, since we added the connection, but wireguard's table is disabled, we also need to manually remove the route. Leaving it running could lead to unwanted behaviors and bugs. so best to remove it outright



#### NEVER RUN UNTRUSTED SCRIPTS
If you cannot read what this is doing Do not run it. I will not explain what this does. Running any sort of script from the internet is dangerous. USE wireproxy! for all you know, this could install a keylogger (and YES! It could be this small of a script!) or delete all your pictures. I was conflicted on whether or not to add this. However I think it will be much more beneficial if I do. (though wireproxy could be malware too, welcome to the internet and modern computing. at least you can isolate it if you know how.) 

```toml
[Interface]
PrivateKey = thisisgarbageinputthatrepresentsaprivatekey
Address = xxx.xxx.xxx.xxx/32
DNS = xxx.xxx.xxx.xxx
PostUp = powershell -command "$wgInterface = Get-NetAdapter -Name %WIREGUARD_TUNNEL_NAME%; route add 0.0.0.0 mask 0.0.0.0 0.0.0.0 IF $wgInterface.ifIndex metric 9999; Set-NetIPInterface -InterfaceIndex $wgInterface.ifIndex -InterfaceMetric 9999;"
PreDown = powershell -command "$wgInterface = Get-NetAdapter -Name %WIREGUARD_TUNNEL_NAME%; route delete 0.0.0.0 mask 0.0.0.0 0.0.0.0 if $wgInterface.ifIndex metric 9999; Set-NetIPInterface -InterfaceIndex $wgInterface.ifIndex -InterfaceMetric 9999;"
Table = off
```


#### Linux VPN setup
For linux things are both easier and more complicated Linux has many, MANY ways to handle VPNs. I will be using network-manager for this since it will apply to most distros out there. if this doesn't apply to you, you likely already have the knowledge to implement this yourself. In theory, it should be really simple since nearly all networking tools allow you to set the interface priority/metric. however it turns out network-manager is kinda stupid, and has a completely seperate config for DNS priority, and when you set a VPN, it get's priority regardless of Interface priority! 

![WHY](https://media1.tenor.com/images/2707732801c38d7c395ca878b17cbcef/tenor.gif?itemid=13916835)

Download your wireguard config. No need for modifications to this one. then run this commands.

```
connection import type wireguard file wireguard-swiss.conf
nmcli con modify wireguard-swiss  ipv4.dns-priority 9999
nmcli con modify wireguard-swiss ipv4.route-metric 9999
nmcli con modify wireguard-swiss ipv4.never-default yes
nmcli con modify wireguard-swiss  ipv6.dns-priority 9999
nmcli con modify wireguard-swiss ipv6.route-metric 9999
nmcli con modify wireguard-swiss ipv6.never-default yes
nmcli con modify wireguard-swiss connection.autoconnect yes
```

We run these commands since we want only connections that explicitly want to use the vpn to use it. After running these commands the new interface should come up every time you log in, and the priority should be the lowest possible each time.

##### Shadowsocks setup
Just like on setting up wireproxy, This uses mostly the same guide. On linux, Create the systemd file, enable it. the command to execute is this `sslocal -b 127.0.0.1:25344 --outbound-bind-interface VPN-NAME-HERE`. Use 127.0.0.1 to bind to localhost on port `25344`. then use `--outbound-bind-interface VPN-NAME-HERE` it's important to keep note that the vpn name is case sensitive.

```
[Unit]
Description=ShadowSocks5 Proxy
After=network.target

[Service]
Type=simple
ExecStartPre=/bin/sleep 2
ExecStart=sslocal -b 127.0.0.1:25344 --outbound-bind-interface wireguard-swiss

[Install]
WantedBy=default.target
```
One thing that is good to keep note of is the `ExecStartPre` command, this simply runs sleep for 2 seconds to make sure every thing else gets up and running first. This may not be necessary, but IMO it's nice to have anyways for a bit of extra reliability. Not to mention, I doubt you could launch a firefox session, open a web page, and have it error out before this manages to start up. If you do manage this, you can always lower the sleep delay.

### Firefox
Finally for the last steps, Setup the addon Multi-account containers for firefox. Firstly Add the addon to your browser using firefox's [addon page for it.](https://addons.mozilla.org/en-CA/firefox/addon/multi-account-containers/) Then open the addon window, Click `Manage containers`, and then `New container`. Give it a good Name, Colour, and Icon.  

You will then go again, click `Manage Containers`, Click the Container you just created, and then click `Advanced Proxy Setup`. Add the socks5 IP and port we chose, in my case it is `127.0.0.1:25344`. However we need to prefix it with `socks://` to tell firefox it is a socks vpn, so I would enter `socks://127.0.0.1:25344`. Finally click apply, and you are done! 

You can now use this profile to browse webpages with that VPN without interrupting any other traffic on your PC. Simply repeat the instructions you had to go through again, using a different port and VPN server to create new profiles that have different VPNs. 
