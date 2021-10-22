---
layout: post
title: Creating a bootable installer for macOS
subtitle: Breathing new life into my MacBook Air one last time
cover-img: /assets/img/macOS-Catalina-sunset-1440x900.jpg
thumbnail-img: /assets/img/macOS-Catalina-230x0w.png
tags: [guide, macos]
date: 2020-09-16 13:23 -0700
last-updated: 2021-10-12 13:08 -0700
---
Since November 2012, I've been a proud owner of a [MacBook Air (13-inch, Mid 2012)](https://support.apple.com/kb/SP670?locale=en_US){:target="_blank"}, and it is with sadness that I must accept it has reached its end-of-life with the upcoming release of [Big Sur](https://www.apple.com/macos/big-sur-preview/){:target="_blank"}.

It came with [Mountain Lion (OS X 10.8)](https://en.wikipedia.org/wiki/OS_X_Mountain_Lion){:target="_blank"}, and with each upgrade, I always perform a clean install because I prefer starting without clutter.

There are many guides on how to create a bootable installer, but for doing this I'll follow the instructions from Apple on [how to create a bootable installer for macOS](https://support.apple.com/en-us/HT201372){:target="_blank"}, along with [how to erase a disk for Mac](https://support.apple.com/en-us/HT208496){:target="_blank"}, since I will do a clean installation.

I will use a 16 GB USB drive labeled "INSTALLER" for creating my bootable drive.

And these are the instructions:

1. Download [macOS Catalina](https://apps.apple.com/us/app/macos-catalina/id1466841314?mt=12){:target="_blank"} from the Mac App Store
1. Once downloaded run the following command:

    ```bash
    sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/INSTALLER
    ```

1. Restart your Mac and press the alt-option key as soon as it restarts, so you are prompted to select a Startup Disk
1. Select the **macOS Catalina Installer**
1. Select **Disk Utility** and click **Continue**
1. Choose **View > Show All Devices** from the menu bar, and select the storage device, **APPLE SSD TS128E Media** in my case
1. Click **Erase** and assign these settings:
    1. **Name:** APPLE SSD
    1. **Format:** APFS
    1. **Scheme:** GUID Partition Map
1. Click **Erase**
1. When done, quit **Disk Utility**
1. Select **Install macOS** from **macOS Utilities**, and click **Continue**
1. To set up the installation of macOS Catalina, click **Continue**
1. **Agree** to the terms of the software license agreement
1. Click **Agree** on the next prompt
1. Select **APPLE SSD** and click **Install**
1. Sit back and relax

Once the installation is done, you'll be asked to configure your Mac.

And that's it! You'll have a "brand new" Mac!
