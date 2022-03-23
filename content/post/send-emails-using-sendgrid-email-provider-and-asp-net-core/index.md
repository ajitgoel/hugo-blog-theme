---
title: Send emails using SendGrid Email provider and ASP.Net Core
date: 2022-03-23T04:09:05.858Z
draft: false
featured: false
tags:
  - ASP.Net-Core
  - C#
  - SendGrid
categories:
  - ASP.Net-Core
  - C#
  - SendGrid
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
Sending emails using SendGrid email provider and ASP.Net Core is very straightforward.\
Firstly install SendGrid package through Package Manager Console using the following command 

`install-package SendGrid`

Now create a account in Sendgrid.com and add an API key to use the SendGrid provider service.  Add this key to your AppSettings.Json or AppSettings.Development.Json under a new SendGridEmailSettings=>SendGridKey node

***AppSettings.Json:***

```
{
  "SendGridEmailSettings": {
    "SendGridKey": <SendGridPrivateAPIKey>
  }
}
```

***StartUp.cs:***

```
public void ConfigureServices(IServiceCollection services) {
  services.AddTransient < IEmailSender, EmailSender > ();
  services.Configure < AuthMessageSenderOptions > (options =>
    Configuration.GetSection("SendGridEmailSettings").Bind(options));
}
```

***EmailSender .cs***

```
using Microsoft.AspNetCore.Identity.UI.Services;
using Microsoft.Extensions.Options;
using SendGrid;
using SendGrid.Helpers.Mail;
using System.Threading.Tasks;
public class AuthMessageSenderOptions {
  public string SendGridUser {get;set;}
  public string SendGridKey {get;set;}
}
public class EmailSender: IEmailSender {
  public EmailSender(IOptions optionsAccessor) {
    Options = optionsAccessor.Value;
  }
  public AuthMessageSenderOptions Options {get;} //set only via Secret Manager
  public Task SendEmailAsync(string email, string subject, string message) {
    return Execute(Options.SendGridKey, subject, message, email);
  }
  public Task Execute(string apiKey, string subject, string message, string email) {
    var sendGridClient = new SendGridClient(apiKey);
    var sendGridMessage = new SendGridMessage() {
      From = new EmailAddress("ajitgoel@gmail.com", "Simpler Products-Social Media Scrubber"),
        Subject = subject,
        PlainTextContent = message,
        HtmlContent = message
    };
    sendGridMessage.AddTo(new EmailAddress(email));
    // Disable click tracking.See //sendgrid.com/docs/User_Guide/Settings/tracking.html
    sendGridMessage.SetClickTracking(false, false);
    return sendGridClient.SendEmailAsync(sendGridMessage);
  }
}
```

If you send an email and you do not receive it in your mailbox check the spam folder. My emails landed in the spam folder when I was testing the application.