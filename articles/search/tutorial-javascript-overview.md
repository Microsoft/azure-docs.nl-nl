---
title: 'Zelf studie java script: overzicht van zoek integratie'
titleSuffix: Azure Cognitive Search
description: Technisch overzicht en Setup voor het toevoegen van een zoek opdracht aan een website en het implementeren van een statische web-app in Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726837"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1-overzicht van het toevoegen van een zoek opdracht aan een website

In deze zelf studie bouwt u een website op om te zoeken in een catalogus van boeken en implementeert u vervolgens de website naar een statische Azure-web-app. 

De toepassing is beschikbaar: 
* [Voorbeeld](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Demo website-aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Wat doet het voor beeld? 

Deze voorbeeld website biedt toegang tot een catalogus van 10.000 boeken. Een gebruiker kan de catalogus doorzoeken door tekst in te voeren in de zoek balk. Terwijl de gebruiker tekst invoert, gebruikt de website de functie suggesties van de zoek index om de tekst te volt ooien. Zodra de query is voltooid, wordt de lijst met boeken weer gegeven met een deel van de details. Een gebruiker kan een boek selecteren om alle details te bekijken, opgeslagen in de zoek index, van het boek. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Deze voorbeeld website biedt toegang tot een catalogus van 10.000 boeken. Een gebruiker kan de catalogus doorzoeken door tekst in te voeren in de zoek balk. Terwijl de gebruiker tekst invoert, gebruikt de website de functie suggesties van de zoek index om de tekst te volt ooien. Zodra de zoek opdracht is voltooid, wordt de lijst met boeken weer gegeven met een deel van de details. Een gebruiker kan een boek selecteren om alle details te bekijken, opgeslagen in de zoek index, van het boek.":::

De zoek ervaring omvat: 

* Search: biedt zoek functionaliteit voor de toepassing.
* Suggesties: biedt suggesties voor het typen van de gebruiker in de zoek balk.
* Document opzoeken: zoekt een document op basis van de ID om alle inhoud op te halen voor de pagina Details.

## <a name="how-is-the-sample-organized"></a>Hoe wordt het voor beeld georganiseerd?

Het voor [beeld](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) omvat het volgende:

|App|Doel|GitHub<br>Opslagplaats<br>Locatie|
|--|--|--|
|Client|App reageren (presentatielaag) om boeken weer te geven met zoeken. De Azure function-app wordt aangeroepen. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Server|Azure function-app (bedrijfslaag): roept de Azure Cognitive Search-API aan met behulp van Java script SDK |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Bulksgewijs invoeren|Java script-bestand om de index te maken en er documenten aan toe te voegen.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Installeer het volgende voor uw lokale ontwikkel omgeving. 

- [Node.js 12 of 14](https://nodejs.org/en/download)
    - Als u een andere versie van Node.js op uw lokale computer hebt geïnstalleerd, kunt u NVM ( [node versie Manager](https://github.com/nvm-sh/nvm) ) of een docker-container gebruiken.  
- [Git](https://git-scm.com/downloads)
- [Visual Studio code](https://code.visualstudio.com/) en de volgende extensies
    - [Azure-resources](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure Cognitive Search 0.2.0 +](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azure statische web-app](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Optioneel:
    - In deze zelf studie wordt de Azure function API niet lokaal uitgevoerd, maar als u deze lokaal wilt uitvoeren, moet u [Azure-functions core-hulpprogram ma's](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) globaal installeren met de volgende bash-opdracht: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Het zoek voorbeeld met git splitsen en klonen

Het splitsen van de voor beeld-opslag plaats is van cruciaal belang om de statische web-app te kunnen implementeren. De web-apps bepalen de build-acties en implementatie-inhoud op basis van uw eigen GitHub Fork-locatie. Het uitvoeren van code in de statische web-app is extern, met Azure static Web Apps het lezen van de code in het gevorkte voor beeld.

1. Splits de voor [beeld-opslag plaats](https://github.com/Azure-Samples/azure-search-javascript-samples)op github. 

    Voltooi het Fork-proces in uw webbrowser met uw GitHub-account. In deze zelf studie wordt uw Fork als onderdeel van de implementatie van een statische Azure-web-app gebruikt. 

1. Down load de voorbeeld toepassing op een bash-terminal naar uw lokale computer. 

    Vervang door `YOUR-GITHUB-ALIAS` uw github-alias. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. Open in Visual Studio code uw lokale map van de gekloonde opslag plaats. De resterende taken worden uitgevoerd vanuit Visual Studio code, tenzij opgegeven.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Een resource groep maken voor uw Azure-resources

1. Open in Visual Studio code de [activiteiten balk](https://code.visualstudio.com/docs/getstarted/userinterface)en selecteer het pictogram Azure. 
1. Klik in de zijbalk met de **rechter muisknop op uw Azure-abonnement** onder het `Resource Groups` gebied en selecteer **resource groep maken**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Klik in de zijbalk * * met de rechter muisknop op uw Azure-abonnement * * onder het gebied resource groepen en selecteer * * resource groep maken * *.":::
1. Voer een naam voor de resource groep in, zoals `cognitive-search-website-tutorial` . 
1. Selecteer een locatie dicht bij u in de buurt.
1. Wanneer u de Cognitive Search en statische web-app-resources maakt, gebruikt u verderop in de zelf studie deze resource groep. 

    Het maken van een resource groep biedt u een logische eenheid voor het beheren van de resources, inclusief het verwijderen ervan wanneer u klaar bent met het gebruik ervan.

## <a name="next-steps"></a>Volgende stappen

* [Een zoek index maken en laden met documenten](tutorial-javascript-create-load-index.md)
* [Uw statische web-app implementeren](tutorial-javascript-deploy-static-web-app.md)
