---
layout: post
title:  "Debugger in YaST"
date:   2016-06-02
category: Programming
tags: [ yast, ruby ]
---

Until now debugging the YaST installation was usually done by checking the
`y2log`. If you needed more details you would add more `log` calls.

This is inconvenient and takes too much time. For better debugging a real
debugger would be nice...

From now on you can use a fully featured Ruby debugger in the installation.
Simply boot the installer with `Y2DEBUGGER=1` and that's it!

![Debugger Screenshot](/images/debugger_session.png)

More over the same support has been done to installed system, just
start the YaST module like this:

    Y2DEBUGGER=1 yast2 <client>

Just make sure the `rubygem-byebug` package is installed in the system, it is not
installed by default.

For more details see the [new documentation](
http://yastgithubio.readthedocs.io/en/latest/debugging/).
