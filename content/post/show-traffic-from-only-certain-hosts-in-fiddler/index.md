---
title: Show traffic from only certain hosts in Fiddler
date: 2022-03-23T03:53:28.505Z
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
A lot of times when we “show traffic” in Fiddler, way too much traffic gets logged. To cut the “noise” out, we want to show traffic from only certain hosts. To do this:

* Check the “use filters” checkbox.
* In the “Show only the following hosts” textbox, type the names of the hosts separated by colon.
* If you have several similar hosts then you can use wildcards also.\
  Example: If you want to log the requests for drive.realpage.com, photos.realpage.com then instead of listing each one of them, you can use *.realpage.com. See below for an example