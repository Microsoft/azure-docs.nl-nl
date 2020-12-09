---
title: Aangepaste gegevens op een raster kaart weer geven | Microsoft Azure kaarten
description: Meer informatie over het toevoegen van markerings punten, labels en geometrische vormen aan een raster kaart. Zie hoe u de statische installatie kopie service gebruikt in Azure Maps voor dit doel.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5c70835c11bafb3fd06645ba51099b33d1eb6149
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906077"
---
# <a name="render-custom-data-on-a-raster-map"></a>Aangepaste gegevens op een raster kaart weer geven

In dit artikel wordt uitgelegd hoe u de [statische installatie kopie service](/rest/api/maps/render/getmapimage)gebruikt, met de functie voor het samen stellen van afbeeldingen, om overlays boven op een raster kaart toe te staan. De samen stelling van de afbeelding bevat de mogelijkheid om een raster tegel terug te halen, met aanvullende gegevens, zoals aangepaste markerings punten, labels en geometrie-overlays.

Als u aangepaste markerings punten, labels en geometrie-overlays wilt weer geven, kunt u de toepassing postman gebruiken. U kunt Azure Maps [Data Service-api's](/rest/api/maps/data) gebruiken om overlays op te slaan en weer te geven.

> [!Tip]
> Het is vaak veel rendabeler om de Azure Maps Web-SDK te gebruiken voor het weer geven van een eenvoudige kaart op een webpagina dan voor het gebruik van de statische installatie kopie service. De Web-SDK gebruikt kaart tegels en tenzij de gebruiker de kaart pannt en inzoomt, wordt er vaak slechts een fractie van een trans actie per kaart belasting gegenereerd. Houd er rekening mee dat de Azure Maps Web-SDK opties heeft voor het uitschakelen van pannen en zoomen. Daarnaast biedt de Azure Maps Web-SDK een uitgebreidere set opties voor gegevens visualisatie dan een statische map-webservice.  

## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken

Als u de procedures in dit artikel wilt uitvoeren, moet u eerst een Azure Maps account maken en de sleutel van uw Maps-account ophalen. Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-azure-maps-account) voor het maken van een abonnement op een Azure Maps-account en volg de stappen in de [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account op te halen. Zie [Verificatie beheren in Azure Maps](./how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Markerings punten met labels en een aangepaste installatie kopie weer geven

> [!Note]
> Voor de procedure in deze sectie is een Azure Maps-account vereist in de prijs categorie S0 of S1.

De S0-laag van het Azure Maps account ondersteunt slechts één exemplaar van de `pins` para meter. U kunt Maxi maal vijf markerings punten weer geven, opgegeven in de URL-aanvraag, met een aangepaste installatie kopie.

Voer de volgende stappen uit om de markerings punten met labels en een aangepaste installatie kopie te genereren:

1. Maak een verzameling waarin de aanvragen worden opgeslagen. Selecteer in de app postman de optie **Nieuw**. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken). Geef de verzameling een naam en selecteer de knop **Create** (Maken). 

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraag naam** in voor de markerings punten. Selecteer de verzameling die u in de vorige stap hebt gemaakt, als de locatie voor het opslaan van de aanvraag. Selecteer vervolgens **Opslaan**.
    
    ![Een aanvraag maken in postman](./media/how-to-render-custom-data/postman-new.png)

3. Selecteer de methode HTTP ophalen op het tabblad opbouw functie en voer de volgende URL in om een GET-aanvraag te maken.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Hier ziet u de resulterende afbeelding:

    ![Een aangepaste punaise met een label](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Gegevens ophalen uit Azure Maps gegevens opslag

> [!Note]
> Voor de procedure in deze sectie is een Azure Maps-account vereist in de prijs categorie S1.

U kunt ook het pad en de locatie gegevens van de pincode verkrijgen met behulp van de API voor het [uploaden van gegevens](/rest/api/maps/data/uploadpreview). Volg de onderstaande stappen om het pad en de pincode gegevens te uploaden.

1. Open in de Postman-app een nieuw tabblad in de verzameling die u hebt gemaakt in de vorige sectie. Selecteer de POST HTTP-methode op het tabblad opbouw functie en voer de volgende URL in om een POST-aanvraag te maken:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Voer op het tabblad **params** de volgende sleutel/waarde-paren in, die worden gebruikt voor de URL van de post-aanvraag. Vervang de `subscription-key` waarde door uw Azure Maps-abonnements sleutel.
    
    ![Para meters voor sleutel/waarde in postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Selecteer op het tabblad **hoofd tekst** de indeling Onbewerkte invoer en kies JSON als de invoer indeling in de vervolg keuzelijst. Geef deze JSON op als gegevens die moeten worden geüpload:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Selecteer **verzenden** en controleer de antwoord header. Bij een succesvol verzoek bevat de koptekst Locatie de status-URI om de huidige status van het uploadverzoek te controleren. De status-URI heeft de volgende indeling.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Kopieer uw status-URI en voeg de para meter van de abonnements sleutel toe met de waarde van uw abonnements sleutel voor uw Azure Maps account. Gebruik dezelfde abonnements sleutel voor het account die u hebt gebruikt voor het uploaden van de gegevens. De URI-indeling voor de status moet er als volgt uitzien:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Open een nieuw tabblad in de Postman-app om de udId op te halen. Selecteer HTTP-methode ophalen op het tabblad opbouw functie. Een GET-aanvraag maken op de status-URI. Als je gegevens zijn geüpload, ontvang je een udId in de antwoordtekst. Kopieer het udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Gebruik de `udId` waarde die is ontvangen van de API voor het uploaden van gegevens om functies op de kaart weer te geven. Als u dit wilt doen, opent u een nieuw tabblad in de verzameling die u in de voor gaande sectie hebt gemaakt. Selecteer de methode HTTP ophalen op het tabblad opbouw functie, vervang de {Subscription-Key} en {udId} door de waarden en voer deze URL in om een GET-aanvraag te maken:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Hier is de reactie afbeelding:

    ![Gegevens ophalen uit Azure Maps gegevens opslag](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Een veelhoek met kleur en dekking weer geven

> [!Note]
> Voor de procedure in deze sectie is een Azure Maps-account vereist in de prijs categorie S1.


U kunt het uiterlijk van een veelhoek wijzigen met behulp van stijl wijzigingen met de [para meter Path](/rest/api/maps/render/getmapimage#uri-parameters).

1. Open in de Postman-app een nieuw tabblad in de verzameling die u eerder hebt gemaakt. Selecteer de methode HTTP ophalen op het tabblad opbouw functie en voer de volgende URL in om een GET-aanvraag te configureren voor het weer geven van een veelhoek met kleur en dekking:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Hier is de reactie afbeelding:

    ![Een ondoorzichtige veelhoek weer geven](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Een cirkel en markerings punten met aangepaste labels renderen

> [!Note]
> Voor de procedure in deze sectie is een Azure Maps-account vereist in de prijs categorie S1.


U kunt de weer gave van de pincodes wijzigen door aanpassings opties voor stijlen toe te voegen. Als u bijvoorbeeld markerings punten en de labels groter of kleiner wilt maken, gebruikt u de `sc` aanpassings functie ' schaal stijl '. Deze modificator heeft een waarde die groter is dan nul. Een waarde van 1 is de standaard schaal. Waarden die groter zijn dan 1 maken de pincodes groter en waarden kleiner dan 1 maken deze kleiner. Zie [para meters voor statisch installatie kopie-pad](/rest/api/maps/render/getmapimage#uri-parameters)voor meer informatie over stijl parameters.


Volg deze stappen om een cirkel en markerings punten met aangepaste labels weer te geven:

1. Open in de Postman-app een nieuw tabblad in de verzameling die u eerder hebt gemaakt. Selecteer de methode HTTP ophalen op het tabblad opbouw functie en voer deze URL in om een GET-aanvraag te maken:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Hier is de reactie afbeelding:

    ![Een cirkel met aangepaste markerings punten weer geven](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Als u de kleur van de markerings punten van de laatste stap wilt wijzigen, wijzigt u de stijl co. `pins=default|la15+50|al0.66|lc003C62|co002D62|`De huidige kleur wordt weer gegeven als #002D62 in CSS. Stel dat u deze wilt wijzigen in #41d42a. Schrijf de nieuwe kleur waarde na de specificatie ' co ', bijvoorbeeld: `pins=default|la15+50|al0.66|lc003C62|co41D42A|` . Een nieuwe GET-aanvraag maken:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Dit is de reactie afbeelding na het wijzigen van de kleuren van de pincodes:

    ![Een cirkel met bijgewerkte markerings punten weer geven](./media/how-to-render-custom-data/circle-updated-pins.png)

Op dezelfde manier kunt u andere stijl aanpassingen wijzigen, toevoegen en verwijderen.

## <a name="next-steps"></a>Volgende stappen


* Verken de documentatie over de [Azure Maps-overzichts-API ophalen](/rest/api/maps/render/getmapimage) .
* Zie de [Service documentatie](/rest/api/maps/data)voor meer informatie over Azure Maps Data Service (preview).