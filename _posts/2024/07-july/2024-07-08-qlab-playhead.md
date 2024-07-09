---
layout: post
title: Raspberry Pi Zero QLab playhead
date: 2024-07-08
categories: [QLab, Raspberry Pi] # Can be anything
tags: [qlab,raspberrypi,usbgadet,dhcp,python,osc] # Must be lowercase
img_path: /media/posts/images/2023-08-05-tp-link-deco-dhcp
---

# A Raspberry Pi Zero play head for QLab
This is a little project that I have had on the back burner for a little while. I recently had a little more time in my schedule and managed to get to a working state.

### What is it?
It is a Raspberry Pi Zero with a TM1637 screen on it which shows us the cue number that is currently standing by on a QLab session running on a USB connected Apple mac.

### Why would I want this?
I work professionally in theatre on a (slightly off) West End show. We use a piece of software called QLab to playback and control the hardware for sound and lighting. However, there are times when the software can fail. Because of this it is very common to have a change over device within the system that allows us to switch all of the sound and control from a main to a backup mac. Sometimes the two mac can get out of sync and you have no way of knowing without toggling between the two. An easy solution would be to put a second screen on the backup mac but the control positions are often quite small or located within the audience where the excess light is undesirable. There are a few software based solutions out there if you know where to look but this again relies on there being another computer with a screen that you don't mind running an app on.

### Why is you solution better?
The project started when one of my old Sound No. 2s asked me if it was possible to run the software solution (yes the show I work on has the space computer situation) on a Raspberry Pi with a screen. After thinking about it for a little while I pointed out that the app that we use was mac only app so no it wouldn't. But after thinking a little longer on it I realised that actually a Rasperry Pi Zero was a perfect candidate to make this a hardware device.

### Why is the Raspberry Pi Zero perfect?
Unlike other Raspberry Pis (when I started thinking about this) the Pi Zero can be used in what's called gadget mode. This means that it can imitate other types of device over its USB port. This means that if connected to another computer it will be seen as an input device (mouse or keyboard), a MIDI or an ethernet device. Why is this useful? Well we can get the Pi to imitate a ethernet device and then (with some DHCP wizardry) communicate with QLab using OSC commands.

## How do we start
To make this project you will need:
  - 1 x Raspberry Pi Zero W
  - 1 x 8GB or higher SD card
  - 1 x 6 digit TM1637 display
  - 1 x Data micro USB cable
  - Some wire and a soldering iron or any other method of attaching the display to the Pi

The first place to start is by installing your Raspberry Pi. For the sake of my sanity right now I am not going to write a Raspberry Pi getting started guide. This may change in the future if I get extremely bored. Here is the [official documentation](https://www.raspberrypi.com/documentation/computers/getting-started.html) from Raspberry Pi themselves. I would, however, recommend combining [this tutorial](https://www.circuitbasics.com/raspberry-pi-zero-ethernet-gadget/) with the newer install method recommended by Raspberry Pi. For this project I picked the Raspberry Pi OS Lite option as I was looking for an easy life. Pretty much any of the OSs should work for this project but installation methods may vary. The three main reasons I went with vanilla Raspberry Pi OS Lite is that:
  1. Given it is made but the creators of the hardware you would hope it would be most compatible
  2. I didn't want an OS with a GUI as this is intended to be a screen-less device so why use the resources?
  3. I am more familiar with Debian based varieties of Linux.
The only things I would note is that configuring your Pi with the correct Wi-Fi and any SSH keys you may have in the Raspberry Pi imager software is much easier than trying to do it later.

### Gadget mode
After that we need to get the Pi into gadget mode. Again for my sanity I'm going to suggest following the [second](https://www.circuitbasics.com/raspberry-pi-zero-ethernet-gadget/) of the two install tutorials above. Once you have successfully gotten the Pi into gadget mode we will give the USB interface a static IP address.

Edit the file `/etc/network/interfaces` in your favourite text editor and add the following to the bottom of your file:
``` allow-hotplug usb0
iface usb0 inet static
        address 192.168.123.1
        netmask 255.255.255.252
        network 192.168.123.0
        broadcast 192.168.123.3
        gateway 0.0.0.0
```

The entire file will now probably look something like this:
``` # interfaces(5) file used by ifup(8) and ifdown(8)
# Include files from /etc/network/interfaces.d:
source /etc/network/interfaces.d/*

allow-hotplug usb0
iface usb0 inet static
        address 192.168.123.1
        netmask 255.255.255.252
        network 192.168.123.0
        broadcast 192.168.123.3
        gateway 0.0.0.0
```
This bit of configuration tells the interface to wait until it has detected that it has been plugged in and sets an IP address of 192.168.123.1/32 (a /32 network only has 2 assignable IP addresses in it. 1 for the Pi and 1 for the QLab mac). This IP address could be anything that takes your fancy. I have made an assumption that your USB gadget has been assigned on USB0 and will continue to make this assumption as if you have managed to get it to something else you probably know how to make sure it works.

Next we move on to configuring the DHCP server. Check that it is installed by running `sudo apt install dnsmasq`. Now in your favourite text editor open `/etc/dnsmasq.conf` and add the following lines at the bottom of your file:
``` interface=usb0
bind-dynamic
domain-needed
bogus-priv
dhcp-range=192.168.123.2,192.168.123.2,255.255.255.252,120
```
The only part that we are really interested in is the last line. This sets the start and end of the DHCP pool to 192.168.123.2 (we only want 1 address for the QLab mac). The subnet mask to 255.255.255.252 (a /32 network). And the lease time in seconds.

A note on the lease time. As you can see from the code snippet this is set to 120s or 2 minutes. This unfortunately is the shortest duration that you can set. This does mean that you will need to wait for at least two minutes if you are trying to swap the playhead to another mac.

For the most part this should be the majority of the setup we need to do on the pi. Next we look at attaching the display.

### Attaching the display
When I originally started this project I bought a 4 digit display from eBay. It worked great but I found out after jumping in that the cheaper displays only do either a : between the second and third digits or an . between every digit. Typically I got the former that I didn't want. However, it was very easy to swap out the 4 digit display for the 6 digit version. I picked a blue display as the seller I purchased from had them in stock and I was impatient and didn't want to wait. There's very little to these displays so as long as they have the TM1637 chip you should be good to go.

I decided that
