---
description: Sending email through the SMTP interface native to System.Net.Mail namespace
---

# SMTP Email

Sending email through an SMTP server is one of the most common ways of delivering email and almost everyone has access to an SMTP server. &#x20;

### Configuration

```csharp
ConfigureRCommon.Using(new DotNetCoreContainerAdapter(builder.Services))
    .WithSmtpEmailServices(settings =>
    {
        settings.EnableSsl = true;
        settings.FromEmailDefault = "you@ytest.com";
        settings.FromNameDefault = "test system";
        settings.Host = "smtp.sendgrid.net";
        settings.Password = "yourpassword";
        settings.Port = 587;
        settings.UserName = "username";
    });
```

### Usage

```csharp
public class MyService
{
    private readonly IEmailService _emailService;
    private readonly SmtpEmailSettings _settings;
    
    public MyService(IEmailService emailService, 
        IOptions<SmtpEmailSettings> settings)
    {
        _emailService = emailService;
        _settings = settings.Value;
    }
    
    public async Task SendNotification(string to, string subject, string body)
    {
        var email = new MailMessage(new MailAddress(this._settings.FromEmailDefault, 
            this._settings.FromNameDefault), 
            new MailAddress(to))
        {
            Body = body,
            Subject = subject
        };
    
        await _emailService.SendEmailAsync(email);
    }
}
```
