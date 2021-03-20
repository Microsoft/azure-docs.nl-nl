---
title: Lokale ontwikkeling voor Azure static-Web Apps instellen
description: Meer informatie over het instellen van uw lokale ontwikkel omgeving voor Azure static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326164"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Lokale ontwikkeling voor Azure static Web Apps-Preview instellen

Een statisch Web Apps exemplaar van Azure bestaat uit twee verschillende soorten toepassingen. De eerste is een web-app voor uw statische inhoud. Web-apps worden vaak gemaakt met front-end frameworks en bibliotheken of met statische site generatoren. Het tweede aspect is de API. Dit is een Azure Functions-app die een uitgebreide ontwikkel omgeving voor de back-end biedt.

Bij uitvoering in de Cloud wijst Azure static Web Apps naadloos aanvragen toe aan de `api` route van de web-app naar de Azure functions-app zonder CORS-configuratie. U moet uw toepassing lokaal configureren om dit gedrag te simuleren.

In dit artikel worden de aanbevolen procedures voor lokale ontwikkeling beschreven, met inbegrip van de volgende concepten:

- De web-app instellen voor statische inhoud
- De Azure Functions-app configureren voor de API van uw toepassing
- Fout opsporing en uitvoering van de toepassing
- Best practices voor de bestands-en mapstructuur van uw app

## <a name="prerequisites"></a>Vereisten

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code
- [Live server-extensie](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) voor Visual Studio code
  - Alleen vereist als u geen front-end Java script-Framework of de CLI van een statische site generator gebruikt

## <a name="run-projects-locally"></a>Projecten lokaal uitvoeren

Het lokaal uitvoeren van een statische Azure-web-app omvat drie processen, afhankelijk van het feit of uw project een API bevat.

- Een lokale webserver uitvoeren
- De API uitvoeren
- Het webproject verbinden met de API

Afhankelijk van hoe een website is gebouwd, is het mogelijk dat een lokale webserver of niet vereist is om de toepassing uit te voeren in de browser. Bij het gebruik van front-end Java script frameworks en statische site generators is deze functionaliteit ingebouwd in hun respectieve Cli's (opdracht regel interfaces). De volgende koppelingen verwijzen naar de CLI-verwijzing voor een selectie van frameworks, Bibliotheken en genera tors.

