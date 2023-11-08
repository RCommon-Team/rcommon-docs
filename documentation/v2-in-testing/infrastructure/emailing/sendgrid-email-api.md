---
description: Sending email through the SendGrid API with RCommon
---

# SendGrid Email API

[SendGrid ](https://sendgrid.com/solutions/email-api/)is one of the most popular ways to send email these days.  RCommon provides a simple interface to the SendGrid API.

### Configuration

```csharp
ConfigureRCommon.Using(new DotNetCoreContainerAdapter(builder.Services))
    .WithSendGridEmailServices(x =>
    {
        // Note that you can use a configuration from appSettings.json (like we did) 
        //  or set the variables anyway you want
        var sendGridSettings = builder.Configuration.Get<SendGridEmailSettings>();
        x.SendGridApiKey = sendGridSettings.SendGridApiKey;
        x.FromNameDefault = sendGridSettings.FromNameDefault;
        x.FromEmailDefault = sendGridSettings.FromEmailDefault;
    });
```

### Usage

```csharp
public class MyService
{
    private readonly IEmailService _emailService;
    private readonly SendGridEmailSettings _settings;
    
    public MyService(IEmailService emailService, 
        IOptions<SendGridEmailSettings> settings)
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
