---
title: Een telefoon gesprek doen vanuit Twilio (.NET) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek met de Twilio API-service in Azure. Code voorbeelden geschreven in .NET.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521118"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Een telefoon gesprek doen met behulp van Twilio in een webrol in azure
In deze hand leiding wordt gedemonstreerd hoe u Twilio kunt gebruiken om een aanroep uit te voeren vanaf een webpagina die wordt gehost in Azure. De resulterende toepassing vraagt de gebruiker om een gesprek te voeren met het opgegeven aantal en bericht, zoals wordt weer gegeven in de volgende scherm afbeelding.

![Azure-oproep formulier met Twilio en ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Vereisten
U moet het volgende doen om de code in dit onderwerp te kunnen gebruiken:

1. Haal een Twilio-account en verificatie token op uit de [Twilio-console][twilio_console]. Meld u aan om aan de slag te gaan met Twilio [https://www.twilio.com/try-twilio][try_twilio] . U kunt prijzen evalueren op [https://www.twilio.com/pricing][twilio_pricing] . Zie voor informatie over de API van Twilio [https://www.twilio.com/voice/api][twilio_api] .
2. Voeg de *Twilio .net-bibliotheek* toe aan uw web-rol. Zie **de Twilio-bibliotheken toevoegen aan het project van uw webrol**, verderop in dit onderwerp.

U moet bekend zijn met het maken van een eenvoudige [ondernemingsrol op Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Procedure: een webformulier maken om een gesprek te voeren
<a id="use_nuget"></a>De Twilio-bibliotheken toevoegen aan het project van uw webrol:

1. Open uw oplossing in Visual Studio.
2. Klik met de rechter muisknop op **verwijzingen**.
3. Klik op **NuGet-pakketten beheren**.
4. Klik op **online**.
5. Typ *twilio* in het vak online zoeken.
6. Klik op **installeren** in het Twilio-pakket.

De volgende code laat zien hoe u een webformulier maakt om gebruikers gegevens op te halen voor het maken van een aanroep. In dit voor beeld wordt een ASP.NET-webrol met de naam **TwilioCloud** gemaakt.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Procedure: de code maken om de aanroep uit te voeren
De volgende code, die wordt aangeroepen wanneer de gebruiker het formulier voltooit, maakt het aanroep bericht en genereert de aanroep. In dit voor beeld wordt de code uitgevoerd in de gebeurtenis-handler OnClick van de knop op het formulier. (Gebruik uw Twilio-account en verificatie token in plaats van de waarden van de tijdelijke aanduiding die zijn toegewezen aan `accountSID` en `authToken` in de onderstaande code.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

De aanroep wordt uitgevoerd en het Twilio-eind punt, de API-versie en de status van de oproep worden weer gegeven. De volgende scherm afbeelding toont uitvoer van een voorbeeld uitvoering.

![Azure-aanroep antwoord met Twilio en ASP.NET][twilio_dotnet_basic_form_output]

Meer informatie over TwiML vindt u op [https://www.twilio.com/docs/api/twiml][twiml] . Meer informatie over &lt; dict &gt; en andere Twilio-werk woorden vindt u op [https://www.twilio.com/docs/api/twiml/say][twilio_say] .

## <a name="next-steps"></a><a id="nextsteps"></a>Volgende stappen
Deze code werd verschaft om u de basis functionaliteit te laten zien met behulp van Twilio in een ASP.NET-Web-rol in Azure. Voordat u naar Azure implementeert in productie, wilt u mogelijk meer fout afhandeling of andere functies toevoegen. Bijvoorbeeld:

* In plaats van een webformulier te gebruiken, kunt u Azure Blob-opslag of een Azure SQL Database-exemplaar gebruiken om telefoon nummers op te slaan en tekst aan te roepen. Zie [de Azure Blob Storage-service in .net gebruiken][howto_blob_storage_dotnet]voor meer informatie over het gebruik van blobs in Azure. Zie [Azure SQL database gebruiken in .NET-toepassingen][howto_sql_azure_dotnet]voor meer informatie over het gebruik van SQL database.
* U kunt gebruiken `RoleEnvironment.getConfigurationSettings` om de Twilio-account-id en het verificatie token op te halen uit de configuratie-instellingen van uw implementatie, in plaats van de waarden in uw formulier vast te schrijven. `RoleEnvironment`Zie de [naam ruimte micro soft. WindowsAzure. ServiceRuntime][azure_runtime_ref_dotnet]voor meer informatie over de-klasse.
* Lees de Twilio Security-richt lijnen op [https://www.twilio.com/docs/security][twilio_docs_security] .
* Meer informatie over Twilio op [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a><a name="seealso"></a>Zie ook
* [Twilio gebruiken voor spraak-en SMS-mogelijkheden van Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md