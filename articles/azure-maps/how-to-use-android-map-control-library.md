---
title: Aan de slag met het besturings element Android map | Microsoft Azure kaarten
description: Vertrouwd raken met de Azure Maps Android SDK. Zie een project maken in Android Studio, de SDK installeren en een interactieve kaart maken.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5888a5f34ef65fc1015b6e73af1d03368a8329b2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098516"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Aan de slag met Azure Maps Android SDK

De Azure Maps Android SDK is een vector map-Bibliotheek voor Android. In dit artikel vindt u instructies voor het installeren van de Azure Maps Android SDK en het laden van een kaart.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de stappen in de [Snelstartgids uitvoert: een Android-app](quick-android-map.md) -document maken.

## <a name="localizing-the-map"></a>Lokaliseren van de kaart

De Azure Maps Android SDK biedt drie verschillende manieren om de taal en de regionale weer gave van de kaart in te stellen. De volgende code laat zien hoe u de taal instelt op Frans (fr-FR) en de regionale weer gave op ' auto '.

De eerste optie is de taal door geven en regionale informatie weer geven in de `AzureMaps` klasse met behulp van de statische `setLanguage` methode en de `setView` methoden wereld wijd. Hiermee stelt u de standaard taal en de regionale weer gave in voor alle Azure Maps besturings elementen die in uw app worden geladen.

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Alternatively use Azure Active Directory authenticate.
    //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

De tweede optie is de taal door geven en informatie weer geven in de XML van het kaart besturings element.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

De derde optie is het programmatisch instellen van de taal en de regionale weer gave van de kaart met behulp van de Maps- `setStyle` methode. Dit kan op elk gewenst moment worden gedaan om de taal en de regionale weer gave van de kaart te wijzigen.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Hier volgt een voor beeld van Azure Maps waarbij de taal is ingesteld op ' fr-FR ' en de regionale weer gave ingesteld op ' auto '.

![Azure Maps, kaart afbeelding met labels in het Frans](media/how-to-use-android-map-control-library/android-localization.png)

Een volledige lijst met ondersteunde talen en regionale weer gaven wordt [hier](supported-languages.md)beschreven.

## <a name="navigating-the-map"></a>Navigeren in de kaart

Er zijn verschillende manieren waarop de kaart kan worden ingezoomd, panned, gedraaid en in hoogte kan worden gesteld. Hieronder vindt u meer informatie over de verschillende manieren waarop u kunt navigeren door de kaart.

**De kaart zoomen**

* Raak de kaart met twee vingers en knijp samen om uit te zoomen of de vingers op elkaar uit te breiden om in te zoomen.
* Dubbeltik op de kaart om op één niveau te zoomen.
* Dubbeltik met twee vingers om uit te zoomen op de kaart één niveau.
* Twee keer tikken; op de tweede Tik houdt u uw vinger op de kaart op en sleept u omhoog om in te zoomen of omlaag om uit te zoomen.

**Kaart pannen**

* Raak de kaart op en sleep deze naar een wille keurige richting.

**De kaart draaien**

* Raak de kaart met twee vingers en roteer.

**De kaart verkopen**

* Raak de kaart met twee vingers en sleep deze naar boven of beneden.

## <a name="azure-government-cloud-support"></a>Cloud ondersteuning Azure Government

De Azure Maps Android SDK ondersteunt de Azure Government Cloud. De Azure Maps Android SDK wordt geopend vanuit dezelfde maven-opslag plaats. De volgende taken moeten worden uitgevoerd om verbinding te maken met de Azure Government Cloud versie van het Azure Maps platform.

Op dezelfde plaats waar de Azure Maps verificatie gegevens zijn opgegeven, voegt u de volgende regel code toe om aan te geven dat de toewijzing gebruikmaakt van het Cloud domein Azure Maps Government.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Zorg ervoor dat u Azure Maps verificatie gegevens van het Azure Government Cloud platform gebruikt wanneer u de kaart en services verifieert.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van overlay-gegevens op de kaart:

> [!div class="nextstepaction"]
> [Verificatie in Azure Maps beheren](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Kaartstijlen wijzigen in Android-kaarten](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](how-to-add-shapes-to-android-map.md)
