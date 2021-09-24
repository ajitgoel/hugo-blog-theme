---
title: ".Net Core: Implement custom execution strategy"
date: 2021-09-24T04:58:27.528Z
draft: false
featured: false
tags:
  - .Net-Core
  - Entity-Framework-Core
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
We can implement database connection resiliency using an inbuilt or custom execution strategy using EFCore in ASP.NET Core. In one of the projects that I worked for, we had a requirement to integrate with a third party application which would auto-rotate and update the database password, every 90 days. \
We integrated the third party application in our application using a custom execution strategy as follows:

```
public void ConfigureServices(IServiceCollection services)
{
  services.AddControllers(); 
  services.AddDbContext<OrdersContext>(options =>
  {
    options.UseSqlServer(Configuration.GetConnectionString("OrdersDatabase"),
      sqlServerOptionsAction: sqlOptions =>
      {
        sqlOptions.ExecutionStrategy(x => new OrdersCustomExecutionStrategy(x));
      });
    });
}
```

The custom strategy class was derived using **ExecutionStrategy** class**.**

```
public class OrdersCustomExecutionStrategy : ExecutionStrategy
{
      public OrdersCustomExecutionStrategy(DbContext context) : base(context, ExecutionStrategy.DefaultMaxRetryCount, ExecutionStrategy.DefaultMaxDelay)
      { 
      } 
      public OrdersCustomExecutionStrategy(ExecutionStrategyDependencies dependencies) : base(dependencies, ExecutionStrategy.DefaultMaxRetryCount, ExecutionStrategy.DefaultMaxDelay)
      { 
      } 
      public OrdersCustomExecutionStrategy(DbContext context, int maxRetryCount, TimeSpan maxRetryDelay) :
          base(context, maxRetryCount, maxRetryDelay)
      { 
      } 
      protected override bool ShouldRetryOn(Exception exception)
      {
          //Add code here to retry creating the connection if there is a database exception
      }
  }
```