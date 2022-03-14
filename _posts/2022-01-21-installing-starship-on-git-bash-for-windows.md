---
layout: post
title: Installing Starship on Git Bash for Windows
subtitle: No Windows Subsystem for Linux required
tags:
- bash
- git
- guide
- howto
- quick-take
- shell
- terminal
- windows
date: 2022-01-21 14:15 -0800
last-updated: 2022-03-14 07:59 -0700
---
Terminal-wise I feel more comfortable in macOS than Windows (Command Prompt or PowerShell), so it makes sense for me to say that the best feature of Git for Windows is definitely Git Bash.

>Git BASH
>
>Git for Windows provides a BASH emulation used to run Git from the command line. *NIX users should feel right at home, as the BASH emulation behaves just like the "git" command in LINUX and UNIX environments.

I heard of [Starship](https://starship.rs/) on episode [#345](https://talkpython.fm/episodes/show/345/10-tips-and-tools-for-developer-productivity) of the Talk Python To Me podcast, and while I'm already using [Powerlevel10k](https://github.com/romkatv/powerlevel10k) on macOS, and [Oh My Posh](https://ohmyposh.dev/) on Windows, I wanted to find a way to get more out of Git Bash and thought Starship would be a perfect candidate.

## Prerequisites

A [Nerd Font](https://www.nerdfonts.com/) installed and enabled in your terminal.

## Installation

If you were to try installing Starship with the suggested command, you would get an error like the following:

```bash
$ curl -sS https://starship.rs/install.sh | sh -s

  Configuration
> Bin directory: /usr/local/bin
> Platform:      pc-windows-msvc
> Arch:          x86_64

> Tarball URL: https://github.com/starship/starship/releases/latest/download/starship-x86_64-pc-windows-msvc.zip
? Install Starship latest to /usr/local/bin? [y/N] y
x Installation location /usr/local/bin does not appear to be a directory
> Make sure the location exists and is a directory, then try again.
install.sh [option]

Fetch and install the latest version of starship, if starship is already
installed it will be updated to the latest version.

Options
        -V, --verbose
                Enable verbose output for the installer

        -f, -y, --force, --yes
                Skip the confirmation prompt during installation

        -p, --platform
                Override the platform identified by the installer [default: pc-windows-msvc]

        -b, --bin-dir
                Override the bin installation directory [default: /usr/local/bin]

        -a, --arch
                Override the architecture identified by the installer [default: x86_64]

        -B, --base-url
                Override the base URL used for downloading releases [default: https://github.com/starship/starship/releases]

        -h, --help
                Display this help message
```

And this is because the `/usr` directory is locked. So the alternative, as suggested by the output above, is to override the bin installation directory.

I chose to do the following:

```bash
$ mkdir -p "$HOME/.local/bin"
$ curl -sS https://starship.rs/install.sh | sh -s -- --bin-dir "$HOME/.local/bin"

  Configuration
> Bin directory: /c/Users/thecesrom/.local/bin
> Platform:      pc-windows-msvc
> Arch:          x86_64

> Tarball URL: https://github.com/starship/starship/releases/latest/download/starship-x86_64-pc-windows-msvc.zip
? Install Starship latest to /c/Users/thecesrom/.local/bin? [y/N] y
! Bin directory /c/Users/thecesrom/.local/bin is not in your $PATH
> Installing Starship, please wait…
✓ Starship installed

> Please follow the steps for your shell to complete the installation:
  bash
  Add the following to the end of ~/.bashrc:

        eval "$(starship init bash)"
```

Add the Starship bin directory to your `$PATH` and update your `~/.bashrc` file as suggested, and you should be ready to go.

## Updating to latest

I am watching the Starship [repo](https://github.com/starship/starship) to be notified about releases, so for upgrading I created a simple bash file called `starship-upgrade.sh` sitting on my `$HOME` directory with the same command from the previous step. Like this:

```bash
#!/usr/bin/bash

curl -sS https://starship.rs/install.sh | sh -s -- --yes --bin-dir "$HOME/.local/bin"

```

And just by running that script I am able to update.

```bash
$ . "$HOME/starship-upgrade.sh"

  Configuration
> Bin directory: /c/Users/thecesrom/.local/bin
> Platform:      pc-windows-msvc
> Arch:          x86_64

> Tarball URL: https://github.com/starship/starship/releases/latest/download/starship-x86_64-pc-windows-msvc.zip
? Install Starship latest to /c/Users/thecesrom/.local/bin? [y/N] y
> Installing Starship, please wait…
✓ Starship installed
```

~~Even though I have added the custom Starship bin directory to my `$PATH` I am warned it is not found, but in my experience this can be dismissed.~~

## Update #1: {{ "2022-03-09 14:32 -0800" | date: site.date_format }}

Since this post was first published I have learned that the issue I initially reported was partially my mistake, as I was passing the path to the `bin-dir` with a trailing forward slash (`/`) at the end, and in my `$PATH` the directory existed without the trailing forward slash. Ref: [starship#1310](https://github.com/starship/starship/issues/1310), [starship#1341](https://github.com/starship/starship/issues/1341), and [starship#3486](https://github.com/starship/starship/discussions/3486) which resulted in the creation of [starship#3493](https://github.com/starship/starship/issues/3493) by [chipbuster](https://github.com/chipbuster).

So, in order to fix any unintentional mistake like this in the future, I have created a PR ([starship#3723](https://github.com/starship/starship/pull/3723)) to prevent false-negatives in case another user makes the same "mistake".

## Update #2: {{ "2022-03-11 14:12 -0800" | date: site.date_format }}

PR ([starship#3723](https://github.com/starship/starship/pull/3723)) was merged and it has been shipped with version [`1.4.2`](https://github.com/starship/starship/tree/v1.4.2).

Thanks for reading!

## Other interesting posts

- Alan P. Barber. ["How To Customize The Git For Windows Bash Shell Prompt"](https://alanbarber.com/post/how-to-customize-the-git-for-windows-bash-shell-prompt/).
- Changhui Xu. ["How to Customize the Git Bash Shell Prompt"](https://blog.devgenius.io/how-to-customize-the-git-bash-shell-prompt-336f6aefcf3f).
- Blikoor. ["Customize Git Bash shell"](https://dev.to/blikoor/customize-git-bash-shell-498l)
- Guilherme_Konan. ["Customizing Git Bash in Windows"](https://dev.to/gmkonan/customizing-git-bash-in-windows-2al3).
