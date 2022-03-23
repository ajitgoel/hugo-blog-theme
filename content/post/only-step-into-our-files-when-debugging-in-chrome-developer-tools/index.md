---
title: Only step into our files when debugging in Chrome Developer tools.
date: 2022-03-23T04:04:47.693Z
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
A lot of times when we are debugging our front end javascript code using Chrome Developer tools,

![](https://web.archive.org/web/20200713012329im_/http://ajitgoel.net/wp-content/uploads/2017/03/030617_1956_Onlystepint1.png)

the system steps into other files. To avoid this, we can ask the debugger to ignore these files by “blackboxing” them. To do this, right click on the file and click “blackbox script”

![](https://web.archive.org/web/20200713012329im_/http://ajitgoel.net/wp-content/uploads/2017/03/030617_1956_Onlystepint2.png)

To get to the blackboxed files, go to the “settings” section by clicking on the symbol as shown below or press F1.

![](https://web.archive.org/web/20200713012329im_/http://ajitgoel.net/wp-content/uploads/2017/03/030617_1956_Onlystepint3.png)

The “blackboxing” tab shows all the files that have been blackboxed.

![](https://web.archive.org/web/20200713012329im_/http://ajitgoel.net/wp-content/uploads/2017/03/030617_1956_Onlystepint4.png)