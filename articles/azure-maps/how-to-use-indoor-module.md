---
title: De module voor het Azure Maps van binnenste kaarten gebruiken met micro soft Creator Services (preview)
description: Meer informatie over het gebruik van de module Microsoft Azure kaarten in de kaart om kaarten weer te geven door de Java script-bibliotheken van de module in te sluiten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: bc80b7dfd433911ef13906db38f59a76827db258
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905278"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>De module voor kaarten van Azure Maps gebruiken

> [!IMPORTANT]
> Azure Maps Creator-Services zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De Azure Maps Web-SDK bevat de module *Azure Maps binnenshuis* . Met de module  *Azure Maps binnenshuis* kunt u kaarten weer geven die zijn gemaakt in azure Maps Creator-Services (preview) 

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een bron voor het maken van een maker (preview-resource)](how-to-manage-creator.md)
3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.
4. Vraag a `tilesetId` en a `statesetId` aan de hand van de [zelf studie voor het maken van plattegronden](tutorial-creator-indoor-maps.md).
 U moet deze id's gebruiken om kaarten weer te geven met de module Azure Maps kaarten.

## <a name="embed-the-indoor-maps-module"></a>De module voor de binnenste kaarten insluiten

U kunt de module *Azure Maps binnenshuis* op een van de volgende twee manieren installeren en insluiten.

Als u de wereld wijd gehoste versie van Azure Content Delivery Network van de module *Azure Maps binnenshuis* wilt gebruiken, raadpleegt u de volgende Java script-en stijl blad verwijzingen in het `<head>` element van het HTML-bestand:

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 Of u kunt de module *Azure Maps binnenshuis* downloaden. De module *Azure Maps binnenshuis* bevat een client bibliotheek voor het verkrijgen van toegang tot Azure Maps Services. Volg de onderstaande stappen voor het installeren en laden van de module *binnen* in uw webtoepassing.  
  
  1. Installeer het [pakket Azure-Maps-binnenshuis](https://www.npmjs.com/package/azure-maps-indoor).
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. Verwijs naar het *Azure Maps* module-java script en stijl blad in het `<head>` element van het HTML-bestand:

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Het kaart object instantiëren

Maak eerst een *kaart object*. Het *kaart object* wordt in de volgende stap gebruikt om het object voor de *binnenste Manager* te instantiëren.  De volgende code laat zien hoe u een exemplaar van het *kaart object* maakt:

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13315, 47.63637],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Instantiëren van de manager in de binnenshuis

Als u de tilesets en kaart stijl van de tegels wilt laden, moet u een exemplaar maken van de *binnenste Manager*. Maak een exemplaar van de *binnenste Manager* door het *kaart object* en de bijbehorende te voorzien `tilesetId` . Als u de stijl van [dynamische kaarten](indoor-map-dynamic-styling.md)wilt ondersteunen, moet u de door geven `statesetId` . De `statesetId` naam van de variabele is hoofdletter gevoelig. Uw code moet zoals hieronder de Java script.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

Als u polling van door u verstrekte status gegevens wilt inschakelen, moet u de `statesetId` aanroep en aanroepen `indoorManager.setDynamicStyling(true)` . Met polling status gegevens kunt u de status van dynamische eigenschappen of *statussen* dynamisch bijwerken. Zo kan een functie zoals room een dynamische eigenschap (*State*) met de naam hebben `occupancy` . Het kan voor komen dat uw toepassing een *status* wijziging doorvoert om de wijziging in de visuele kaart weer te geven. De volgende code laat zien hoe u status polling inschakelt:

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Besturings element voor kiezer op niveau binnen

 Met het besturings element op het *niveau* van de inschakeling kunt u het niveau van de gerenderde kaart wijzigen. U kunt eventueel het besturings element voor het kiezen van het niveau van de *binnenste* van de *Manager* initialiseren. Hier volgt de code voor het initialiseren van de beheer groep voor niveau:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Gebeurtenissen in de binnenshuis

 De module *Azure Maps binnen* in ondersteunt *kaart object* gebeurtenissen. De Event listeners van het *kaart object* worden aangeroepen wanneer een niveau of faciliteit is gewijzigd. Als u code wilt uitvoeren wanneer een niveau of een faciliteit is gewijzigd, plaatst u de code in de gebeurtenislistener. De volgende code laat zien hoe gebeurtenislisteners kunnen worden toegevoegd aan het *kaart object*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

