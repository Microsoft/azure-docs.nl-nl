---
title: Een kaart stijl instellen met behulp van Azure Maps Android SDK
description: Meer informatie over twee manieren om de stijl van een kaart in te stellen. Zie How to use the Microsoft Azure Maps Android SDK in het indelings bestand of de klasse activity om de stijl aan te passen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532470"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Kaart stijl instellen met Azure Maps Android SDK

In dit artikel leest u hoe u kaart stijlen kunt instellen met behulp van de Azure Maps Android SDK. Azure Maps heeft zes verschillende kaarten stijlen waaruit u kunt kiezen. Zie [ondersteunde kaart stijlen in azure Maps](./supported-map-styles.md)voor meer informatie over ondersteunde kaart stijlen.

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.
3. Down load en installeer de [Azure Maps ANDROID SDK](./how-to-use-android-map-control-library.md).


## <a name="set-map-style-in-the-layout"></a>Kaart stijl instellen in de lay-out

U kunt een kaart stijl instellen in het indelings bestand voor uw activiteiten klasse. Bewerken `res > layout > activity_main.xml` , zodat deze er als volgt uitziet:

```XML
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

`mapcontrol_style`Met het bovenstaande kenmerk stelt u de stijl van de kaart in op **grayscale_dark**.

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure Maps, kaart afbeelding met stijl als grayscale_dark":::

## <a name="set-map-style-in-the-mainactivity-class"></a>Kaart stijl instellen in de MainActivity-klasse

De kaart stijl kan ook worden ingesteld in de MainActivity-klasse. Open het `java > com.example.myapplication > MainActivity.java` bestand en kopieer het volgende code fragment in de methode **onCreate ()** . Met deze code wordt de kaart stijl ingesteld op **satellite_road_labels**.

>[!WARNING]
>Android Studio zijn mogelijk de vereiste klassen niet geïmporteerd.  Als gevolg hiervan bevat de code enkele onherleidbare-verwijzingen. Als u de vereiste klassen wilt importeren, houdt u de muis aanwijzer boven elke onopgeloste verwijzing en drukt u op `Alt + Enter` (Option + Return op een Mac).

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure Maps, kaart afbeelding met stijl als satellite_road_labels":::