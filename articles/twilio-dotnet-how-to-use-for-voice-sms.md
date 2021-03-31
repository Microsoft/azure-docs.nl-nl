---
title: Twilio gebruiken voor spraak en SMS (.NET) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek en het verzenden van een SMS-bericht met de Twilio API-service in Azure. Code voorbeelden geschreven in .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.custom: devx-track-dotnet
ms.openlocfilehash: 104f969f5e27ef36ad43eb10e19176a4bcfd6648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003753"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Twilio gebruiken voor spraak-en SMS-mogelijkheden van Azure
In deze hand leiding wordt gedemonstreerd hoe u veelvoorkomende programmeer taken uitvoert met de Twilio API-service in Azure. De besproken scenario's zijn onder andere het maken van een telefoon oproep en het verzenden van een SMS-bericht (Short Message Service). Zie de sectie [volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en SMS in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is de toekomst van bedrijfs communicatie ingeschakeld, waardoor ontwikkel aars spraak, VoIP en berichten in toepassingen kunnen insluiten. Ze virtualiseren alle infra structuur die nodig is in een in de cloud gebaseerde wereld wijde omgeving, waardoor deze toegankelijk is via het Twilio Communications API-platform. Toepassingen zijn eenvoudig te bouwen en te schaalbaar. Profiteer van flexibiliteit met betalen per gebruik-prijzen en profiteer van de betrouw baarheid van de Cloud.

