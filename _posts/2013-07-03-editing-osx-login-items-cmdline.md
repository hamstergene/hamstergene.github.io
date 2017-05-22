---
title: "Manipulating OS X login items from command line"
redirect_from:
    - /blog/2013-07-04-os-x-login-items-command-line/
    - /post/editing-osx-login-items-cmdline/
---

I will leave this post as a reference for those who need their installer postflight scripts (and uninstallers) to manipulate login items in OS X.

### Add login item

    osascript -e 'tell application "System Events" to make login item at end with properties {path:"/path/to/itemname.app", hidden:true, name:"Customized Name"}'

The `name` property can be omitted.

The `hidden` property is useful for applications that do not have `LSUIElement` set, it causes them to start with all windows hidden (what `Cmd+H` shortcut does). Please be good to users and set it to `true` if you are adding non-LSUIElement app into login items.

**NOTE:** If `path` is symlink, OS X will resolve it, and you will not be able to delete the item later by that path. You can use `name` property to locate the item.

### Delete login item

By path:

    osascript -e 'tell application "System Events" to delete every login item whose path is "/path/to/itemname.app"'

By name:

    osascript -e 'tell application "System Events" to delete every login item whose name is "Customized Name"'

The `every login item` may be changed to `first login item` or `last login item` if you are afraid of deleting too much.

### Get list of all login items

    osascript -e 'tell application "System Events" to get every login item'

Add `whose property is ...` clause to narrow down the search.


