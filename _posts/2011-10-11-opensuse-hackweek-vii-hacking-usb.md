---
layout: post
title: OpenSUSE Hackweek VII - Hacking USB Joysticks in YaST
category: Programming
tags: [ yast, joystick ]
---

## Introduction

This [hackweek](http://en.opensuse.org/Portal:Hackweek) I spent playing with
joysticks in [YaST](http://en.opensuse.org/Portal:YaST) and hwinfo (libhd).
YaST already has a module for configuring joysticks, but I only supported
[Gameport](http://en.wikipedia.org/wiki/Gameport) joysticks which are quite
obsoleted these days.  

AFAIK all recent mainboards do not have gamport connectors (just a pin header)
or the gameport is completely missing. And if you want to buy a joystick you
will find only USB models anyway.  

There was a note in the YaST module that it only supports Gameport joysticks but
some users find it confusing.  

So I decided to change the situation and do something interesting during my
Hackweek project - to add USB joystick support to YaST.  

## Hacking

The support in YaST actually has three parts. YaST uses libhd (from
[hwinfo](https://gitorious.org/opensuse/hwinfo) package) for hardware detection,
than there is yast2-hardware-detection package which is a libhd wrapper
converting C functions and data to YCP (the main YaST language) and finally
there is yast2-sound package which contains the joystick configuration module.  

The first step was to add USB joystick support to hwinfo so it could be used for
joystick detection. The problem was that hwinfo found the USB joystick device
but it didn't know that it's a joystick and reported it as an unclassified
generic USB device and also the joystick result was empty:  

```
# hwinfo --usb  
_[...removed not relevant info...]_  
12: USB 00.0: 0000 **Unclassified device**  
[Created at usb.122]  
Unique ID: JPTW.MyFI+3nAFw7  
Parent ID: FKGF.4Nx_qoDfSd7  
SysFS ID: /devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.3/2-1.3:1.0  
SysFS BusID: 2-1.3:1.0  
Hardware Class: **unknown**  
Model: "KYE Flight2000 F-23 Joystick"  
Hotplug: USB  
Vendor: usb 0x0458 "KYE Systems"  
Device: usb 0x1004 "Flight2000 F-23 Joystick"  
Revision: "1.02"  
Driver: "usbhid"  
Driver Modules: "usbhid"  
Device File: /dev/input/event5  
Device Files: /dev/input/event5, /dev/input/by-id/usb-0458_4-axis_8-button_joystick-event-joystick, /dev/input/by-path/pci-0000:00:1d.0-usb-0:1.3:1.0-event-joystick  
Device Number: char 13:69  
Speed: 1.5 Mbps  
Module Alias: "usb:v0458p1004d0102dc00dsc00dp00ic03isc00ip00"  
Config Status: cfg=no, avail=yes, need=no, active=unknown  
Attached to: #8 (Hub)  
# hwinfo --joystick  
#  
```

So the detection code was there, it just needed small improvement. This was
rather easy. Then I checked also `hwinfo --joystick` output, it was fine.  

Then I put an old low-end sound (Sound Blaster 128 PCI) with gameport into my PC
and connected an old analog joystick. The old YaST joystick module worked fine I
and I configured it.  

Then I checked `hwinfo --joystick` output again and the analog joystick was not
found. It turned out that the hwinfo code actually could never detect any
joystick. So I also added Gameport support and fixed Gameport joystick
detection.  

So at this point I had working joystick detection, but I wanted to improve it a
little bit. I wanted detect and report some joystick properties, like number of
buttons and axes it has. This turned out to be similar to mouse button detection
which already worked fine. So again not a big problem.  

The detection part was ready, then I just added  passing joystick details into
the yast2-hardware-detection layer so it could be used from YaST.  

The most complicated part was the YaST joystick module itself because it
supported only gameport joystick and USB joysticks could not be simply added to
the UI and also the internal structure had to be completely rewritten.  

## The Outcome

So what has been changed?  

The `hwinfo --joystick` output will contain all detected joysticks (both USB and
Gameport):  

```
# hwinfo --joystick   
24: USB 00.0: 10d00 Joystick
[Created at usb.122]  
Unique ID: o2Ga.iGyiCvRqXrA  
Parent ID: FKGF.4Nx_qoDfSd7  
SysFS ID: /devices/pci0000:00/0000:00:1d.0/usb2/2-1/2-1.7/2-1.7:1.0  
SysFS BusID: 2-1.7:1.0  
Hardware Class: joystick  
Model: "KYE Flight2000 F-23 Joystick"  
Hotplug: USB  
Vendor: usb 0x0458 "KYE Systems"  
Device: usb 0x1004 "Flight2000 F-23 Joystick"  
Revision: "1.02"  
Driver: "usbhid"  
Driver Modules: "usbhid"  
Device File: /dev/input/event6 (/dev/input/js1)
Device Files: /dev/input/event6, /dev/input/js1, /dev/input/by-id/usb-0458_4-axis_8-button_joystick-event-joystick, /dev/input/by-path/pci-0000:00:1d.0-usb-0:1.7:1.0-event-joystick, /dev/input/by-id/usb-0458_4-axis_8-button_joystick-joystick, /dev/input/by-path/pci-0000:00:1d.0-usb-0:1.7:1.0-joystick  
Device Number: char 13:70  
Speed: 1.5 Mbps  
Module Alias: "usb:v0458p1004d0102dc00dsc00dp00ic03isc00ip00"  
Buttons: 8  
Axes: 6  
Config Status: cfg=no, avail=yes, need=no, active=unknown  
Attached to: #20 (Hub)  

25: Gameport 00.0: 10d00 Joystick
[Created at input.316]  
Unique ID: pJ6W.6RgETWGhHg1  
Parent ID: i_aO.F2uWXyTiEa3  
SysFS ID: /pci0000:00/0000:00:1e.0/0000:04:02.0/gameport0/input/input58  
Hardware Class: joystick
Model: "Analog 4-axis 4-button joystick"  
Vendor: 0x0001   
Device: 0x000f "Analog 4-axis 4-button joystick"  
Device File: /dev/input/event5 (/dev/input/js0)
Device Files: /dev/input/event5, /dev/input/js0, /dev/input/by-path/pci-0000:04:02.0-event-joystick, /dev/input/by-path/pci-0000:04:02.0-joystick  
Device Number: char 13:69  
Buttons: 4  
Axes: 4  
Config Status: cfg=no, avail=yes, need=no, active=unknown  
Attached to: #16 (Multimedia audio controller)
```

It correctly finds the joystick, reports number of axes and buttons,
`/dev/input/jsX` device name is reported, game port joystick is found and for
game port joysticks it reports also the sound card to which it is attached to.  

The changes in the YaST module are best described by the following screen shots.
Let's start with the old YaST module:  

{% include image.html
  imgurl="http://3.bp.blogspot.com/-PfM1ymwGhKE/TpQDao-sakI/AAAAAAAAABY/H_uumPY-62I/s200/YaST_joystick_old1.png"
  caption="The old YaST module with one configured Gameport joystick." %}

{% include image.html
  imgurl="http://2.bp.blogspot.com/-5-0_QCFkskA/TpQDbTdSvbI/AAAAAAAAABo/LtvYWykRecs/s200/YaST_joystick_old3.png"
  caption="When the computer doesn't have any game port the old module displayed just this message although an USB joystick could be attached." %}

{% include image.html
  imgurl="http://3.bp.blogspot.com/-h0CcCPHJ3xY/TpQDax7VqJI/AAAAAAAAABg/8YP_VE3_ppk/s200/YaST_joystick_old2.png"
  caption="Old joystick test dialog, notice the wrong number of buttons and axes, also
using progress bars is not appropriate for this dialog." %}

Here is the new updated YaST module:  

{% include image.html
  imgurl="http://3.bp.blogspot.com/-PZgqn39NTF4/TpQDy1lUbgI/AAAAAAAAABw/EadgYXD7-xs/s200/YaST_joystick_new3.png"
  caption="Both USB and Gameport joysticks are displayed with more details" %}

  {% include image.html
    imgurl="http://2.bp.blogspot.com/-VPzNx9LMU0k/TpQDaA9F9AI/AAAAAAAAABQ/k-jrKbPU7DQ/s200/YaST_joystick_new2.png"
    caption="The testing dialog has been improved - button and axes detection is correct,
sliders are used instead of progress bars and also the joystick name is
displayed (usefull when there are more joysticks attached)." %}

This is quite a nice change, isn't it?  

As a bonus I have added hotplug handling into the joystick module - if you plug
or unplug any USB joystick the table get refreshed. AFAIK that is unique feature
among all YaST modules...  

All these changes have been submitted to Factory/12.1 and should be available
in 12.1-RC1 when released.