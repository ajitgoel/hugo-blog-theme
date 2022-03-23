---
title: "Fix for ‘’Npgsql.PostgresException: syntax error at or near ‘[‘”"
date: 2022-03-23T04:27:26.288Z
draft: false
featured: false
tags:
  - Postgres
  - C#
  - ASP.Net-Core
  - SQL-Server
categories:
  - Postgres
  - C#
  - ASP.Net-Core
  - SQL-Server
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
We decided to change our database from SQL server to Postgres in our application. When I tried to apply the database migrations to the database it failed with a\
`Npgsql.PostgresException (0x80004005): 42601: syntax error at or near "[" error.`\
On further analyzing the issue I found that this issue occurs as the migrations were created on SQL Server and contain SQL Server-specific code.

To fix this issue, I\
a. Removed the Migrations folder in your application.\
b. Ran “Add-Migration Init” in the package manager console\
c. Ran “Update-Database”, to flush the SQL Server predefined context.