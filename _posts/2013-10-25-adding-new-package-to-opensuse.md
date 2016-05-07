---
layout: post
title: Adding a new package to the inst-sys (openSUSE installation system) or to the
  rescue system
category: Programming
tags: [ openSUSE ]
---

# Adding a New Package to the inst-sys

## Why?

Sometimes you need to add a new RPM to the openSUSE installation system (called
_inst-sys_) or to the rescue image. Especially in Yast development we usually
need to include new tools or some new subsystems (like the recent Yast switch to
Ruby required adding Ruby interpreter into the inst-sys).  

## How?

The overall procedure is quite simple, basically you need to modify
`installation-images` package and then remaster the installation medium (or
update the boot server, depending how you boot the system). But there are some
tricky parts...  

## Installation-images

This RPM builds file system images (using compressed
[squashfs](http://squashfs/) to save valuable space) with installation system
and the rescue system.  

If you want to add a new package then follow these steps:  

1. Checkout the `installation-images` package for your target distribution from
   [OBS](https://build.opensuse.org/), e.g. `osc co openSUSE:13.1 installation-images`
2. Add your new package to the `BuildRequires` list in the `installation-images.spec` file
3. Modify the package list for the target file system image, which is stored
   in `*.file_list` file. You can find a full example
   [here](https://build.opensuse.org/package/rdiff/openSUSE:13.1/installation-images?linkrev=base&rev=2),
   there is a diff for adding `libyui-qt-graph` package to the inst-sys.
   You can include a complete package or only explicitly listed files (this is
   useful if the package is huge and you need just a small file from it).  
   _Note: If you want to later update the installation-images package used as
   the base then it is better to use a patch instead of directly modifying the
   file list. It can be later easily applied to an updated version._
4. Build the package locally using `osc build --userootforbuild` command.
   This will take some time, it needs a lot of packages and building the target
   file system images is also not trivial. The build requires `root`
   user, without the extra option it would fail. If you really need to build the
   package in the OBS server automatically then you need to ask the OBS maintainers
   for adding an exception for your package. (In
   [YaST:Head:installer/installation-images](https://build.opensuse.org/package/show/YaST:Head:installer/installation-images)
   we have such an exception.)

### Updating the medium

Updating the boot medium is quite tricky, you need to unpack the `/CD1` and
`/SuSE/openSUSE/CD1` directories from the built installation-images RPM package
and overwrite the original files and create the ISO image again.  

But I have actually never tried that, in OBS we simply build our own ISO image using [a kiwi project](https://build.opensuse.org/package/show/YaST:Head:installer/minidvd-x86_64).