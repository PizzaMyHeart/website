---
title: 'Late to the Focal Fossa Party'
description: Updating to Ubuntu 20.04 LTS.
date: 2021-01-03
tags: ['computing', 'linux']
layout: layouts/post.njk
---

Eight months after Ubuntu 20.04 LTS was officially released, and five months after Ubuntu 19.10 reached end-of-life, I finally upgraded. That should have been plenty of time to iron out the bugs right? Not quite, as it turns out, though I'm not sure how much of them are release-related and how much is just due to me tinkering with things beyond my understanding. Here are some bugs(?) I experienced on my ThinkPad x250 running Lubuntu. 

*Note: I switched from Windows to Linux around a year ago, in case the following doesn't make it clear that I'm a total n00b.*

## VLC crashes when opening video files

VLC started up fine, but whenever I tried to load a video it would freeze and crash. Sometimes it just froze and locked up my system entirely. I fixed this by going to Tools > Preferences > Video and changing the 'Output' from 'Automatic' to 'X11 Video Output'.

## Steam crashes on launch 

Steam kept crashing just when the login screen would normally show up. I found [this Ask Ubuntu thread](https://askubuntu.com/questions/1230499/steam-not-working-on-ubuntu-20-04) about missing 32-bit libraries, but all the necessary 32-bit libraries were already installed on my system. I also had no issues installing Steam from the official repo. 

I was fairly sure it was a graphics driver issue after finding [this thread](https://ubuntuforums.org/showthread.php?t=2443218). My x250 only has Intel HD 5500 integrated graphics, so I didn't have to worry about Nvidia or AMD drivers. 

*Note: Launching Steam from the terminal with the flag `STEAM_RUNTIME=0` returned an error related to the Iris driver. I can't remember the actual error message and I'm kicking myself for not taking a note of it at the time. But here's what I did.*

I tried the following but they didn't help:

- Running Steam with i965 drivers: `env MESA_LOADER_DRIVER_OVERRIDE=i965 steam` ([Launchpad](https://bugs.launchpad.net/ubuntu/+source/xserver-xorg-video-intel/+bug/1876219))
- Adding DRI 3 as an option in my Intel Xorg config ([GitHub issue](https://github.com/ValveSoftware/steam-for-linux/issues/7067#issuecomment-619363489))




This was my Intel Xorg config in `/etc/X11/xorg.conf.d`:

	Section "OutputClass"
  		Identifier "Intel Graphics"
  		MatchDriver "i915"
  		Driver "intel"
  		Option "TearFree" "true"
	EndSection
	
I changed `"i915` to `"i965"` and it worked: Steam now launches and I can play all my games as before. Yay!

Not so fast. Turns out I'm actually still using i915 driver instead of the i965 one:

	$ lspci -k | grep -EA3 'VGA|3D|Display'
	00:02.0 VGA compatible controller: Intel Corporation HD Graphics 5500 (rev 09)
        Subsystem: Lenovo HD Graphics 5500
        Kernel driver in use: i915
        Kernel modules: i915

Something in my original Intel config file must have been causing issues, and I've somehow managed to get around them by avoiding the use of the config file entirely (effectively the same as deleting it as mentioned [here](https://steamcommunity.com/groups/SteamClientBeta/discussions/0/2259061617884071771/)). So, problem *not* solved. But I'm reluctant to mess around now that everything seems to be working fine, and I don't want to break my system again.

On further reading, this seems related to my VLC issues as outlined in [this thread](https://askubuntu.com/questions/1254544/vlc-crashes-when-opening-any-file-ubuntu-20-04). There's also something called `modesetting` that I should read up on.

 

## Calibre crashes on launch

Calibre installed fine from the official repo but on starting it would crash with the error message `AttributeError: 'NoneType' object has no attribute 'cancel'.`

This bug was fixed in later releases, but unfortunately the Focal Fossa package for Calibre is [stuck at 4.99.4](https://launchpad.net/ubuntu/+source/calibre). I should probably switch to a PPA instead but for now [this](https://github.com/kovidgoyal/calibre/commit/7b6416ac6522fc40f24f6baf3ca552b17a8b91d6) is a quick and dirty workaround:

- Open `/usr/lib/calibre/calibre/utils/ipc/server.py`
- Edit line 110 as shown in the link above

## xrandr and dual monitors

I'm almost always connected to a second monitor when I use my x250. Before I updated to Focal Fossa, this line in my i3 config dealt with my dual monitor setup upon starting a session from lightdm:

	exec xrandr --output eDP1 --primary --left-of HDMI1

After updating, my second monitor went into mirrored mode on startup. Entering the xrandr command above in a terminal returned `warning: output eDP1 not found; ignoring`.

This took me longer than it should have, but changing `eDP1` to `eDP-1` and `HDMI1` to `HDMI-1` solved the problem. [This thread](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=208647) makes me think it's another quirk to do with the video drivers. 

## Conclusion

Drivers, am I right? Ugh. What are drivers even. Actually, what *are* drivers? I don't know much beyond the fact that they allow software to communicate with hardware. Time to do some reading ...

Day 28 of [#100DaysToOffload](https://100daystooffload.com/)