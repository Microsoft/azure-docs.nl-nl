---
title: Besturings elementen toevoegen aan een Android-kaart | Microsoft Azure kaarten
description: Het toevoegen van een zoom besturings element, besturings element pitch, Control en een stijl kiezer aan een kaart in Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100216"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Besturings elementen toevoegen aan een kaart (Android SDK)

Dit artikel laat u zien hoe u UI-besturings elementen aan de kaart kunt toevoegen.

## <a name="add-zoom-control"></a>Zoom besturings element toevoegen

Een zoom besturings element voegt knoppen toe om in en uit te zoomen op de kaart. In het volgende code voorbeeld wordt een exemplaar van de `ZoomControl` klasse gemaakt en toegevoegd aan een kaart.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

De onderstaande scherm afbeelding is een zoom besturings element dat is geladen op een kaart.

![Zoom besturings element is toegevoegd aan kaart](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Besturings element voor Pitch toevoegen

Met een besturings element pitch voegt u knoppen toe voor het kantelen van de hoogte om toe te wijzen aan de horizon. In het volgende code voorbeeld wordt een exemplaar van de `PitchControl` klasse gemaakt en toegevoegd aan een kaart.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

De onderstaande scherm afbeelding is een besturings element dat op een kaart wordt geladen.

![Besturings element waarmee de hoogte wordt toegevoegd aan de kaart](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Besturings element kompas toevoegen

Met een kompas besturings element wordt een knop voor het draaien van de kaart toegevoegd. In het volgende code voorbeeld wordt een exemplaar van de `CompassControl` klasse gemaakt en toegevoegd aan een kaart.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

De onderstaande scherm afbeelding is een kompas besturings element dat is geladen op een kaart.

![Het besturings element kompas dat is toegevoegd aan de kaart](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Verkeers beheer toevoegen

Een verkeers controle voegt een knop toe om de zicht baarheid van verkeers gegevens op de kaart te scha kelen. In het volgende code voorbeeld wordt een exemplaar van de `TrafficControl` klasse gemaakt en toegevoegd aan een kaart.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

De onderstaande scherm afbeelding is een verkeers controle die is geladen op een kaart.

![Traffic Control is toegevoegd aan de kaart](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Een kaart met alle besturings elementen

Meerdere besturings elementen kunnen in een matrix worden geplaatst en in één keer aan de kaart worden toegevoegd en in hetzelfde gebied van de kaart worden geplaatst om de ontwikkeling te vereenvoudigen. In het volgende voor beeld wordt de standaard navigatie besturings elementen aan de kaart toegevoegd met behulp van deze methode.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

In de onderstaande scherm afbeelding ziet u alle besturings elementen die worden geladen op een kaart. De volg orde waarin ze worden toegevoegd aan de kaart is de volg orde waarin ze worden weer gegeven.

![Alle besturings elementen die zijn toegevoegd aan de kaart](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
