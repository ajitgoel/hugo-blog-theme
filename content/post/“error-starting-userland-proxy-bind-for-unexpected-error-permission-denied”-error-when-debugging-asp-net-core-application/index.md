---
title: "“Error starting userland proxy: Bind for unexpected error Permission
  denied” error when debugging ASP.Net Core application"
date: 2022-03-23T04:21:51.010Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
If you get an “`Error starting userland proxy: Bind for unexpected error Permission denied`” error, when debugging your ASP.Net Core application in Docker, then run the following command to find the excluded ports range, being used by different applications:

`netsh int ip show excludedportrange protocol=tcp`

I then change my launchSettings.json file to use a port that is not reserved

```
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "//localhost:50504",
      "sslPort": 44392
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "Scrubber": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "//localhost:5001;//localhost:5000"
    },
    "Docker": {
      "commandName": "Docker",
      "launchBrowser": true,
      "launchUrl": "{Scheme}://{ServiceHost}:{ServicePort}",
      "environmentVariables": {
        "ASPNETCORE_URLS": "//+:443;//+:80",
        "ASPNETCORE_HTTPS_PORT": "44393"
      },
       "httpPort": 54558,<=Change here
      "useSSL": true,
      "sslPort": 44393
    }
  }
}
```