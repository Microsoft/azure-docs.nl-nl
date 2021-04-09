---
title: Aan de slag met Microsoft Azure Maps web map-besturings element
description: Meer informatie over het toevoegen van Maps aan web-en mobiele toepassingen met behulp van de Map Control java script-bibliotheek aan de client zijde in Azure Maps. Zie overzichten lokaliseren.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b283cacf94ff3567428787e50a3d7db9ba91fd9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864037"
---
# <a name="use-the-azure-maps-map-control"></a>Het kaartbesturingselement in Azure Maps gebruiken

Met de Map Control java script-bibliotheek aan de client zijde kunt u kaarten en Inge sloten Azure Maps functionaliteit weer geven in uw web-of mobiele toepassing.

In deze documentatie wordt gebruikgemaakt van de Azure Maps Web-SDK, maar de Azure Maps Services kunnen worden gebruikt met elk kaart besturings element. [Hier](open-source-projects.md#third-part-map-control-plugins) vindt u een aantal populaire open-source kaart besturings elementen waarvoor het Azure Maps-team een invoeg toepassing heeft gemaakt.

## <a name="prerequisites"></a>Vereisten

Als u de Map Control op een webpagina wilt gebruiken, moet u beschikken over een van de volgende vereisten:

* [Maak een Azure Maps-account](quick-demo-map-app.md#create-an-azure-maps-account) en [Verkrijg een sleutel voor een primair abonnement](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnements sleutel.

* Vraag uw Azure Active Directory-referenties (AAD) aan met [verificatie opties](/javascript/api/azure-maps-control/atlas.authenticationoptions).

## <a name="create-a-new-map-in-a-web-page"></a>Een nieuwe kaart maken op een webpagina

U kunt een kaart insluiten in een webpagina met behulp van de Map Control java script-bibliotheek aan de client zijde.

1. Maak een nieuw HTML-bestand.

2. Laden in de Azure Maps Web-SDK. U kunt een van de volgende twee opties kiezen:

    * Gebruik de wereld wijd gehoste CDN-versie van de Azure Maps Web-SDK door verwijzingen toe te voegen aan het Java script en het opmaak model in het `<head>` element van het HTML-bestand:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Laad de Azure Maps Web SDK-bron code lokaal met behulp van het [Azure-Maps-Control NPM-](https://www.npmjs.com/package/azure-maps-control) pakket en host dit met uw app. Dit pakket bevat ook TypeScript-definities.

      > **NPM Azure-Maps-beheer installeren**

    Voeg vervolgens verwijzingen naar het Azure Maps opmaak model toe aan het `<head>` element van het bestand:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > Type script definities kunnen in uw toepassing worden geïmporteerd door de volgende code toe te voegen:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Als u de kaart zo wilt genereren dat de volledige hoofd tekst van de pagina wordt gevuld, voegt u het volgende `<style>` element toe aan het- `<head>` element.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. Voeg in de hoofd tekst van de pagina een `<div>` element toe en geef het de naam `id` **myMap**.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Nu gaan we het kaart besturings element initialiseren. Als u het besturings element wilt verifiëren, moet u eigenaar zijn van een Azure Maps-abonnements sleutel of gebruikmaken van de referenties van Azure Active Directory (AAD) met [verificatie opties](/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Als u een abonnements sleutel voor verificatie gebruikt, kopieert en plakt u het volgende script-element in het `<head>` -element en onder het eerste `<script>` element. Vervang door `<Your Azure Maps Key>` uw Azure Maps primaire abonnements sleutel.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    Als u Azure Active Directory (AAD) gebruikt voor verificatie, kopieert en plakt u het volgende script-element in het `<head>` -element en onder het eerste `<script>` element.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            });
        }
    </script>
   ```

    Zie [verificatie met Azure Maps](azure-maps-authentication.md) document voor meer informatie over verificatie met Azure Maps. Daarnaast vindt u [hier](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)een lijst met voor beelden waarin wordt getoond hoe u Azure Active Directory (Aad) met Azure Maps kunt integreren.

    >[!TIP]
    >In dit voor beeld zijn we door gegeven in de `id` kaart `<div>` . Een andere manier om dit te doen, is door door te geven in het `HTMLElement` object door door te geven `document.getElementById('myMap')` als de eerste para meter.

6. U kunt er ook voor kiezen om de volgende elementen toe te voegen `meta` aan het `head` element van de pagina:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Het HTML-bestand moet er ongeveer als volgt uitzien:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Open het bestand in uw webbrowser en Bekijk de weer gegeven kaart. De afbeelding moet er als volgt uitzien:

   ![Kaart afbeelding die het gegenereerde resultaat weergeeft](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokaliseren van de kaart

Azure Maps biedt twee verschillende manieren om de taal-en regionale weer gave voor de gerenderde kaart in te stellen. De eerste optie is om deze informatie toe te voegen aan de globale `atlas` naam ruimte, wat resulteert in alle instanties van de kaart besturing in uw app die standaard worden ingesteld op deze instellingen. Met de volgende code stelt u de taal in op Frans ('fr-FR') en de regionale weergave op 'Auto':

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

De tweede optie is om deze informatie door te geven aan de kaart opties bij het laden van de kaart zoals deze:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> Het is mogelijk om meerdere toewijzings exemplaren op dezelfde pagina met andere taal-en regio-instellingen te laden. Deze instellingen kunnen bovendien worden bijgewerkt na de kaart belasting met behulp `setStyle` van de functie van de kaart.

Hier volgt een voor beeld van Azure Maps waarbij de taal is ingesteld op "fr-FR" en de regionale weer gave ingesteld op ' auto '.

![Kaart afbeelding met labels in het Frans](./media/how-to-use-map-control/websdk-localization.png)

Een volledige lijst met ondersteunde talen en regionale weer gaven wordt [hier](supported-languages.md)beschreven.

## <a name="azure-government-cloud-support"></a>Cloud ondersteuning Azure Government

De Azure Maps Web-SDK ondersteunt de Azure Government Cloud. Alle Java script-en CSS-Url's die worden gebruikt voor toegang tot de Azure Maps Web-SDK blijven hetzelfde. De volgende taken moeten worden uitgevoerd om verbinding te maken met de Azure Government Cloud versie van het Azure Maps platform.

Wanneer u het besturings element interactieve map gebruikt, voegt u de volgende regel code toe voordat u een instantie van de `Map` klasse maakt.

```javascript
atlas.setDomain('atlas.azure.us');
```

Zorg ervoor dat u Azure Maps verificatie gegevens van het Azure Government Cloud platform gebruikt wanneer u de kaart en services verifieert.

Wanneer u de Services-module gebruikt, moet het domein voor de services worden ingesteld bij het maken van een exemplaar van een API-URL-eind punt. Met de volgende code wordt bijvoorbeeld een instantie van de `SearchURL` klasse gemaakt en wordt het domein naar de Azure Government Cloud genoteerd.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Als u rechtstreeks toegang hebt tot de Azure Maps REST-services, wijzigt u het URL-domein in `atlas.azure.us` . Als u bijvoorbeeld de Search API-service gebruikt, wijzigt u het URL-domein van `https://atlas.microsoft.com/search/` in naar `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>Java script-Frameworks

Bij het ontwikkelen met behulp van een JavaScript-framework kan een van de volgende opensource-projecten nuttig zijn:

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): Angular 10-wrapper rond Azure-kaarten.
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): een Azure Maps Blazor-onderdeel.
* [Azure Maps React-onderdeel](https://github.com/WiredSolutions/react-azure-maps): een react-wrapper voor het Azure Maps-besturingselement.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps): een Azure Maps-onderdeel voor de Vue-toepassing.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken en gebruiken van een kaart:

> [!div class="nextstepaction"]
> [Een kaart maken](map-create.md)

Meer informatie over het opmaken van een kaart:

> [!div class="nextstepaction"]
> [Een kaartstijl kiezen](choose-map-style.md)

Lees de aanbevolen procedures en Bekijk voor beelden:

> [!div class="nextstepaction"]
> [Aanbevolen procedures](web-sdk-best-practices.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](/samples/browse/?products=azure-maps)

Zie voor een lijst met voor beelden die laten zien hoe u Azure Active Directory (AAD) integreert met Azure Maps:

> [!div class="nextstepaction"]
> [Voor beelden van Azure AD-verificatie](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
