---
title: Mock ASP.Net internals when unit testing ASP.Net MVC.
date: 2022-03-23T03:45:13.665Z
draft: false
featured: false
tags:
  - Unit-Testing
  - C#
categories:
  - Unit-Testing
  - C#
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**Problem:**\
ASP.NET MVC controller context depends on asp.net internals. How should these be mocked for unit tests?\
**\
Solution:**\
Here is a sample unit test class using MsTest and Moq which mocks HttpRequest and HttpResponse objects. Controller action get value from request and sets http header in response objects. Other http context objects could be mocked up in similar way.

```
using System.Web;
using System.Web.Mvc;
using System.Web.Routing;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
 
[TestClass]
public class SampleControllerTest
{
    protected Mock<HttpContextBase> httpContextBaseMock;
    protected Mock<HttpRequestBase> httpRequestBaseMock;
    protected Mock<HttpResponseBase> httpResponseBaseMock;
 
    [TestInitialize]
    public void TestInitialize()
    {
        this.httpContextBaseMock = new Mock<HttpContextBase>();
        this.httpRequestBaseMock = new Mock<HttpRequestBase>();
        this.httpResponseBaseMock = new Mock<HttpResponseBase>();
        this.httpContextBaseMock.SetupGet(x => x.Request).Returns(
        this.httpRequestBaseMock.Object);
        this.httpContextBaseMock.SetupGet(x => x.Response).Returns(
        this.httpResponseBaseMock.Object);
    }
 
    protected SampleController SetupController()
    {
        var controller = new SampleController();
        var routeData = new RouteData();
        controller.ControllerContext = new ControllerContext(
        this.httpContextBaseMock.Object, routeData, controller);
        controller.Url = new UrlHelper(
        new RequestContext(this.httpContextBaseMock.Object, routeData),
        new RouteCollection());
        return controller;
    }
 
    [TestMethod]
    public void IndexTest()
    {
        this.httpRequestBaseMock.Setup(x => x["x"]).Returns("1");
        this.httpResponseBaseMock.Setup(x => x.AddHeader("name", "value"));
 
        var controller = SetupController();
        var result = controller.Index();
        Assert.AreEqual("1", result.Content);
 
        this.httpRequestBaseMock.VerifyAll();
        this.httpResponseBaseMock.VerifyAll();
    }
}
 
public class SampleController : Controller
{
    public ContentResult Index()
    {
        var x = Request["x"];
        Response.AddHeader("name", "value");
        return Content(x);
    }
}
```