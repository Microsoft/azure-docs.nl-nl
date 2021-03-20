---
title: De SendGrid-e-mail service (.NET) gebruiken | Microsoft Docs
description: Meer informatie over het verzenden van e-mail met de SendGrid-e-mail service op Azure. Code voorbeelden geschreven in C# en de .NET API gebruiken.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.custom: devx-track-dotnet
ms.openlocfilehash: ae816d2be592ab774500d1cfe8f2f6a7b7905b91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98196551"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>E-mail verzenden met SendGrid met Azure
## <a name="overview"></a>Overzicht
In deze hand leiding wordt gedemonstreerd hoe u algemene programmeer taken uitvoert met de SendGrid-e-mail service op Azure. De voor beelden zijn geschreven in C \# en ondersteunen .NET Standard 1,3. De volgende scenario's omvatten het samen stellen van e-mail, het verzenden van e-mail, het toevoegen van bijlagen en het inschakelen van verschillende e-mail-en tracerings instellingen. Zie de sectie [volgende stappen][Next steps] voor meer informatie over het SendGrid en verzenden van e-mail.

## <a name="what-is-the-sendgrid-email-service"></a>Wat is de SendGrid-e-mail service?
SendGrid is een [cloud-gebaseerde e-mail service] die betrouw bare [transactionele e-mail levering], schaal baarheid en real-time analyse biedt, samen met flexibele api's die eenvoudig aangepaste integratie maken. Veelvoorkomende SendGrid-use-cases zijn:

* Automatisch ontvangst bewijzen of aankoop bevestigingen verzenden aan klanten.
* Het beheren van distributie lijsten voor het verzenden van klanten per maand flyers en promoties.
* Het verzamelen van metrische gegevens in realtime voor zaken als geblokkeerde e-mail en klant betrokkenheid.
* Vragen van klanten door sturen.
* Binnenkomende e-mail berichten verwerken.

