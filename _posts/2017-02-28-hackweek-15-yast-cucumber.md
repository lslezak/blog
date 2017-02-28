---
layout: post
title: Hackweek 15 - the YaST Integration Tests
category: YaST
tags: [ Linux, YaST, TDD, testing ]

---

# Hackweek 15

I decided to spend the last SUSE Hackweek with YaST and find a way which
would allow us to YaST integration tests easily.

Some time ago I found the [cucumber-cpp](
https://github.com/cucumber/cucumber-cpp) project. It is a [cucumber](
https://github.com/cucumber/cucumber) support for the C++ programming language.

The reason is that the YaST UI uses the
[libyui](https://github.com/libyui/libyui) library which is written in C++.
If we want to control and check the YaST UI we need to implement it on the
libyui level.

# The Hackweek Result

At the end 

# What I Learnt

- Using boost for implementing a simple TCP server is probably an overkill,
  after seeing [this example](
  http://www.boost.org/doc/libs/1_63_0/doc/html/boost_asio/tutorial.html#boost_asio.tutorial.tutdaytime3)
  I decided to use plain and simple `socket()`/`bind()`/`listen()` C functions.
- Synchronizing with the application is crucial, you cannot verify the UI
  state until it is fully built by the application. In case of YaST it
  is when `UI.UserInput` (or similar) function is called.

# TODO

- Add more matchers
- Support for more UI calls (e.g. `UI.PollInput`)
- Support the other UIs (ncurses, GTK)
- Support for the packager widget (in the `libyui-*-pkg` subpackages)
