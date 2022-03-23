---
title: Distinct on multiple properties in a collection
date: 2022-03-23T04:43:00.119Z
draft: false
featured: false
tags:
  - MoreLinq
  - C#
categories:
  - MoreLinq
  - C#
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**Problem:**\
How do I get distinct members of a collection, based on multiple properties?

**Solution:**\
Use [MoreLinq](http://www.nuget.org/packages/morelinq/) to get distinct members of a collection, based on multiple properties.\
eg: For our application, each member in the household was supposed to have a unique combination of driver’s licence number and driver’s state

`var query = household.DistinctBy(
            counter => new { counter.DriverLicenceNumber, counter.DriverLicenceState });`