---
title: Configuring Azure app services logging for ASP.Net Core application
date: 2022-03-23T04:33:00.809Z
draft: false
featured: false
tags:
  - Azure
  - ASP.Net-Core
  - C#
categories:
  - Azure
  - ASP.Net-Core
  - C#
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
If you are using Azure to host your ASP.Net Core website and don’t want to spend time configuring logging in your web application, then you can use Azure App services logging. Here are the changes that you need to make in your web application to configure logging.

***Startup.cs:***

```
using Microsoft.Extensions.Logging.AzureAppServices;
public class Startup {
  public void ConfigureServices(IServiceCollection services) {
    services.Configure < AzureFileLoggerOptions >
      (Configuration.GetSection("AzureAppServicesLogging"));
  }
}
```

***AppSettings.json:***

```
{
  "AzureAppServicesLogging": {
    "BlobName": "Log.txt"
  }
}
```