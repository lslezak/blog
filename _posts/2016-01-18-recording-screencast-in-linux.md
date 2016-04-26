---
layout: post
title: Recording a screencast in Linux
tags:
  - screencast
---

# Screencasts

> A picture is worth a thousand words

And what if you want to make it even better? What about using an animated image
to show some action or progress? Like this one:

![](https://cloud.githubusercontent.com/assets/907998/11747115/b7ca1362-a021-11e5-98ed-6111d9fa1410.gif)

Personally I like the animated images on the main project pages at GitHub. See
for example [git-plus](https://github.com/akonwi/git-plus) or [color-picker](
https://github.com/thomaslindstrom/color-picker) Atom plugins. (Or my
[build-rake](https://github.com/lslezak/build-rake) plugin displayed above.)

The question is how to record such a [screencast](https://en.wikipedia.org/wiki/Screencast)
in Linux.

## Screen Recording in Linux

There are several tools for Linux which can be used for screen recording. It
also depends it you want to create a standard video file (e.g. for uploading to
youtube) or an animated image which can be embedded into web pages.

### Standard Video

For recording the desktop to a standard video file (like in AVI or WEBM format)
you can use the ffmpeg tool.

To record the screen with audio capture (via PulseAudio) to a WebM file
(using open codecs VP9 for video and Opus for audio) use command like this:

```
ffmpeg -f pulse -i default -video_size x -framerate 15 -f x11grab -i :0.0+<width>,<height> -b:v 1M -crf 10 output.webm
```

See these links for more details:

  - <https://trac.ffmpeg.org/wiki/Capture/Desktop>
  - <https://trac.ffmpeg.org/wiki/Encode/VP8>
  - <https://www.ffmpeg.org/ffmpeg-devices.html#x11grab>
  - <http://www.lxtips.com/2011/07/single-window-screen-capture-script.html>

### Animated Image

If you want to create an animated image you need to use the 
[GIF image format](https://en.wikipedia.org/wiki/GIF) (you could use an
[animated PNG](https://en.wikipedia.org/wiki/APNG) as well, but that is not
supported by all browsers).

You could use `ffmpeg` as well (just use `output.gif` file name), but in my
tests the resulting image is too big for using on web pages. It's probably
caused by the fact that ffmpeg uses dithering for images with 24bit colors
which obviously leads to less compression. Maybe there are some options to tune
this, but still writing the position of the recorded area manually is a bit
uncomfortable...

Then I found the `byzanz` tool. It's by default available to install in the
openSUSE distribution, simply run: 

```
sudo zypper in byzanz xwininfo
```

(This will also install the `xwininfo` package, see later why.)

Now you can record the desktop in a similar way like `ffmpeg`, see the accepted
parameters: `byzanz-record --help`

Again, you need to manually enter the coordinates of the recorded area if you do
not want to record complete screens. And that's not trivial... Fortunately I
found this nice [byzanz-record-window](https://gist.github.com/lucy/3042755)
helper script.

You can install it locally via these commands:

```
cd ~/bin
wget https://gist.githubusercontent.com/lucy/3042755/raw/eda2b85b51779affbc392a2bb3f1b60ed5fe98ff/byzanz-record-window
chmod a+x byzanz-record-window
```

The script uses the `xwinfo` helper to obtain the coordinates of the selected
window. They are then passed to the `byzanz-record` tool.

I'd suggest to use about 3 second delay before starting the actual recording to
have some time to get ready (switch to the correct window, position the mouse
cursor, etc...).

So the final recording command would look like this:

```
byzanz-record-window --delay=3 --duration=5 screencast.gif
```

## Final Notes

Be prepared to repeat the recording several times, you'll probably need more
than a single attempt. I also found it easier to repeat the recording than
editing and rebuilding the animation just to remove the useless parts at the
beginning or the end.

Check also the resulting image size, for embedding into web pages it should be
as small as possible. If it is too big then decrease the window size, do a
shorter recording or avoid too many changes in the image.

