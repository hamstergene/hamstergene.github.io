---
title: Prevent Mac sleep when `make` is running
redirect_from:
    - /blog/2013-01-22-make-nosleep/
    - /post/make-nosleep/
---

Ever came back in the morning only to find 40-minute `make` is still running? OS X 10.8 “Mountain Lion” now includes a nice command `/usr/bin/caffeinate` which will prevent system from going to sleep while a program is running.

So if that ever happened to you, you might want to start using this:

```sh
alias make='caffeinate make'
```

