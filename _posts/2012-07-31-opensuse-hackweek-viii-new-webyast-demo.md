---
layout: post
title: OpenSUSE Hackweek VIII - New WebYaST Demo Appliance
category: Programming
tags: [ webyast ]
---

# WebYaST Demo Appliance

I decided to work on WebYaST home page and finish the WebYaST demo appliance.  

Some time ago we had a workshop where we decided to create cool web pages for
our projects. We started with WebYaST. The goal is to create a nice looking web
presentation.  

And we want to have some WebYaST demo so users could easily try it without any
setup or installation. So we decided to create a [WebYaST demo
appliance](http://susestudio.com/a/N3CQ8b/webyast-demo) in [SUSE
Studio](http://susestudio.com/) which can be used as a LiveCD or USB stick or
even directly in Studio testdrive in a web browser (no need to download
anything!).  

### Including WebYaST in an openSUSE-12.1 SUSE Studio Appliance

This is really easy as Studio has WebYaST support built-in, just go into
Configuration -> Appliance tab and check _Enable WebYaST_ check box at the very
bottom of the page. And that's it!  

Studio will add all needed WebYaST packages, opens port at firewall (54984) and
autostarts WebYaST at boot.  

But if we want to have a really nice demo we still need to do some
improvements...  

### Appliance Fine-tuning

Originally I started with KDE desktop but WebYaST basically does not depend on
any desktop environment so I switched to LXDE which takes less space and should
run faster than KDE (especially on slower machines).  

SUSE Studio supports autologin configuration (Configuration -> Desktop) so users
do not have to enter any password to start graphical session, that's nice.  

Another nice feature is automatic application start,  so we can easily start
Firefox. The only problem was that in testdrive WebYaST was sometimes started
later than Firefox which obviously displayed error page. This is solved by 5
second delay before starting Firefox.  

### Importing WebYaST Certificate into Firefox

This was the hard part and it's quite tricky. When you first time connect to a
running WebYaST instance in Firefox you'll see a certificate warning. The
problem is that WebYaST generates a self-signed certificate (when there is no
existing certificate yet) which is not trusted so Firefox displays that warning.
And this might be scary for beginner users, we want our users to try WebYaST
without any doubts...  

Then I found `certutil` tool available in `mozilla-nss-tools` package in
openSUSE. This can be used to import a certificate to Firefox from command line.
So for WebYaST this means running this command for the default user:  

```shell
certutil -A -n "Webyast certificate" -t "C,," -d /home/tux/.mozilla/firefox/*.default \  
-i /etc/lighttpd/certs/webyast.pem  
```
(See `certutil --help` for more details.)  

The only problem is that Firefox uses profiles (named configurations) stored at
random generated directory which is created at first start. And that directory
must exist before executing the `certutil` command.  

This is solved by overlay files in Studio, there is a prepared directory with
the default Firefox profile.  

#### Server Name in the Generated certificate

During tests I found out that there is a problem with certificate server name
and the URL. Firefox displayed a certificate error when opening
https://localhost:54984 with message saying that the certificate is valid for
`linux-<foo>` server only (with *foo* replaced by some random characters).  

The problem is that the random hostname is automatically generated and we cannot
easily change that in the URL for Firefox.  

The trick is to use IP address directly instead of a host name. The WebYaST
certificate in the demo appliance is generated for host 127.0.0.1 and URL for
Firefox is set to https://127.0.0.1:54984.  

See `yastwc` overlay file for all certificate related changes.  

### The Final WebYaST Demo Appliance

[The final WebYaST demo appliance](http://susestudio.com/a/N3CQ8b/webyast-demo)
is available in SUSE Studio Gallery. You can easily try WebYaST as a
LiveCD/LiveUSB stick or directly in your browser in Studio testdrive.  

And how to use it? That's really simple! Just boot the image, wait until Firefox
with WebYaST login dialog opens. Then use `root` user name with `linux` password
to log into WebYaST.  

*Have a lot of fun!*
