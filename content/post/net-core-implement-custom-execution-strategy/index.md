---
title: "Entity framework core: Implement custom execution strategy"
date: 2021-09-24T04:58:27.528Z
draft: false
featured: false
tags:
  - .Net-Core
  - Entity-Framework-Core
categories:
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
        sqlOptions.ExecutionStrategy(x => 
          new CustomExecutionStrategy(x, 10, TimeSpan.FromSeconds(10)));
        sqlOptions.CommandTimeout(_conninfo.ConmandTimeoutInSeconds);
      });
    });
}
```

The custom strategy class was derived using **ExecutionStrategy** class.

```
///<summary>
/// The purpose of this class is to wrap the Executionstrategy for the purpose of 
/// detecting an exception, primarily for rotating passwords, where the password has 
/// expired/changed since the last use
///</summary>
public class CustomExecutionStrategy : ExecutionStrategy
{
    private readonly ExecutionstrategyDependencies executionStrategyDependencies;
	public CustomExecutionStrategy(ExecutionStrategyDependencies executionStrategyDependencies, int maxRetryCount, Timespan maxRetryDelay) : 
		base(executionStrategyDependencies, maxRetryCount, maxRetryDelay)
	{
		executionStrategyDependencies = executionStrategyDependencies;
	}
	protected override bool shouldRetryon(Exception exception)
	{
		bool retry = false;
		//If we get a specific sqLexception, then we're going to assume it is 
        //due to the password expiring, so we'll attempt to reset the connection 
        //string, forcing the secrets container to refresh its password
		if(exception.GetType() == typeof (Microsoft.Data.SqlClient.Sqlexception))
		{
			//get the new connection string from the third party library into 
            //connectionstring variable
			executionStrategyDependencies.currentContext.Context.Database.SetConnectionstring(connectionstring);
			retry=true;
		}
		return retry;
	}
  }
```