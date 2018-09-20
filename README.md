# metadata

[![Homebrew](https://img.shields.io/badge/dynamic/json.svg?url=https://api.github.com/repos/zmwangx/metadata/tags&label=homebrew&query=$[0].name&colorB=orange&maxAge=1800)](#homebrew)
[![crates.io](https://img.shields.io/crates/v/metadata.svg)](https://crates.io/crates/metadata)

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [History](#history)
- [Installation](#installation)
  - [Homebrew](#homebrew)
- [Usage](#usage)
- [Performance](#performance)
- [Bugs](#bugs)
- [Copyright](#copyright)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

`metadata` is a media metadata parser and formatter designed for human consumption. Powered by FFmpeg.

Example:

```
$ metadata '20160907 Apple Special Event.m4v'
Title:                  Apple Special Event, September 2016 (1080p)
Filename:               20160907 Apple Special Event.m4v
File size:              6825755188 (6.83GB, 6.36GiB)
Container format:       MPEG-4 Part 14 (M4V)
Duration:               01:59:15.88
Pixel dimensions:       1920x800
Sample aspect ratio:    1:1
Display aspect ratio:   12:5
Scan type:              Progressive scan*
Frame rate:             29.97 fps
Bit rate:               7631 kb/s
    #0: Video, H.264 (High Profile level 4), yuv420p, 1920x800 (SAR 1:1, DAR 12:5), 29.97 fps, 7500 kb/s
    #1: Audio (und), AAC (LC), 48000 Hz, stereo, 125 kb/s
    #2: Subtitle (eng), EIA-608 closed captions

```

Compare this to `ffprobe` or `mediainfo` (both great tools, just not so human-readable):

![ffprobe](https://user-images.githubusercontent.com/4149852/45572668-f5b82c00-b837-11e8-8295-f066bca019e9.png)
![mediainfo](https://user-images.githubusercontent.com/4149852/45572674-fa7ce000-b837-11e8-8fdc-dcccc57d55d9.png)

(`mediainfo` prints so much, the output doesn't even fit on my screen with 85 lines. Now try using it in a 80x24 terminal.)

`metadata` can print tags too if you want to, hopefully better organized:

```
$ metadata -t '20160907 Apple Special Event.m4v'
Title:                  Apple Special Event, September 2016 (1080p)
Filename:               20160907 Apple Special Event.m4v
File size:              6825755188 (6.83GB, 6.36GiB)
Container format:       MPEG-4 Part 14 (M4V)
Duration:               01:59:15.88
Pixel dimensions:       1920x800
Sample aspect ratio:    1:1
Display aspect ratio:   12:5
Scan type:              Progressive scan*
Frame rate:             29.97 fps
Bit rate:               7631 kb/s
Streams:
    #0: Video, H.264 (High Profile level 4), yuv420p, 1920x800 (SAR 1:1, DAR 12:5), 29.97 fps, 7500 kb/s
    #1: Audio (und), AAC (LC), 48000 Hz, stereo, 125 kb/s
    #2: Subtitle (eng), EIA-608 closed captions
Tags:
    title:              Apple Special Event, September 2016 (1080p)
    artist:             Apple
    album:              Apple Keynotes (1080p)
    compilation:        0
    description:        iPhone 7, iPhone 7 Plus, AirPods, Apple Watch Series 2, Apple Watch Hermès and Apple Watch Nike+
    podcast:            1
    episode_uid:        104
    synopsis:           See Apple CEO Tim Cook and team introduce the iPhone 7, iPhone 7 Plus, AirPods, Apple Watch Series 2, Apple Watch Hermès and Apple Watch Nike+.
    genre:              Podcast
    gapless_playback:   0
    date:               2016-09-09T12:00:00Z
    rating:             0
    season_number:      0
    episode_sort:       0
    media_type:         0
  #2
    rotate:             0
    language:           eng

```

You can also request *all* tags with `-A, --all-tags`. Output not shown here for the sake of brevity (that ship has sailed, but still).

## History

`metadata` is a Rust port of the Python tool of the same name bundled in my [storyboard](https://pypi.org/project/storyboard/) project. I started the storyboard project to replicate the storyboard/thumbnail images generated by some proprietary media players, in order to facilitate media file sharing on online forums. While I long stopped sharing media files online (and hence stopped using `storyboard`), I still love the command line metadata formatter that came out of the storyboard effort, and use it all the time in place of `ffprobe` or `mediainfo`. Years later, `metadata` could use some decoupling and refresh, and I could learn myself some Rust, so I started this port.

Since this is my intro to Rust project, *the code is guaranteed to be shitty*.

## Installation

**FFmpeg and the Rust toolchain are required.** Currently supported FFmpeg version:

- 4.0.x (tested: 4.0.2);
- 3.4.x (tested: 3.4.4).

3.2.x is known to *not* work (tested 3.2.12 on Debian Stretch).

Since `metadata` links to `libav*`, which themselves are a beast to compile and come with complicated licensing and distribution restrictions (see `--enable-gpl`, `--enable-nonfree`, etc.), it is inconvenient for me to compile and distribute static binaries. Please compile from source.

The basic build command to run once you have the dependencies is

```console
$ make release
```

You should find `metadata` and `metadata.1` in `dist/<version>/`.

OS/distro-specific instructions can be found below.

### Homebrew

On macOS, `metadata` can be installed with Homebrew:

```console
$ brew tap zmwangx/metadata https://github.com/zmwangx/metadata
$ brew install zmwangx/metadata/metadata
```

### Debian/Ubuntu

On Debian/Ubuntu, the following dependencies need to be satisfied (in addition to the Rust toolchain):

```console
$ apt install -y build-essential clang libavcodec-dev libavdevice-dev libavformat-dev libavfilter-dev pkg-config
```

Note that FFmpeg 3.4.x on Ubuntu 18.04 LTS (bionic) and forward are supported; older FFmpeg from 16.04 LTS (xenial) and prior releases are not. FFmpeg 3.2.x on Debian Stretch is not supported. You may use the [jonathonf/ffmpeg-4](https://launchpad.net/~jonathonf/+archive/ubuntu/ffmpeg-4) PPA, which supports trusty, xenial, and bionic.

## Usage

```console
$ metadata -h
metadata 0.1.0
Zhiming Wang <metadata@zhimingwang.org>
Media file metadata for human consumption.

USAGE:
    metadata [FLAGS] <FILE>...

FLAGS:
    -A, --all-tags    Print all metadata tags
    -c, --checksum    Include file checksum(s)
    -h, --help        Prints help information
    -s, --scan        Decode frames to determine scan type (slower, but determines interlaced more accurately; see man
                      page for details)
    -t, --tags        Print metadata tags, except mundane ones
    -V, --version     Prints version information

ARGS:
    <FILE>...    Media file(s)
```

The [man page](man/metadata.1.adoc) has more details.

## Performance

`metadata` is fast (a vast improvement over the old Python tool). I tested a collection of ~1300 video files on one of my USB 3.0 media drives, and `metadata` (with `xargs`) managed to chew through all of them within 50 seconds, less than 40ms per file on average. It might be faster for files hosted on a native SSD. `ffprobe` was actually slower in the test since it only accepts one file at a time.

## Bugs

<https://github.com/zmwangx/metadata/issues>.

## Copyright

Copyright (c) 2018 Zhiming Wang <metadata@zhimingwang.org>. The MIT License.
