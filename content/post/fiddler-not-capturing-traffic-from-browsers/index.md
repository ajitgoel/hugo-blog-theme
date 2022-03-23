---
title: Fiddler not capturing traffic from browsers
date: 2022-03-23T03:52:17.410Z
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
Sometimes when you are trying to capture browser traffic for debugging, Fiddler does not capture the same.\
One of the options to try is to make sure that the “Use Filters” checkbox is unchecked in the “Filters” tab in Fiddler.

![](https://web.archive.org/web/20200713012118im_/http://ajitgoel.net/wp-content/uploads/2017/03/031317_1750_Fiddlernotc1.png)

You can also troubleshoot filters to see which filters are hiding your traffic.

![](https://web.archive.org/web/20200713012118im_/http://ajitgoel.net/wp-content/uploads/2017/03/031317_1750_Fiddlernotc2.png)

![](https://web.archive.org/web/20200713012118im_/http://ajitgoel.net/wp-content/uploads/2017/03/031317_1750_Fiddlernotc3.png)

If this does not work then you might have an extension (Like `ZenMate `or `TunnelBear`) installed that might have messed with your traffic. You might need to disable those extensions to ensure that Fiddler captures all the traffic from the browser.