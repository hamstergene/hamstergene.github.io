---
title: How to install Xcode 3 on Lion
redirect_from:
    - /blog/2012-04-03-xcode3-lion/
    - /post/install-xcode3-osx-lion/
---

The installer does not allow it, so here is how to trick it.

> Note: for OS X Mountain Lion, see [How to install Xcode 3 on Mountain Lion](/post/install-xcode3-osx-mountain-lion/).

0. If Xcode 3 is on a DMG, mount it.

0. Open **Terminal** and `cd` to whereever the installation package is.

    ``` console
    $ cd "/Volumes/Xcode and iOS SDK"
    $ ls -1
    About Xcode and iOS SDK.pdf
    Packages
    Xcode and iOS SDK.mpkg
    ```

0. Open the installation package like this:

    ``` console
    $ COMMAND_LINE_INSTALL=1 open "Xcode and iOS SDK.mpkg"
    ```

0. Even though the installer ends with “Install failed” message, everything is installed and works properly.

It works because the installation package does this check for whatever reason:

```js
    // Xcode and iOS SDK.mpkg/Contents/iPhoneSDKSL.dist
    function isDevToolsCompatible()
    {
        if ( system.env.COMMAND_LINE_INSTALL == 1 ) { return true; }
        if( system.compareVersions(my.target.systemVersion.ProductVersion, '10.6.6') >= 0 
            && system.compareVersions(my.target.systemVersion.ProductVersion, '10.7') < 0 ) {
                
                return true;
             }
        return false;
    }
```
