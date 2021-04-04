---
title: Verbinding maken met een WFS-service (Web Feature Service) | Microsoft Azure kaarten
description: Meer informatie over hoe u verbinding maakt met een WFS-service en hoe u vervolgens een query kunt uitvoeren op de WFS-service met de Azure Maps Web SDK en de ruimtelijke IO-module.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75a7da41e4f0d5356ca135eb3cb4cbe5f29e8116
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891445"
---
# <a name="connect-to-a-wfs-service"></a>Verbinding maken met een WFS-service

Een Web Feature Service (WFS) is een webservice voor het opvragen van ruimtelijke gegevens met een gestandaardiseerde API die is gedefinieerd door de Open Geospatial Consortium (OGC). `WfsClient`Met de klasse in de ruimtelijke IO-module kunnen ontwikkel aars verbinding maken met een WFS-service en query's uitvoeren op gegevens van de service.

De volgende functies worden ondersteund door de `WfsClient` klasse:

- Ondersteunde versies: `1.0.0` , `1.1.0` en `2.0.0`
- Ondersteunde filter operators: binaire vergelijkingen, logica, wiskunde, waarde en `bbox` .
- Aanvragen worden alleen gemaakt met behulp van `HTTP GET` .
- Ondersteunde bewerkingen:

    | Bewerking | Beschrijving |
    | :-- | :-- |
    | GetCapabilities | Hiermee wordt een meta gegevens document gegenereerd met geldige WFS-bewerkingen en-para meters |
    | GetFeature | Hiermee wordt een selectie van functies uit een gegevens bron geretourneerd |
    | DescribeFeatureType | Retourneert de ondersteunde functie typen |

## <a name="using-the-wfs-client"></a>De WFS-client gebruiken

Met de `atlas.io.ogc.WfsClient` klasse in de ruimtelijke IO-module kunt u eenvoudig een query uitvoeren op een WFS-service en de antwoorden converteren naar GEOjson-objecten. Dit geojson-object kan vervolgens worden gebruikt voor andere toewijzings doeleinden.

Met de volgende code wordt een query uitgevoerd op een WFS-service en worden de geretourneerde functies op de kaart weer gegeven.

<br/>

<iframe height='700' scrolling='no' title='Eenvoudig WFS-voor beeld' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bekijk het <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>eenvoudige WFS-voor beeld</a> van Pen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Ondersteunde filters

De specificatie voor de WFS-standaard maakt gebruik van OGC-filters. De onderstaande filters worden ondersteund door de WFS-client, ervan uitgaande dat de service die wordt aangeroepen ook deze filters ondersteunt. Aangepaste filter teken reeksen kunnen worden door gegeven aan de `CustomFilter` klasse.

**Logische operators**

- `And`
- `Or`
- `Not`

**Operator waarde**

- `GmlObjectId`
- `ResourceId`

**Reken kundige Opera tors**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Vergelijkingsoperatoren**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

De volgende code toont het gebruik van verschillende filters met de WFS-client.

<br/>

<iframe height='500' scrolling='no' title= 'Voor beelden van WFS-filter' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de WFS-Azure Maps <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>voor beelden</a> voor pen-filters op <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>().
</iframe>

## <a name="wfs-service-explorer"></a>WFS service Explorer

De volgende code maakt gebruik van de WFS-client om WFS services te verkennen. Selecteer een laag met het type eigenschap in de service en Bekijk de bijbehorende legenda.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS service Explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS service Explorer</a> door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

Om toegang te krijgen tot WFS-services die worden gehost op niet-CORS-eind punten, kan een proxy service met CORS worden door gegeven aan de `proxyService` optie van de WFS-client, zoals hieronder wordt weer gegeven. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Kernbewerkingen gebruiken](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Details van ondersteunde gegevensindeling](spatial-io-supported-data-format-details.md)