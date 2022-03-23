---
title: Configure error logging using Serilog.Sinks.Postgres and appSettings.json
date: 2022-03-23T04:25:05.353Z
draft: false
featured: false
tags:
  - Serilog
  - ASP.Net-Core
  - PostGres
  - C#
categories:
  - Serilog
  - ASP.Net-Core
  - PostGres
  - C#
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
Install nuget packages\
`Serilog.Sinks.PostgreSQL
Microsoft.Extensions.Configuration.Json`

**Program.cs:**

```
public class Program {
  public static void Main(string[] args) {
    var path = Directory.GetCurrentDirectory();
    var environmentName = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") ?? "Production";
    var configuration = new ConfigurationBuilder()
      .SetBasePath(path)
      .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
      .AddJsonFile($"appsettings.{environmentName}.json", optional: true, reloadOnChange: true)
      .AddEnvironmentVariables()
      .Build();
    Log.Logger = new LoggerConfiguration().
    Enrich.FromLogContext().
    ReadFrom.Configuration(configuration).
    CreateLogger();
    try {
      var iWebHost = CreateWebHostBuilder(args).Build();
      Log.Information("Application starting");
      iWebHost.Run();
    } catch (Exception exception) {
      Log.Error(exception.ToString());
    } finally {
      Log.CloseAndFlush();
    }
  }
  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
    .UseStartup()
    .UseSerilog();
}
```

**appsettings.json:**

```
"Serilog": {
  "Using": ["Serilog.Sinks.PostgreSQL"],
  "MinimumLevel": "Warning",
  "WriteTo": [{
    "Name": "PostgreSQL",
    "Args": {
      "connectionString": "",
      "tableName": "Logs",
      "needAutoCreateTable": true,
      "batchPostingLimit": 1
    }
  }]
}
```