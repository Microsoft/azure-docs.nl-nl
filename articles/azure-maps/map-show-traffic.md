---
title: Verkeer op een kaart weer geven | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van verkeers gegevens aan Maps. Meer informatie over stroom gegevens en hoe u de Azure Maps Web-SDK kunt gebruiken om incident gegevens en stroom gegevens toe te voegen aan Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b6fcf39e05c4649503f0b7a80aadaaa16df24674
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599548"
---
# <a name="show-traffic-on-the-map"></a>Verkeer weergeven op de kaart

Er zijn twee soorten verkeers gegevens beschikbaar in Azure Maps:

- Incident gegevens: bestaat uit gegevens op basis van een punt en lijn voor dingen, zoals bouw, wegsluitingen en ongel ukken.
- Stroom gegevens: voorziet in metrische informatie over de stroom van verkeer op de wegen. De verkeers stroom gegevens worden vaak gebruikt om de wegen te kleuren. De kleuren zijn gebaseerd op de hoeveelheid verkeer die de stroom vertraagt, ten opzichte van de snelheids limiet of een andere metriek. Er zijn vier waarden die kunnen worden door gegeven aan de verkeers `flow` optie van de kaart.

    |Stroom waarde | Beschrijving|
    | :-- | :-- |
    | `none` | Verkeers gegevens worden niet weer gegeven op de kaart |
    | `relative` | Geeft verkeers gegevens weer die relatief zijn ten opzichte van de vrije stroom snelheid van de weg |
    | `relative-delay` | Hiermee worden gebieden weer gegeven die langzamer zijn dan de gemiddelde verwachte vertraging |
    | `absolute` | Toont de absolute snelheid van alle Voer tuigen op de weg |

De volgende code laat zien hoe verkeers gegevens op de kaart worden weer gegeven.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='Verkeer op een kaart weer geven' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie het <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>netwerk verkeer weer geven op een kaart</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opties voor verkeersgegevens als overlay

Met het volgende hulp programma kunt u scha kelen tussen de verschillende instellingen van Traffic overlay om te zien hoe de rendering verandert. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opties voor verkeersgegevens als overlay" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>bedekkings opties</a> voor pen-verkeer per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Verkeers besturings elementen toevoegen

Er zijn twee verschillende verkeers besturings elementen die aan de kaart kunnen worden toegevoegd. Het eerste besturings element, `TrafficControl` , voegt een wissel knop toe die kan worden gebruikt om verkeer in of uit te scha kelen. Met de opties voor dit besturings element kunt u opgeven wanneer verkeers instellingen moeten worden gebruikt wanneer verkeer wordt weer gegeven. Standaard worden met dit besturings element relatieve verkeers stroom en incident gegevens weer gegeven, maar u kunt dit wijzigen om zo nodig een absolute verkeers stroom en geen incidenten weer te geven. Het tweede besturings element, `TrafficLegendControl` , voegt een verkeers stroom toe aan de kaart waarmee de gebruiker kan begrijpen wat de kleur van de verkeers markeringen betekenen. Dit besturings element wordt alleen weer gegeven op de kaart wanneer er verkeer stroom gegevens op de kaart worden weer gegeven en op alle andere tijdstippen worden verborgen.

De volgende code laat zien hoe u de besturings elementen voor verkeer kunt toevoegen aan de kaart.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Verkeers controles" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>bedienings elementen</a> voor pen-verkeer door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Kaart](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

Verbeter uw gebruikers ervaring:

> [!div class="nextstepaction"]
> [Interactie met muis gebeurtenissen koppelen](map-events.md)

> [!div class="nextstepaction"]
> [Een toegankelijke kaart bouwen](map-accessibility.md)

> [!div class="nextstepaction"]
> [Voorbeeld pagina voor code](https://aka.ms/AzureMapsSamples)