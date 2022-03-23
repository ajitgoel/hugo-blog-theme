---
title: Filter network requests when debugging.
date: 2022-03-23T04:01:14.534Z
draft: false
featured: false
tags:
  - Debugging
  - Chrome
categories:
  - Debugging
  - Chrome
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
When you are debugging, you would like to filter out the various requests so you can concentrate on what’s important eg: filter image or css file requests. Google has finally listened to the developer community and has added support for “negative filters” in Google Chrome-version 42.\
Here is the text filter I use when debugging where the “-” sign before each file extension keyword filters the requests. The requests then do not show in the “Network” tab

`-png -svg -aspx -axd -jpg -css -ttf -js -gif -ico -woff2`

![](https://web.archive.org/web/20200713012329im_/http://ajitgoel.net/wp-content/uploads/2017/08/2017-08-14-08_55_09-Clipboard.jpg)

When you are adding the text filter make sure that the “Regex” checkbox is unchecked.

You can further filter the requests by using the following supported keywords![](https://web.archive.org/web/20200713012329im_/http://ajitgoel.net/wp-content/uploads/2017/08/081317_0228_Filternetwo2.png)

eg:

```
-mime-type:image/png,-larger-than:20k,-domain:yoursite.com,-status-code:404
```