---
title: ".Net Core: Async-await tips"
date: 2021-09-26T17:36:58.907Z
draft: true
featured: false
tags:
  - .Net
  - .Net-Core
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
1. Whenever a method in the .NET framework takes some time, or performs some disk or network IO, always use the asynchronous version of the method you can use instead. eg: use `Task.Delay` instead of `Thread.Sleep`,  `dbContext.SaveChangesAsync` instead of `dbContext.SaveChanges, fileStream.ReadAsync` instead of `fileStream.Read`. *These changes free up the thread-pool threads to do other more useful work, allowing your program to process a higher volume of requests.*

*2.*