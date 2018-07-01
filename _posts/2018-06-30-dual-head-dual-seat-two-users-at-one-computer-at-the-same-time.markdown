---
layout: post
title: "Dual head? Dual seat! Two users at one computer at the same time!"
category: Linux
tags: [ openSUSE, SUSE, Linux, Multi-seat ]
description: How to better use your computer
---

## Dual Head, Multi-monitor

If you have two monitors attached to your computer then the setup is called
dual head, the generic term for any number of monitors is [multi-monitor](
https://en.wikipedia.org/wiki/Multi-monitor).

This setup useful if a single monitor is not enough to show all needed
windows at one. I use this setup for development where on the primary
screen I have an editor or IDE running and on the second I have IRC chat,
documentation, running virtual machines, etc... so I do not need to switch
windows to see them.

But in this setup both monitors can used only one person at the same time.

## Dual Seat or Multi Seat

If you have two monitors what about attaching one more keyboard and mouse and
"split" the computer in half and have independent sessions for each user? That
setup is called dual seat or [multi
seat](https://en.wikipedia.org/wiki/Multiseat_configuration) in general.

Linux is a multi user systems from the very beginning. But normally
these users either work remotely or they simply share one seat and need
to cooperate who will use the computer when.


## Hardware

For this multi seat solution you need a separate graphics adapter for each seat.
Fortunately to to save some money you can combine discrete graphics cards with
an integrated one. In my case I used an integrated Intel graphics adapter and
a discrete AMD Radeon card.

If you use an integrated card you might need to enable the multi graphics support in BIOS
because usually when a discrete graphics card is found the integrated one
is disabled. In my case I had to enable the *Multi-Monitor* option in the BIOS:

{% include blog_img.md alt="BIOS settings" src="bios_gpu_settings.jpg" %}


## Linux

I wanted to configure a multi seat already in the past, but it was really
complicated. I would have to tweak the X.org config manually and there were
lots of hacks.

But actually it turned out that using a modern Linux distribution like
openSUSE Leap 15.0 makes this very easy!


## Using the *loginctl* Tool

As in almost all modern Linux distributions also in the openSUSE Leap 15.0
the console is managed by the systemd login manager. To interact with it from
the command line you can use a tool called
[*loginctl*](https://www.freedesktop.org/software/systemd/man/loginctl.html).

It can handle the sessions, seats and users. Let's see which seats are defined
by default:

```console
# loginctl list-seats
SEAT            
seat0           

1 seats listed.
```

Now we can list all hardware devices assigned to that seat:

```console
# loginctl seat-status seat0 
seat0
        Sessions: *5
         Devices:
                  ├─/sys/devices/LNXSYSTM:00/LNXPWRBN:00/input/input1
                  │ input:input1 "Power Button"
                  ├─/sys/devices/LNXSYSTM:00/LNXSYBUS:00/PNP0A08:00/LNXVIDEO:00/input/input2
                  │ input:input2 "Video Bus"
                  ├─/sys/devices/LNXSYSTM:00/LNXSYBUS:00/PNP0C0C:00/input/input0
                  │ input:input0 "Power Button"
                  ├─/sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0/drm/card0
                  │ [MASTER] drm:card0
                  │ ├─/sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0/drm/card0/card0-DP-1
                  │ │ [MASTER] drm:card0-DP-1
                  │ ├─/sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0/drm/card0/card0-DVI-D-1
                  │ │ [MASTER] drm:card0-DVI-D-1
                  │ └─/sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0/drm/card0/card0-HDMI-A-3
                  │   [MASTER] drm:card0-HDMI-A-3
                  ├─/sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0/drm/renderD128
                  │ drm:renderD128
                  ├─/sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0/graphics/fb0
                  │ [MASTER] graphics:fb0 "amdgpudrmfb"
...
```

Obviously all are devices are assigned to the only seat defined in the system.

## Creating a New Seat



## Persistence

The configuration is persistent, the attached devices are remembered and
automatically set after reboot. You do not need to assign the devices after
each reboot.

## Drawback

There is one drawback of enabling the multi seat feature - the multi head
setup does not work anymore. :worried:

To make it work back you would have to reconnect the monitors back to the same card
and move the assigned devices back to the original seat. That's quite annoying
but you could possibly automate the monitor connection by and automatic HDMI
switch if you use HDMI (or DVI) for connecting the monitors.

## The Use Case: Minecraft! :smiley:

To avoid arguing who will play Minecraft right now you can simply enable
running two Minecrafts in separate sessions!

{% include blog_img.md alt="Two Minecrafts at one computer!"
src="minecraft_dualseat_small.jpg" %}

It's not obvious from the picture, but there is only one computer below the table!
