---
title: Preproxy
comments: false
keywords: ["automatic", "authorization", "authentication", "parent proxy", "kerberos", "NTLM", "authoxy", "Mac", "macOS", "OS X"]
---

A local proxy app that forwards requests to a parent proxy with automatic autorization. Preproxy is basically an improved, sandboxed, Mac App Store-enabled version of Authoxy. I made it because Authoxy has been abandoned for quite some time and has started having problems since macOS Sierra came out.

[![Download Preproxy on Mac App Store](/img/macapps/mac-app-store-badge.svg)](https://itunes.apple.com/app/id1237580019)

Most open source apps are incapable of reading proxy password saved in System Preferences.
Hardcoding the password in `export http_proxy=...` is no good either.
Preproxy accepts proxy requests on localhost and forwards them to the given remote proxy, automatically taking care of proxy authorization using the  credentials stored in Keychain.

NTLM and Kerberos authentication methods are supported. Even those apps that do read proxy credentials from macOS keychain often don't care to support advanced methods like Kerberos; with Preproxy it is now possible to have any app in the system to authorize at the proxy uniformly and without saving password at all. Kerberos is main authorization method in Windows Active Directory domains, so most corporate network proxies should already support it.

Preproxy also supports Proxy Auto-Configuration scripts that are commonly used in Windows networks. This allows command line apps to automatically use the latest configuration provided by network administrators.

Support links:

  * Create a [Support Ticket](https://hamstergene.freshdesk.com/support/tickets/new) to report a bug, suggest a feature or ask a question (*ignore account creation email if you don't want to monitor ticket status*).

  * Check [Knowledge Base](https://hamstergene.freshdesk.com/support/solutions) for tips on using the app.

[![Preproxy 1.4 NTLM Screenshot](/img/macapps/preproxy-1.4-screenshot-ntlm.png)](/img/macapps/preproxy-1.4-screenshot-ntlm.png)

[![Preproxy 1.4 Kerberos Screenshot](/img/macapps/preproxy-1.4-screenshot-krb.png)](/img/macapps/preproxy-1.4-screenshot-krb.png)

Changes:

1.4:
- Proxy Auto-Configuration support (w/o SOCKS)

1.3:
- displaying latest requests
- logging failed requests into error log
- fixed problems connecting to some web servers running on localhost
- NOTE: Upgrading to High Sierra turns autostart at login off for unknown reason. You will have to open GUI and click the checkbox to reenable it.

1.2:
- added Kerberos support
- added NTLM support
- displaying latest request status for easier diagnostics

1.0.1:
- the initial release.
