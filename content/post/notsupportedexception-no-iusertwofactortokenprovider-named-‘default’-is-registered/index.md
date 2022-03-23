---
title: "NotSupportedException: No IUserTwoFactorTokenProvider named ‘Default’ is
  registered."
date: 2022-03-23T04:29:29.069Z
draft: false
featured: false
tags:
  - ASP.Net-Core
  - C#
  - Azure
categories:
  - ASP.Net-Core
  - C#
  - Azure
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
If you have changed the AddDefaultIdentity in the Startup.cs file to AddIdentity and you receive a \
`An unhandled exception occurred while processing the request.`\
`NotSupportedException: No IUserTwoFactorTokenProvider<TUser> named ‘Default’ is registered.`\
`Microsoft.AspNetCore.Identity.UserManager<TUser>.GenerateUserTokenAsync(TUser user, string tokenProvider, string purpose)` error when registering a user, there is a easy fix. \
\
You need to a add a [AddDefaultTokenProviders](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.2)() when adding identity() to services.

`public void ConfigureServices(IServiceCollection services)`\
`  {`\
`  ………………`\
`  services.AddIdentity<User, UserRole>()`\
`   .AddEntityFrameworkStores<ApplicationDbContext>()`\
`   .AddDefaultTokenProviders();`\
`…………………….`