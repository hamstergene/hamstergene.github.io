---
title: "How to install Xcode 3 on Mountain Lion"
redirect_from:
    - /blog/2012-11-13-xcode3-mountain-lion/
    - /post/install-xcode3-osx-mountain-lion/
---

It is basically the same trick as with Lion, except that it renders your system unbootable. Oops. Be prepared to boot into single-user mode and remove the incompatible kernel extensions.

Note that Interface Builder plugins from iOS platform won't load, so you can't use iOS SDK with Xcode 3 anymore.

0. Make sure you know how to boot into single-user mode (*hit Cmd+S on boot*).

0. Save a repair script somewhere (e.g. `/Users/my-user-name/repair-script`):

    ``` sh 
    fsck -fy
    mount -uw /
    rm -rf /System/Library/Extensions/AppleProfileFamily.kext
    rm -rf /System/Library/Extensions/CHUD*.kext
    ```

0. If Xcode 3 is on a DMG, mount it.

0. Open **Terminal** and `cd` to whereever the installation package is.

    ``` console
    $ cd "/Volumes/Xcode and iOS SDK"
    $ ls -1
    About Xcode and iOS SDK.pdf
    Packages
    Xcode and iOS SDK.mpkg
    ```

0. Run the installation package like this:

    ``` console
    $ COMMAND_LINE_INSTALL=1 open "Xcode and iOS SDK.mpkg"
    ```

0. Uncheck iOS SDK option, or the Interface Builder will not be able to start.

0. You get kernel panic and your **system won't boot anymore**. Hooray. Turn the power off.

0. Turn the power back on and hit Cmd+S once gray screen appears, then run the repair script:

    ```console
    # . /Users/my-user-name/repair-script
    # exit
    ```

Done.