### <a name="javascript-frameworks-and-libraries"></a>Java script-frameworks en-bibliotheken

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [CLI-reageren](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Statische site generatoren

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Als u een CLI-hulp programma gebruikt voor uw site, kunt u door gaan naar de sectie [het uitvoeren van de API](#run-api-locally) .

### <a name="running-a-local-web-server-with-live-server"></a>Een lokale webserver met live server uitvoeren

De live server-extensie voor Visual Studio code biedt een lokale ontwikkel webserver die statische inhoud verzendt.

#### <a name="create-a-repository"></a>Een opslagplaats maken

1. Zorg ervoor dat u bent aangemeld bij GitHub en ga naar [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) en maak een nieuw github-project met de naam **vanille-API**, met behulp van deze sjabloon.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="GitHub nieuw opslag plaats-venster":::

1. Open Visual Studio Code.

1. Druk op **F1** om het opdrachtenpalet te openen.

1. Typ **Clone** in het zoekvak en selecteer **git: Clone**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Git-kloon optie in Visual Studio code":::

1. Voer de volgende waarde in voor de URL van de **opslag plaats**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Selecteer een maplocatie voor het nieuwe project.

1. Wanneer u wordt gevraagd de gekloonde opslagplaats te openen, selecteert u **Openen**.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="In nieuw venster openen":::

Visual Studio code opent het gekloonde project in de editor.

### <a name="run-the-website-locally-with-live-server"></a>De website lokaal uitvoeren met live server

1. Druk op **F1** om het opdrachtenpalet te openen.

1. Typ **Live server** in het zoekvak en selecteer **Live server: openen met live server**

    Er wordt een browser tabblad geopend om de toepassing weer te geven.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Eenvoudige statische site die in de browser wordt uitgevoerd":::

    Met deze toepassing wordt een HTTP-aanvraag voor het `api/message` eind punt. De aanvraag is nu mislukt omdat het API-gedeelte van deze toepassing moet worden gestart.

### <a name="run-api-locally"></a>API lokaal uitvoeren

Azure static Web Apps-Api's worden aangedreven door Azure Functions. Zie [een API toevoegen aan statische Azure-web apps met Azure functions](add-api.md) voor meer informatie over het toevoegen van een API aan een statisch web apps project van Azure.

Als onderdeel van het proces voor het maken van de API wordt een start configuratie gemaakt voor Visual Studio code. Deze configuratie bevindt zich in de map _. vscode_ . Deze map bevat alle vereiste instellingen voor het lokaal bouwen en uitvoeren van de API.

1. Druk in Visual Studio code op **F5** om de API te starten.

1. Er wordt een nieuw terminal exemplaar geopend waarin de uitvoer van het API-buildproces wordt weer gegeven.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="API die wordt uitgevoerd in Visual Studio code Terminal":::

   De status balk in Visual Studio code is nu oranje. Deze kleur geeft aan dat de API nu wordt uitgevoerd en dat het fout opsporingsprogramma is gekoppeld.

1. Druk vervolgens op **Ctrl/Cmd** en klik op de URL in de terminal om een browser venster te openen dat de API aanroept.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Browser weergave resultaat van API-aanroep":::

### <a name="debugging-the-api"></a>Fout opsporing van de API

1. Open het bestand _API/GetMessage/index.js_ in Visual Studio code.

1. Klik in de linkermarge op regel 2 om een onderbrekings punt in te stellen. Er wordt een rode stip weer gegeven die aangeeft dat het onderbrekings punt is ingesteld.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Onderbrekings punt in Visual Studio code":::

1. Vernieuw de pagina die wordt uitgevoerd in de browser <http://127.0.0.1:7071/api/message> .

1. Het onderbrekings punt wordt in Visual Studio code en de uitvoering van Program ma's gepauzeerd.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Onderbrekings punt in Visual Studio code":::

   [In Visual Studio code](https://code.visualstudio.com/Docs/editor/debugging) voor uw API is een complete ervaring voor het opsporen van fouten beschikbaar.

1. Druk op de knop **door gaan** in de fout balk om de uitvoering voort te zetten.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Knop door gaan in Visual Studio code":::

### <a name="calling-the-api-from-the-application"></a>De API aanroepen vanuit de toepassing

Tijdens de implementatie worden deze aanvragen door Azure static Web Apps automatisch toegewezen aan de eind punten in de _API_ -map. Deze toewijzing zorgt ervoor dat aanvragen van de toepassing naar de API eruitzien zoals in het volgende voor beeld.

```javascript
let response = await fetch("/api/message");
```

Afhankelijk van het feit of uw toepassing is gebouwd met een Java script-Framework CLI, zijn er twee manieren om het pad naar de `api` route te configureren wanneer uw toepassing lokaal wordt uitgevoerd.

- Omgevings configuratie bestanden (aanbevolen voor Java script-frameworks en-bibliotheken)
- Lokale proxy

### <a name="environment-configuration-files"></a>Omgevings configuratie bestanden

Als u uw app bouwt met front-end Frameworks met een CLI, moet u omgevings configuratie bestanden gebruiken. Elk Framework of bibliotheek heeft een andere manier om deze omgevings configuratie bestanden te verwerken. Het is gebruikelijk om een configuratie bestand te hebben voor ontwikkeling dat wordt gebruikt wanneer uw toepassing lokaal wordt uitgevoerd en één voor productie die wordt gebruikt wanneer uw toepassing wordt uitgevoerd in de productie omgeving. De CLI voor het Java script-Framework of de statische-site generator die u gebruikt, wordt automatisch vertrouwd om het ontwikkel bestand lokaal en het productie bestand te gebruiken wanneer uw app wordt gebouwd door Azure static Web Apps.

In het ontwikkel configuratie bestand kunt u het pad naar de API opgeven, die verwijst naar de lokale locatie `http:127.0.0.1:7071` waar de API voor uw site lokaal wordt uitgevoerd.

```
API=http:127.0.0.1:7071/api
```

Geef in het productie configuratie bestand het pad op naar de API als `api` . Op deze manier roept uw toepassing de API aan via "yoursite.com/api" wanneer deze wordt uitgevoerd in de productie omgeving.

```
API=api
```

Naar deze configuratie waarden kan worden verwezen als omgevings variabelen voor knoop punten in de Java script van de web-app.

```js
let response = await fetch(`${process.env.API}/message`);
```

Wanneer de CLI wordt gebruikt voor het uitvoeren van uw site in de ontwikkelings modus of voor het bouwen van de site voor productie, `process.env.API` wordt de waarde vervangen door de waarde uit het juiste configuratie bestand.

Zie de volgende artikelen voor meer informatie over het configureren van omgevings bestanden voor front-end Java script-frameworks en-bibliotheken:

- [Hoek omgevings variabelen](https://angular.io/guide/build#configuring-application-environments)
- [Reageren-aangepaste omgevings variabelen toevoegen](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue-modi en omgevings variabelen](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Live server opnieuw opstarten

1. Druk op **F1** om het opdracht palet te openen in Visual Studio code.

1. Typ **Live server** en selecteer **Live server: stop Live server**.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="De live server-opdracht stoppen in het Visual Studio-opdracht palet":::

1. Druk op **F1** om het opdrachtenpalet te openen.

1. Typ **Live server** en selecteer **Live server: openen met live server**.

1. Vernieuw de toepassing die wordt uitgevoerd op `http://locahost:3000` . Het bericht dat wordt geretourneerd door de API wordt nu weer gegeven in de browser.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Hello van de API die in de browser wordt weer gegeven":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-instellingen configureren](application-settings.md)
