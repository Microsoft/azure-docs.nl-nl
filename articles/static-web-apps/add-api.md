---
title: Een API toevoegen aan statische Azure-Web Apps met Azure Functions
description: Ga aan de slag met statische Azure-Web Apps door een Serverloze API toe te voegen aan uw statische web-app met behulp van Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 33adaf33ba15b49b0a7e4d3e8f1b2a8c435a7966
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449032"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Een API toevoegen aan de preview-versie van statische Web Apps van Azure met Azure Functions

U kunt serverloze Api's toevoegen aan statische Azure-Web Apps via integratie met Azure Functions. In dit artikel wordt beschreven hoe u een API toevoegt en implementeert op een statische Web Apps-site van Azure.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement.
  - Als u geen account hebt, kunt u er [gratis een maken](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code
- [Live server Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) -extensie.
- [Node.js](https://nodejs.org/download/) de API-app lokaal uit te voeren

## <a name="create-a-git-repository"></a>Een Git-opslagplaats maken

De volgende stappen laten zien hoe u een nieuwe opslag plaats maakt en de bestanden naar uw computer kloont.

1. Zorg ervoor dat u bent aangemeld bij GitHub en ga naar [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) om een nieuwe opslag plaats te maken.
1. Voer in het vak _naam van opslag plaats_ **mijn-vanille-API** in.
1. Klik op **opslagplaats maken op basis van sjabloon**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Een nieuwe opslag plaats maken op basis van vanille-Basic":::

Wanneer het project is gemaakt, kopieert u de URL in uw browser naar de nieuwe opslag plaats. U gebruikt deze URL in Visual Studio code om de Git-opslag plaats te klonen.

1. Druk op **F1** om de opdracht in het opdracht palet te openen.
1. Plak de URL in de modus _git: Clone_ en druk op **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Een GitHub-project klonen met Visual Studio code":::

    Volg de aanwijzingen om een locatie voor de opslag plaats te selecteren om het project te klonen.

## <a name="create-the-api"></a>De API maken

Vervolgens maakt u een Azure Functions project als de API van de toepassing. 

1. Maak in het _vanille-API-_ project een submap met de naam **API**.
1. Druk op **F1** om het opdracht palet te openen
1. Typ **Azure functions: nieuw project maken...**
1. Druk op **Enter**
1. Klik op **Bladeren**
1. Selecteer de map **API** als de Directory voor uw project werkruimte
1. Kies **Selecteren**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Scherm afbeelding toont de map P I en de knop selecteren.":::

1. Geef de volgende informatie op bij de prompts:

    - _Selecteer een taal_: **Java script** kiezen
    - _Selecteer een sjabloon voor de eerste functie van uw project_: **http-trigger** kiezen
    - _Geef een functie naam_ op: Voer **GetMessage** in
    - _Autorisatie niveau_: Kies **anoniem**, waarmee iedereen uw functie-eind punt kan aanroepen.
        - Zie [autorisatie sleutels](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie over autorisatie niveaus.

Met Visual Studio code wordt een Azure Functions project gegenereerd met een door HTTP geactiveerde functie.

Uw app heeft nu een project structuur die vergelijkbaar is met het volgende voor beeld.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

Vervolgens wijzigt u de `GetMessage` functie om een bericht naar de front-end te retour neren.

1. Werk de `GetMessage` functie onder _API/GetMessage/index.js_ met de volgende code bij.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Werk de `GetMessage` configuratie onder `api/GetMessage/function.json` met de volgende instellingen.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

Met de bovenstaande instellingen is het API-eind punt:

- Geactiveerd wanneer een HTTP-aanvraag wordt gedaan voor de functie
- Beschikbaar voor alle aanvragen ongeacht de verificatie status
- Beschikbaar via de _/API/Message_ -route

## <a name="run-the-api-locally"></a>De API lokaal uitvoeren

Visual Studio Code integreert met [Azure Functions Core Tools](../azure-functions/functions-run-local.md) om u een project te laten uitvoeren vanaf uw lokale ontwikkelaarscomputer voordat u in Azure publiceert.

> [!TIP]
> Zorg ervoor dat alle resources in de sectie [vereisten](#prerequisites) zijn geïnstalleerd voordat u doorgaat.

1. Voer de functie uit door op **F5** te drukken om de functions-app te starten.

1. Als Azure Functions Core Tools nog niet is geïnstalleerd, selecteert u **installeren** bij de prompt.

    De kern Hulpprogramma's tonen uitvoer van de toepassing die wordt uitgevoerd in het deel venster _Terminal_ . Als onderdeel van de uitvoer ziet u het URL-eind punt van de HTTP-geactiveerde functie die lokaal wordt uitgevoerd.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Scherm afbeelding toont het tabblad TERMINAL waar u de U R L kunt zien.":::

1. Als er kern Hulpprogramma's worden uitgevoerd, gaat u naar de volgende URL om te controleren of de API correct wordt uitgevoerd: `http://localhost:7071/api/message` .

   Het antwoord in de browser moet er ongeveer uitzien als in het volgende voor beeld:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="ALT-textScreenshot toont een antwoord op een tekst in een browser.":::

1. Druk op **SHIFT + F5** om de foutopsporingssessie te stoppen.

### <a name="call-the-api-from-the-application"></a>De API aanroepen vanuit de toepassing

Bij implementatie naar Azure worden aanvragen voor de API automatisch doorgestuurd naar de functie-app voor aanvragen die worden verzonden naar de `api` route. Als u lokaal werkt, moet u de toepassings instellingen configureren voor proxy aanvragen voor de lokale API.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>HTML-bestanden bijwerken voor toegang tot de API

1. Werk vervolgens de inhoud van het bestand _index.html_ bij met de volgende code om de tekst op te halen uit de API-functie en weer te geven op het scherm:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. Druk op **F5** om het API-project te starten.

1. Druk op **F1** en kies **Live server: openen met live server**.

    U ziet nu het API-bericht op de webpagina.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Scherm afbeelding toont het e-mail bericht in een browser.":::

   > [!NOTE]
   > U kunt andere HTTP-servers of-proxy's gebruiken om het `index.html` bestand te verwerken. Het `index.html` is niet mogelijk om toegang te krijgen tot de van `file:///` .

1. Druk op **SHIFT + F5** om het API-project te stoppen.

### <a name="commit-and-push-your-changes-to-github"></a>Uw wijzigingen door voeren en pushen naar GitHub

Gebruik Visual Studio code om uw wijzigingen door te voeren en te pushen naar de externe Git-opslag plaats.

1. Druk op **F1** om het opdracht palet te openen
1. Type **git: alles door voeren**
1. Een commit-bericht toevoegen en op **Enter** drukken
1. Druk op **F1**
1. Typ **git: push** en druk op **Enter**

## <a name="create-a-static-web-app"></a>Statische web-app maken

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Klik op **Een resource maken**
1. Zoeken naar **statische web-app**
1. Klik op **statische web-app (preview-versie)**
1. Klik op **Maken**.

Voeg vervolgens de app-specifieke instellingen toe.

1. Selecteer uw _Azure-abonnement_
1. Een nieuwe _Resourcegroep_ selecteren of maken
1. Geef de app de naam **My-vanille-API**.
1. Selecteer de _regio_ die het dichtst bij u ligt
1. Selecteer de **gratis** _SKU_
1. Klik op de knop **Aanmelden bij GitHub** en verifieer u bij GitHub
1. Kies de gewenste _organisatie_
1. Selecteer **mijn-vanille-API** in de vervolg keuzelijst voor de _opslag plaats_
1. Selecteer **Hoofd** in de vervolgkeuzelijst _Vertakking_
1. Selecteer het Framework van uw keuze voor het maken van de configuratie in de vervolg keuzelijst _Build presets_

 > Deze velden worden weer gegeven in de standaard project structuur van het app-type. Wijzig de waarden in aanpassen aan uw app.

Voeg vervolgens de volgende details van de build toe.

1. Voer **/** in voor de locatie van de _app_.
1. Voer de **API** in het vak _API-locatie_ in.
1. Wis de standaard waarde uit de locatie van het _app-artefact_ en laat het vak leeg.
1. Klik op **Controleren + maken**.
1. Klik op de knop **Maken**

    Nadat u op de knop _maken_ hebt geklikt, voert Azure twee dingen uit. Eerst worden de onderliggende Cloud Services gemaakt ter ondersteuning van de app. Vervolgens begint een achtergrond proces met het bouwen en implementeren van de toepassing.

1. Klik op de knop **Ga naar resource** om naar de _overzichts_ pagina van de web-app te gaan.

    Wanneer de app op de achtergrond wordt gebouwd, kunt u op de banner klikken met een koppeling om de status van de build weer te geven.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="GitHub-werk stroom":::

1. Zodra de implementatie is voltooid, kunt u naar de web-app navigeren door te klikken op de _URL_ -koppeling die wordt weer gegeven op de pagina _overzicht_ .

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Toegang tot de statische app-URL vanuit de Azure Portal":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken voor verder gebruik, kunt u de volgende stappen gebruiken om de statische Azure-web-app en de bijbehorende resources te verwijderen.

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Typ in de bovenste zoek balk **resource groepen**
1. Klik op **resource groepen**
1. Selecteer **myResourceGroup**.
1. Controleer op de pagina _myResourceGroup_ of de weergegeven resources de resources zijn die u wilt verwijderen.
1. Selecteer **Verwijderen**
1. Typ **myResourceGroup** in het tekstvak
1. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-instellingen configureren](./application-settings.md)
