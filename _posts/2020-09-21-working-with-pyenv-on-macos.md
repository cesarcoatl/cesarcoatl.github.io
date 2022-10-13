---
layout: post
title: Working with pyenv on macOS
tags:
- guide
- homebrew
- macos
- pyenv
- python
- tutorial
date: 2020-09-21 13:02 -0700
last-updated: 2022-10-13 08:34 -0700
---
## Table of Contents

- [Introduction](#introduction)
- [Pre-requisites](#pre-requisites)
- [Why pyenv?](#why-pyenv)
- [Installing pyenv](#installing-pyenv)
  - [Installing Python 3.9](#installing-python-39)
  - [Installing Python 2.7](#installing-python-27)
  - [Setting a global version of Python](#setting-a-global-version-of-python)
- [Success!](#success)
- [Further reading](#further-reading)
- [Sources](#sources)

## Introduction

This is not meant to be a detailed guide on the many ways you could install Homebrew, and pyenv, but a look into what I did to get my environment set up for my personal projects.

## Pre-requisites

Xcode Command Line Tools ([link](https://developer.apple.com/download/more/?=xcode){:target="_blank"})

Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

Let's begin.

## Why pyenv?

In my personal experience, working with multiple versions of Python in Windows is as easy as downloading as many versions as you wish or need, and drop them somewhere in your C drive. For some reason that seems easier than managing multiple versions of Python in macOS.

This is where `pyenv` comes in.

> pyenv lets you easily switch between multiple versions of Python. It's simple, unobtrusive, and follows the UNIX tradition of single-purpose tools that do one thing well.[^1]

Before [breathing new life into my MacBook Air]({{ page.previous.url | relative_url }}), I had already read about pyenv in a Real Python article titled ["Managing Multiple Python Versions With pyenv."](https://realpython.com/intro-to-pyenv/){:target="_blank"}[^2] So following the steps from that guide, and pyenv's own guide on using [Homebrew on macOS](https://github.com/pyenv/pyenv#homebrew-on-macos){:target="_blank"} I got my system ready for action.

## Installing pyenv

Install dependencies

```bash
brew install openssl readline sqlite3 xz zlib
```

Install pyenv

```bash
brew install pyenv
```

Load `pyenv` automatically by running the following command to add it to your shell

{: .box-note}
Starting with `pyenv` [v2.0.0](https://github.com/pyenv/pyenv/releases/tag/v2.0.0){:target="_blank"} the `pyenv init` command has changed.

```bash
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

Restart shell

```bash
exec "$SHELL"
```

Next, I'll install specific Python versions.

### Installing Python 3.9

First, let's get a list of all 3.9 versions:

```bash
$ pyenv install --list | grep " 3\.9"
  3.9.0
  3.9-dev
  3.9.1
  3.9.2
  3.9.3
  3.9.4
  3.9.5
```

Installing the latest 3.9 release.

```bash
pyenv install 3.9.5
```

### Installing Python 2.7

{: .box-note}
For the [Ignition](https://github.com/thecesrom/Ignition/){:target="_blank"} project I maintain, both Python 2.7.18 or Jython 2.7.1 are required.

So, first, I will install Python 2.7.18.

```bash
$ pyenv install --list | grep " 2\.7"
  2.7.0
  2.7-dev
  2.7.1
  2.7.2
  2.7.3
  2.7.4
  2.7.5
  2.7.6
  2.7.7
  2.7.8
  2.7.9
  2.7.10
  2.7.11
  2.7.12
  2.7.13
  2.7.14
  2.7.15
  2.7.16
  2.7.17
  2.7.18
```

{: .box-note}
Python 2.7 reached its EOL on January 1, 2020. See: [Sunsetting Python 2](https://www.python.org/doc/sunset-python-2/){:target="_blank"}

Installing the final 2.7 version:

```bash
pyenv install 2.7.18
```

### Setting a global version of Python

Run `pyenv versions` to list all Python versions known to pyenv; an asterisk will be shown next to the currently active version.

```bash
$ pyenv versions
* system (set by /Users/thecesrom/.pyenv/version)
  2.7.18
  3.9.5
```

Run `pyenv global <version>` to set the global version of Python to be used in all shells.

```bash
pyenv global 3.9.5
```

Verify your selection by running `pyenv versions`.

```bash
$ pyenv versions
  system
  2.7.18
* 3.9.5 (set by /Users/thecesrom/.pyenv/version)
```

Alternatively, you could [specify multiple versions as global at once](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-global-advanced){:target="_blank"}.

## Success

And by doing all of the above I have completed setting up my computer for working on my projects.

Happy coding!

## Further reading

1. pyenv :: Modern Python Developer's Toolkit - [https://pycon.switowski.com/02-packages/pyenv/](https://pycon.switowski.com/02-packages/pyenv/){:target="_blank"}

## Sources

[^1]: pyenv/pyenv: Simple Python version management - [https://github.com/pyenv/pyenv](https://github.com/pyenv/pyenv){:target="_blank"}
[^2]: Managing Multiple Python Versions With pyenv - [https://realpython.com/intro-to-pyenv/](https://realpython.com/intro-to-pyenv/){:target="_blank"}
