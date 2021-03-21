---
title: Een tegel laag toevoegen aan de Azure Maps Power BI Visual | Microsoft Azure kaarten
description: In dit artikel leert u hoe u de tegel laag kunt gebruiken in de Visual Microsoft Azure Maps voor Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896205"
---
# <a name="add-a-tile-layer"></a>Een titellaag toevoegen

Met de functie voor de tegel laag, zoals de functie referentielaag, kunnen extra gegevens op de kaart worden overlapt om meer context te bieden. Met tegel lagen kunt u afbeeldingen boven op de Azure Maps basis kaart tegels plaatsen. Dit is een uitstekende manier om grote of complexe gegevens sets te bedekken, zoals installatie kopieën van Drones, of miljoenen rijen.

> [!div class="mx-imgBorder"]
> ![Een kaart met een laag met de huidige infra rood weer gegeven gegevens van Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Een tegel laag wordt in tegels van een server geladen. Deze installatie kopieën kunnen vooraf worden weer gegeven of dynamisch worden weer gegeven. Vooraf gerenderde installatie kopieën worden op dezelfde manier opgeslagen als andere installatie kopieën op een server met behulp van een naamgevings Conventie die de laag van de tegel begrijpt. Dynamisch gerenderde afbeeldingen gebruiken een service om de afbeeldingen in realtime te laden. Tegel lagen zijn een uitstekende manier om grote gegevens sets op de kaart te visualiseren. U kunt niet alleen een tegel laag genereren op basis van een afbeelding, maar u kunt ook vector gegevens weer geven als een tegel laag.

Het selectie kader en het zoom bereik waar een tegel service beschikbaar is, kunnen worden door gegeven als instellingen om te beperken waar tegels worden aangevraagd. Dit is zowel een prestatie verbetering voor zowel de Visual als de tegel service. Hieronder vindt u een overzicht van alle beschik bare instellingen in het deel venster **opmaak** die beschikbaar zijn in de sectie laag van de **tegel** .

| Instelling        | Beschrijving   |
|----------------|---------------|
| URL            | Een ingedeelde URL die verwijst naar een tegel service.  |
| Tegelgrootte      | Een geheel getal dat de breedte en hoogte van de tegels aangeeft.   |
| Noord-gebonden    | De noordelijke breedte van het selectie kader waarin tegels beschikbaar zijn. |
| Zuid gebonden    | De geografische breedte van het selectie kader waarin tegels beschikbaar zijn. |
| Oost-gebonden     | De lengte graad van het selectie kader waarin tegels beschikbaar zijn.  |
| Westelijke grens     | Westerse lengte graad van het selectie kader waarin tegels beschikbaar zijn.   |
| Transparantie   | De transparantie van de laag van de tegel.   |
| Is TMS         | Tegel toewijzing Services, een specificatie waarmee de Y-coördinaatas van de laag van de tegel wordt omgekeerd. |
| Min. zoom niveau       | De tegels met mini maal zoom niveau zijn beschikbaar. |
| Maxi maal zoom niveau       | Er zijn maximale zoom niveau tegels beschikbaar.  |
| Laag positie | Geeft de positie van de laag aan ten opzichte van andere kaart lagen. |

## <a name="tile-url-formatting"></a>Tegel-URL-indeling

Er worden drie verschillende naamgevings conventies voor tegel Services ondersteund door de Azure Maps Visual:

-   **X, Y, zoom notatie** -X is de kolom, Y is de rijpositie van de tegel in het tegel raster en de zoom notatie een waarde op basis van het zoom niveau.
-   **Quadkey notatie** : Hiermee wordt de x-, y-en zoom informatie gecombineerd tot één teken reeks waarde. Deze teken reeks waarde wordt een unieke id voor één tegel.
-   **Begrenzingsvak** : Geef een afbeelding op in de coördinaten notatie van het begrenzingsvak `{west},{south},{east},{north}` . Deze indeling wordt vaak gebruikt door [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

De tegel-URL een HTTPS-URL naar een URL-sjabloon voor tegels die gebruikmaakt van de volgende para meters:

-   `{x}` -X-positie van de tegel. Ook nodig `{y}` en `{z}` .
-   `{y}` -Y-positie van de tegel. Ook nodig `{x}` en `{z}` .
-   `{z}` -Zoom niveau van de tegel. Ook nodig `{x}` en `{y}` .
-   `{quadkey}` -Tegel- `quadkey` id gebaseerd op de naam Conventie voor Bing Maps-tegel systemen.
-   `{bbox-epsg-3857}` -Een teken reeks voor selectie kader met de indeling `{west},{south},{east},{north}` in het EPSG 3857 Spatial Reference System.

Het volgende voor beeld is een indelings-URL met een opmaak die wordt [weer](/rest/api/maps/renderv2/getmaptilepreview) gegeven in azure Maps. Opmerking: dit `[subscription-key]` is een tijdelijke aanduiding voor uw Azure Maps-abonnements sleutel.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Zie [zoom niveaus en tegel raster](zoom-levels-and-tile-grid.md)voor meer informatie over Azure Maps tegel systeem.

## <a name="next-steps"></a>Volgende stappen

Meer context toevoegen aan de kaart:

> [!div class="nextstepaction"]
> [Realtime verkeer weergeven](power-bi-visual-show-real-time-traffic.md)