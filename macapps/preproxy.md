---
title: Preproxy 1.0.1
date: 2017-05-19T19:12:21+03:00
comments: true
---

A simple local proxy that forwards requests to a parent proxy with automatic autorization.

Most open source apps are incapable of reading proxy password saved in System Preferences.
Hardcoding the password in `export http_proxy=...` is no good either.
So I made this simple app that accepts proxy requests on localhost and forwards them to the given remote proxy, automatically handling authorization using the given credentials which are stored in Keychain.

Your suggestions and bug reports are welcome in the comments to this post.

[![Preproxy 1.0.1 Screenshot](/img/macapps/preproxy-1.0.1-screenshot.png)](/img/macapps/preproxy-1.0.1-screenshot.png)

Current version: 1.0.1, the initial release.
