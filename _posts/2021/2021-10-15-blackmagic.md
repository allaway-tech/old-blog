---
layout: post
title: Controlling Blackmagic Design Smart Videohubs from QLab
date: 2021-10-15
redirect_from:
  - /qlab/controlling-blackmagic-design-smart-videohubs-from-qlab/
categories: [qlab, blackmagic]
tags: [qlab,blackmagic,python,applescript]
---

Controlling Blackmagic Design Smart Videohubs from QLab is always a little bit difficult. There are network cues built into QLab but the required TCP cue does not exist. However, it is fairly simple to create a python script to give us this functionality and call that from within an

Back in the summer of 2021, I was working on a small show that used eight cameras recording into eight Blackmagic Design HyperDecks. As part of this system, there was a [Smart VideoHub](https://www.blackmagicdesign.com/products/smartvideohub). A way to control all of this equipment was missed off the list. So I ended up writing a set of scripts to allow control Blackmagic Design video equipment from QLab. I have planned to clean up the scripts and make them available to the public. But this project is currently sat on the back burner. I have released this script as there is a user on one of the groups who has a similar problem to myself and I feel that this might help them.

This script is intended to allow for the control of a single unit over TCP/IP. And was created to allow control from the show control software QLab. However, it can also be run manually, where it should be cross-platform compatible.

This script is written in python V2, as this is the default version that ships with macOS. If you have a different version of python running on your mac or PC I believe it will still work, however, I haven’t had the chance to test it.

Input and output numbers are normalized by the script to allow for human numbering to be used.

## Usage for controlling VideoHub from QLab
Downloading the script from [GitHub](https://github.com/harmanhobbit/smarthubctl) and save it as smarthubctl.py

All methods of use require the placeholder to be changed for the IP address of the Smart Videohub unit. For example, the line 

![test](/media/posts/images/ip_address.png)

should read something like `address = "192.168.1.100"`.

Using the script from the command line called in the following way: `python smarthubctl.py *input* *output*`

To run the script from QLab. Call the python script from an AppleScript cue like so: `'do shell script "python /path/to/script/smarthubctl.py *input* *output*"'`

This script was intended to be a simple way of controlling Blackmagic Design Smart Videohubs from QLab. Hopefully, you will find it useful.
