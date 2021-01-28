---
title: Quickstart voor de C#-clientbibliotheek voor Insluitende lezer
titleSuffix: Azure Cognitive Services
description: In deze quickstart maakt u een nieuwe web-app en voegt u de API-functionaliteit voor de Insluitende lezer toe.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 080b9dac8dad099f2901f2b820da58501310471b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947328"
---
[Insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulpprogramma dat bewezen technieken implementeert om begrijpend lezen te verbeteren voor beginnende lezers, mensen die een taal willen leren en mensen met leerproblemen, zoals dyslexie. U kunt de Insluitende lezer in uw toepassingen gebruiken om tekst te isoleren voor het verbeteren van de focus, het weergeven van afbeeldingen voor veelgebruikte woorden, het markeren van bepaalde secties, het hardop voorlezen van geselecteerde tekst, het vertalen van woorden en zinsdelen in realtime en meer.

In deze quickstart maakt u een nieuwe web-app en integreert u Insluitende lezer door de clientbibliotheek voor Insluitende lezer te gebruiken. U vindt [op GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp) een volledig werkend voorbeeld van deze quickstart.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Een Insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](../../how-to-create-immersive-reader.md) om deze in te stellen. U hebt enkele waarden nodig die u hier hebt gemaakt wanneer u de eigenschappen van het voorbeeldproject configureert. Sla de uitvoer van uw sessie op in een tekstbestand voor later gebruik.

## <a name="create-a-web-app-project"></a>Een web-app-project maken

Maak een nieuw project in Visual Studio met behulp van de sjabloon voor ASP.NET Core-webtoepassing met ingebouwde Model-View-Controller en ASP.NET Core 2.1. Geef het project de naam 'QuickstartSampleWebApp'.

![Nieuw project - C#](../../media/quickstart-csharp/1-createproject.png)

![Nieuw project configureren - C#](../../media/quickstart-csharp/2-configureproject.png)

![Nieuwe ASP.NET Core-webtoepassing - C#](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Verificatie instellen

### <a name="configure-authentication-values"></a>Verificatietypewaarden configureren

Klik met de rechtermuisknop op het project in de _Solution Explorer_ en kies **Gebruikersgeheimen beheren**. Hiermee opent u een bestand met de naam _Secrets.json_. Dit bestand is niet ingecheckt in broncodebeheer. Klik [hier](/aspnet/core/security/app-secrets?tabs=windows) voor meer informatie. Vervang de inhoud van _secrets.json_ met het volgende, waarbij u de waarden opgeeft die zijn verstrekt bij het maken van uw Insluitende lezer-resource.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="install-active-directory-nuget-package"></a>Active Directory NuGet-pakket installeren

De volgende code gebruikt objecten uit het **Microsoft.IdentityModel.Clients.ActiveDirectory**-NuGet-pakket, zodat u een verwijzing naar dat pakket in uw project moet toevoegen.

Open de NuGet Package Manager Console vanuit **Tools -> NuGet Package Manager -> Package Manager Console** en voer de volgende opdracht uit:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Werk de controller bij om het token te verkrijgen 

Open _Controllers\HomeController.cs_ en voeg de volgende code toe na de _met behulp van_-instructies boven aan het bestand.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Nu gaan we de controller configureren voor het verkrijgen van de Azure AD-waarden van _secrets.json_. Voeg de volgende code toe aan de bovenkant van de klasse _HomeController_ na ```public class HomeController : Controller {```.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Voorbeeldinhoud toevoegen
Open eerst _Views\Shared\Layout.cshtml_. Voeg voor de regel in ```</head>``` de volgende code toe:

```html
@RenderSection("Styles", required: false)
```

Nu gaan we voorbeeldinhoud toevoegen aan deze web-app. Open _Views\Home\Index.cshtml_ en vervang alle automatisch gegenereerde code door dit voorbeeld:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

U ziet dat alle tekst een **taal**-kenmerk bevat waarmee de talen van de tekst worden beschreven. Dit kenmerk helpt de Insluitende lezer de relevante taal-en grammaticafuncties te bieden.

## <a name="add-javascript-to-handle-launching-immersive-reader"></a>Javascript toevoegen aan de ingang voor het starten van Insluitende lezer

De bibliotheek voor insluitende lezers biedt functionaliteiten zoals het starten van de Insluitende lezer en het weergeven van Insluitende lezer-knoppen. Klik [hier](../../reference.md) voor meer informatie.

Voeg aan de onderkant van _Views\Home\Index.cshtml_ de volgende code toe:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Selecteer in de menubalk **Debug > Start Debugging** of druk op **F5** om de toepassing te starten.

In uw browser ziet u het volgende:

![Voorbeeld-app - C#](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>De Insluitende lezer starten

Wanneer u op de knop ‘Immersive Reader’ klikt, ziet u dat de Insluitende lezer wordt gestart met de inhoud van de pagina.

![Insluitende lezer - C#](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Volgende stappen

* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](../../reference.md) verkennen
