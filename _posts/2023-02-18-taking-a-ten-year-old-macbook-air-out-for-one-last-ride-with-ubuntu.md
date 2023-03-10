---
layout: post
title: Taking a ten-year-old MacBook Air out for one last ride with Ubuntu
subtitle: From macOS to Ubuntu, Pt. 1
tags:
- linux
- macos
- python
- ubuntu
date: 2023-02-18 12:42 -0800
---
When I started this blog I wrote about reinstalling macOS 10.15, better know as Catalina, on my Mid 2012 13" MacBook Air, which in its 10 years with me all I've done to it is replace the battery, and more recently I had to get a new power adapter. Not bad if you ask me.

I remember I had this Fujitsu laptop, a LifeBook C Series, purchased back in the Summer of 2003 that was falling apart by 2007.

Now that macOS Ventura has been released Catalina is no longer supported by both Apple and Homebrew, and while I did try MacPorts for a while I found myself missing having the ability to install `pyenv` and found MacPorts portfiles harder to maintain. Despite MacPorts supporting older macOS versions, some "ports" are not up to date. See three PRs I created: [`bash`](https://github.com/macports/macports-ports/pull/16622){:target="_blank"}, [`nano`](https://github.com/macports/macports-ports/pull/16698){:target="_blank"}, and [`tree`](https://github.com/macports/macports-ports/pull/16695){:target="_blank"}.

So, I decided to install a Linux distro and [Ubuntu 22.10 (Kinetic Kudu)](https://releases.ubuntu.com/kinetic/){:target="_blank"} was the one. I installed [belenaEtcher](https://www.balena.io/etcher){:target="_blank"} and did a clean install.

Side note, I did try installing Fedora 37, but the installer failed to install. See [here](https://ask.fedoraproject.org/t/bootloader-installer-fails-for-fedora-37-on-macbook-pro-2015-manjaro-installs-fine-on-the-same-machine/28968){:target="_blank"}. I did install Fedora 36 and then upgrade to 37, which worked, but went back to Ubuntu before my MacBook Air died on December 16, 2022.

And even though I no longer have my beloved Mid 2012 13" MacBook Air, it still gave me the oportunity to share with you the following tweaks, which I will list below.

### Disable IPv6

I had some issues connecting my Google account and installing Python packages was taking longer than usual. And apparently the issue was related with IPv6.

As suggested in [this article](https://itsfoss.com/disable-ipv6-ubuntu-linux/){:target="_blank"}, I first tried modifying `/etc/sysctl.conf` but it did not work. What did work was modifying `/etc/default/grub`.

```sh
$ diff -u grub grub.patch 
--- grub		2022-11-23 13:31:37.741913789 -0800
+++ grub.patch	2022-11-23 13:31:24.578158824 -0800
@@ -7,7 +7,7 @@
 GRUB_TIMEOUT_STYLE=hidden
 GRUB_TIMEOUT=0
 GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
-GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
+GRUB_CMDLINE_LINUX_DEFAULT="quiet splash ipv6.disable=1"
 GRUB_CMDLINE_LINUX=""
 
 # Uncomment to enable BadRAM filtering, modify to suit your needs
```

### Disable Wayland by modifying `/etc/gdm3/custom.conf`

I installed Microsoft Teams, but I couldn't share my screen. After a quick search I found that disabling [Wayland](https://wayland.freedesktop.org/){:target="_blank"} should do it, and it did.

```sh
$ diff -u custom.conf custom.conf.patch
--- custom.conf	2022-11-23 14:43:52.825219460 -0800
+++ custom.conf.patch	2022-11-23 14:43:42.741407162 -0800
@@ -4,7 +4,7 @@
 
 [daemon]
 # Uncomment the line below to force the login screen to use Xorg
-# WaylandEnable=false
+WaylandEnable=false
 
 # Enabling automatic login
 #  AutomaticLoginEnable = true

```

### Run `sudo` inside kitty

Last, but not least, I wanted to try a good terminal emulator, and I found [`kitty`](https://sw.kovidgoyal.net/kitty/){:target="_blank"}; which is my terminal emulator of choice on macOs, too.

The only issue I had was that I could not run commands with elevated privileges.

As recommended [here](https://sw.kovidgoyal.net/kitty/faq/#keys-such-as-arrow-keys-backspace-delete-home-end-etc-do-not-work-when-using-su-or-sudo){:target="_blank"}, running `sudo visudo` on Terminal and adding the following lines did the trick.

```text
# kitty
Defaults	env_keep += "TERM TERMINFO"
```

As always, thanks for reading.
