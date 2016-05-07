---
layout: post
title: Improved Disk Usage Estimation in YaST Installer
category: Programming
tags: [ yast ]
---

# Disk Usage

YaST installer estimates used disk space in the final installed system. This is
possible thanks to the disk usage metadata in installation repository (located
in file `suse/setup/descr/packages.DU.gz`).

This file contains disk usage statistics for every package, so YaST needs just
to sum them for all selected packages. (Actually it's slightly more complicated,
the disk usage is stored per directory, so YaST/libzypp has to sum it up
according to the proposed disk partitioning. Imagine you have separate `/usr` or
`/boot` partition...)  

## The Problem

This works well, but the problem is that the final sum does not match the real
value you find after installation is completed.  

For example in openSUSE-12.1-M3 in default KDE installation the installer
estimates this disk usage:  

```
Free: 3.3GB  
Used: 3.1GB  
Total: 6.4GB
```

But if you install the system and check the real disk usage you will find these
numbers:  

```
# df -h /  
Filesystem      Size  Used Avail Use% Mounted on  
/dev/sda2       6,4G  3,7G  2,5G  61% /</pre>
```

So the difference between estimated and real available free space is about
800MB! That's quite a lot!

In this case it was not a problem, but if the target partition was smaller, YaST
could tell that there would be e.g. 500MB free space but the installation would
actually fail because of not enough free space. This problem was reported
several times in Novell bugzilla, e.g.
[bnc#263275](https://bugzilla.novell.com/show_bug.cgi?id=263275),
[bnc#495251](https://bugzilla.novell.com/show_bug.cgi?id=495251) and more.

### Improvement

So where is the extra space spent? Why it doesn't match? There are basically
these reasons:  

* Files not owned by any package - the repository metadata contains just disk
  usage for files owned by packages (the files which are in RPMs), it doesn't
  and cannot know anything about files which are created at runtime outside of
  RPM package files.  

  The files are mainly (with usual size in default installation)  

  * RPM database (contains info about the installed packages): 42MB
  * Zypp cache (contains info about the configured software repositories): 38MB
  * System logs: 14MB
  * Config backup files: 10MB
  * Kernel initrd: 10MB
  * ... and many more small files, but they almost don't affect the overall size
    so they can be ignored


* File system fragmentation - space is allocated in blocks, so small files
  effectively take more space than their total size. (ReiserFS is an
  exception, it supports tail joining.)

* File system's journal - modern file systems use journaling so the file system
  is in consistent state after system crash or power failure. On the other hand
  the journal takes some space, for example default Ext3/Ext4 journal size is
  128MB (but it can be smaller, depending on partition size and block size). The
  journal decreases the usable free space.

* Reserved space for root user - Ext file systems by default reserve 5% space
  for root user. RPM explicitly checks for non-root space (even if it is running
  as root and could actually use the space), the reserved space also decreases
  the usable free space.

## Current State

I have added all the above mentioned extra space into the YaST installer (except
the FS fragmentation, that is hard to implement and would not significantly
change the estimation.)  

The most difficult task was to figure out the default journal size depending the
partition size. I had to look to the respective mkfs utility sources. Some
options (like reserved space) might be also set in the YaST partitioner so the
space calculation has to read and check the partitioning options.  

If you are interested in the implementation details the relevant commit can be
found in
[here](http://lists.opensuse.org/yast-commit/2011-08/msg00377.html)  

## Result

After patching the installer the estimated sizes (for the same installation) are:  

```
Free: 2.6GB  
Used: 3.8GB  
Total: 6.4GB
```

This pretty matches the real disk usage mentioned above. It's not exactly the
same, but it's a big improvement. And it actually will never be perfect as it
will be always a guess (just more or less good).  

The improvement has been submitted to Factory and will be available in
openSUSE-12.1-Beta.  

# To Do

The only thing which is missing is Btrfs support. I'll have to check the
`mkfs.btrfs` utility or Btrfs documentation. So far I have found out that after
formatting 8GB partition using Btrfs `df` reports about 7.2GB free space. I have
to found out how the final free space corresponds to the partition size.
Moreover Btrfs supports transparent file compression, snapshots, etc. which also
make the estimation more complicated...
