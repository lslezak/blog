---
layout: post
title: "Dual head? Dual Seat! Two Users at One Computer at the Same Time!"
category: Linux
tags: [ openSUSE, SUSE, Linux, Multi-seat ]
description: How to share your home computer with 2 monitors, 2 keyboards and 2 mice.
---

## Dual Head or Multi Monitor

If you have two monitors attached to your computer then the setup is called
dual head, the generic term for any number of monitors is [multi-monitor](
https://en.wikipedia.org/wiki/Multi-monitor).

This setup useful if a single monitor is not enough for you to see all needed
windows at once. But in this setup both monitors can be used only one person at the
same time.

## Dual Seat or Multi Seat

If you have two monitors what about attaching one more keyboard and mouse and
"split" the computer and have independent sessions for each user? That
setup is called dual seat or [multi
seat](https://en.wikipedia.org/wiki/Multiseat_configuration) in general.

Linux is a multi user systems from the very beginning, but normally
these users either work remotely or they simply share one seat and need
to cooperate who will use the computer when.


## Hardware

For this multi seat solution you need a separate graphics adapter for each seat.
Fortunately to save some money you can combine discrete graphics cards with
an integrated one.

If you use an integrated card you might need to enable the multi graphics support in BIOS
because usually when a discrete graphics card is found the integrated one
is automatically disabled.

{% include blog_img.md alt="BIOS settings" src="bios_gpu_settings.jpg" %}

:information_source: *This option is vendor dependant, check your mainboard manual.*

## Linux

I wanted to configure a multi seat in the past, but it was really
complicated. I would have to tweak the X.org config manually and there were
lots of hacks to make it work.

But actually it turned out that using a modern Linux distribution like
openSUSE Leap 15.0 makes this very easy!


## Using the *loginctl* Tool

As in almost all modern Linux distributions also in the openSUSE Leap 15.0
the console is managed by the systemd login manager. To interact with it from
the command line you can use a tool called
[*loginctl*](https://www.freedesktop.org/software/systemd/man/loginctl.html).

It can handle sessions, seats and users. Let's see which seats are defined
by default:

```console
# loginctl list-seats
SEAT            
seat0           

1 seats listed.
```

Now we can list all hardware devices assigned to the default seat:

```console
# loginctl seat-status seat0 
seat0
	Sessions: *2
	 Devices:
		  ├─/sys/devices/LNXSYSTM:00/LNXPWRBN:00/input/input5
		  │ input:input5 "Power Button"
		  ├─/sys/device…LNXSYSTM:00/LNXSYBUS:00/PNP0C0C:00/input/input4
		  │ input:input4 "Power Button"
		  ├─/sys/devices/pci0000:00/0000:00:01.3/0000:02:00.0/usb1
		  │ usb:usb1
		  ├─/sys/devices/pci0000:00/0000:00:01.3/0000:02:00.0/usb2
		  │ usb:usb2
		  ├─/sys/device…2:00.1/ata2/host1/target1:0:0/1:0:0:0/block/sr0
		  │ block:sr0
		  ├─/sys/device…ata2/host1/target1:0:0/1:0:0:0/scsi_generic/sg1
		  │ scsi_generic:sg1
		  ├─/sys/device…1.3/0000:02:00.2/0000:03:04.0/0000:05:00.0/usb4
		  │ usb:usb4
		  ├─/sys/devices/pci0000:00/0000:00:03.1/0000:09:00.0/drm/card0
		  │ [MASTER] drm:card0
		  │ ├─/sys/device…000:00:03.1/0000:09:00.0/drm/card0/card0-DP-1
		  │ │ [MASTER] drm:card0-DP-1
		  │ ├─/sys/device…:00:03.1/0000:09:00.0/drm/card0/card0-DVI-D-1
		  │ │ [MASTER] drm:card0-DVI-D-1
		  │ └─/sys/device…00:03.1/0000:09:00.0/drm/card0/card0-HDMI-A-1
		  │   [MASTER] drm:card0-HDMI-A-1
		  ├─/sys/device…000:00/0000:00:03.1/0000:09:00.0/drm/renderD128
		  │ drm:renderD128
		  ├─/sys/device…i0000:00/0000:00:03.1/0000:09:00.0/graphics/fb0
		  │ [MASTER] graphics:fb0 "amdgpudrmfb"
...
```

The list will be very likely long as it contains all devices present in the system.
Obviously all devices are currently assigned to this single seat.


## Creating a New Seat

Each seat needs a master (main) device. In the list you should see the graphics card
devices marked with the `[MASTER]` tag.

### Adding Output Device

Each graphics card should have two devices, `drm:card<number>` and `graphics:fb<number>`.
To create a new seat simply move these two master devices to the new seat. Use the
`loginctl attach seat <path>` command, copy and paste the full device path from the
previous list command:

```console
# loginctl attach seat1 /sys/devices/pci0000:00/0000:00:03.2/0000:0a:00.0/graphics/fb1
# loginctl attach seat1 /sys/devices/pci0000:00/0000:00:03.2/0000:0a:00.0/drm/renderD129
```

You can check that the new seat is defined properly and contains the specified devices:

```console
# loginctl seat-status seat1
seat1
        Sessions: *1
         Devices:
                  ├─/sys/devices/pci0000:00/0000:00:03.2/0000:0a:00.0/drm/card1
                  │ [MASTER] drm:card1
                  │ ├─/sys/device…000:00:03.2/0000:0a:00.0/drm/card1/card1-DP-2
                  │ │ [MASTER] drm:card1-DP-2
                  │ ├─/sys/device…:00:03.2/0000:0a:00.0/drm/card1/card1-DVI-D-2
                  │ │ [MASTER] drm:card1-DVI-D-2
                  │ └─/sys/device…00:03.2/0000:0a:00.0/drm/card1/card1-HDMI-A-2
                  │   [MASTER] drm:card1-HDMI-A-2
                  ├─/sys/device…000:00/0000:00:03.2/0000:0a:00.0/drm/renderD129
                  │ drm:renderD129
                  └─/sys/device…i0000:00/0000:00:03.2/0000:0a:00.0/graphics/fb1
                    [MASTER] graphics:fb1 "amdgpudrmfb"
```

Now I'd suggest rebooting the system. After reboot you should see the login screen on the
both monitors. (BTW I'm using the default KDE SDDM login manager, I do not know if the other
login managers support multi seat...)


### Adding Input Devices

Now we need to assign also the input devices - a keyboard and a mouse. Find the keyboard
and the mouse devices in the `loginctl seat-status seat0` output and move them to the
other seat with the `loginctl attach seat1 <path>` command.

It is easy if you use different keyboard and mouse models for each seat. If you use
the same models then you cannot easily distinguish between the devices. In that case
I suggest using the simple trial-and-error approach, just move a device to the other
seat and test if it is routed to the correct monitor. If not then simply move it back to
`seat0` and move the other device to `seat1`. You do not need to reboot, you can
test it immediately.

:information_source: *Multimedia keyboards might have several devices, do not forget to move them all.*

### And That's it!

And that's it! Now monitor should behave as an independent login screen with separate
mouse and keyboard devices. Enjoy! :smile:


## The Data Persistence

The configuration is persistent, the attached devices are remembered and
automatically set after reboot. You do not need to reassign the devices after
each reboot.

## The Drawback

There is one drawback of enabling the multi seat feature - the multi head
setup does not work anymore.

To make it work back you would have to reconnect the monitors back to the same card
and move the assigned devices back to the original seat. That's quite annoying
but you could possibly automate the monitor connection by an automatic HDMI
switch if you use HDMI (or DVI) for connecting the monitors and some scripting...

## The Use Case: Minecraft! :video_game:

With this approach you can avoid arguing who will play Minecraft. Since now
you can run two Minecraft instances in separate sessions at once! :tada:

{% include blog_img.md alt="Two Minecraft instances at one computer!"
src="minecraft_dualseat_small.jpg" %}

*It's not obvious from the picture, but there is only one computer below the table!*
