---
layout: post
title: Debugging Ruby on Rails Application
category: Programming
tags:
- debugging
- ruby on rails
---

# How to debug a Ruby on Rails application?

I needed to debug a Ruby on Rails application which I just have started to
develop. I put some logging commands to the code but it's not as good as using
a full debugger for inspecting internal data at runtime.  

## What's needed?

For debugging a Rails application `ruby-debug` gem is needed. It can be
installed by command `sudo gem install ruby-debug` in Linux or as a package
from an installation repository.  

## How to start the debugger?

At first, the place in the application where the debugger should be started
must be marked by `debugger` keyword.  

Then the application must be started in the debugger - it's simple, just use
`--debugger` option of the starting script, `script/server --debugger`.

## How to use the debugger?

When the application executes `debugger` command it stops and a debugger
session is opened in the terminal.  

It supports these basic commands:  
l (**line**) - print the source script  
c (**continue**) - stop the debug session at continue executing the application  
bt (**backtrace**) - print the execution stack (which functions were called)  
n (**next**) - execute the next command  
s (**step**) - execute the next command, step inside a function  
h (**help**) - will list more available commands with details  

Moreover it's possible to use normal ruby commands to print or inspect the
current objects, using e.g. `object.class`, `objec.inspect` methods.
And it's also possible to modify the current variables, just use
a `var = value` command.  

This makes the debugger really powerful, it's far better than simple `puts`
command!
