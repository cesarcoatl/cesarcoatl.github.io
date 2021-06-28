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
---
Hello, friends!

As I have [mentioned before]({{ site.url }}/2020/09/21/working-with-pyenv-on-macos/), I am using [`pyenv`](https://github.com/pyenv/pyenv) to manage multiple Python installations, but since I installed some Hombrew formulae that depend on [`python@3.9`](https://formulae.brew.sh/formula/python@3.9) I decided to uninstall Python 3 installed via `pyenv` and use the one from Homebrew.

So here I will share a quick guide on how I accomplished that.

1. If you've installed Python `3.9.5` via `pyenv`, uninstall it by running `uninstall` and then `rehash`

    ```bash
    $ pyenv uninstall 3.9.5
    $ pyenv rehash
    ```

1. Create a `symlink` at `~/.pyenv/versions`

    ```bash
    $ cd ~/.pyenv/versions
    $ ln -sfv /usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9 3.9.5
    $ ls -al
    total 0
    drwxr-xr-x  5 thecesrom  staff   160 Jun  8 11:08 .
    drwxr-xr-x  6 thecesrom  staff   192 Jun  8 10:58 ..
    drwxr-xr-x  7 thecesrom  staff   224 Jun  8 10:58 2.7.18
    lrwxr-xr-x  1 thecesrom  staff    75 Jun  8 11:08 3.9.5 -> /usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9
    ```

    {: .box-note}
    I chose `/usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9` instead of `/usr/local/Cellar/python@3.9/3.9.5` because I wanted to include the same folders (`bin`, `include`, `lib`, `share`) as my other Python installation (`2.7.18`), which I installed via `pyenv`.

1. Create `symlinks` for `idle`, `python` and `wheel` at the following location:

    ```bash
    $ cd /usr/local/Cellar/python@3.9/3.9.5/Frameworks/Python.framework/Versions/3.9/bin
    $ ln -sfv idle3.9 idle
    idle -> idle3.9
    $ ln -sfv python3 python
    python -> python3
    $ ln -sfv /usr/local/Cellar/python@3.9/3.9.5/bin/wheel3 wheel
    wheel -> /usr/local/Cellar/python@3.9/3.9.5/bin/wheel3
    ```

1. Once that's done, run `pyenv rehash` as recommended by `pyenv` whenever you install new versions

    ```bash
    $ pyenv rehash
    ```

1. Now, you're ready to verify all versions managed by `pyenv`, and select a `global`, `local`, or `shell` version

    ```bash
    $ pyenv versions
    * system (set by /Users/thecesrom/.pyenv/version)
      2.7.18
      3.9.5
    ```

1. If you want to set `3.9.5` as global, just run by running 
    
    ```bash
    $ pyenv global 3.9.5
    ```

1. Verify your selection by running
    
    ```bash
    $ pyenv versions
      system
      2.7.18
    * 3.9.5 (set by /Users/thecesrom/.pyenv/version)
    ```

1. And now when I run `python --version` I get the following output:

    ```bash
    $ python --version
    Python 3.9.5
    ```

And that's it! You've done it!

Thanks for reading and happy coding!