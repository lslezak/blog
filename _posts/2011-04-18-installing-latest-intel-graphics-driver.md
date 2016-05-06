---
layout: post
title: Installing latest Intel graphics driver to openSUSE 11.4
category: Linux
tags: [ drivers ]
---

**Edit: The bug has been fixed in openSUSE update repository, you do not need to
compile the driver manually anymore, just install the latest update for
`xorg-x11-driver-video` package.)**

I have upgraded my home PC to Intel i5-2500K CPU (Sandy Bridge family). The CPU
has a new integrated graphics core (Intel HD 3000) and it works out-of-box in
openSUSE-11.4 including 3D and composition. The only problem I noticed are
broken popup menus and buttons in title bars in KDE. Esp. broken popups are very
annoying as they are hardly usable, see e.g.
[https://bugs.freedesktop.org/attachment.cgi?id=45061](https://bugs.freedesktop.org/attachment.cgi?id=45061).

Fortunately Intel has released updated X driver version 2.15 which fixes this
problem. Here is a step by step how to install the updated driver in openSUSE 11.4.

1.  Install xorg-x11-server-sdk package `sudo zypper in xorg-x11-server-sdk`

2.  Download [http://xorg.freedesktop.org/archive/individual/driver/xf86-video-intel-2.15.0.tar.bz2](http://xorg.freedesktop.org/archive/individual/driver/xf86-video-intel-2.15.0.tar.bz2)

3.  Unpack the archive `tar xfjv xf86-video-intel-2.15.0.tar.bz2`

4.  Now compile the driver:
    ```shell
    cd xf86-video-intel-2.15.0
    ./configure --prefix=/usr --libdir=/usr/lib64
    make
    ```
    (If you have installed 32-bit system then use `/usr/lib` path in the second
    command.)
5.  Install the driver (will overwrite the files from RPM package)
    `sudo make install`
6.  Restart the X server (simply relogin to a new session)

Voila, now your system should use the new driver and the artifacts in the KDE
popups should be gone!

Maybe someone can pack the driver into a RPM package in the openSUSE build
service, but for me this solution is sufficient...