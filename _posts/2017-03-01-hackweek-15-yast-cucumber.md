---
layout: post
title: Hackweek 15 - the YaST Integration Tests
category: Hackweek
tags: [ Linux, YaST, Hackweek, testing ]
description: Controlling and inspecting the YaST UI remotely.

---

# Hackweek 15

I decided to spend the last SUSE Hackweek with YaST and find a way which
would allow us to write and run YaST integration tests easily. See the
details in the [project Hackweek page](
https://hackweek.suse.com/projects/yast-integration-tests-using-cucumber).

Some time ago I found the [cucumber-cpp](
https://github.com/cucumber/cucumber-cpp) project. It is a [cucumber](
https://github.com/cucumber/cucumber) support for the C++ programming language.

The reason is that the YaST UI uses the
[libyui](https://github.com/libyui/libyui) library which is written in C++.
If we want to control and check the YaST UI we need to implement it on the
libyui level.

## The Hackweek Result

Here are some Cucumber test examples which I was able to run in a real system.
The source code for the tests and the details how to run them can be found at
the [lslezak/cucumber-yast](https://github.com/lslezak/cucumber-yast) GitHub
repository.

Currently only the graphical (Qt) UI is adapted, the textmode (ncurses) will
not work and crash (because of the ABI incompatibility).

The code is available in my GitHub forks of [libyui](
https://github.com/libyui/libyui/compare/master...lslezak:cucumber?expand=1),
[libyui-qt](https://github.com/libyui/libyui-qt/compare/master...lslezak:cucumber?expand=1)
and small improvement was done for the [cucumber-cpp](
https://github.com/cucumber/cucumber-cpp/compare/master...lslezak:improvements?expand=1)
library. The experimental RPM packes are available in the [home:lslezak:cucumber](
https://build.opensuse.org/project/monitor/home:lslezak:cucumber) OBS repository.

### Running a Cucumber Test in Installed system

Here is the output of the [adding_new_repo.feature](
https://github.com/lslezak/cucumber-yast/blob/master/features/adding_new_repo.feature)
Cucumber test.

{% include blog_img.md alt="Adding a Repository in YaST" src="add_repo.gif" %}

### Running a Cucumber Test During Installation

This needs a patched openSUSE Leap 42.2 installer so it is not trivial to reproduce...

In this case the installation is running in a VirtualBox virtual machine
and the test is running outside on my workstation. The test source is [here](
https://github.com/lslezak/cucumber-yast/blob/master/features/installation.feature).

{% include blog_img.md alt="openSUSE Leap 42.2 Installation" src="install_leap_42.2.gif" %}

### Running a Cucumber Test for a Plain Libyui Application

The Cucumber tests can be written actually for any application which uses the
libyui framework, not only for YaST. This might be interesting for the other users
of the libyui framework, for example the [Mageia tools](
https://wiki.mageia.org/en/Feature:UiAbstraction4mcc).

Here is a [test](https://github.com/lslezak/cucumber-yast/blob/master/features/libyui_selectionbox2.feature)
for the libyui [SelectionBox2.cc](
https://github.com/libyui/libyui/blob/master/examples/SelectionBox2.cc) example.


{% include blog_img.md alt="Libyui SelectionBox2.cc Example" src="libyui_selectionbox2.gif" %}


## What I Learnt

- Using boost for implementing a simple TCP server is probably an overkill,
  after seeing [this boost::asio example](
  http://www.boost.org/doc/libs/1_63_0/doc/html/boost_asio/tutorial.html#boost_asio.tutorial.tutdaytime3)
  :flushed: I decided to use plain and simple `socket()`/`bind()`/`listen()` C functions.
- Synchronizing with the application is crucial, you cannot verify the UI
  state until it is fully built by the application. In case of YaST it
  is when `UI.UserInput` (or similar) function is called.
- I refreshed my C++ knowledge, I even used a C++ template in the code :wink:

## Technical details

- The cucumber-cpp library uses the [Cucumber wire protocol](
https://github.com/cucumber/cucumber/wiki/Wire-Protocol) which basically sends
JSON messages over a TCP port. The advantage is that it is possible to
test the application running at another machine. This is useful for testing
the YaST installation.
- I had to reimplement the server part as the cucumber-cpp library can only test
an application represented by a single C++ object. Because YaST uses a plugin
architecture where the parts are loaded dynamically and they cannot be easily
accessed from outside so the server part must have been implemented directly on
the libyui level.
- The advantage of this solution is that the integration tests are available
to any libyui based application, not only YaST.


## TODO

- Add more matchers
- Support for more UI calls (e.g. `UI.PollInput`)
- Support the other UIs (ncurses, GTK)
- Support for the packager widget (in the `libyui-*-pkg` subpackages)
- Increase the Cucumber timeout - when a step takes too much time (e.g. installing
  packages) then the test runner times out
- Closing the application crashes the test (the TCP port is closed and cucumber
  reports broken pipe)
- The application needs to be already running, it is difficult to ensure
  clean initial state or restart the application for another test scenario

The last two issues are quite tricky, it seems we will need to run some wrapper
and communicate with YaST in the tests indirectly. In that case we probably
should use some simpler communication protocol like the
[Test Anything Protocol (TAP)](https://testanything.org/). I was told that
this protocol is already supported by openQA so it should be easy to use it
also there...
