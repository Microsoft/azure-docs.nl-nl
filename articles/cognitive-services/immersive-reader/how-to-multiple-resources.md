---
title: Meerdere bronnen voor insluitende lezer integreren
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een Node.js-toepassing waarmee Insluitende lezer wordt gestart met meerdere resources voor Insluitende lezer.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-js
ms.openlocfilehash: dc6c7bd9e38aa21ab10f55fca2500b0c12194d43
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620069"
---
# <a name="integrate-multiple-immersive-reader-resources"></a>Meerdere bronnen voor insluitende lezer integreren

In het [overzicht](./overview.md) hebt u geleerd wat de Insluitende lezer is en hoe het bewezen technieken implementeert om begrijpend lezen te verbeteren voor taalverwervers, beginnende lezers en studenten met leerproblemen. In de [Snelstartgids](./quickstarts/client-libraries.md)hebt u geleerd hoe u insluitende lezer kunt gebruiken met één resource. In deze zelfstudie wordt uitgelegd hoe u Insluitende lezer met meerdere resources in dezelfde toepassing kunt integreren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Meerdere resources voor Insluitende lezer maken onder een bestaande resourcegroep
> * Insluitende lezer met meerdere resources starten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Volg de [quickstart](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) om een web-app te maken waarmee Insluitende lezer met Node.js wordt gestart. In die quickstart configureert u Insluitende lezer met één resource. In deze zelfstudie borduren we hierop voort.

## <a name="create-the-immersive-reader-resources"></a>Resources voor Insluitende lezer maken

Volg [deze instructies](./how-to-create-immersive-reader.md) om elke resource voor Insluitende lezer te maken. Het script **Create-ImmersiveReaderResource** heeft `ResourceName`, `ResourceSubdomain` en `ResourceLocation` als parameters. Deze moeten uniek zijn voor elke resource die wordt gemaakt. De overige parameters moeten hetzelfde zijn als de parameters die u hebt gebruikt bij het instellen van uw eerste resource voor Insluitende lezer. Op deze manier kan elke resource worden gekoppeld aan dezelfde Azure-resourcegroep en Azure AD-toepassing.

In het onderstaande voorbeeld ziet u hoe u twee resources maakt: een in US - west en een in US - oost. Merk op dat de waarden voor `ResourceName`, `ResourceSubdomain` en `ResourceLocation` uniek zijn.

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Resources toevoegen aan de omgevingsconfiguratie

In de quickstart hebt u een omgevingsconfiguratiebestand gemaakt dat de parameters `TenantId`, `ClientId`, `ClientSecret` en `Subdomain` bevat. Omdat al uw resources dezelfde Azure AD-toepassing gebruiken, kunnen we dezelfde waarden gebruiken voor `TenantId`, `ClientId` en `ClientSecret`. De enige wijziging die u moet aanbrengen, is dat u het subdomein voor elke resource moet opgeven.

Het nieuwe bestand ( __.env__) moet er nu ongeveer als volgt uitzien:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Zorg ervoor dat u dit bestand niet doorvoert in broncodebeheer, omdat het geheimen bevat die niet openbaar moeten worden gemaakt.

Vervolgens gaan we het bestand _routes\index.js_ wijzigen, dat we hebben gemaakt om meerdere resources te kunnen gebruiken. Vervang de inhoud ervan door de volgende code.

Net als voorheen wordt met deze code een API-eindpunt gemaakt waarmee een Azure AD-verificatietoken wordt verkregen met behulp van uw wachtwoord voor de service-principal. Ditmaal kan de gebruiker een resourcelocatie opgeven en deze doorgeven als een queryparameter. Vervolgens wordt een-object met het token en het bijbehorende subdomein geretourneerd.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Het API-eindpunt **getimmersivereaderlaunchparams** moet worden beveiligd achter een vorm van verificatie (bijvoorbeeld [OAuth-](https://oauth.net/2/)) om te voorkomen dat niet-geautoriseerde gebruikers tokens verkrijgen die kunnen worden gebruikt voor uw Insluitende lezer-service en -facturering. Dit valt buiten het bereik van deze zelfstudie.

## <a name="launch-the-immersive-reader-with-sample-content"></a>De Insluitende lezer starten met voorbeeldinhoud

1. Open _views\index.pug_ en vervang de inhoud ervan door de volgende code. Met deze code wordt de pagina met wat voorbeeldinhoud gevuld en worden twee knoppen toegevoegd waarmee Insluitende lezer kan worden gestart. De ene knop is voor het starten van Insluitende lezer voor de resource EastUS (US - oost), de andere is voor de resource WestUS (US -west).

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
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
    ```

3. Onze web-app is nu klaar. Start de app met de volgende opdracht:

    ```bash
    npm start
    ```

4. Open uw browser en ga naar `http://localhost:3000`. De bovenstaande inhoud wordt weergegeven op de pagina. Klik op de knop **EastUS Immersive Reader** of **WestUS Immersive Reader** om Immersive Reader te starten met behulp van een van beide resources.

## <a name="next-steps"></a>Volgende stappen

* De [SDK voor Insluitende lezer](https://github.com/microsoft/immersive-reader-sdk) en de [naslaginformatie voor de SDK voor Insluitende lezer](./reference.md) verkennen
* Codevoorbeelden op [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) bekijken