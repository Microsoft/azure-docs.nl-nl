---
title: Een kaart maken met Azure Maps | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van Maps aan webpagina's met behulp van de Azure Maps Web-SDK. Meer informatie over opties voor animatie, stijl, camera, services en gebruikers interacties.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 833b6413cc5dfde1129075a286e5fe93a06e159f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890918"
---
# <a name="create-a-map"></a>Een kaart maken

Dit artikel laat u zien hoe u een kaart maakt en een kaart kunt animeren.  

## <a name="loading-a-map"></a>Een kaart laden

Als u een kaart wilt laden, maakt u een nieuw exemplaar van de [kaart klasse](/javascript/api/azure-maps-control/atlas.map). Wanneer u de kaart initialiseert, geeft u de ID van een DIV-element door om de kaart weer te geven en een set opties te geven die moeten worden gebruikt bij het laden van de kaart. Als er geen standaard verificatie gegevens zijn opgegeven in de `atlas` naam ruimte, moet deze informatie worden opgegeven in de kaart opties bij het laden van de kaart. De kaart laadt verschillende bronnen asynchroon voor prestaties. Koppel, na het maken van het exemplaar van de kaart, een `ready` of- `load` gebeurtenis aan de kaart en voeg vervolgens extra code toe die met de kaart communiceert met de gebeurtenis-handler. De `ready` gebeurtenis wordt geactiveerd zodra de kaart voldoende resources heeft geladen om via een programma te communiceren. De `load` gebeurtenis wordt geactiveerd nadat het laden van de eerste kaart weergave volledig is voltooid. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eenvoudige kaart belasting" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> U kunt meerdere kaarten op dezelfde pagina laden. Meerdere kaarten op dezelfde pagina kunnen dezelfde of verschillende verificatie-en taal instellingen gebruiken.

## <a name="show-a-single-copy-of-the-world"></a>Eén exemplaar van de wereld weer geven

Wanneer de kaart op een breed scherm wordt ingezoomd, worden meerdere kopieën van de wereld horizon taal weer gegeven. Deze optie is ideaal voor sommige scenario's, maar voor andere toepassingen is het wenselijk om één exemplaar van de wereld te bekijken. Dit gedrag wordt geïmplementeerd door de toewijzings `renderWorldCopies` optie in te stellen op `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = False" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = False</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Kaart opties

Wanneer u een kaart maakt, zijn er verschillende soorten opties die kunnen worden door gegeven om aan te passen hoe de kaart functies zoals hieronder worden weer gegeven.

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) en [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) worden gebruikt om het gebied op te geven dat wordt weer gegeven op de kaart.
- [ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions) worden gebruikt om op te geven hoe de kaart moet communiceren met services die de kaart uitschakelen.
- [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions) worden gebruikt om op te geven dat de kaart moet worden opgemaakt en gerenderd.
- [UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) worden gebruikt om op te geven hoe de kaart moet worden bereikt wanneer de gebruiker met de kaart communiceert. 

Deze opties kunnen ook worden bijgewerkt nadat de kaart is geladen met de `setCamera` functies, `setServiceOptions` , en `setStyle` `setUserInteraction` . 

## <a name="controlling-the-map-camera"></a>De kaart camera beheren

Er zijn twee manieren om het weer gegeven gebied van de kaart in te stellen met behulp van de camera van een kaart. U kunt de camera opties instellen tijdens het laden van de kaart. U kunt de `setCamera` optie ook op elk gewenst moment aanroepen nadat de kaart is geladen om de kaart weergave programmatisch bij te werken.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>De camera instellen

De kaart camera bepaalt wat wordt weer gegeven in de View Port van het canvas van de kaart. Camera opties kunnen worden door gegeven aan de kaart opties wanneer deze worden geïnitialiseerd of door gegeven aan de `setCamera` functie Maps.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

In de volgende code wordt een [kaart object](/javascript/api/azure-maps-control/atlas.map) gemaakt en worden de opties voor centreren en zoomen ingesteld. Eigenschappen van kaarten, zoals centreren en zoom niveau, maken deel uit van de [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>een kaart `CameraOptions` maken </a>met behulp van Azure Location Based Services ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>De grenzen van de camera instellen

Een selectie kader kan worden gebruikt om de kaart camera bij te werken. Als het selectie kader is berekend op basis van punt gegevens, is het vaak handig om ook een waarde voor pixel opvulling op te geven in de camera opties om te bepalen wat de grootte van het pictogram is. Dit helpt ervoor te zorgen dat punten de rand van de kaart-View Port niet vallen.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

In de volgende code wordt een [kaart object](/javascript/api/azure-maps-control/atlas.map) gemaakt via `new atlas.Map()` . Kaart eigenschappen zoals `CameraBoundsOptions` kan worden gedefinieerd via de functie [setCamera](/javascript/api/azure-maps-control/atlas.map) van de klasse map. Eigenschappen voor grenzen en opvulling worden ingesteld met `setCamera` .

<br/>

<iframe height='500' scrolling='no' title='Een kaart maken via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>een kaart `CameraBoundsOptions` maken </a>met behulp van Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Kaart weergave animeren

Wanneer u de camera opties van de kaart instelt, kunnen er ook [animatie opties](/javascript/api/azure-maps-control/atlas.animationoptions) worden ingesteld. Met deze opties geeft u het type animatie en de duur op waarmee de camera moet worden verplaatst.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

In de volgende code wordt in het eerste code blok een kaart gemaakt en worden de stijlen voor het invoeren en inzoomen ingesteld. In het tweede code blok wordt een gebeurtenis-handler voor klikken gemaakt voor de knop animatie. Wanneer u op deze knop klikt, `setCamera` wordt de functie aangeroepen met een wille keurige waarde voor [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) en [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Kaart weergave animeren' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/WayvbO/'>kaart weergave</a> van de pen animeren per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="request-transforms"></a>Trans formaties aanvragen

Soms is het handig om HTTP-aanvragen te wijzigen die zijn gemaakt door het kaart besturings element. Bijvoorbeeld:

- Voeg extra headers toe aan tegel aanvragen. Dit wordt vaak gedaan voor met wacht woord beveiligde services.
- Wijzig Url's om aanvragen uit te voeren via een proxy service.

De [Service opties](/javascript/api/azure-maps-control/atlas.serviceoptions) van de kaart `transformRequest` kunnen worden gebruikt om alle aanvragen te wijzigen die door de kaart worden gemaakt voordat ze worden gemaakt. De `transformRequest` optie is een functie die twee para meters heeft: een teken reeks-URL en een bron type teken reeks die aangeeft waarvoor de aanvraag wordt gebruikt. Deze functie moet een [RequestParameters](/javascript/api/azure-maps-control/atlas.requestparameters) resultaat retour neren.

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

In het volgende voor beeld ziet u hoe u dit kunt gebruiken om alle aanvragen op de grootte aan te passen `https://example.com` door een gebruikers naam en wacht woord toe te voegen als kopteksten aan de aanvraag.

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>De code uitproberen

Bekijk de code voorbeelden. U kunt de Java script-code bewerken op het **tabblad js** en de kaart weergave wijzigingen weer geven op het **tabblad resultaat**. U kunt ook op **Bewerken klikken op CodePen** in de rechter bovenhoek en de code wijzigen in CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Zie code voorbeelden om functionaliteit toe te voegen aan uw app:

> [!div class="nextstepaction"]
> [Stijl van de kaart wijzigen](choose-map-style.md)

> [!div class="nextstepaction"]
> [Besturingselementen toevoegen aan de kaart](map-add-controls.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](/samples/browse/?products=azure-maps)