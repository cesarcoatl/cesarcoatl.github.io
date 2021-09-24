---
layout: post
title: The one-liner for updating PIP and all outdated packages
tags:
- macos
- pip
- powershell
- python
- quick-take
- windows
date: 2021-05-26 14:59 -0700
---
I spend most of my time working on a Windows machine for work-related projects, and for [some](https://github.com/thecesrom/Ignition){:target="_blank"} [personal](https://github.com/thecesrom/incendium){:target="_blank"} [projects](https://github.com/coatl-dev/homebrew-coatl-dev){:target="_blank"} I use my still trustworthy [MacBook Air (13-inch, Mid 2012)]({{ site.url }}/2020/09/16/creating-a-bootable-installer-for-macos){:target="_blank"}, and on both systems I spend some of my time working on the Terminal. On Windows I use the new, aptly named, [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701){:target="_blank"}, and on macOS I still use the Terminal; both with a certain level of customization, but more on that on another post.

Also, on both systems I have installed Python and some packages, which include black, flake8, isort, pipgrip, pip-autoremove, pre-commit, pydocstyle, and toml. And at some point I wondered if there was a way to update all outdated packages with one single command, so I did what every decent human being does: I asked the Internet.

Here are the top results provided by the most common search engines:

- [How to upgrade all Python packages with pip - Stack Overflow](https://stackoverflow.com/questions/2720014/how-to-upgrade-all-python-packages-with-pip){:target="_blank"}
- [How to Update All Python Packages - ActiveState](https://www.activestate.com/resources/quick-reads/how-to-update-all-python-packages/){:target="_blank"}
- [How to Update All of Your Python Packages With pip Using One Simple Command - dougie.io](https://dougie.io/answers/pip-update-all-packages/){:target="_blank"}
- [Upgrade all packages in Python using pip - Studytonight](https://www.studytonight.com/python-howtos/upgrade-all-packages-in-python-using-pip){:target="_blank"}

And all of them agree on a command like this on Linux, macOS, or Git Bash for Windows:

```bash
pip freeze | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install --upgrade
```

And for PowerShell:

```powershell
> pip freeze | %{$_.split('==')[0]} | %{pip install --upgrade $_}
```

And I agree that both work fine, but I wondered what would happen when `pip` itself was outdated and had to be upgraded.

Since `pip freeze` only outputs the installed packages in requirements format, but not `pip` itself, we must run `pip list --format=freeze` instead.

As for the recommended command to install or upgrade packages, I found that pip documentation suggests the following:

To [install](https://pip.pypa.io/en/stable/cli/pip_install/){:target="_blank"} a package:

```bash
python -m pip install black
```

And for [upgrading](https://pip.pypa.io/en/stable/installing/#upgrading-pip){:target="_blank"} a package:

```bash
python -m pip install --upgrade pip
```

So, considering all of the above, this is what I ended up using:

macOS:

```bash
python -m pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1 | xargs -n1 python -m pip install --upgrade
```

Windows:

```powershell
> python -m pip list --outdated --format=freeze | %{$_.split('==')[0]} | %{python -m pip install --upgrade $_}
```

But of course I don't type those commands each and every time I am curious about checking for upgrades, so I recommend creating an alias on macOS, and a function on Windows PowerShell.

Add this alias to your `.zshrc`:

```bash
alias pipu="python -m pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1 | xargs -n1 python -m pip install --upgrade"
```

Add this to your `$PROFILE`:

```powershell
function global:Update-Pip {
  $packages = python -m pip list --outdated --format=freeze
  foreach ($package in $packages) {
    $pkg = $package.split("==")[0]
    python -m pip install --upgrade $pkg
  }
}
```

And that should do the trick.

Thanks for reading.
