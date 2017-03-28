---
layout: post
title: Docker at Travis
category: Programming
tags: [ TDD, CI, testing, YaST ]
description: How to build your software at Travis CI easily and effectively using Docker.

---

# Travis CI

The [Travis CI](https://travis-ci.org/) service offers a free [Continuous Integration
(CI)](https://en.wikipedia.org/wiki/Continuous_integration) service for open
source projects hosted at [GitHub](https://github.com).

## Ubuntu 12.04 LTS

Unfortunately it runs Ubuntu 12.04 LTS at the worker nodes. That means if your
software needs some newer tools or libraries you have to find it in some
external repository. You might be lucky with the [Ubuntu LaunchPad](
https://launchpad.net/), but in the worst case you will need to backport the
package by yourselves.

## Other Distributions?

But what if you need a newer Ubuntu version for your build? Or a completely
different distribution like openSUSE or Fedora?

Originally that was not possible with Travis, but fortunately they allow using
[Docker containers](https://www.docker.com/) at build for some time. That means
you can run even a completely different distribution for building your software.

## Travis and Docker Tricks

### Building for Several Distributions in Paralell

Travis allows to setup a [build matrix](
https://docs.travis-ci.com/user/customizing-the-build#Build-Matrix) which can
run the build in different environments. The usual use case is to run the tests
using different versions of compilers or interpreters.

But you can easily use this feature for using different Docker images
with different distributions. You just define different `Dockerfile` and
building script for each environment.

We use this feature in [snapper](https://github.com/openSUSE/snapper) and the
code is built for five different distributions in parallel!

{% include blog_img.md alt="Snapper Build" src="travis_snapper.png"
ext_link="https://travis-ci.org/openSUSE/snapper" %}

As you can see the code is built for Debian, Ubuntu, Fedora, openSUSE Leap
and openSUSE Tumbleweed. That means we know that the code still builds on all
these distributions even before merging a change!

See the `Dockerfile.*` and `.travis.*` [source files](
https://github.com/openSUSE/snapper) for more details.

### Building Docker Images at Docker Hub

The [Docker Hub](https://hub.docker.com/) is a platform for publishing and
sharing the the Docker images.

The snapper example above builds the Docker image locally and then runs it. But
what if you need to build the same image many times? What if your software
changes a lot? Or you have several packages which run in the same environment?

Then it makes sense to build the image only once and then reusing everywhere where
needed. That is exactly the case for YaST, we have about one hundred
repositories which build in the same environment.

Actually we split the environment into two parts - one for Ruby based packages
and one for C++ based packages. The reason is to have a smaller Docker image for
faster downloads.

So at Travis we either download the [Ruby
image](https://hub.docker.com/r/yastdevel/ruby/) or the [C++
image](https://hub.docker.com/r/yastdevel/cpp/). You can check the sources for
both Docker images at GitHub ([Ruby image](
https://github.com/yast/docker-yast-ruby), [C++ image](
https://github.com/yast/docker-yast-cpp)).

### Local Build

Normally you cannot reproduce the Travis builds locally as Travis uses a customized
Ubuntu image which is not available for download. That means even if you build your
software on Ubuntu 12.04 you might still get a slightly different results at
Travis.

With Docker you can download or build the very same Docker image and run it
locally. Just run the same Docker commands as in Travis.

(Technically it still will not be 100% the same as at Travis, e.g. Docker uses
the host system kernel so there still might be some differences but it is
very very close...)

## Try it in Your Projects!

So hopefully these hints will be helpful for somebody and will allow you to run
CI also for your project. And if Travis does not fit your needs then there are
similar alternatives available...
