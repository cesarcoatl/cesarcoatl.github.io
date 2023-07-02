---
layout: post
title: Installing pyenv and managing Python versions in Ubuntu
subtitle: From macOS to Ubuntu, Pt. 2
tags:
- github
- linux
- macos
- pyenv
- python
- ubuntu
date: 2023-03-10 14:35 -0800
last-updated: 2023-07-01 14:56 -0700
---
Read **Part 1** [here]({{ site.url }}/2023/02/18/taking-a-ten-year-old-macbook-air-out-for-one-last-ride-with-ubuntu/).

Coming from macOS into Ubuntu required some "getting used to things". Like using `apt` (and
[`nala`](https://gitlab.com/volian/nala/-/wikis/Installation)), but still I installed Homebrew for Linux, as most of
the utilities are not up to date, and being more used to Homebrew it only made sense to me.

So, as usual, I started setting up my development environment, which requires Python 2.7 and Python 3.10, and for
managing multiple versions of Python `pyenv` is my go-to over similar tools like `asdf`.

Installing Python 2.7 was a breeze, but I did run into some issues when installing Python 3.10 mainly because `pyenv`'s
`python-build` script relies in Homebrew only when running on macOS, which might be an opportunity for creating a
feature request.

Even after getting most of the errors listed in `pyenv`'s
["Common build problems"](https://github.com/pyenv/pyenv/wiki/Common-build-problems) wiki page it dawned upon me that I
was approaching this the wrong way. If `pyenv`'s `python-build` script can't do it, I can build Python following
Python's guide!

Although I did notice some issues with the instructions in there, and so PR
[python/devguide#1052](https://github.com/python/devguide/pull/1052) was created.

And this is how one can build Python in Ubuntu and macOS with Homebrew.

Using Python's [Setup and Building](https://devguide.python.org/getting-started/setup-building/), and the script I
generated using [Build, compile and install Python from source code](https://www.build-python-from-source.com/) I had
success doing the following.

{: .box-error}
I'm sure that seasoned Linux and Ubuntu users might not even consider Homebrew as an alternative to their built-in
package manager, but `brew` is what I know.

First install all required packages with `brew`:

```sh
brew install pkg-config openssl@1.1 xz gdbm tcl-tk wget pyenv
```

And for building Python 3.10:

```sh
$ cd /tmp/
$ wget https://www.python.org/ftp/python/3.10.12/Python-3.10.12.tgz
$ tar xzf Python-3.10.12.tgz
$ cd Python-3.10.12
$ CPPFLAGS="-I$(brew --prefix gdbm)/include -I$(brew --prefix xz)/include" \
  LDFLAGS="-L$(brew --prefix gdbm)/lib -L$(brew --prefix xz)/lib" \
  ./configure --with-openssl="$(brew --prefix openssl@3.0)" \
              --with-tcltk-libs="$(pkg-config --libs tcl tk)" \
              --with-tcltk-includes="$(pkg-config --cflags tcl tk)" \
              --prefix="${PYENV_ROOT}/versions/3.10.12/"
$ make -s -j2
 CC='gcc' LDSHARED='gcc -shared -L/home/linuxbrew/.linuxbrew/opt/gdbm/lib -L/home/linuxbrew/.linuxbrew/opt/xz/lib   ' OPT='-g -Og -Wall'      _TCLTK_INCLUDES='-I/home/linuxbrew/.linuxbrew/Cellar/tcl-tk/8.6.13_3/include/tcl-tk -I/home/linuxbrew/.linuxbrew/opt/zlib/include' _TCLTK_LIBS='-L/home/linuxbrew/.linuxbrew/Cellar/tcl-tk/8.6.13_3/lib -ltk8.6 -ltkstub8.6 -ltcl8.6 -ltclstub8.6'  ./python -E ./setup.py -q build

The following modules found by detect_modules() in setup.py, have been
built by the Makefile instead, as configured by the Setup files:
_abc                  pwd                   time

renaming build/scripts-3.10/pydoc3 to build/scripts-3.10/pydoc3.10
renaming build/scripts-3.10/idle3 to build/scripts-3.10/idle3.10
renaming build/scripts-3.10/2to3 to build/scripts-3.10/2to3-3.10
$ make altinstall
```

Finally add symlinks:

```sh
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/2to3-3.10            ${PYENV_ROOT}/versions/3.10.12/bin/2to3
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/idle3.10             ${PYENV_ROOT}/versions/3.10.12/bin/idle
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/pip3.10              ${PYENV_ROOT}/versions/3.10.12/bin/pip
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/pip3.10              ${PYENV_ROOT}/versions/3.10.12/bin/pip3
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/pydoc3.10            ${PYENV_ROOT}/versions/3.10.12/bin/pydoc
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/python3.10           ${PYENV_ROOT}/versions/3.10.12/bin/python
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/python3.10-config    ${PYENV_ROOT}/versions/3.10.12/bin/python-config
ln -s ${PYENV_ROOT}/versions/3.10.12/bin/python3.10           ${PYENV_ROOT}/versions/3.10.12/bin/python3
```

And just like that I was ready to go.

Thanks for reading, and happy building!
