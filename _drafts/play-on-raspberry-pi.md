---
layout: post
title:  "Play on the Raspberry Pi"
date:   2014-12-10 11:56:36
categories: jekyll update
---

## Intro

Rewrite
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
### OS image
### Installation
### Update
## Music library
### Free for all
### Using quotas
## play
### Requirements
### Bootstrap
### Tests
## Automate everything
### Start play at boot
### Update library

---

# Install Raspbian OS

Version?

Plug the microSD card into your computer and follow one of the fine guides from the Raspberry Foundation to write the Raspbian image on it:

* [Linux](http://www.raspberrypi.org/documentation/installation/installing-images/linux.md)
* [Mac OS](http://www.raspberrypi.org/documentation/installation/installing-images/mac.md)
* [Windows](http://www.raspberrypi.org/documentation/installation/installing-images/windows.md)

Then plug the microSD card in your Raspberry Pi and connect the later to your USB keyboard and you HDMI display. Then power the Pi with the microUSB power adapter.
You should now be screaming "It's alive!!!".
The install process will take you through all the OS configuration (see [this step-by-step guide]() for hand-holding) and then reboot the Pi so it's yours to play with.

Out of the box, Raspbian uses DHCP to configure its network interfaces, in most networks that should work straight away. If you don't have control over DNS and DHCP though, you might want to assign a static IP to your Pi, the [Debian wiki](https://wiki.debian.org/NetworkConfiguration#Configuring_the_interface_manually) has the information you need.

You should now be able to SSH into your server.

Congrats! You got yourself a proper server that could fit in your back pocket.

# Getting Play on

First you will need to have a working Internet connection from the Raspberry Pi to install some Debian packages and get Play from Github.
To check whether the Pi can acces the Internet, issue the following command at the prompt:

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

Next we need to install Git in order to get the **play** code from GitHub:

`pi@play ~ $ sudo apt-get install git`


**play** being a Ruby on Rails app, we need Ruby installed to run it. Here we'll go with RVM so it's easier to switch the Ruby version when we need to.
As per the [RVM guide](https://rvm.io/rvm/install), get the GPG keys first:

`pi@play ~ $ gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3`

And install RVM:

`pi@play ~ $ \curl -sSL https://get.rvm.io | bash -s stable --ruby`

It will take a while as it is compiling the latest Ruby from source.
When it's finished, source the RVM env script:

`pi@play ~ $ source /home/pi/.rvm/scripts/rvm`


/!\ create the music directory

**play** has a bootstrap script that will set up everything but it's still a bit rough and I had to fix a lot of things while running it to get everything working. Instead of making you go through the same hoops, we'll set up everything that's needed before bootstrapping **play**.
First we'll install the dependencies:

`pi@play ~/play $ sudo apt-get install mysql-server libmysqlclient-dev mysql-client libtag1-dev mpd mpc nodejs libavahi-compat-libdnssd-dev zip`

I went ahead and integrated most of the fixes to the `raspberry-pi` branch of my **play** fork so you can just git clone it and reap the rewards of my efforts!

`pi@play ~ $ git clone --single-branch https://github.com/karouf/play --branch raspberry-pi play && cd play`

RVM should tell you which Ruby version to install, just do what it tells you:

`pi@play ~ $ rvm install ruby-2.0.0-p195`

Again it will take about an hour to compile the new Ruby version, so you can take a book and have a coffee.
When it's done, force RVM switch to the right Ruby version:

`pi@play ~ $ cd ../play`

And verify it's using the right one:

`pi@play ~/play $ rvm info`

run the bootstrap script


```bash
pi@play ~/play $ MYSQL_USER=root MYSQL_PASSWORD=yourmysqlpassword BOXEN_MYSQL_SOCKET=/var/run/mysqld/mysqld.sock script/bootstrap

  [ ? ] Are you going to use Play within a GitHub organization? (y,n) › n

    You need to set up an OAuth application on GitHub. Go here:

    https://github.com/settings/applications/new

    and set up your application. Unless you're using a different
    hostname, you'll want to use values that look like:

      Main URL:     http://localhost:3030
      Callback URL: http://localhost:3030/auth/github/callback
```
Go [create a new OAuth application](https://github.com/settings/applications/new) in your GitHub account so **play** can use it for authentication:

![Create a new GitHub Oauth application](/images/create-new-github-oauth-app-for-play.png)

You should be able to see the details of your new application on the next page, including the client ID and secret you need to configure **play**:

![GitHub Oauth application details](/images/github-oauth-app-for-play-details.png)
Blur the ID and secret and highlight this section. Change the name in the top left to not be capitalized

Good because the bootstrap process is asking for them:

```bash
  [ ? ] What is your GitHub client ID? › xxxxxxxxxxxxxxxxxxxx
  [ ? ] What is your GitHub client secret? › xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Then you will be asked for the absolute path of your music directory:

```bash
  [ ok ] play config loaded
  [ ? ] What is the full, absolute path to your music? › /home/pi/music
```

And the bootstrap script will do all the configuration for you:

```bash
  [ ok ] mpd config loaded
  [ ok ] mpd directories
  [ ok ] ruby installed
  [ ok ] bundler
  [ ok ] mysql-server
  [ ok ] libmysqlclient-dev
  [ ok ] mysql-client
  [ ok ] libtag1-dev
  [ ok ] mpd
  [ ok ] nodejs
  [ ok ] gems
  [ ok ] mysql database ready
  [ ok ] data migrated
  [ ok ] assets compiled
  [ ok ] music server up
  [ ok ] album art cached

  You're all set!
```

Run the tests to check if everything is OK:

pi@saas-play ~/play $ bundle exec rake test

Add `env: production` to the `config/play.yml` file so it defaults to the `production` environment.

Stop the MPD daemon that has automatically been started, play will later start it with its own config:

`pi@play ~/play $ sudo service mpd stop`

And start the **play** MPD daemon:
First the MPD music streaming server:

```bash
pi@play ~/play $ script/music start
pi@play ~/play $ ps -fu pi
UID        PID  PPID  C STIME TTY          TIME CMD
pi       14792     1  0 10:30 ?        00:00:00 mpd config/mpd.conf
pi       14805 32505  0 10:31 pts/1    00:00:00 ps -fu pi
pi       32504 32470  0 déc.12 ?      00:00:44 sshd: pi@pts/1
pi       32505 32504  0 déc.12 pts/1  00:00:18 -bash
```

MPD is running and using the **play** config instead of the default one in /etc.

Create the production DB:

pi@play ~/play $ bundle exec rake db:migrate RAILS_ENV=production

Put at least 2 music files in your music library directory.

Compile the static assets:

`pi@play ~/play $ bundle exec rake assets:precompile RAILS_ENV=production`

Cache the albums covers:

`pi@play ~/play $ script/cache-art RAILS_ENV=production`

start play

`pi@play ~/play $ script/play start RAILS_ENV=production`

check all the processes

have a look at the web interface

add all your files to the music library

update music library
pi@play ~/play $ mpc update

run mpc with a cron to update regularly the music library

Init scripts to run **play** when the Raspberry Pi starts up.