Bezoek [https://sendgrid.com](https://sendgrid.com) of SendGrid [C# Library][sendgrid-csharp] github opslag plaats voor meer informatie.

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Naslag informatie over de SendGrid .NET-klassen bibliotheek
Het [SendGrid NuGet-pakket](https://www.nuget.org/packages/Sendgrid) is de eenvoudigste manier om de SENDGRID-API op te halen en om uw toepassing te configureren met alle afhankelijkheden. NuGet is een Visual Studio-extensie die is opgenomen in micro soft Visual Studio 2015 en hoger, waarmee u eenvoudig bibliotheken en hulpprogram ma's kunt installeren en bijwerken.

> [!NOTE]
> Als u NuGet wilt installeren als u een versie van Visual Studio gebruikt die ouder is dan Visual Studio 2015, gaat u naar [https://www.nuget.org](https://www.nuget.org) en klikt u op de knop **NuGet installeren** .
>
>

Ga als volgt te werk om het SendGrid NuGet-pakket in uw toepassing te installeren:

1. Klik op **Nieuw project** en selecteer een **sjabloon**.

   ![Een nieuw project maken][create-new-project]
2. Klik in **Solution Explorer** met de rechter muisknop op **verwijzingen** en vervolgens op **NuGet-pakketten beheren**.

   ![SendGrid NuGet-pakket][SendGrid-NuGet-package]
3. Zoek naar **SendGrid** en selecteer het item **SendGrid** in de lijst met resultaten.
4. Selecteer de nieuwste stabiele versie van het Nuget-pakket in de vervolg keuzelijst versie om te kunnen werken met het object model en de Api's die in dit artikel worden getoond.

   ![SendGrid-pakket][sendgrid-package]
5. Klik op **installeren** om de installatie te volt ooien en sluit dit dialoog venster.

De .NET-klassen bibliotheek van SendGrid heet **SendGrid**. Deze bevat de volgende naam ruimten:

* **SendGrid** voor communicatie met de API van SendGrid.
* **SendGrid. helpers. mail** voor hulp methoden om eenvoudig SendGridMessage-objecten te maken die aangeven hoe e-mails moeten worden verzonden.

Voeg de volgende code naam ruimte declaraties toe aan de bovenkant van een C#-bestand waarin u programmatisch toegang wilt krijgen tot de SendGrid-e-mail service.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>Procedure: een E-mail maken
Gebruik het object **SendGridMessage** om een e-mail bericht te maken. Zodra het bericht object is gemaakt, kunt u eigenschappen en methoden instellen, waaronder de e-mail afzender, de e-mail ontvanger en het onderwerp en de hoofd tekst van de e-mail.

In het volgende voor beeld ziet u hoe u een volledig ingevuld e-mail object maakt:

```csharp
var msg = new SendGridMessage();

msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

var recipients = new List<EmailAddress>
{
    new EmailAddress("jeff@example.com", "Jeff Smith"),
    new EmailAddress("anna@example.com", "Anna Lidman"),
    new EmailAddress("peter@example.com", "Peter Saddow")
};
msg.AddTos(recipients);

msg.SetSubject("Testing the SendGrid C# Library");

msg.AddContent(MimeType.Text, "Hello World plain text!");
msg.AddContent(MimeType.Html, "<p>Hello World!</p>");
```

Zie [SendGrid-csharp][sendgrid-csharp] op github voor meer informatie over alle eigenschappen en methoden die worden ondersteund door het type **SendGrid** .

## <a name="how-to-send-an-email"></a>Procedure: een E-mail verzenden
Nadat u een e-mail bericht hebt gemaakt, kunt u het verzenden met behulp van de API van SendGrid. U kunt ook gebruikmaken van [. Ingebouwde bibliotheek van NET][NET-library].

Als u een e-mail bericht wilt verzenden, moet u uw SendGrid API-sleutel opgeven. Ga naar de [documentatie][documentation]van de API-sleutels van SendGrid als u meer wilt weten over het configureren van API-sleutels.

U kunt deze referenties opslaan via uw Azure Portal door te klikken op toepassings instellingen en de sleutel-waardeparen toe te voegen onder App-instellingen.

![Azure-app-instellingen][azure_app_settings]

Vervolgens kunt u deze als volgt openen:

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

In de volgende voor beelden ziet u hoe u een e-mail bericht verzendt met behulp van de SendGrid-Web-API met een console toepassing.

```csharp
using System;
using System.Threading.Tasks;
using SendGrid;
using SendGrid.Helpers.Mail;

namespace Example
{
    internal class Example
    {
        private static void Main()
        {
            Execute().Wait();
        }

        static async Task Execute()
        {
            var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
            var client = new SendGridClient(apiKey);
            var msg = new SendGridMessage()
            {
                From = new EmailAddress("test@example.com", "DX Team"),
                Subject = "Hello World from the SendGrid CSharp SDK!",
                PlainTextContent = "Hello, Email!",
                HtmlContent = "<strong>Hello, Email!</strong>"
            };
            msg.AddTo(new EmailAddress("test@example.com", "Test User"));
            var response = await client.SendEmailAsync(msg);
        }
    }
}
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Procedure: e-mail verzenden vanuit ASP .NET core API met behulp van de klasse mail helper

Het onderstaande voor beeld kan worden gebruikt om één e-mail bericht te verzenden naar meerdere personen uit de ASP .NET core API met behulp `MailHelper` van de klasse van `SendGrid.Helpers.Mail` naam ruimte. Voor dit voor beeld gebruiken we ASP .NET Core 1,0. 

In dit voor beeld is de API-sleutel opgeslagen in het `appsettings.json` bestand dat kan worden overschreven van de Azure Portal zoals weer gegeven in de bovenstaande voor beelden.

De inhoud van het `appsettings.json` bestand moet er ongeveer als volgt uitzien:

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  },
  "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

Eerst moet u de onderstaande code toevoegen in het `Startup.cs` bestand van het .net core API-project. Dit is vereist om toegang te krijgen tot de `SENDGRID_API_KEY` vanuit het `appsettings.json` bestand met behulp van afhankelijkheids injectie in de API-controller. De `IConfiguration` interface kan worden ingevoegd bij de constructor van de controller nadat deze is toegevoegd in de `ConfigureServices` onderstaande methode. De inhoud van het `Startup.cs` bestand ziet er ongeveer als volgt uit nadat u de vereiste code hebt toegevoegd:

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

Na het injecteren van de interface kunt u op de controller `IConfiguration` de `CreateSingleEmailToMultipleRecipients` methode van de `MailHelper` -klasse gebruiken om één e-mail bericht naar meerdere ontvangers te verzenden. De methode accepteert een extra Booleaanse para meter met de naam `showAllRecipients` . Deze para meter kan worden gebruikt om te bepalen of e-mail ontvangers elk ander e-mail adres kunnen zien in het gedeelte aan van de koptekst van een e-mail. De voorbeeld code voor de controller moet er als volgt uitzien 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using SendGrid;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Configuration;

namespace SendgridMailApp.Controllers
{
    [Route("api/[controller]")]
    public class NotificationController : Controller
    {
       private readonly IConfiguration _configuration;

       public NotificationController(IConfiguration configuration)
       {
         _configuration = configuration;
       }

       [Route("SendNotification")]
       public async Task PostMessage()
       {
          var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
          var client = new SendGridClient(apiKey);
          var from = new EmailAddress("test1@example.com", "Example User 1");
          List<EmailAddress> tos = new List<EmailAddress>
          {
              new EmailAddress("test2@example.com", "Example User 2"),
              new EmailAddress("test3@example.com", "Example User 3"),
              new EmailAddress("test4@example.com","Example User 4")
          };

          var subject = "Hello world email from Sendgrid ";
          var htmlContent = "<strong>Hello world with HTML content</strong>";
          var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
          var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
          var response = await client.SendEmailAsync(msg);
      }
   }
}
```

## <a name="how-to-add-an-attachment"></a>Procedure: een bijlage toevoegen
Bijlagen kunnen worden toegevoegd aan een bericht door de methode **AddAttachment** aan te roepen en mini maal de bestands naam en base64-gecodeerde inhoud op te geven die u wilt koppelen. U kunt meerdere bijlagen opnemen door deze methode eenmaal aan te roepen voor elk bestand dat u wilt koppelen of door de methode **AddAttachments** te gebruiken. In het volgende voor beeld ziet u het toevoegen van een bijlage aan een bericht:

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Procedure: e-mail instellingen gebruiken om voet tekst, tracering en analyse in te scha kelen
SendGrid biedt extra e-mail functionaliteit via het gebruik van instellingen voor e-mail en tracerings instellingen. Deze instellingen kunnen worden toegevoegd aan een e-mail bericht om specifieke functionaliteit in te scha kelen, zoals het bijhouden van Google Analytics, het bijhouden van abonnementen, enzovoort. Zie de documentatie van de [instellingen][settings-documentation]voor een volledige lijst met apps.

Apps kunnen worden toegepast op **SendGrid** -e-mail berichten met behulp van methoden die zijn geïmplementeerd als onderdeel van de klasse **SendGridMessage** . In de volgende voor beelden ziet u de voet tekst en klikt u op traceer filters:

In de volgende voor beelden ziet u de voet tekst en klikt u op traceer filters:

### <a name="footer-settings"></a>Instellingen voor voet tekst

```csharp
    msg.SetFooterSetting(
        true,
        "Some Footer HTML",
        "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>Klik op bijhouden

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>Procedure: extra SendGrid Services gebruiken
SendGrid biedt verschillende Api's en webhooks die u kunt gebruiken om gebruik te maken van extra functionaliteit binnen uw Azure-toepassing. Zie de [SENDGRID API-verwijzing][SendGrid API documentation]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van de SendGrid-e-mail service hebt geleerd, volgt u deze koppelingen voor meer informatie.

* SendGrid C \# Library opslag plaats: [SendGrid-csharp][sendgrid-csharp]
* SendGrid API-documentatie: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/api-reference/
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[e-mail service op basis van de Cloud]: https://sendgrid.com/solutions
[e-mail levering via een transactie]: https://sendgrid.com/use-cases/transactional-email

