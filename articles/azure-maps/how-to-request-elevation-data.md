---
title: Gegevens over benodigde bevoegdheden aanvragen met behulp van de Azure Maps-uitbreidings service (preview)
description: Meer informatie over het aanvragen van uitbrei ding van gegevens met behulp van de Azure Maps-uitbreidings service (preview).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 76232a917e8856a06645fabc0ab4716195c5c0e1
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094196"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Gegevens over benodigde bevoegdheden aanvragen met behulp van de Azure Maps-uitbreidings service (preview)

> [!IMPORTANT]
> De Azure Maps-uitbreidings service is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

De Azure Maps- [uitbreidings service](https://docs.microsoft.com/rest/api/maps/elevation) voorziet in api's voor het uitvoeren van een query op een wille keurige plaats in het Opper vlak van de aarde. U kunt voor beelden van uitbrei ding van bevoegdheden aanvragen in paden, binnen een bepaald begrenzingsvak of op specifieke coördinaten. U kunt ook de [weer gave-API voor de tegel v2-ophalen](https://docs.microsoft.com/rest/api/maps/renderv2) gebruiken om de uitbreidings gegevens op te halen in de tegel indeling. De tegels worden in de GeoTIFF-Raster indeling geleverd. Dit artikel laat u zien hoe u Azure Maps-uitbreidings service en de tegel-API Get-toewijzing kunt gebruiken om gegevens over benodigde bevoegdheden te vragen. De verhogings gegevens kunnen worden aangevraagd in geojson-en GeoTiff-indelingen.

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken in de prijs categorie S1](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.

Voor meer informatie over verificatie in Azure Maps, [beheert u verificatie in azure Maps](how-to-manage-authentication.md).

In dit artikel wordt gebruikgemaakt van de toepassing [postman](https://www.postman.com/) , maar u kunt een andere API-ontwikkel omgeving kiezen.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Uitbrei ding van gegevens aanvragen in een raster tegel met opmaak

Voor het aanvragen van uitbrei ding van gegevens in de indeling raster tegel, gebruikt u de [tegel-API render v2-kaart ophalen](https://docs.microsoft.com/rest/api/maps/renderv2). Als de tegel kan worden gevonden, retourneert de API de tegel als een GeoTIFF. Anders retourneert de API 0. Alle raster-DEM tegels maken gebruik van de geo-modus (Sea-Level). In dit voor beeld vragen we om verhogings gegevens voor mt. Everest.

>[!TIP]
>Als u een tegel op een specifiek gebied op de wereld kaart wilt ophalen, moet u de juiste tegel vinden op het juiste zoom niveau. Houd er ook rekening mee dat WorldDEM de hele wereld wijde landmass omvat, maar niet voor oceanen.  Zie [zoom niveaus en tegel raster](zoom-levels-and-tile-grid.md)voor meer informatie.

1. Open de Postman-app. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken).  Geef de verzameling een naam en selecteer de knop **Create** (Maken).

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

3. Selecteer de methode http **ophalen** op het tabblad opbouw functie en voer de volgende URL in om de raster tegel aan te vragen. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. Klik op de knop **Verzenden**. U ontvangt de raster tegel met de verhogings gegevens in de GeoTIFF-indeling. Elke pixel in de raster tegel heeft onbewerkte gegevens van het type `float` . De waarde van elke pixel vertegenwoordigt de hoogte van de verhoging in meters.

## <a name="request-elevation-data-in-geojson-format"></a>Uitbrei ding van gegevens aanvragen in geojson-indeling

Gebruik de Api's van de Elevation-service (preview-versie) om verhogings gegevens in geojson-indeling aan te vragen. In deze sectie ziet u een van de drie Api's:

* [Gegevens ophalen voor punten](/rest/api/maps/elevation/getdataforpoints)
* [Gegevens plaatsen voor punten](/rest/api/maps/elevation/postdataforpoints)
* [Gegevens ophalen voor poly lijn](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)
* [Gegevens voor poly lijn plaatsen](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline)
* [Gegevens ophalen voor selectie kader](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Wanneer er geen gegevens kunnen worden geretourneerd, retour neren alle Api's `0` .

### <a name="request-elevation-data-for-points"></a>Gegevens over benodigde bevoegdheden voor punten aanvragen

In dit voor beeld gebruiken we de [API gegevens ophalen voor Points](/rest/api/maps/elevation/getdataforpoints) om verhogings gegevens aan te vragen bij Mt. Everest en Chamlang bergen. Vervolgens gebruiken we de post- [gegevens voor Points API](/rest/api/maps/elevation/postdataforpoints) om gegevens over benodigde bevoegdheden te vragen met behulp van dezelfde twee punten. De breedte graad en lengte graad van de URL worden verwacht in WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Vanwege de lengte limiet van 2048 voor de URL-tekens is het niet mogelijk om meer dan 100 coördinaten door te geven als een door pijp lijn gescheiden teken reeks in een URL GET-aanvraag. Als u wilt dat er meer dan 100 coördinaten worden door gegeven als een door de pijp lijn gescheiden teken reeks, gebruikt u de POST-gegevens voor punten.

1. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. Klik op de knop **Verzenden**.  U ontvangt de volgende JSON-reactie:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Nu noemen we de post- [gegevens voor punt-API](/rest/api/maps/elevation/postdataforpoints) om uitbrei ding van gegevens voor dezelfde twee punten te verkrijgen. Selecteer de **post** HTTP-methode op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Stel in de **headers** van de **POST**-aanvraag `Content-Type` in op `application/json`. Geef in de **hoofd tekst** de gegevens van het coördinaten punt hieronder op. Wanneer u klaar bent, klikt u op **Send**.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Voor beelden van uitbrei ding van gegevens aanvragen langs een poly lijn

In dit voor beeld gebruiken we de [gegevens ophalen voor poly lijn](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline) om vijf gelijkmatige steek proeven van verhoogde gegevens langs een rechte lijn tussen coördinaten op Mt aan te vragen. Everest en Chamlang bergen. Beide coördinaten moeten worden gedefinieerd in een lange/lat-indeling. Als u geen waarde voor de `samples` para meter opgeeft, wordt het aantal voor beelden standaard ingesteld op 10. Het maximum aantal steek proeven is 2.000.

Vervolgens gebruiken we de gegevens ophalen voor poly lijn om drie even geplaatste steek proeven aan te vragen langs een pad. We definiëren de exacte locatie voor de voor beelden door door te geven in drie lange/lat-coördinaten paren.

Ten slotte gebruiken we de [Post-gegevens voor de poly lijn-API](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) voor het aanvragen van verhogings gegevens in dezelfde drie, gelijkmatig geplaatste steek proeven.

De breedte graad en lengte graad van de URL worden verwacht in WGS84 (World Geodetic System).

 >[!IMPORTANT]
 >Vanwege de lengte limiet van 2048 voor de URL-tekens is het niet mogelijk om meer dan 100 coördinaten door te geven als een door pijp lijn gescheiden teken reeks in een URL GET-aanvraag. Als u wilt dat er meer dan 100 coördinaten worden door gegeven als een door de pijp lijn gescheiden teken reeks, gebruikt u de POST-gegevens voor punten.

1. Selecteer **Nieuw**. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraagnaam** in en selecteer een verzameling. Klik op **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. Klik op de knop **Verzenden**.  U ontvangt de volgende JSON-reactie:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Nu vragen we drie steek proeven van verhogings gegevens langs een pad tussen coördinaten bij het koppelen van Everest, Chamlang en Jannu bergen. In de sectie **params** kopieert u de volgende coördinaten matrix voor de waarde van de `lines` query sleutel.

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. Wijzig de `samples` waarde van de query sleutel in `3` .  In de onderstaande afbeelding ziet u de nieuwe waarden.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Drie voor beelden van verhogings gegevens ophalen.":::

6. Klik op de knop voor blauwe **verzen ding** . U ontvangt de volgende JSON-reactie:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Nu noemen we de post- [gegevens voor de poly lijn-API](https://docs.microsoft.com/rest/api/maps/elevation/postdataforpolyline) om de verhogings gegevens voor dezelfde drie punten te verkrijgen. Selecteer de **post** HTTP-methode op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. Stel in de **headers** van de **POST**-aanvraag `Content-Type` in op `application/json`. Geef in de **hoofd tekst** de gegevens van het coördinaten punt hieronder op. Wanneer u klaar bent, klikt u op **Send**.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Gegevens van verhoogde bevoegdheid aanvragen door middel van selectie kader

We gebruiken nu het selectie [vakje gegevens ophalen voor omsluitend kader](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox) voor het aanvragen van verhoogde bevoegdheden bij Mt. Rainier, WA. De verhogings gegevens worden op gelijkmatige locaties in een selectie kader geretourneerd. Het omsluitende gebied dat is gedefinieerd door (2) sets van lat/long-coördinaten (Zuid-breedte graad, westelijke lengte graad | Noord-breedte graad), wordt onderverdeeld in rijen en kolommen. De randen van het selectie kader account voor twee (2) van de rijen en twee (2) van de kolommen. Er worden verhogingen geretourneerd voor de raster hoekpunten die zijn gemaakt bij rij-en kolom intervallen. Maxi maal 2000 verhogingen kunnen in één aanvraag worden geretourneerd.

In dit voor beeld geven we rijen = 3 en kolommen = 6. 18 verhogings waarden worden geretourneerd in het antwoord. In het volgende diagram worden de verhogings waarden besteld, te beginnen met de zuidwest hoek, en gaat u verder met West naar Oost en Zuid naar Noord.  De verhogings punten zijn genummerd in de volg orde waarin ze worden geretourneerd.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="Coördinaten voor selectie kaders in NE en in de hoeken.":::

1. Selecteer **Nieuw**. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraagnaam** in en selecteer een verzameling. Klik op **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Klik op de knop voor blauwe **verzen ding** . 18 voor beelden van uitbrei ding van bevoegdheden, één voor elk hoek punt van het raster, worden geretourneerd in het antwoord.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Voor beelden: gebruik de Api's van de uitbreidings service (preview-versie) in Azure Maps besturings element

### <a name="get-elevation-data-by-coordinate-position"></a>Verhogings gegevens ophalen op positie van coördinaat

Op de volgende voorbeeld webpagina ziet u hoe u het kaart besturings element kunt gebruiken om de uitbrei ding van gegevens op een coördinaat punt weer te geven. Wanneer de gebruiker de markering sleept, worden de uitbrei ding gegevens in een pop-upvenster weer gegeven.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Uitbrei ding op positie ophalen" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie Azure Maps ( <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'></a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>() op de pen voor meer informatie.
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Uitbrei ding van gegevens ophalen door begrenzingsvak

Op de volgende voorbeeld webpagina ziet u hoe u het kaart besturings element kunt gebruiken om uitbrei ding gegevens weer te geven die zijn opgenomen in een selectie kader. De gebruiker definieert het selectie kader door te klikken op het `square` pictogram in de linkerbovenhoek en het vier kant op de kaart te tekenen. Het kaart besturings element geeft vervolgens de verhogings gegevens weer volgens de kleuren die zijn opgegeven in de sleutel in de rechter bovenhoek.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Uitbrei ding van bevoegdheden op begrenzingsvak" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>kanteling</a> van de Pen door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>().
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Verhogings gegevens ophalen per poly lijn-pad

Op de volgende voorbeeld webpagina ziet u hoe u het kaart besturings element kunt gebruiken om uitbrei ding gegevens weer te geven langs een pad. De gebruiker definieert het pad door te klikken op het `PolyLine` pictogram in de linkerbovenhoek en de poly lijn op de kaart te tekenen. Het kaart besturings element geeft vervolgens de benodigde gegevens weer in de kleuren die zijn opgegeven in de sleutel in de rechter bovenhoek.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kleur overgang van uitbrei ding van pad" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>kleur overgang</a> voor het pad van de Pen met Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>().
</iframe>


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Api's voor de Azure Maps-uitbrei ding (preview):

> [!div class="nextstepaction"]
> [Uitbrei ding (preview): gegevens ophalen voor lat-lange coördinaten](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Uitbrei ding (preview): gegevens ophalen voor selectie kader](https://docs.microsoft.com/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Uitbrei ding (preview): gegevens ophalen voor poly lijn](https://docs.microsoft.com/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Rendering v2 – kaart tegel ophalen](https://docs.microsoft.com/rest/api/maps/renderv2)

Voor een volledige lijst van Azure Maps REST API's, bekijk:

> [!div class="nextstepaction"]
> [Azure Maps REST API's](https://docs.microsoft.com/rest/api/maps/)
