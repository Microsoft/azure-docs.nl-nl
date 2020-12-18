---
title: Toewijzings gebeurtenissen in Android-kaarten verwerken | Microsoft Azure kaarten
description: Meer informatie over de gebeurtenissen die worden geactiveerd wanneer gebruikers met kaarten communiceren. Een lijst met alle ondersteunde toewijzings gebeurtenissen weer geven. Zie de Azure Maps Android SDK gebruiken voor het verwerken van gebeurtenissen.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681535"
---
# <a name="interact-with-the-map-android-sdk"></a>Interactie met de kaart (Android SDK)

Dit artikel laat u zien hoe u het beheer van Maps-gebeurtenissen kunt gebruiken.

## <a name="interact-with-the-map"></a>Interactie met de kaart

De kaart beheert alle gebeurtenissen via de bijbehorende `events` eigenschap. De volgende tabel geeft een overzicht van alle ondersteunde toewijzings gebeurtenissen.

| Gebeurtenis                  | Indeling van gebeurtenis-handler | Beschrijving |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Deze gebeurtenis wordt gestart nadat het laatste frame is weer gegeven voordat de kaart de status ' inactief ' krijgt:<ul><li>Er worden geen camera overgangen uitgevoerd.</li><li>Alle momenteel aangevraagde tegels zijn geladen.</li><li>Alle animaties voor vervagen/overgangen zijn voltooid.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Wordt herhaaldelijk geactiveerd tijdens een bewegende overgang van de ene weer gave naar de andere, als gevolg van de interactie of methoden van een gebruiker. |
| `OnCameraMoveCanceled` | `()`                 | Deze gebeurtenis wordt gestart wanneer een verplaatsings aanvraag naar de camera is geannuleerd. |
| `OnCameraMoveStarted`  | `(int reason)`       | Wordt geactiveerd vlak voordat de kaart een overgang van de ene naar de andere weer gave start, als gevolg van de interactie of methoden van de gebruiker. Het `reason` argument van de gebeurtenislistener retourneert een geheel getal dat details geeft over de manier waarop de camera verplaatsing is gestart. Hier volgt een lijst met mogelijke oorzaken:<ul><li>1: beweging</li><li>2: animatie van ontwikkel aars</li><li>3: API-animatie</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Deze gebeurtenis wordt gestart wanneer de kaart op hetzelfde punt op de kaart wordt ingedrukt en losgelaten. |
| `OnFeatureClick`       | `(List<Feature>)`    | Deze gebeurtenis wordt gestart wanneer de kaart op hetzelfde punt in een functie wordt ingedrukt en losgelaten.  |
| `OnLongClick`          | `(double lat, double lon)` | Deze gebeurtenis wordt gestart wanneer de kaart wordt ingedrukt, even geduld en vervolgens op hetzelfde punt op de kaart worden vrijgegeven. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Deze gebeurtenis wordt gestart wanneer de kaart wordt ingedrukt, even geduld en vervolgens op hetzelfde punt op een functie worden losgelaten. |
| `OnReady`              | `(AzureMap map)`     | Deze gebeurtenis wordt gestart wanneer de kaart in eerste instantie wordt geladen of wanneer de afdruk stand van de app wordt gewijzigd en de mini maal vereiste toewijzings resources worden geladen en de kaart gereed is om programmatisch te worden gecommuniceerd met. |

De volgende code laat zien hoe u de `OnClick` -, `OnFeatureClick` -en- `OnCameraMove` gebeurtenissen kunt toevoegen aan de kaart.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

Zie voor meer informatie de documentatie over de [kaart](how-to-use-android-map-control-library.md#navigating-the-map) om te communiceren met de kaart en gebeurtenissen te activeren.

## <a name="scope-feature-events-to-layer"></a>Bereik functie gebeurtenissen op laag

Bij het toevoegen `OnFeatureClick` `OnLongFeatureClick` van de-of-gebeurtenissen aan de kaart kan een laag-id worden door gegeven als een tweede para meter. Wanneer een laag-ID wordt door gegeven, wordt de gebeurtenis alleen geactiveerd als de gebeurtenis op die laag optreedt. Gebeurtenissen die zijn gericht op lagen, worden ondersteund door de lagen Symbol, bellen, lijn en veelhoek.

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor voor beelden van volledige code:

> [!div class="nextstepaction"]
> [Navigeren in de kaart](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Een symbool laag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
