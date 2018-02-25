---
title: Use a Raspberry Pi as a jukebox - Part 1
date: 2018-02-25 16:46:00.383000000 Z
categories:
- jekyll
- update
layout: post
---

Working to the rythm of your favorite tune is awesome. And Raspberry Pis are awesome too!

Thanks to a nice little piece of software called [Play](https://github.com/play/play/tree/v3) (not the beany wearing pop band), I was able to turn a $35 Raspberry Pi into a flipping jukebox for my team so we can all bang our heads in synch. Love it!

This series of posts will walk you through the setup of the whole system from getting the hardware up and running to ensuring that your co-workers can't fill up the device with Britney Spears songs. They will still be able to put some though. But then again, you should probably change co-workers if it's the case.
On the other hand you could be suprised by what your colleagues listen to. You might even like it! We found out that it's a good way to discover new music and it always makes for a good conversation.

In this first part, we'll get the hardware up and running with a Debian Linux.


## What it can do for you

Before you embark on this project, you probably want to know if it will be worth the effort. And you're right.
Our jukebox has the following features:

* HTTP streaming so you can connect to it with VLC or your browser
* a nice web interface to
  * see what's currently playing and what's next
  * star songs you like
  * search for songs, albums or artists
  * add songs or whole albums to the playlist
  * download songs or albums
  * see user profiles including
    * the songs they ask for the most
    * their history
    * the songs they starred
* a queueing process that will select random songs but play favorites a bit more often
* authentication using GitHub OAuth
* use of ID3 tags if they are correctly set

Sounds good? 
Let's get to it then.


## The shopping list

It's always good to know what you'll need beforehand so you don't spend your time looking around for missing parts.

Here is everything you'll need:

* a Raspberry Pi B+, like [this one](http://www.amazon.com/dp/B00LPESRUK/?tag=renaumarti-20)
* a [nice case](http://www.amazon.com/dp/B00M8ZEHIQ/?tag=renaumarti-20) or [a regular one](http://www.amazon.com/dp/B00LU4QIO2/?tag=renaumarti-20), to protect the Pi
* a microUSB power outlet, to feed the Pi: [european model](http://www.amazon.com/dp/B00EZLBJ32/?tag=renaumarti-20), US model or UK model, depending on where you are
* a MicroSD card, big enough to store the OS and all the music you want to stream. Here I used [a 64GB one](http://www.amazon.com/dp/B00CHHTOII/?tag=renaumarti-20) which is enough for my use case
* a computer, to prepare the microSD and to connect remotely to the Pi later on
* a microSD card reader, to plug the microSD into your computer
* a USB keyboard, to control the OS installation process before you can connect remotely to the Pi
* a HDMI cable
* a HDMI compatible screen, like a recent TV
* a working Internet connection, to update the OS and install required software

**Disclaimer:** I've included the links to Amazon for your convenience but bear in mind that those are affiliated links. You don't have to use them. Shop around for better prices if you wish, I won't get offended.

Got everything? We're good to go then. So let's start by putting together the Raspberry Pi.


## Hardware TO CHANGE

Get the case and the Raspberry Pi out of their respective boxes and wrappings.
Be careful with the Pi though: you're handling the PCB directly and you could damage it either through a static discharge or being too rough with it.

![Raw Raspberry Pi B+ PCB](/images/raw-raspberry-pi-pcb.jpg)
![OneNineDesign Raspberry Pi B+ black case](/images/oneninedesign-raspberry-pi-bplus-black-case.jpg)

Open up the case. Most cases will just split in bottom and top halves.
You can see here that mine is a bit trickier as it has a bottom part to receive the PCB, a middle part that covers the PCB but still gives you access to the display, camera and GPIO connectors, and a top cover.

![OneNineDesign Raspberry Pi B+ black case parts](/images/oneninedesign-raspberry-pi-bplus-black-case-parts.jpg)

Place the Pi on the bottom part, slide it in towards the microSD card slot and it should settle in.

![Sliding the Pi into position on the bottom part of the case](/images/sliding-the-pi-into-position-on-the-bottom-of-the-case.jpg)
![The Pi settled in on the bottom of the case](/images/the-pi-settled-in-on-the-bottom-of-the-case.jpg)

Insert the bottom part into the middle one. You might have to use a little force but be careful not to damage the PCB.

![Inserting the bottom part of the case into the middle part](/images/inserting-the-bottom-part-of-the-case-into-the-middle-part.jpg)
![Bottom part of the case inserted into the middle part](/images/bottom-part-of-the-case-inserted-into-the-middle-part.jpg)

As you can see, the middle part covers the PCB but still gives access to all the internal connectors.

![The middle part of the case gives you access to the connectors while still protecting the PCB](/images/middle-part-of-the-case-from-above.jpg)

The only thing left is to put the top cover in place: just place so the guides match at the corners and on the sides. It will insert a little but will not click and will leave a small gap between the top and the middle part. This is normal: it will help to create an air flow to keep the Pi from overheating.

![Putting the top cover of the case in place](/images/putting-the-top-cover-of-the-case-in-place.jpg)

Now that the whole case is put together, it will be easier to handle the Raspberry Pi.

![The whole case put together](/images/raspberry-pi-put-together.jpg)

You can have a look at the sides of the case and will find the following connectors:

Ethernet and 4 USB connectors
![Ethernet and 4 USB connectors](/images/rapsberry-pi-ethernet-and-usb-connectors.jpg)

MicroUSB power, HDMI and 3.5mm jack audio and composite video connectors
![MicroUSB power, HDMI and 3.5mm jack audio and composite video connectors](/images/rapsberry-pi-microusb-power-hdmi-and-jack-audio-and-composite-video-connectors.jpg)

MicroSD card push-push socket
![MicroSD card push-push socket](/images/rapsberry-pi-microsd-card-push-push-socket.jpg)

Your Raspberry Pi is now ready to be booted up for the OS installation.


## Install Raspbian

In order to run anything on the Raspberry Pi, we first have to install an operating system.
I went with Raspbian which is a Linux distribution for the Pi based on one I'm used to: Debian. The current version corresponds to Debian Wheezy and can be found on [the Raspberry Pi Foundation website](http://www.raspberrypi.org/downloads/) along with several other distributions.
I will show how to install Raspbian but the instructions can easily be used for any distributions available from the Raspberry Pi Foundation.


### Prepare the OS image

For the Pi to kick off the installation process at boot, the Raspbian OS image has to be written onto the MicroSD card.
So get the image from [the Raspberry Pi Foundation website](http://www.raspberrypi.org/downloads/), please prefer the [torrent download](http://downloads.raspberrypi.org/raspbian_latest.torrent) to spare their bandwidth, but note that the [direct download](http://downloads.raspberrypi.org/raspbian_latest) is also available.
When the download is complete, extract it, plug the microSD card into your computer and follow one of the fine guides from the Raspberry Foundation to write the Raspbian image on it:

* [For Linux](http://www.raspberrypi.org/documentation/installation/installing-images/linux.md)
* [For Mac OS](http://www.raspberrypi.org/documentation/installation/installing-images/mac.md)
* [For Windows](http://www.raspberrypi.org/documentation/installation/installing-images/windows.md)


### Install

When your MicroSD card is ready, plug it in your Raspberry Pi and connect the later to your USB keyboard and HDMI display. Then power the Pi with the MicroUSB power adapter.

> It's alive!!!

The install process will take you through all the OS configuration (see [this step-by-step guide](http://www.raspbian.org/RaspbianInstaller) for hand-holding) and then reboot the Pi.

Out of the box, Raspbian uses DHCP to configure its network interfaces, in most networks that should work straight away. If you don't have control over DNS and DHCP though, you might want to assign a static IP to your Pi, the [Debian wiki](https://wiki.debian.org/NetworkConfiguration#Configuring_the_interface_manually) has the information you need to do that.

You should then be able to SSH into the Pi:

```bash
login as: raspbian
raspbian@10.0.0.3's password:
Linux play 3.12.28+ #709 PREEMPT Mon Sep 8 15:28:00 BST 2014 armv6l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Dec 17 19:45:29 2014 from karouf-laptop
raspbian@play ~ $
```

Congrats! You now have a proper server that could fit in your back pocket.


### Keep it up to date

I don't know when has been released the version of Raspbian you just installed but a good habit is to update it with all the latest packages and security fixes.
For that you will first need to have access to the Internet from the Raspberry Pi.
To check if it's the case, issue the following command at the prompt:

`raspbian@play ~ $ wget http://google.com`

If it works, great! Else it should tell you what's wrong with your network connectivity. Troubleshooting this is out of the scope of this article but online resources should be able to help you with that.

In my network for example, I had to set up environment variables so all HTTP, HTTPS and FTP traffic would use my NTLMaps proxy:

`raspbian@play ~ $ sudo vi /etc/profile.d/proxy.sh`

```bash
export http_proxy="http://10.0.0.1:5865"
export https_proxy=$http_proxy
export ftp_proxy=$http_proxy
export no_proxy=localhost
```

And also to configure APT to use my APT caching proxy:

`raspbian@play ~ $ sudo vi /etc/apt/apt.conf.d/80proxy`

```bash
Acquire::http::Proxy "http://10.0.0.1:3142";
```

You should now be able to update APT's packages list:

`raspbian@play ~ $ sudo apt-get update`

It should display a laundry list of things it will update and after a while hopefuly tell you that everything went A-ok.
So far so good! Your system is now ready to have [Play](https://github.com/play/play/tree/v3) installed on it. We will go through all that in the next article though.

See you next time!
