---
title: Configure fiddler to not capture requests for certain domains
date: 2022-03-23T03:47:43.711Z
draft: false
featured: false
tags:
  - Fiddler
categories:
  - Fiddler
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
When you are debugging using fiddler, fiddler generally captures requests from all the domains. An easy way to not capture requests from certain domains is to use the build in “filters” tab in fiddler. Use the “Hide the following hosts” dropdown and key in the list of all the domains that you want to hide.

![](https://web.archive.org/web/20200713012118im_/http://ajitgoelwebsite.000webhostapp.com/wp-content/uploads/2017/03/032417_1827_Configurefi1.png)

If you want more granular control then you can override the `OnBeforeRequest `method in Fiddler’s script using [Fiddler’s Script Editor](https://web.archive.org/web/20200713012118/http://www.telerik.com/download/fiddler/fiddlerscript-editor).

![](https://web.archive.org/web/20200713012118im_/http://ajitgoelwebsite.000webhostapp.com/wp-content/uploads/2017/03/032417_1827_Configurefi2.png)

```
static function OnBeforeRequest(oSession: Session) {       
        if (
            oSession.HostnameIs("www.google.com") ||
            oSession.HostnameIs("inbox.google.com") ||
            oSession.HostnameIs("13.client-channel.google.com") ||
            oSession.HostnameIs("d.docs.live.net") ||
            oSession.HostnameIs("slack.com") ||
            oSession.HostnameIs("notifications.google.com") ||
            oSession.HostnameIs("play.google.com") ||
            oSession.HostnameIs("cello.client-channel.google.com") ||
            oSession.HostnameIs("lp-push-server-777.lastpass.com") ||
            oSession.HostnameIs("ssl.gstatic.com") ||
            oSession.HostnameIs("clients4.google.com") ||
            oSession.HostnameIs("clients1.google.com")
        )
        { 
            oSession["ui-hide"] = "hidden";   
        }
        //code omitted for brevity
}
```