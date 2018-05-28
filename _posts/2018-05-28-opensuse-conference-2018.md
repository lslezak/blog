---
layout: post
title: The openSUSE Conference 2018
category: Conference
tags: [ openSUSE, SUSE, Linux ]
description: Some interesting topics and notes from the conference.
---

# The openSUSE Conference 2018

I visited the openSUSE conference this year, it took place in Prague, at the
same place as few years ago. You can check the schedule and the list of the
talks [here](https://events.opensuse.org/conference/oSC18/schedule).

## The Interesting Topics

Here are some interesting sessions I visited, for the complete list see
the schedule link above.

- [The openSUSE Leap 15.0 has been released!](
  https://events.opensuse.org/conference/oSC18/program/proposal/1900)

- If you want to share a service without a having a public IP address
  the tor network might be an option. Using setting up a [service using
  Docker makes it really easy](
  https://events.opensuse.org/conference/oSC18/program/proposal/1678).

- The [package dependencies can be improved](
  https://events.opensuse.org/conference/oSC18/program/proposal/1864)
  using `Supplements` dependency this way:

  ```
  Supplements: (foo and bar)
  ```

  This would automatically install the package when *both* packages are
  installed.

- The openSUSE distribution is also yours, [openSUSE is what you make it](
  https://events.opensuse.org/conference/oSC18/program/proposal/1912).

- The [openSUSE Kubic](
  https://events.opensuse.org/conference/oSC18/program/proposal/1909) is a
  Tumbleweed flavor designed specially for running container workloads.

- There is a [tumbleweed-cli](
  https://build.opensuse.org/package/show/openSUSE:Factory/tumbleweed-cli)
  package which makes using the [Tumbleweed snapshots much easier](
  https://events.opensuse.org/conference/oSC18/program/proposal/1828).

- The [transactional updates](
  https://events.opensuse.org/conference/oSC18/program/proposal/1906)
  provide a completely new way how to update the running system.

- The btrfs filesystem provides a lot of interesting features. But if something
  goes wrong it might a bit tricky to fix it. The most important thing is that
  the `btrfs check --repair` command should used as the very last option
  (if at all). There are [other more safe options](
  https://events.opensuse.org/conference/oSC18/program/proposal/1915) to try
  first.

- Using the LetsEncrypt certificates should be much easier in the openSUSE
  Leap 15.0 because the automatic [certificate renewal is supported](
  https://events.opensuse.org/conference/oSC18/program/proposal/1801) out of
  box.

- If you miss some packages in SLE which are present in openSUSE then the
  [SUSE PackageHub might help you](
  https://events.opensuse.org/conference/oSC18/program/proposal/1957).
  
- The OBS (Open Build Service) can also build container images, that can
  [help with testing and distributing your packages](
  https://events.opensuse.org/conference/oSC18/program/proposal/1966).
  If you want to start in an easy way then you can choose from many
  [image templates](https://build.opensuse.org/image_templates).

- If you want to try the [openSUSE Kubic](https://kubic.opensuse.org/)
  you might be interested in [some basic concepts behind](
  https://events.opensuse.org/conference/oSC18/program/proposal/1960).

- Developing the community and the enterprise products separately has many
  disadvantages, it is [much better to develop them together](
  https://events.opensuse.org/conference/oSC18/program/proposal/1768).

### Portainer

One of the Kubic presentations mentioned the [portainer.io](
https://portainer.io/) project which looks really interesting.

If you are new to the Docker and the container world and you would like to use
some GUI at the beginning to make the start easier than the [portainer.io](
https://portainer.io/) project looks very interesting. It provides a nice web UI
for managing the Docker containers. And obviously it is provided as a Docker
image.

What is nice that starting it is basically a matter of running two commands:
```console
# docker volume create portainer_data
# docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data portainer/portainer
```

Then point your web browser to [http://localhost:9000/](http://localhost:9000/)
and that's it!

{% include blog_img.md alt="Portainer" src="portainer.png"
   full_img="portainer.png" %}

## Conclusion

The conference was really good and I'd like say thank you to the presenters, the
organization team and the sponsors to make this happen!