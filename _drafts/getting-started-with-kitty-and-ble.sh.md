---
layout: post
title: Getting started with the kitty terminal emulator and ble.sh
---

As a developer who spends a lot of time at the command line tools for work, I wanted two things from my terminal setup: ease of use and speed. My goal was to get a speedy setup that still had the pleasant ergonomics of iTerm and the awesome auto-completions like those in fish.sh. 

In this post I'd like to share how I configured two tools to achieve get the terminal setup I wanted.

1) [kitty](https://sw.kovidgoyal.net/kitty/index.html) - a very fast and highly configurable terminal emulator
2) [ble.sh](https://github.com/akinomyoga/ble.sh) - a pure bash line editor with features like enhanced completions.

Some of the settings in this post may be macOS specific.

## kitty

### Configuration

Ok, you've downloaded kitty and have it running. First impressions? Well, mine was "the text is awfully small".

kitty is an opinionated piece of software, but fortunately it's also highly configurable. This means you'll like want to start customizing kitty to solve issues like tweaking your font size. kitty does not have a GUI interface and is instead configured using a file that you need to create. By default kitty looks at `~/.config/kitty/kitty.conf` but this is [configurable](https://sw.kovidgoyal.net/kitty/conf.html).

Before switching to kitty I used iTerm, so I wanted configure kitty to use the same shorcuts I was used to. I figured out the settings for this setup myself, but it looks like there's a decent post on getting that setup going now [here](https://dev.to/tiemen/configuring-kitty-2akj), but this post will go over some settings not included there.
