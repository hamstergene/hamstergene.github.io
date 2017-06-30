---
title: Preproxy 1.2
date: 2017-06-30T12:40:00+03:00
comments: true
---

A simple local proxy that forwards requests to a parent proxy with automatic autorization. Preproxy is basically an improved, sandboxed, Mac App Store-enabled version of Authoxy, which has been abandoned for quite some time and has started having problems ever since macOS Sierra came out.

[![Download Preproxy on Mac App Store](/img/macapps/mac-app-store-badge.svg)](https://itunes.apple.com/app/id1237580019)

Most open source apps are incapable of reading proxy password saved in System Preferences.
Hardcoding the password in `export http_proxy=...` is no good either.
Preproxy accepts proxy requests on localhost and forwards them to the given remote proxy, automatically taking care of proxy authorization using the  credentials stored in Keychain.

Now in version 1.2, NTLM and Kerberos authentication methods are supported. Even those apps that do read proxy credentials from macOS keychain often don't care to support advanced methods like Kerberos; with Preproxy it is now possible to have any app in the system to authorize at the proxy uniformly and without saving password anywhere at all. Kerberos is main authorization method in Active Directory domains, so most corporate network proxies should already support it.

Feature requests, suggestions and bug reports are welcome in the comments to this post.

[![Preproxy 1.2 NTLM Screenshot](/img/macapps/preproxy-1.2-screenshot-ntlm.png)](/img/macapps/preproxy-1.2-screenshot-ntlm.png)

[![Preproxy 1.2 Kerberos Screenshot](/img/macapps/preproxy-1.2-screenshot-krb.png)](/img/macapps/preproxy-1.2-screenshot-krb.png)

1.2: 2017-06-30
- added Kerberos support
- added NTLM support
- displaying latest request status for easier diagnostics

1.0.1: 2017-05-19
- the initial release.
