---
title: "Null Object Pattern : Avoid accidentally throwing a
  NullReferenceException and null object checking code"
date: 2021-09-26T20:14:47.398Z
draft: false
featured: false
tags:
  - C#
  - Design-Patterns
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**Null Object Pattern:** Avoid accidentally throwing a NullReferenceException and null object checking code by using the **Null Object** design pattern.

```
using System;
using System.Linq;
public static class StringExtensions 
{ 
    public static int GetSafeLength(this string valueOrNull) 
    { 
        return (valueOrNull ?? string.Empty).Length; 
    }
}
public static class Program 
{
    static readonly string[] strings = new [] { "ajit", "goel", null, "kumar" };
    public static void Main(string[] args) 
    {
      //no need to do any checks here
      var query = from text in strings select text.GetSafeLength();
      Console.WriteLine(query.Sum());
    }
}
```