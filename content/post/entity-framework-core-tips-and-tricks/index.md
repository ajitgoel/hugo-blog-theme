---
title: "Entity Framework Core: Tips and Tricks"
date: 2021-10-03T14:10:10.654Z
draft: false
featured: false
tags:
  - C#
  - Entity-Framework-Core
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**Executing client-side functions in LINQ queries**\
When Entity Framework Core analyzes the LINQ expression it is given, it goes through all\
its nodes and eventually finds a method call that it does not know how to translate to the\
underlying data source. In this case, it does the translation exactly as if the method call wasn't there, and, if the property (or properties) is present in the projected results, it then calls the method on the retrieved results.

```
var blogs = dbContext.Blogs.Where(x=>x.Name.ComputeHash() == 0);

public static class StringExtensions
{
  public static int ComputeHash(this string str)
  {
    var hash = 0;
    foreach (var ch in str)
    {
      hash += (int) ch;
    }
    return hash;
  }
}
```

**Mixing SQL with LINQ queries**\
If you look at the code below, all of the filtering and projection is taking place in the database, not in memory. We can also pass parameters to the FromSql method, which will then be passed to the SQL. Eg, imagine your stored procedure looked like this:

```
CREATE PROCEDURE dbo.GetBlogs
(
  @creationdate DATETIME = NULL
)
as
.................
```

You could pass a parameter to match @creationdate as this:

```
var blogs = dbContext.Blogs.FromSql("EXEC dbo.GetBlogs @p0", 
  DateTime.Today).Where(x => x.Name.Contains("Development")).
  Select(x => x.Name).ToList();
```

\
So, each parameter will look like @p0, @p1, and so on.