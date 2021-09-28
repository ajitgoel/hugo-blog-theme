---
title: "C#: How to Use Dispose and Finalize"
date: 2021-09-23T03:02:49.247Z
draft: false
featured: false
tags:
  - C#
  - .Net
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
We can use the following code block to ensure that a class implements the IDisposable interface and cleans up both managed and unmanaged resources. 

```
public class Test : IDisposable
{
  private bool isDisposed = false;
  //Finalization is process by which the Garbage controller 
  //allows objects to clean up any unmanaged resources that 
  //they're holding, before actually destroying the instance. 
  //.Net framework translates the explicit destructor to create 
  //a call to Finalize(see  Listing 2). It allows the system 
  //run cleanup code during the finalize operation. 
  ~Test()
  {
    Dispose(false);
  }
  protected void Dispose(bool disposing)
  {
    //wrap the dispose method in try-catch to ensure that 
    //the memory is always reclaimed in case of an exception.
    try
    {		
      if(!isDisposed)
      {
        if (disposing)
        {
          //Code to dispose the managed resources held by 
          //the class
        }
        //Code to dispose the un-managed resources held by 
        //the class like a database connection instance
        isDisposed = true;
        base.Dispose(disposing);
      }
    }
    catch(Exception exception){}
  }
  //client application should call the Dispose method explicitly 
  //to free any unmanaged resources for which the object may 
  //be holding references or if it instantiates managed classes 
  //which in turn use such unmanaged resources
  public void Dispose()
  {
    Dispose(true);
    //This prevents the finalizer from running.
    GC.SuppressFinalize(this);
  }
}
```

**Listing 1:**

```
public interface IDisposable
{
  void Dispose();
}
```

**Listing 2:**

```
protected override void Finalize()
{
  try
  {
    //Necessary cleanup code
  }
  finally
  {
    base.Finalize();
  }
}
```