---
title: Configure Serilog SQL server sink logging for ASP.Net Core application
date: 2022-03-23T04:16:16.713Z
draft: false
featured: false
tags:
  - Serilog
  - C#
  - ASP.Net-Core
  - SQL-Server
categories:
  - Serilog
  - C#
  - ASP.Net-Core
  - SQL-Server
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
Here are the changes that you need to make in your ASP.Net Core web application to configure Serilog logging with SQL server sink.

a. Install the following libraries from Nuget, using the Nuget Package Manager\
`Serilog.AspNetCore`\
`Serilog.Extensions.Logging`\
`Serilog.Settings.AppSettings`\
`Serilog.Settings.Configuration`\
`Serilog.Sinks.MSSqlServer`

b. ***Program.cs:***

```
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Serilog;
using System;
using System.IO;

namespace Scrubber {
  public class Program {
    public static void Main(string[] args) {
      var path = Directory.GetCurrentDirectory();
      var environmentName = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") ?? "Production";
      var configuration = new ConfigurationBuilder()
        .SetBasePath(path)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{environmentName}.json", optional: false, reloadOnChange: true)
        .Build();
      Log.Logger = new LoggerConfiguration()
        .Enrich.FromLogContext()
        .ReadFrom.Configuration(configuration)
        .CreateLogger();
      try {
        WebHost.CreateDefaultBuilder(args)
          .UseStartup()
          .UseSerilog()
          .Build()
          .Run();
      } finally {
        Log.CloseAndFlush();
      }
    }
  }
}
```

***AppSettings.json:***

```
{
  "Serilog": {
    "Using": ["Serilog.Sinks.MSSqlServer"],
    "MinimumLevel": "Information",
    "WriteTo": [{
      "Name": "MSSqlServer",
      "Args": {
        "connectionString": "YourSQLServerConnectionString",
        "tableName": "Logs",
        "autoCreateSqlTable": true
      }
    }]
  }
}
```