Met **Twilio Voice** kunnen uw toepassingen telefoon gesprekken voeren en ontvangen. Met **TWILIO SMS** kunnen uw toepassingen SMS-berichten verzenden en ontvangen. Met **Twilio-client** kunt u VoIP-aanroepen van elke telefoon, tablet of browser maken en WebRTC ondersteunen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prijzen voor Twilio en speciale aanbiedingen
Azure-klanten ontvangen een [speciale aanbieding](https://www.twilio.com/azure): gratis $10 aan Twilio-tegoed bij het upgraden van uw Twilio-account. Dit Twilio-tegoed kan worden toegepast op elk Twilio-gebruik ($10-credit equivalent om zoveel 1.000 SMS-berichten te verzenden of Maxi maal 1000 binnenkomende spraak minuten te ontvangen), afhankelijk van de locatie van uw telefoon nummer en bericht-of oproep doel. U kunt dit Twilio tegoed inwisselen en aan de slag gaan op [twilio.com/azure](https://twilio.com/azure).

Twilio is een service voor betalen naar gebruik. Er zijn geen instel kosten en u kunt uw account op elk gewenst moment sluiten. U kunt meer informatie vinden op [Twilio prijzen](https://www.twilio.com/voice/pricing).

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio-API is een resterende API die spraak-en SMS-functionaliteit biedt voor toepassingen. Client bibliotheken zijn beschikbaar in meerdere talen. Zie [TWILIO API libraries][twilio_libraries]voor een lijst.

De belangrijkste aspecten van de Twilio-API zijn Twilio-werk woorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-werk woorden
De API maakt gebruik van Twilio-werk woorden. de term **&lt; zeg &gt;** geeft bijvoorbeeld Twilio aan audibly een bericht te verzenden bij een aanroep.

Hier volgt een lijst met Twilio-werk woorden.  Meer informatie over de andere bewerkingen en mogelijkheden via [documentatie voor Twilio Markup Language](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Verbindt de beller met een andere telefoon.
* `<Gather>`: Verzamelt numerieke cijfers die zijn ingevoerd op het telefoon blok.
* `<Hangup>`: Beëindigt een aanroep.
* `<Play>`: Hiermee speelt u een audio bestand af.
* `<Pause>`: Wacht een opgegeven aantal seconden op de achtergrond.
* `<Record>`: Registreert de stem van de beller en retourneert een URL van een bestand dat de opname bevat.
* `<Redirect>`: Overdracht van het beheer van een aanroep of SMS naar de TwiML op een andere URL.
* `<Reject>`: Hiermee wordt een inkomende oproep naar uw Twilio-nummer afgewezen zonder dat u wordt gefactureerd
* `<Say>`: Hiermee wordt tekst geconverteerd naar spraak die tijdens een aanroep wordt gemaakt.
* `<Sms>`: Hiermee wordt een SMS-bericht verzonden.

### <a name="twiml"></a>TwiML
TwiML is een reeks op XML gebaseerde instructies op basis van de Twilio-werk woorden die Twilio over het verwerken van een aanroep of SMS.

Als voor beeld wordt met de volgende TwiML de tekst **Hallo wereld** naar spraak geconverteerd.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Wanneer uw toepassing de Twilio API aanroept, is een van de API-para meters de URL die het TwiML-antwoord retourneert. Voor ontwikkelings doeleinden kunt u Twilio-Url's gebruiken om de TwiML-reacties te geven die worden gebruikt door uw toepassingen. U kunt ook uw eigen Url's hosten om de TwiML-reacties te maken en een andere optie is het object **TwiMLResponse** te gebruiken.

Zie [TwiML][twiml]voor meer informatie over Twilio-werk woorden, hun kenmerken en TwiML. Zie [TWILIO API][twilio_api](Engelstalig) voor meer informatie over de TWILIO-API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Wanneer u klaar bent om een Twilio-account op te halen, meldt u zich aan bij [try Twilio][try_twilio]. U kunt beginnen met een gratis account en later uw account bijwerken.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een account-ID en een verificatie token. Beide zijn nodig voor het maken van Twilio-API-aanroepen. Zorg ervoor dat uw verificatie token is beveiligd om onbevoegde toegang tot uw account te voor komen. Uw account-ID en verificatie token zijn te zien op de [pagina Twilio-account][twilio_account]in de velden met de naam **account sid** en **verificatie token**.

## <a name="create-an-azure-application"></a><a id="create_app"></a>Een Azure-toepassing maken
Een Azure-toepassing die als host fungeert voor een Twilio-toepassing is niet anders dan andere Azure-toepassingen. U voegt de Twilio .NET-bibliotheek toe en configureert de rol voor het gebruik van de Twilio .NET-bibliotheken.
Zie [een Azure-project maken met Visual Studio][vs_project]voor meer informatie over het maken van een eerste Azure-project.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren voor het gebruik van Twilio-bibliotheken
Twilio biedt een reeks .NET-helper-bibliotheken waarmee verschillende aspecten van Twilio eenvoudig en eenvoudig kunnen communiceren met de Twilio REST API en Twilio-client om TwiML-antwoorden te genereren.

Twilio biedt vijf bibliotheken voor .NET-ontwikkel aars:

| Bibliotheek | Beschrijving |
| --- | --- |
| Twilio.API | De Twilio-bibliotheek van de kern waarmee de Twilio-REST API in een beschrijvende .NET-bibliotheek worden geplaatst. Deze bibliotheek is beschikbaar voor .NET, Silverlight en Windows Phone 7. |
| Twilio.TwiML | Biedt een beschrijvende .NET-manier voor het genereren van TwiML-opmaak. |
| Twilio.MVC | Voor ontwikkel aars die gebruikmaken van ASP.NET MVC bevat deze bibliotheek een TwilioController, TwiML ActionResult en aanvraag validatie kenmerk. |
| Twilio.WebMatrix | Voor ontwikkel aars die gebruikmaken van het gratis webmatrix ontwikkel programma van micro soft, bevat deze bibliotheek haar syntaxis helpers voor verschillende Twilio-acties. |
| Twilio. client. Capability | Bevat de functionaliteits token generator voor gebruik met de Twilio-client java script SDK. |

> [!Important]
> Voor alle bibliotheken is .NET 3,5, Silverlight 4 of Windows Phone 7 of hoger vereist.

In de voor beelden in deze hand leiding wordt gebruikgemaakt van de bibliotheek Twilio. API.

De bibliotheken kunnen worden [geïnstalleerd met behulp van de NuGet package manager-extensie](https://www.twilio.com/docs/csharp/install) die beschikbaar is voor Visual Studio 2010 tot 2015.  De bron code wordt gehost op [github][twilio_github_repo], die een wiki bevat met volledige documentatie over het gebruik van de bibliotheken.

Micro soft Visual Studio 2010 installeert standaard versie 1,2 van NuGet. Voor het installeren van de Twilio-bibliotheken is versie 1,6 van NuGet of hoger vereist. Zie voor meer informatie over het installeren of bijwerken van NuGet [https://nuget.org/][nuget] .

> [!NOTE]
> Als u de meest recente versie van NuGet wilt installeren, moet u eerst de geladen versie verwijderen met behulp van Visual Studio Extension Manager. Hiervoor moet u Visual Studio als beheerder uitvoeren. Anders is de knop verwijderen uitgeschakeld.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>De Twilio-bibliotheken toevoegen aan uw Visual Studio-project:
1. Open uw oplossing in Visual Studio.
2. Klik met de rechter muisknop op **verwijzingen**.
3. Klik op **NuGet-pakketten beheren...**
4. Klik op **online**.
5. Typ *twilio* in het vak online zoeken.
6. Klik op **installeren** in het Twilio-pakket.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedure: een uitgaande oproep doen
Hieronder ziet u hoe u een uitgaande oproep maakt met behulp van de **CallResource** -klasse. Deze code maakt ook gebruik van een Twilio-site om het TwiML-antwoord (Twilio Markup Language) te retour neren. Vervang uw waarden door **naar** en **van** de telefoon nummers en zorg ervoor dat u het telefoon nummer **van** uw Twilio-account controleert voordat u de code uitvoert.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Zie voor meer informatie over de para meters die worden door gegeven aan de methode **CallResource. Create** [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Zoals vermeld, gebruikt deze code een Twilio-site voor het retour neren van het TwiML-antwoord. U kunt in plaats daarvan uw eigen site gebruiken om het TwiML-antwoord op te geven. Zie [How to: TwiML Responses van uw eigen website bieden](#howto_provide_twiml_responses)voor meer informatie.

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Procedure: een SMS-bericht verzenden
De volgende scherm afbeelding laat zien hoe u een SMS-bericht verzendt met behulp van de **MessageResource**  -klasse. Het **van** -nummer wordt verzorgd door Twilio voor proef accounts om SMS-berichten te verzenden. Het **to** -nummer moet worden geverifieerd voor uw Twilio-account voordat u de code uitvoert.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Procedure: TwiML-reacties van uw eigen website opgeven
Wanneer uw toepassing een aanroep initieert naar de Twilio-API, bijvoorbeeld via de methode **CallResource. Create** , stuurt Twilio uw aanvraag naar een URL die naar verwachting een TwiML-antwoord retourneert. In het voor beeld in [procedure: het maken van een uitgaande oproep](#howto_make_call) maakt gebruik van de TWILIO-URL [https://twimlets.com/message][twimlet_message_url] voor het retour neren van het antwoord.

> [!NOTE]
> Hoewel TwiML is ontworpen voor gebruik door Web Services, kunt u de TwiML in uw browser weer geven. Klik bijvoorbeeld [https://twimlets.com/message][twimlet_message_url] om een leeg element weer te geven. `<Response>` als een ander voor beeld klikt [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) u op om een element te zien `<Response>` dat een &lt; dict-element bevat &gt; .
>

In plaats van te vertrouwen op de Twilio-URL, kunt u uw eigen URL-site maken die HTTP-antwoorden retourneert. U kunt de site maken in elke taal waarin HTTP-antwoorden worden geretourneerd. In dit onderwerp wordt ervan uitgegaan dat u de URL wilt hosten vanuit een algemene ASP.NET-handler.

De volgende ASP.NET-handler bewaart een TwiML-antwoord met de tekst **Hallo wereld** bij de aanroep.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Zoals u in het bovenstaande voor beeld kunt zien, is het TwiML-antwoord slechts een XML-document. De bibliotheek Twilio. TwiML bevat klassen waarmee TwiML voor u wordt gegenereerd. In het volgende voor beeld wordt het equivalente antwoord gegenereerd zoals hierboven wordt weer gegeven, maar wordt de klasse **VoiceResponse** gebruikt.

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Zie voor meer informatie over TwiML [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) .

Zodra u een manier hebt ingesteld om TwiML-reacties te geven, kunt u die URL door geven aan de methode **CallResource. Create** . Als u bijvoorbeeld een webtoepassing met de naam MyTwiML hebt geïmplementeerd in een Azure-Cloud service en de naam van uw ASP.NET-handler MyTwiML. ashx is, kan de URL worden door gegeven aan **CallResource. Create** , zoals weer gegeven in het volgende code voorbeeld:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Voor meer informatie over het gebruik van Twilio op Azure met ASP.NET, Zie [hoe u een telefoon oproep maakt met behulp van Twilio in een webrol in azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
