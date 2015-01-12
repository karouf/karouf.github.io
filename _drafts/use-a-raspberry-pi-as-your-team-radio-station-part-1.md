---
layout: post
title:  "Use a Raspberry Pi as your team's radio station - Part 1"
date:   2014-12-10 11:56:36
categories: jekyll update
---

## Intro

TODO: Rewrite
I love having music playing along when I'm working but it can sometimes feel a bit like I'm isolated with headphones on. It so much better to have my co-workers bobbing their heads to the same music as I do. So when I came across an article explaining how the guys at GitHub share the same music even though they don't share offices together, I thought t was a really cool idea. The sad bit was that it was only working on a Mac as it used Apple AirPlay to stream the music.
Earlier this year, I was sifting through my starred repos on GitHub and found out that there was a v3 branch in the **play** repo that is replacing AirPlay with mpd. I forked the repo and managed to install it on a VM after some hacking because, well, [it's not used at GitHub anymore]() and so nobody is maintaining it.
We tried it for a while but I felt like we could do better and have it running on dedicated hardware. Introducing the Raspberry Pi, another thing I wanted to play with for a while.
It's a tiny computer on a small PCB and you can get one for about $35. Plug it to the power and the network and there you go, you have a perfectly acceptable server. And with larger capacity SD cards now available, you can have enough room for a lot of music.


## Checklist

It's always good to know what you'll need beforehand so you don't spend half an hour looking around for that part you can't go on without at some point during the setup.

For this project, here are all the things you'll need to have access to:

* a Raspberry Pi B+, like [this one](http://www.amazon.com/dp/B00LPESRUK/?tag=renaumarti-20)
* a [nice case](http://www.amazon.com/dp/B00M8ZEHIQ/?tag=renaumarti-20) or [a more regular one](http://www.amazon.com/dp/B00LU4QIO2/?tag=renaumarti-20), to protect the Pi
* a microUSB power outlet, to feed the Pi: [european model](http://www.amazon.com/dp/B00EZLBJ32/?tag=renaumarti-20), US model or UK model, depending on where you are
* a MicroSD card, big enough to store the OS and all the music you want to stream. Here I used [a 64GB one](http://www.amazon.com/dp/B00CHHTOII/?tag=renaumarti-20) which is enough for my use case
* a computer, to prepare the microSD and to connect remotely to the Pi later on
* a microSD card reader, to plug the microSD into your computer
* a USB keyboard, to control the OS installation process before you can connect remotely to the Pi
* a HDMI cable
* a HDMI compatible screen, like a recent TV
* a working Internet connection, to update the OS and install required software

Disclaimer: I've included the links to Amazon for your convenience but bear in mind that those are affiliated links.

Got everything? We're good to go then, we'll start by putting together the Raspberry Pi.


## Hardware

Get the case and the Raspberry Pi out of their respective boxes and wrappings.
Be careful with the Pi though: you're handling the PCB directly and you could damage it either through a static discharge or being too rough with it.

![Raw Raspberry Pi B+ PCB](/images/raw-raspberry-pi-pcb.png)
![OneNineDesign Raspberry Pi B+ black case](/images/oneninedesign-raspberry-pi-bplus-black-case.png)

Open up the case. Most cases will just split in bottom and top halves.
You can see here that mine is a bit trickier as it has a bottom part to receive the PCB, a middle part that covers the PCB but still gives you access to the display, camera and GPIO connectors, and a top cover.

![OneNineDesign Raspberry Pi B+ black case parts](/images/oneninedesign-raspberry-pi-bplus-black-case-parts.png)

Place the Pi on the bottom part, slide it in towards the microSD card slot and it should settle in.

![Sliding the Pi into position on the bottom part of the case](/images/sliding-the-pi-into-position-on-the-bottom-of-the-case.png)
![The Pi settled in on the bottom of the case](/images/the-pi-settled-in-on-the-bottom-of-the-case.png)

Insert the bottom part into the middle one. You might have to use a little force but be careful not to damage the Pi.

![Inserting the bottom part of the case into the middle part](/images/inserting-the-bottom-part-of-the-case-into-the-middle-part.png)
![Bottom part of the case inserted into the middle part](/images/bottom-part-of-the-case-inserted-into-the-middle-part.png)

As you can see, the middle part covers the PCB but still gives access to all the internal connectors.

![The middle part of the case gives you access to the connectors while still protecting the PCB](/images/middle-part-of-the-case-from-above.png)

The only thing left is to put the top cover in place: just place so the guides match at the corners and on the sides. It will insert a little but will not click and will leave a small gap between the top and the middle part.

![Putting the top cover of the case in place](/images/putting-the-top-cover-of-the-case-in-place.png)

Now the whole case is put together and will make it easier to work with the Raspberry Pi.

![The whole case put together](/images/.png)

You can have a look at the sides of the case and will find the following connectors:

Ethernet and 4 USB connectors
![Ethernet and 4 USB connectors](/images/rapsberry-pi-ethernet-and-usb-connectors.png)

MicroUSB power, HDMI and 3.5mm jack audio and composite video connectors
![MicroUSB power, HDMI and 3.5mm jack audio and composite video connectors](/images/rapsberry-pi-microusb-power-hdmi-and-jack-audio-and-composite-video-connectors.png)

MicroSD card push-push socket
![MicroSD card push-push socket](/images/rapsberry-pi-microsd-card-push-push-socket.png)

Your Raspberry Pi is now ready to be booted up for the OS installation.


## OS

In order to run anything on the Raspberry Pi, we first have to install an operating system.
I went with Raspbian which is a Linux distribution for the Pi based on one I'm used to: Debian. The current version is Wheezy and can be found on [the Raspberry Pi Foundation website](http://www.raspberrypi.org/downloads/) along with several other distributions.
I will show how to install Raspbian but the instructions can easily be used for any distributions available from the Raspberry Pi Foundation.


### OS image

For the Pi to kick off the installation process at boot, the Raspbian OS image has to be written onto the MicroSD card.
So get the image from [the Raspberry Pi Foundation website](http://www.raspberrypi.org/downloads/), please prefer the [torrent download](http://downloads.raspberrypi.org/raspbian_latest.torrent) but note that the [direct download](http://downloads.raspberrypi.org/raspbian_latest) is also available.
When the download is complete, extract it, plug the microSD card into your computer and follow one of the fine guides from the Raspberry Foundation to write the Raspbian image on it:

* [Linux](http://www.raspberrypi.org/documentation/installation/installing-images/linux.md)
* [Mac OS](http://www.raspberrypi.org/documentation/installation/installing-images/mac.md)
* [Windows](http://www.raspberrypi.org/documentation/installation/installing-images/windows.md)


### Installation

When your MicroSD card is ready, plug it in your Raspberry Pi and connect the later to your USB keyboard and HDMI display. Then power the Pi with the MicroUSB power adapter.

> It's alive!!!

The install process will take you through all the OS configuration (see [this step-by-step guide](http://www.raspbian.org/RaspbianInstaller) for hand-holding) and then reboot the Pi.

Out of the box, Raspbian uses DHCP to configure its network interfaces, in most networks that should work straight away. If you don't have control over DNS and DHCP though, you might want to assign a static IP to your Pi, the [Debian wiki](https://wiki.debian.org/NetworkConfiguration#Configuring_the_interface_manually) has the information you need to do that.

You should then be able to SSH into your server:

```bash
login as: pi
pi@10.0.0.3's password:
Linux play 3.12.28+ #709 PREEMPT Mon Sep 8 15:28:00 BST 2014 armv6l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Dec 17 19:45:29 2014 from karouf-laptop
pi@play ~ $
```

Congrats! You now have a proper server that can fit in your back pocket.


### Update

You will first need to have a working Internet connection from the Raspberry Pi.
To check whether it can acces the Internet, issue the following command at the prompt:

`pi@play ~ $ wget http://google.com`

If it works, great! Else it should tell you what's wrong with your network connectivity. Troubleshooting this is out of the scope of this article but online resources should be able to help you with that.

In my network for example, I had to set up environment variables so all HTTP, HTTPS and FTP traffic would use my NTLMaps proxy:

`pi@play ~ $ sudo vi /etc/profile.d/proxy.sh`

```bash
export http_proxy="http://10.0.0.1:5865"
export https_proxy=$http_proxy
export ftp_proxy=$http_proxy
export no_proxy=localhost
```

And also to configure APT to use my APT caching proxy:

`pi@play ~ $ sudo vi /etc/apt/apt.conf.d/80proxy`

```bash
Acquire::http::Proxy "http://10.0.0.1:3142";
```

You should now be able to update APT's packages list:

`pi@play ~ $ sudo apt-get update`
