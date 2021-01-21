---
title: 'Zelfstudie: Een geofence maken en apparaten bijhouden op een Microsoft Azure-kaart'
description: Zelfstudie over hoe u een geofence kunt instellen. Zie hoe u apparaten ten opzichte van de geofence kunt volgen met behulp van de ruimtelijke service van Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 759adea3cf34b79c76b6facec3bd4626ca54107e
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625029"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Zelfstudie: Een geofence instellen met behulp van Azure Maps

In deze zelfstudie leert u de basisbeginselen van het maken en gebruiken van geofence-services van Azure Maps. U doet dit in de context van het volgende scenario:

*Een bouwsitebeheerder moet de apparatuur bijhouden die een bouwterrein binnenkomt en verlaat. Telkens wanneer een apparaat de grenzen van het terrein binnengaat of verlaat, wordt er een e-mailbericht verzonden naar de Operations Manager.*

Azure Maps biedt een aantal services ter ondersteuning van het bijhouden van apparatuur die het bouwterrein binnengaat en verlaat. In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Upload [Geofencing GeoJSON-gegevens](geofence-geojson.md) waarmee de bouwterreinen worden gedefinieerd die u wilt bewaken. U gebruikt de [Data Upload API](/rest/api/maps/data/uploadpreview) om geofences als veelhoekcoördinaten te uploaden naar uw Azure Maps-account.
> * Het instellen van twee [logische apps](../event-grid/handler-webhooks.md#logic-apps) die, wanneer ze worden geactiveerd, e-mailmeldingen sturen naar de Operations Manager van het bouwterrein wanneer er apparatuur het geofence-gebied binnenkomt of verlaat.
> * Gebruik [Azure Event Grid](../event-grid/overview.md) om een abonnement te nemen op Enter- en Exit-gebeurtenissen voor uw Azure Maps-geofence. U stelt twee webhook-gebeurtenisabonnementen in die de HTTP-eindpunten aanroepen die in uw twee logische apps zijn gedefinieerd. De logische apps sturen vervolgens de juiste e-mailmeldingen over apparatuur die de geofence verlaat of binnenkomt.
> * Gebruik [Search Geofence Get API](/rest/api/maps/spatial/getgeofence) om meldingen te krijgen wanneer een apparaat de geofence-gebieden binnenkomt of verlaat.

## <a name="prerequisites"></a>Vereisten

1. [Maak een Azure Maps-account](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.

In deze zelfstudie wordt gebruikgemaakt van de [Postman](https://www.postman.com/)-toepassing, maar u kunt ook een andere API-ontwikkelomgeving kiezen.

## <a name="upload-geofencing-geojson-data"></a>Geofencing GeoJSON-gegevens uploaden

In deze zelfstudie gaat u Geofencing GeoJSON-gegevens uploaden die een `FeatureCollection` bevatten. De `FeatureCollection` bevat twee geofences waarmee u veelhoekgebieden binnen het bouwterrein definieert. De eerste geofence heeft geen tijdverloop of beperkingen. Op de tweede kunnen alleen tijdens kantooruren (9:00 uur - 17:00 uur in de Pacific Time Zone) query's worden uitgevoerd en deze is na 1 januari 2022 niet meer geldig. Zie [Geofencing GeoJSON data](geofence-geojson.md) (Geofencing GeoJSON-gegevens) voor meer informatie over de GeoJSON-indeling.

>[!TIP]
>U kunt uw geofence-gegevens op elk gewenst moment bijwerken. Zie [Data Upload-API](/rest/api/maps/data/uploadpreview) voor meer informatie.

1. Open de Postman-app. Selecteer rechtsboven **Nieuw**. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken). Geef de verzameling een naam en selecteer **Maken**.

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

3. Selecteer de HTTP-methode **POST** op het tabblad Builder en voer de volgende URL in om de geofence-gegevens naar Azure Maps te uploaden. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    De parameter `geojson` in het URL-pad geeft de gegevensindeling aan van de gegevens die worden geüpload.

4. Selecteer het tabblad **Hoofdtekst**. Selecteer **raw** (onbewerkt), en selecteer vervolgens **JSON** als invoerindeling. Kopieer de volgende GeoJSON-gegevens en plak ze in het tekstgebied **Body** (Hoofdtekst):

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Selecteer **Verzenden** en wacht tot het verzoek is verwerkt. Wanneer de aanvraag is voltooid, gaat u naar het tabblad **Kopteksten** van het antwoord. Kopieer de waarde van de **Location**-sleutel (Locatie), de `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Maak een **GET** HTTP-aanvraag op de `status URL` om de status van de API-aanroep te controleren. U moet uw primaire abonnementssleutel toevoegen aan de URL voor authenticatie. De **GET**-aanvraag moet lijken op de volgende URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Wanneer de HTTP-aanvraag **GET** met succes is uitgevoerd, retourneert deze een `resourceLocation`. De `resourceLocation` bevat de unieke `udid` voor de geüploade inhoud. Sla deze `udid` op om in de laatste sectie van deze zelfstudie een query uit te voeren op de Get Geofence API. U kunt desgewenst de `resourceLocation`-URL gebruiken om in de volgende stap metagegevens van deze resource op te halen.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Als u metagegevens van inhoud wilt ophalen, maakt u een **GET** HTTP-aanvraag op de `resourceLocation` URL die is opgehaald in stap 7. Zorg ervoor dat u uw primaire abonnementssleutel toevoegt aan de URL voor authenticatie. De **GET**-aanvraag moet lijken op de volgende URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Wanneer de HTTP-aanvraag **GET** is voltooid, bevat de antwoordtekst de `udid` die is opgegeven in de `resourceLocation` van stap 7. Het bevat ook de locatie om in de toekomst de inhoud te benaderen en downloaden, en andere metagegevens over de inhoud. Een voorbeeld van het totale antwoord is:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Werkstromen maken in Azure Logic Apps

Vervolgens maakt u twee [logische-app](../event-grid/handler-webhooks.md#logic-apps)-eindpunten die een e-mailmelding activeren. U kunt als volgt de eerste maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.

3. Typ **logische app** in het vak **Marketplace doorzoeken**.

4. Selecteer **Logische app** > **Maken** in de resultaten.

5. Voer op de pagina **Logische app** de volgende waarden in:
    * Het **Abonnement** dat u wilt gebruiken voor deze logische app.
    * De naam van de **Resourcegroep** voor deze logische app. U kunt kiezen om een **nieuwe** of **bestaande** resourcegroep te gebruiken.
    * De **Logische-app-naam** van uw logische app. In dit geval gebruiken we `Equipment-Enter` als naam.

    Laat in deze zelfstudie alle andere waarden op de standaardinstellingen staan.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Schermopname van logische app maken.":::

6. Selecteer **Controleren + maken**. Controleer uw instellingen en selecteer **Maken** om de implementatie te verzenden. Wanneer de implementatie is voltooid, selecteert u **Naar de resource gaan**. U wordt naar de **Logic App Designer** geleid.

7. Selecteer een triggertype. Schuif naar beneden naar de sectie **Beginnen met een algemene trigger**. Selecteer **Wanneer een HTTP-aanvraag wordt ontvangen**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Schermopname van een HTTP-trigger van een logische app maken.":::

8. Selecteer **Opslaan** in de rechterbovenhoek van Logic App Designer. De **HTTP POST-URL** wordt automatisch gegenereerd. Sla de URL op. U hebt deze in de volgende sectie nodig om een gebeurteniseindpunt te maken.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Schermopname van URL en JSON voor de HTTP-aanvraag van de logische app.":::

9. Selecteer **+ Nieuwe stap**. Nu gaat u een actie kiezen. Typ `outlook.com email` in het zoekvak. Schuif in de lijst **Acties** omlaag en selecteer **Een e-mailbericht verzenden (V2)** .
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Schermopname van logische app designer maken.":::

10. Meld u aan bij uw Outlook-account. Zorg ervoor dat u **Ja** selecteert om de logische app toegang te geven tot het account. Vul de velden in voor het verzenden van een e-mail.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Schermopname van de stap e-mail verzenden maken in logische app.":::

    >[!TIP]
    > U kunt de gegevens van een GeoJSON-reactie, zoals `geometryId` of `deviceId`, ophalen in uw e-mailmeldingen. U kunt Logic Apps zo configureren dat de gegevens die door Event Grid worden verzonden, worden gelezen. Zie voor informatie over het configureren van Logic Apps voor het consumeren en doorgeven van gebeurtenisgegevens naar e-mailmeldingen [Zelfstudie: E-mailmeldingen over gebeurtenissen van Azure IoT Hub verzenden met Event Grid en Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. Selecteer **Opslaan** in de linkerbovenhoek van Logic App Designer.

Herhaal de stappen 3-11 om een tweede logische app te maken om de manager op de hoogte te stellen wanneer apparatuur het bouwterrein verlaat. Noem de logische app `Equipment-Exit`.

## <a name="create-azure-maps-events-subscriptions"></a>Abonnementen op Azure Maps-gebeurtenissen maken

Azure Maps ondersteunt [drie typen gebeurtenissen](../event-grid/event-schema-azure-maps.md). Hier moet u twee verschillende gebeurtenisabonnementen maken: één voor Enter-gebeurtenissen en een voor Exit-gebeurtenissen voor de geofence.

De volgende stappen laten u zien hoe u een gebeurtenisabonnement maakt voor Enter-gebeurtenissen voor de geofence. U kunt zich op een vergelijkbare manier abonneren op Exit-gebeurtenissen voor de geofence.

1. Ga naar uw Azure Maps-account. Selecteer **Abonnementen** op het dashboard. Selecteer de naam van uw abonnement en selecteer **gebeurtenissen** in het instellingenmenu.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Schermopname van navigeren naar gebeurtenissen in uw Azure Maps-account.":::

2. U maakt een gebeurtenisabonnement door **+ Gebeurtenisabonnement** te selecteren op de gebeurtenissenpagina.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Schermopname van een abonnement op Azure Maps-gebeurtenissen maken.":::

3. Voer op de pagina **Gebeurtenisabonnement maken** de volgende waarden in:
    * De **Naam** van het gebeurtenisabonnement.
    * Het **Gebeurtenisschema** moet *Gebeurtenisrasterschema* zijn.
    * De **Naam van het systeemonderwerp** voor dit gebeurtenisabonnement, die in dit geval `Contoso-Construction` is.
    * Kies voor **Filteren op gebeurtenistypen** het gebeurtenistype `Geofence Entered`.
    * Kies `Web Hook` voor **Eindpunttype**.
    * Voor **Eindpunt** kopieert u de HTTP POST-URL voor het Logic App Enter-eindpunt dat u in de vorige sectie hebt gemaakt. Als u bent vergeten deze op te slaan, kunt u teruggaan naar Logic App Designer en de URL kopiëren uit de HTTP-triggerstap.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Schermopname van abonnementdetails van Azure Maps-gebeurtenissen.":::

4. Selecteer **Maken**.

Herhaal stap 1-4 voor het Logic App Exit-eindpunt dat u in de vorige sectie hebt gemaakt. Zorg ervoor dat u bij stap 3 `Geofence Exited` kiest als het gebeurtenistype.

## <a name="use-spatial-geofence-get-api"></a>Spatial Geofence Get API gebruiken

Gebruik [Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence) om e-mailmeldingen te sturen naar de Operations Manager wanneer een apparaat de geofences binnenkomt of verlaat.

Elk apparaat heeft een `deviceId`. In deze zelfstudie volgt u één apparaat, waarvan de unieke ID `device_1` is.

Het volgende diagram laat de vijf locaties van het apparaat in de loop der tijd zien, te beginnen bij de *Start*-locatie, ergens buiten de geofences. Voor deze zelfstudie wordt de *Start*-locatie niet gedefinieerd, omdat u geen query uitvoert op het apparaat op die locatie.

Wanneer u een query uitvoert op [Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence) met een apparaatlocatie die aangeeft dat de geofence voor het eerst wordt binnengegaan of verlaten, roept de Event Grid het juiste Logic App-eindpunt aan om een e-mailbericht naar de Operations Manager te sturen.

Elk van de volgende secties verzendt API-aanvragen met behulp van de vijf verschillende locatiecoördinaten van de apparatuur.

![Diagram van geofence-kaart in Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Apparaatlocatie 1 (47.638237,-122.132483)

1. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Maak er *Locatie 1* van. Selecteer de verzameling die u hebt gemaakt in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data)en selecteer **Opslaan**.

2. Selecteer de HTTP-methode **GET** op het tabblad Builder en voer de volgende URL in. Zorg ervoor dat u `{Azure-Maps-Primary-Subscription-key}` vervangt door uw primaire abonnementssleutel en `{udid}` door de `udid` die u heb opgeslagen in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selecteer **Verzenden**. De volgende GeoJSON wordt in het antwoordvenster weergegeven.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

In het voorgaande GeoJSON-antwoord betekent de negatieve afstand van de geofence van de hoofdsite dat het apparaat zich in de geofence bevindt. De positieve afstand van de subsite-geofence betekent dat de apparatuur zich buiten de subsite-geofence bevindt. Omdat dit de eerste keer is dat dit apparaat zich in de geofence van de hoofdsite bevindt, wordt de parameter `isEventPublished` ingesteld op `true`. De Operations Manager krijgt een e-mailmelding waarmee wordt aangegeven dat apparatuur een geofence is binnengegaan.

### <a name="location-2-4763800-122132531"></a>Locatie 2 (47.63800,-122.132531)

1. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Maak er *Locatie 2* van. Selecteer de verzameling die u hebt gemaakt in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data)en selecteer **Opslaan**.

2. Selecteer de HTTP-methode **GET** op het tabblad Builder en voer de volgende URL in. Zorg ervoor dat u `{Azure-Maps-Primary-Subscription-key}` vervangt door uw primaire abonnementssleutel en `{udid}` door de `udid` die u heb opgeslagen in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Selecteer **Verzenden**. De volgende GeoJSON wordt in het antwoordvenster weergegeven:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

In het voorgaande GeoJSON-antwoord is het apparaat in de geofence van de hoofdsite gebleven en is niet de subsite-geofence binnengegaan. Als gevolg hiervan wordt de parameter `isEventPublished` ingesteld op `false` en ontvangt de Operations Manager geen e-mailmeldingen.

### <a name="location-3-4763810783315048-12213336020708084"></a>Locatie 3 (47.63810783315048,-122.13336020708084)

1. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Maak er *Locatie 3* van. Selecteer de verzameling die u hebt gemaakt in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data)en selecteer **Opslaan**.

2. Selecteer de HTTP-methode **GET** op het tabblad Builder en voer de volgende URL in. Zorg ervoor dat u `{Azure-Maps-Primary-Subscription-key}` vervangt door uw primaire abonnementssleutel en `{udid}` door de `udid` die u heb opgeslagen in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Selecteer **Verzenden**. De volgende GeoJSON wordt in het antwoordvenster weergegeven:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

In het voorgaande GeoJSON-antwoord is het apparaat in de geofence van de hoofdsite gebleven, maar is de subsite-geofence binnengegaan. Als gevolg hiervan wordt de parameter `isEventPublished` ingesteld op `true`. De Operations Manager krijgt een e-mailmelding die aangeeft dat het apparatuur een geofence is binnengegaan.

>[!NOTE]
>Als het apparaat na kantooruren naar de subsite zou zijn gegaan, zou er geen gebeurtenis worden gepubliceerd en zou de Operations Manager geen meldingen ontvangen.  

### <a name="location-4-47637988-1221338344"></a>Locatie 4 (47.637988,-122.1338344)

1. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Maak er *Locatie 4* van. Selecteer de verzameling die u hebt gemaakt in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data)en selecteer **Opslaan**.

2. Selecteer de HTTP-methode **GET** op het tabblad Builder en voer de volgende URL in. Zorg ervoor dat u `{Azure-Maps-Primary-Subscription-key}` vervangt door uw primaire abonnementssleutel en `{udid}` door de `udid` die u heb opgeslagen in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selecteer **Verzenden**. De volgende GeoJSON wordt in het antwoordvenster weergegeven:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

In het voorgaande GeoJSON-antwoord is het apparaat in de geofence van de hoofdsite gebleven, maar is de subsite-geofence uitgegaan. U ziet echter dat de waarde `userTime` na de `expiredTime` komt, zoals gedefinieerd in de geofence-gegevens. Als gevolg hiervan wordt de parameter `isEventPublished` ingesteld op `false` en ontvangt de Operations Manager geen e-mailmelding.

### <a name="location-5-4763799--122134505"></a>Locatie 5 (47.63799, -122.134505)

1. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Maak er *Locatie 5* van. Selecteer de verzameling die u hebt gemaakt in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data)en selecteer **Opslaan**.

2. Selecteer de HTTP-methode **GET** op het tabblad Builder en voer de volgende URL in. Zorg ervoor dat u `{Azure-Maps-Primary-Subscription-key}` vervangt door uw primaire abonnementssleutel en `{udid}` door de `udid` die u heb opgeslagen in de sectie [Geofencing GeoJSON-gegevens uploaden](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Selecteer **Verzenden**. De volgende GeoJSON wordt in het antwoordvenster weergegeven:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

In het voorgaande GeoJSON-antwoord heeft het apparaat de geofence van de hoofdsite verlaten. Als gevolg hiervan wordt de parameter `isEventPublished` ingesteld op `true` en krijgt de Operations Manager een e-mailmelding die aangeeft dat het apparatuur een geofence heeft verlaten.


U kunt ook [e-mail meldingen verzenden met behulp van Event Grid en logische apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) en [ondersteunde gebeurtenis-handlers in Event Grid](../event-grid/event-handlers.md) controleren met behulp van Azure Maps.

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Typen inhoud in Azure Logic Apps afhandelen](../logic-apps/logic-apps-content-type.md)