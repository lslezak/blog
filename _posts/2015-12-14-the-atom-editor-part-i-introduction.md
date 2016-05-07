---
layout: post
title: The Atom Editor - part I. - Introduction
category: Programming
tags: [ Atom, IDE ]
---

## The Atom Editor

For the 13th SUSE HackWeek I chose the [Atom Editor](https://atom.io/) as the
topic.

I wanted to get familiar with it and to learn how to customize it and how to
develop new features.  

### Atom Overview

For the quick overview of the Atom features just watch this video:  

<div class="separator" style="clear: both; text-align: center;"><iframe allowfullscreen="" class="YOUTUBE-iframe-video" data-thumbnail-src="https://i.ytimg.com/vi/U5POoGSrtGg/0.jpg" frameborder="0" height="266" src="https://www.youtube.com/embed/U5POoGSrtGg?feature=player_embedded" width="320"></iframe></div>

<br>If you have more time you can watch this longer video:

<div class="separator" style="clear: both; font-size: 18.72px; text-align: center;"><iframe allowfullscreen="" class="YOUTUBE-iframe-video" data-thumbnail-src="https://i.ytimg.com/vi/hj0R15-5fy8/0.jpg" frameborder="0" height="266" src="https://www.youtube.com/embed/hj0R15-5fy8?feature=player_embedded" width="320"></iframe></div>

### Installation

Installation is quite easy, there are pre-built binary packages for the most
used Linux distributions. If there is no package for your distribution or it
does not work for you then you can build Atom from the sources.  

Fortunately for the openSUSE distribution is it easy, just download the RPM
package from the main page. It has been built for Fedora 21 but it works without
any problem in openSUSE Leap 42.1\. (I have tested an older version also in
openSUSE 13.2 and it was fine so it should work there as well.)  

### Setup

I suggest to switch from the default white-on-black scheme to more usual
black-on-white which is used by most desktop applications and make switching to
the other applications (like a web browser) more eye-friendly.  

At the configuration tab (`Ctrl + ,`) go to the *Theme* section and select
*Atom Light* as the UI theme and *One Light* as the syntax theme. You can
try the other themes or you can download some new one in the *Install* section
as shown in the videos above.

### The Atom Features

Here is a short summary of the features which I found interesting during the
short testing. I do not mention the usual editor features and I likely
overlooked something. You can also see some features in the videos above.  

* *The Command Panel* (`Ctrl+Shift+P`) is probably the most important feature.
  It displays all known commands (the core commands and also from all enabled
  plugins) with a keyboard shortcut if available. There is also a quick search
  for looking up.   So if you cannot remember a shortcut or you do not know
  where to find a command just simply type it into the search bar and Atom will
  list all (even fuzzy) matches.

* The Atom authors say it is a hackable editor. I can confirm this, you can very
  easily change the look or layout or add new features. But let's go into the
  details in another blogpost...

* Open at GitHub - if you are editing a file which is stored at a GitHub
  repository you can easily open it in web browser. That's useful if you want to
  send a position in the code via IM, mail etc...

* Live Markdown preview can be displayed while you are typing. This helps a lot
  with the correct formatting.

![](http://1.bp.blogspot.com/-CjIah4A4XPs/Vm8q4YlQkaI/AAAAAAAAPog/cL_1hxuUOMU/s320/atom-markdown-preview.png)

The Atom features can be easily extended by plugins, let's look at them later.  

### Documentation

You can find the detailed [user documentation
here](https://atom.io/docs/v1.3.1/using-atom-atom-packages).

### To Be Continued...

Next time we check some Atom plugins which extend the basic functionality.

