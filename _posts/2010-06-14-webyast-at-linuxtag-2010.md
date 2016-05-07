---
layout: post
title: WebYaST at Linuxtag 2010
category: Misc
tags: [ conference, webyast ]
---

### LinuxTag - June 9-12, 2010

This year I took part of the LinuxTag event in Berlin. It is the biggest Linux
and Open Source event in Germany and probably in Europe.
[See more details here.](http://www.linuxtag.org/2010/en.html)  

{% include image.html
  imgurl="http://3.bp.blogspot.com/_bXo5IecJ3Ag/TCTBLFxcbZI/AAAAAAAAAAU/nKWwg9M1hMI/s1600/linuxtag_gentoo_centos_mandriva.jpg"
  caption="Gentoo, CentOS, Mandriva..." %}

#### Booths

There were many booths at the event, every major distribution like openSUSE,
Gentoo, Ubuntu... had a booth were users could try the latest version and talk
with the developers.

{% include image.html
  imgurl="http://2.bp.blogspot.com/_bXo5IecJ3Ag/TCTFPR2ueOI/AAAAAAAAAAs/y9BpPrTsbUI/s1600/linuxtag_opensuse.jpg"
  caption="... and of course openSUSE!" %}

And you could also meet people from interesting projects like KDE, FFmpeg,
XBMC and so on. I found interesting the demos which showed Linux in embedded
devices or there were even robots running Linux.  

#### Presentations

I hadn't much time to visit the presentations but I visited a presentation about
[Communtu](http://en.communtu.org/), which is a web server where you can build
your customized DVD Ubuntu image. You can add a software bundle which is not
included in Ubuntu by default. So you don't have to search for additional
repositories and install packages from them.  

{% include image.html
  imgurl="http://1.bp.blogspot.com/_bXo5IecJ3Ag/TCTBXHLS_TI/AAAAAAAAAAk/6kyIngavwcM/s1600/linuxtag_embedded_linux.jpg"
  caption="Embedded Linux: here you could see Linux running in various devices" %}

It's somewhat similar to [SUSE Studio](http://susestudio.com/), but with less
features and targeted to Ubuntu users.  

### WebYaST

I had a talk about WebYaST which a new web based remote management for Linux.  
It's a an open source project and it's a successor of YaST.  

The main difference between YaST and WebYaST is that WebYaST is a web
application in contrast to YaST which is a standard desktop application. WebYaST
is accessed via a web browser.  

Another difference is that WebYaST supports access control, so you don't have
to use the root account for managing the remote machine, you can use any regular
user provided the he/she has the access right granted via PolicyKit.  

In WebYaST we try reusing the existing code from YaST if possible, but we want
to have the YaST dependency as small as possible so for example installing
patches is done via PackageKit.  

You can find more details in the slides which I made available online using
slideshare.net service or at
[the main WebYaST wikipage](http://en.opensuse.org/WebYaST).  

I thought that there could have been more attendees in my talk, but the hall was
really big and even relatively big group of people seemed small there. Anyway,
the presentation was successful in my opinion and I hope I spread a word
about WebYaST...  

