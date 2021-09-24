---
layout: post
title: How to add Python installed via Homebrew to pyenv versions
tags:
- guide
- homebrew
- pyenv
- python
- quick-take
- tutorial
- macos
date: 2021-06-28 13:10 -0700
last-updated: 2021-08-31 08:13 -0700
---
Hello, friends!

As I have [mentioned before]({{ site.url }}/2020/09/21/working-with-pyenv-on-macos/), I am using [`pyenv`](https://github.com/pyenv/pyenv) to manage multiple Python installations, but since I installed some Hombrew formulae that depend on [`python@3.9`](https://formulae.brew.sh/formula/python@3.9) I decided to uninstall Python 3 installed via `pyenv` and use the one from Homebrew.

So here I will share a quick guide on how I accomplished that.

1. If you've installed the latest Python `3.9` version via `pyenv`, uninstall it by running `uninstall` and then `rehash`

    ```bash
    pyenv uninstall 3.9.X
    pyenv rehash
    ```

1. Create a `symlink` at `~/.pyenv/versions`

    ```bash
    $ cd ~/.pyenv/versions
    $ ln -sfv "$(brew --prefix python@3.9)" 3.9
    $ ls -al
    total 0
    drwxr-xr-x  4 thecesrom  staff  128 Aug 31 07:51 .
    drwxr-xr-x  6 thecesrom  staff  192 Jun  8 10:58 ..
    drwxr-xr-x  6 thecesrom  staff  192 Aug 31 07:48 2.7.18
    lrwxr-xr-x  1 thecesrom  staff   25 Aug 31 07:51 3.9 -> /usr/local/opt/python@3.9
    ```

1. If you wish to also include the `include` directory, run the following

    ```bash
    $ cd "$(brew --prefix python@3.9)"
    $ ln -sfv Frameworks/Python.framework/Versions/3.9/include/python3.9 include
    include -> Frameworks/Python.framework/Versions/3.9/include/python3.9
    $ ls -al
    total 64
    drwxr-xr-x  14 thecesrom  staff    448 Aug 31 07:43 .
    drwxr-xr-x   3 thecesrom  staff     96 Jul  3 09:36 ..
    drwxr-xr-x   3 thecesrom  staff     96 Jun 28 01:57 .brew
    drwxr-xr-x   3 thecesrom  staff     96 Jun 28 01:57 Frameworks
    drwxr-xr-x   3 thecesrom  staff     96 Jun 28 01:57 IDLE 3.app
    -rw-r--r--   1 thecesrom  staff   3770 Jul  3 09:36 INSTALL_RECEIPT.json
    -rw-r--r--   1 thecesrom  staff  13925 Jun 28 01:57 LICENSE
    drwxr-xr-x   3 thecesrom  staff     96 Jun 28 01:57 Python Launcher 3.app
    -rw-r--r--   1 thecesrom  staff  10140 Jun 28 01:57 README.rst
    drwxr-xr-x  19 thecesrom  staff    608 Aug 31 07:50 bin
    lrwxr-xr-x   1 thecesrom  staff     58 Aug 31 07:43 include -> Frameworks/Python.framework/Versions/3.9/include/python3.9
    drwxr-xr-x   3 thecesrom  staff     96 Jun 28 01:57 lib
    drwxr-xr-x   4 thecesrom  staff    128 Jun 28 01:57 libexec
    drwxr-xr-x   3 thecesrom  staff     96 Jun 28 01:57 share
    ```

1. Create `symlinks` for `idle`, `pip`, `python` and `wheel` at the following location:

    ```bash
    $ cd "$(brew --prefix python@3.9)/bin"
    $ ln -sfv idle3 idle
    idle -> idle3
    $ ln -sfv pip3 pip 
    pip -> pip3
    $ ln -sfv python3 python
    python -> python3
    $ ln -sfv wheel3 wheel
    wheel -> wheel3
    ```

1. Once that's done, run `pyenv rehash` as recommended by `pyenv` whenever you install new versions

    ```bash
    pyenv rehash
    ```

1. Now, you're ready to verify all versions managed by `pyenv`, and select a `global`, `local`, or `shell` version

    ```bash
    $ pyenv versions
    * system (set by /Users/thecesrom/.pyenv/version)
      2.7.18
      3.9
    ```

1. If you want to set `3.9` as global, just run by running

    ```bash
    pyenv global 3.9
    ```

1. Verify your selection by running

    ```bash
    $ pyenv versions
      system
      2.7.18
    * 3.9 (set by /Users/thecesrom/.pyenv/version)
    ```

1. And now when I run `python --version` I get the following output:

    ```bash
    $ python --version
    Python 3.9.6
    ```

And that's it! You've done it!

Thanks for reading and happy coding!
