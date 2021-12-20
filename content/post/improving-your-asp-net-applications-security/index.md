---
title: Improving your ASP.Net application's security.
date: 2021-12-19T23:34:33.014Z
draft: false
featured: false
categories:
  - ASP.Net
  - ASP.NetCore
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**Cross-site scripting (XSS) attacks** involve malicious users injecting content into your app, typically to run malicious JavaScript when users browse your app. You can avoid XSS injection attacks by always encoding unsafe input before writing it to a page. Razor does this automatically unless you use the @Html.Raw() method, so use it sparingly and carefully.\
**Cross-site request forgery (CSRF) attacks** are a problem for apps that use cookie-based authentication, such as ASP.NET Core Identity. It relies on the fact browsers automatically send cookies to a website. A malicious website could create a form that POSTs to your API, and the browser will send the authentication cookie with the request.  This allows malicious websites to send requests as though they’re the logged-in user.\
You can mitigate CSRF attacks using anti-forgery tokens. These involve writing a hidden field in every form that contains a random string based on the current user. A similar token is stored in a cookie. A legitimate request will have both parts, but a forged request from a malicious website will only have the cookie half; they cannot create the hidden field in the form. By validating these tokens, your API can reject forged requests.\
ASP.NET Core automatically adds anti-forgery tokens to any forms you create using Razor. You can validate the tokens by adding the \[ValidateAntiForgeryToken] attribute to your controllers and actions. Alternatively, you can apply the \[AutoValidateAntiForgeryToken] attribute globally to protect your whole app, and only disable the check as necessary using the \[IgnoreAntiForgeryToken] attribute \
**Open redirect attacks** use the common returnURL mechanism after logging in to redirect users to malicious websites. You can prevent this attack by ensuring you only redirect to local URLs, URLs that belong to your app.\
**Insecure direct object references** are a common problem where you expose the ID of database entities in the URL. You should always verify that users have permission to access or change the requested resource by using resource-based authorization in your action methods.\
**SQL injection attacks** are a common attack vector when you build SQL requests manually. Always use parameterized queries when building requests, or instead use a framework like EF Core, which isn’t vulnerable to SQL injection.