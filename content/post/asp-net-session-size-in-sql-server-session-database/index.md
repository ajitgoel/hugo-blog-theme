---
title: ASP.Net session size in SQL Server session database
date: 2022-03-23T03:36:14.225Z
draft: false
featured: false
tags:
  - ASP.Net
  - SQL-Server
  - Performance-Optimization
  - T-SQL
categories:
  - ASP.Net
  - SQL-Server
  - Performance-Optimization
  - T-SQL
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
**Problem:**\
We need to calculate ASP.Net Session size in a SQL server session database\
**\
Solution:**\
Use the following query(shamelessly stolen from [asp.net: checking session size in SQL Server ASPState DB](https://web.archive.org/web/20200713012133/http://blogs.msdn.microsoft.com/rextang/2008/01/12/asp-net-checking-session-size-in-sql-server-aspstate-db/)) to check the size of the session state data.

```
SELECT 
    a.SessionId, 
    SUBSTRING(a.SessionId, 25, 8) AS AppIDHex, 
    b.AppId AS AppIDDec, 
    b.AppName, 
    DATALENGTH(a.SessionItemLong) AS SessionSize 
FROM 
    dbo.ASPStateTempSessions AS a 
    LEFT OUTER JOIN 
    dbo.ASPStateTempApplications AS b 
    ON 
    SUBSTRING(a.SessionId, 25, 8) = 
    SUBSTRING(sys.fn_varbintohexstr(CONVERT(VarBinary,b.AppId)), 3, 8) 
WHERE 
    (DATALENGTH(a.SessionItemLong) > 0) 
ORDER BY SessionSize DESC
```