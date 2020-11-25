---
title: De Azure Maps Services-module gebruiken | Microsoft Azure kaarten
description: Meer informatie over de Azure Maps Services-module. Zie deze helper-bibliotheek laden en gebruiken voor toegang tot Azure Maps REST-services in web-of Node.js toepassingen.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008596"
---
# <a name="use-the-azure-maps-services-module"></a>De Azure Maps Services-module gebruiken

De Azure Maps Web-SDK biedt een *Services-module*. Deze module is een helper-bibliotheek waarmee u gemakkelijk de Azure Maps REST-services in web-of Node.js toepassingen kunt gebruiken met behulp van Java script of type script.

## <a name="use-the-services-module-in-a-webpage"></a>De Services-module op een webpagina gebruiken

1. Maak een nieuw HTML-bestand.
1. Laad de Azure Maps Services-module. U kunt deze op twee manieren laden:
    - Gebruik de wereld wijd gehoste Azure Content Delivery Network-versie van de module Azure Maps Services. Voeg een script verwijzing toe aan het `<head>` element van het bestand:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - U kunt ook de Services-module voor de Azure Maps Web SDK-bron code lokaal laden met behulp van het [Azure-Maps-rest NPM-](https://www.npmjs.com/package/azure-maps-rest) pakket en dit vervolgens hosten met uw app. Dit pakket bevat ook TypeScript-definities. Gebruik hiervoor deze opdracht:
    
        > **npm install azure-maps-rest**
    
        Voeg vervolgens een script verwijzing toe aan het `<head>` element van het bestand:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Maak een verificatie pijplijn. De pijp lijn moet worden gemaakt voordat u een service-URL-client eindpunt kunt initialiseren. Gebruik uw eigen Azure Maps account sleutel of Azure Active Directory (Azure AD)-referenties om een Azure Maps Search-serviceclient te verifiëren. In dit voor beeld wordt de URL-client van de zoek service gemaakt. 

    Als u een abonnements sleutel voor verificatie gebruikt:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Als u Azure AD gebruikt voor verificatie:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Zie [verificatie met Azure Maps](azure-maps-authentication.md)voor meer informatie.

1. De volgende code maakt gebruik van de zojuist gemaakte Azure Maps Search service URL-client naar Geocode a address: "1 micro soft Way, Redmond, WA". De code gebruikt de `searchAddress` functie en geeft de resultaten weer als een tabel in de hoofd tekst van de pagina.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Hier volgt het volledige, actieve code voorbeeld:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="De Services-module gebruiken" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Bekijk de pen <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>met behulp van de Services-module</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Cloud ondersteuning Azure Government

De Azure Maps Web-SDK ondersteunt de Azure Government Cloud. Alle Java script-en CSS-Url's die worden gebruikt voor toegang tot de Azure Maps Web-SDK blijven hetzelfde, maar de volgende taken moeten worden uitgevoerd om verbinding te maken met de Azure Government Cloud versie van het Azure Maps platform.

Wanneer u het besturings element interactieve map gebruikt, voegt u de volgende regel code toe voordat u een instantie van de `Map` klasse maakt. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Zorg ervoor dat u een Azure Maps verificatie gegevens van het Azure Government Cloud platform gebruikt wanneer u de kaart en services verifieert.

Wanneer u de Services-module gebruikt, moet het domein voor de services worden ingesteld bij het maken van een exemplaar van een API-URL-eind punt. Met de volgende code wordt bijvoorbeeld een instantie van de `SearchURL` klasse gemaakt en wordt het domein naar de Azure Government Cloud genoteerd.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Als u rechtstreeks toegang hebt tot de Azure Maps REST-services, wijzigt u het URL-domein in `atlas.azure.us` . Als u bijvoorbeeld de Search API-service gebruikt, wijzigt u het URL-domein van `https://atlas.microsoft.com/search/` in naar `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

Zie de volgende artikelen voor meer code voorbeelden die gebruikmaken van de Services-module:

> [!div class="nextstepaction"]
> [Zoek resultaten weer geven op de kaart](./map-search-location.md)

> [!div class="nextstepaction"]
> [Informatie ophalen uit een coördinaat](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Routebeschrijving van A naar B](./map-route.md)