De `eventData` variabele bevat informatie over het niveau of de faciliteit die respectievelijk het `levelchanged` of de `facilitychanged` gebeurtenis heeft aangeroepen. Wanneer een niveau wordt gewijzigd, `eventData` bevat het object de `facilityId` , de nieuwe `levelNumber` en andere meta gegevens. Wanneer een faciliteit wordt gewijzigd, `eventData` bevat het object de nieuwe `facilityId` , de nieuwe `levelNumber` en andere meta gegevens.

## <a name="example-use-the-indoor-maps-module"></a>Voor beeld: gebruik de module binnenste kaarten

In dit voor beeld ziet u hoe u de module *Azure Maps binnenshuis* gebruikt in uw webtoepassing. Hoewel het voor beeld beperkt is, zijn de basis principes van wat u nodig hebt om aan de slag te gaan met behulp van de module *Azure Maps binnenshuis* . De volledige HTML-code bevindt zich onder de volgende stappen.

1. Gebruik de Azure-Content Delivery Network [optie](#embed-the-indoor-maps-module) om de module *Azure Maps binnenshuis* te installeren.

2. Een nieuw HTML-bestand maken

3. In de HTML-header verwijzen we naar de *Azure Maps* module-java script en stijl blad stijlen.

4. Initialiseer een *kaart object*. Het *kaart-object* ondersteunt de volgende opties:
    - `Subscription key` is uw Azure Maps primaire abonnements sleutel.
    - `center` Hiermee definieert u een breedte graad en lengte graad voor de locatie van het kaarten centrum. Geef een waarde op `center` Als u geen waarde wilt opgeven voor `bounds` . De notatie moet er als volgt uitzien `center` : [-122,13315, 47,63637].
    - `bounds` is de kleinste rechthoekige vorm die de kaart gegevens van de tegelset omsluit. Stel een waarde in voor `bounds` Als u geen waarde wilt instellen voor `center` . U kunt de toewijzings grenzen vinden door de List-API van de [tegelset](/rest/api/maps/tileset/listpreview)aan te roepen. De listing-API van de Tegelset retourneert de `bbox` , die u kunt parseren en toewijzen `bounds` . De notatie moet er als volgt uitzien `bounds` : [# West, # Zuid, # East, # Noord].
    - `style` Hiermee kunt u de kleur van de achtergrond instellen. Als u een witte achtergrond wilt weer geven, definieert u `style` als leeg.
    - `zoom` Hiermee kunt u het minimum-en maximum zoom niveau voor de kaart opgeven.

5. Maak vervolgens de module voor het beheer van de *binnenste* . Wijs het *Azure Maps binnen* `tilesetId` en voeg eventueel de toe `statesetId` .

6. Instantie van de kiezer van het besturings element op het niveau van de *hoogte*

7. Voeg de gebeurtenislisteners van het *kaart object* toe.  

Het bestand moet er nu ongeveer als volgt uitzien.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

Als u de kaart wilt weer geven, laadt u deze in een webbrowser. Deze moet eruitzien als de onderstaande afbeelding. Als u op de functie stairwell klikt, wordt de *niveau kiezer* weer gegeven in de rechter bovenhoek.

  ![kaart afbeelding binnenste](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Api's die zijn gerelateerd aan de module *Azure Maps binnenshuis* :

> [!div class="nextstepaction"]
> [Vereisten voor tekenpakketten](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Creator (preview) voor kaarten in de binnenste](creator-indoor-maps.md)

Meer informatie over het toevoegen van meer gegevens aan uw kaart:

> [!div class="nextstepaction"]
> [Dynamische stijlen voor kaarten in de binnenste](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](/samples/browse/?products=azure-maps)