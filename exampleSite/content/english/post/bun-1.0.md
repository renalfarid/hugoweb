---
title: 'Bun 1.0'
date: 2023-09-12T21:47:41+00:00
draft: false
tags:
  - bun
  - javascript
---

Bun is a fast JavaScript all-in-one toolkit. It can be used as a runtime (a drop-in replacement much faster than Node.js), as a test runner, and even as a package manager.

## Why would you switch away from NPM, pnpm, or Yarn?

Well, if you actually test Bun, you will notice how incredibly faster than Node.js it is. Up to 30x!
<ul>
  <li>Your front-end dependencies will install faster.</li>
  <li>Your assets will compile faster.</li>
  <li>Your continuous integration environment will also run faster since installing and compiling front-end dependencies  takes less time.
  </li>
</ul>

## How to install Bun on macOS using Homebrew

Installing Bun on macOS couldn’t be easier. Just add the new source using : 
```
$ brew tap oven-sh/bun
``` 
and install Bun by running :
```
$ brew install bun
```

## How to install on Linux and WSL

Installing Bun on Linux is as easy as on macOS. Run : 
```
$ curl -fsSL https://bun.sh/install | bash
``` 
That’s it!

Linux users are recommended to make sure the unzip package is installed first. You should also be running the kernel in at least version 5.1, even if version 5.6 or higher is a better choice.

## Install your front-end dependencies using Bun

To install your dependencies using Bun, use : 
``` 
$bun install 
``` 
So, how fast was it? I bet you didn’t expect that!

Oh and by the way, in case of a problem, if you want to disable the cache, use bun install --no-cache.

For additional information and options, please refer to [the official documentation of the bun install command](https://bun.sh/docs/cli/install).

