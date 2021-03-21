---
title: 'Zelfstudie: Microsoft Azure Maps Creator (preview) gebruiken voor het maken van indoorplattegronden'
description: Zelfstudie over het gebruik van Azure Maps Creator (preview) voor het maken van indoorplattegronden
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ac53dab29feddd36a95b8b2b041caaf5c3598d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720134"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>Zelfstudie: Creator (preview) gebruiken om indoorplattegronden te maken

> [!IMPORTANT]
> Azure Maps Creator-services zijn momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In deze zelfstudie leert u hoe u indoorplattegronden kunt maken. In deze zelfstudie leert u hoe u de API gebruikt voor het volgende:

> [!div class="checklist"]
> * Uploaden van het tekenpakket voor indoorplattegronden
> * Uw tekenpakket converteren naar plattegrondgegevens
> * Een gegevensset maken op basis van plattegrondgegevens
> * Een tegelset maken op basis van de gegevens in uw gegevensset
> * Een query uitvoeren op de API van Azure Maps Web Feature Service (WFS) voor meer informatie over uw plattegrondfuncties
> * Een functiestatusset maken met behulp van de plattegrondfuncties en de gegevens in uw gegevensset
> * De functiestatusset bijwerken

## <a name="prerequisites"></a>Vereisten

Voor het maken van indoorplattegronden:

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.
3. [Een resource voor Creator (preview) maken](how-to-manage-creator.md)
4. Download het [voorbeeldtekenpakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip).

In deze zelfstudie wordt gebruikgemaakt van de [Postman](https://www.postman.com/)-toepassing, maar u kunt ook een andere API-ontwikkelomgeving kiezen.

>[!IMPORTANT]
> De API-URL's in dit document moeten mogelijk worden aangepast op basis van de locatie van de Creator-resource. Zie [Toegang tot Creator-services](how-to-manage-creator.md#access-to-creator-services) voor meer informatie.

## <a name="upload-a-drawing-package"></a>Een tekenpakket uploaden

Gebruik de [API voor het uploaden van gegevens](/rest/api/maps/data/uploadpreview) om het tekenpakket te uploaden naar Azure Maps-resources.

De API voor het uploaden van gegevens is een langdurige transactie die het hier gedefinieerde patroon implementeert. Zodra de bewerking is voltooid, wordt de `udid` gebruikt om het geüploade pakket te openen om het te converteren. Volg de onderstaande stappen voor het verkrijgen van `udid`.

1. Open de Postman-app. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken).  Geef de verzameling een naam en selecteer de knop **Create** (Maken).

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

3. Selecteer de HTTP-methode **POST** op het tabblad Builder en voer de volgende URL in om het tekenpakket naar de Azure Maps-service te uploaden. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Geef op het tabblad **Headers** een waarde op voor de `Content-Type`-sleutel. Het tekenpakket is een gezipte map. Gebruik daarom de waarde `application/octet-stream`. Selecteer op het tabblad **Body** de optie **binary**. Klik op **Select File** en kies een tekenpakket.

     ![gegevensbeheer](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Klik op de blauwe **Send**-knop (Verzenden) en wacht tot de aanvraag is verwerkt. Zodra de aanvraag is voltooid, gaat u naar het tabblad **Headers** (Kopteksten) van het antwoord. Kopieer de waarde van de **Location**-sleutel (Locatie), de `status URL`.

6. Maak een **GET** HTTP-aanvraag op de `status URL` om de status van de API-aanroep te controleren. U moet uw primaire abonnementssleutel toevoegen aan de URL voor authenticatie. De **GET**-aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. Wanneer de **GET** HTTP-aanvraag met succes wordt uitgevoerd, retourneert deze een `resourceLocation`. De `resourceLocation` bevat de unieke `udid` voor de geüploade inhoud. U kunt desgewenst de `resourceLocation`-URL gebruiken om in de volgende stap metagegevens van deze resource op te halen.

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

9. Wanneer de **GET** HTTP-aanvraag slaagt, bevat de hoofdtekst van het antwoord de `udid` die in de `resourceLocation` van stap 7 is opgegeven, de locatie waar de inhoud in de toekomst kan worden gevonden/gedownload, en enkele andere metagegevens over de inhoud, zoals de datum waarop deze is gemaakt/bijgewerkt, de grootte, enzovoort. Een voorbeeld van het totale antwoord is:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Een tekenpakket converteren

 Als het tekenpakket is geüpload, wordt de `udid` voor het geüploade pakket gebruikt om het pakket te converteren naar plattegrondgegevens. De conversie-API gebruikt een langdurige transactie die het [hier](creator-long-running-operation.md) gedefinieerde patroon implementeert. Zodra de bewerking is voltooid, wordt de `conversionId` gebruikt om de geconverteerde gegevens te openen. Volg de onderstaande stappen voor het verkrijgen van de `conversionId`.

1. Selecteer **Nieuw**. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraagnaam** in en selecteer een verzameling. Klik op **Opslaan**.

2. Selecteer de HTTP-methode **POST** op het tabblad Builder en voer de volgende URL in om het geüploade tekenpakket naar plattegrondgegevens te converteren. Gebruik de `udid` voor het geüploade pakket.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > De API-URL's in dit document moeten mogelijk worden aangepast op basis van de locatie van de Creator-resource. Zie [Toegang tot Creator-services (preview)](how-to-manage-creator.md#access-to-creator-services) voor meer informatie.
    > Als er een fout met de code wordt weer gegeven `"RequiresCreatorResource"` , moet u ervoor zorgen dat u [een Azure Maps Maker-bron hebt ingericht](how-to-manage-creator.md) Azure Maps account.

3. Klik op de knop **Send** en wacht tot de aanvraag is verwerkt. Zodra de aanvraag is voltooid, gaat u naar het tabblad **Headers** van het antwoord en zoekt u de **Location**-sleutel. Kopieer de waarde van de **Location**-sleutel; dit is de `status URL` voor de aanvraag van de conversie. U gebruikt deze in de volgende stap.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="De waarde van de locatiesleutel kopiëren":::

4. Start op het tabblad Builder een nieuwe HTTP-methode van het type **GET**. Voeg de primaire Azure Maps-abonnementssleutel aan de `status URL` toe. Voer een **GET**-aanvraag uit bij de `status URL` die u in stap 3 hebt gekopieerd. De `status URL` lijkt op de volgende URL:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Als het conversieproces nog niet is voltooid, ziet u mogelijk iets zoals het volgende JSON-antwoord:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. Zodra de aanvraag is voltooid, ziet u in een statusbericht in de hoofdtekst van het antwoord dat de aanvraag is geslaagd.  Kopieer de `conversionId` van de `resourceLocation`-URL voor het geconverteerde pakket. De `conversionId` wordt gebruikt door de andere API om toegang te krijgen tot de geconverteerde plattegrondgegevens.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>De Postman-toepassing biedt geen systeemeigen ondersteuning voor langdurige HTTP-aanvragen. Als gevolg hiervan kan er een lange vertraging optreden tijdens het uitvoeren van een **GET**-aanvraag voor de status-URL.  Wacht ongeveer dertig seconden en klik op de knop **Send** totdat u een antwoord krijgt dat de aanvraag al dan niet is geslaagd.

Het voorbeeldtekeningpakket moet zonder fouten of waarschuwingen worden geconverteerd. Als u echter fouten of waarschuwingen van uw eigen tekenpakket ontvangt, krijgt u in het JSON-antwoord een koppeling naar de [visualisator van tekenfouten](drawing-error-visualizer.md). Met de visualisator van tekenfouten kunt u de details in de fouten en waarschuwingen inspecteren. Als u aanbevelingen wilt ontvangen over het oplossen van conversiefouten en -waarschuwingen, raadpleegt u [Fouten en waarschuwingen over tekenconversies](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Een gegevensset maken

De gegevensset is een verzameling plattegrondfuncties, zoals gebouwen, niveaus en kamers. Als u een gegevensset wilt maken, gebruikt u de [API voor het maken van gegevenssets](/rest/api/maps/dataset/createpreview). De API voor het maken van gegevenssets gebruikt de `conversionId` voor het geconverteerde tekenpakket en retourneert een `datasetId` van de gemaakte gegevensset. In de volgende stappen kunt u zien hoe u een gegevensset kunt maken.

1. Selecteer in de Postman-toepassing de optie **New**. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraagnaam** in en selecteer een verzameling. Klik op **Opslaan**.

2. Voer een **POST**-aanvraag uit bij de [API voor het maken van gegevenssets](/rest/api/maps/dataset/createpreview) om een nieuwe gegevensset te maken. Voordat u de aanvraag indient, voegt u de abonnementssleutel en de `conversionId` toe met de `conversionId` die u tijdens het conversieproces in stap 5 hebt gekregen.  De aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Haal de `statusURL` op in de **Location**-sleutel van de **antwoordheaders**.

4. Voer een **GET**-aanvraag uit bij de `statusURL` om de `datasetId` te verkrijgen. Voeg de primaire Azure Maps-abonnementssleutel toe voor verificatie. De aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. Wanneer de HTTP-aanvraag van het type **GET** is voltooid, bevat de antwoordheader de `datasetId` voor de gemaakte gegevensset. Kopieer het `datasetId`. U moet de `datasetId` gebruiken om een tegelset te maken.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Tegelset maken

Een tegelset is een set vectortegels die op de kaart worden weergegeven. Tegelsets worden gemaakt op basis van bestaande gegevenssets. Een tegelset is echter onafhankelijk van de gegevensset die als bron heeft gediend. Als de gegevensset wordt verwijderd, blijft de tegelset bestaan. Volg de onderstaande stappen om een tegelset te maken:

1. Selecteer in de Postman-toepassing de optie **New**. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraagnaam** in en selecteer een verzameling. Klik op **Opslaan**.

2. Voer een **POST**-aanvraag uit op het tabblad Builder. De aanvraag-URL moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Voer een **GET**-aanvraag uit bij de `statusURL` voor de tegelset. Voeg de primaire Azure Maps-abonnementssleutel toe voor verificatie. De aanvraag moet lijken op de volgende URL:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Wanneer de HTTP-aanvraag van het type **GET** is voltooid, bevat de antwoordheader de `tilesetId` voor de gemaakte tegelset. Kopieer het `tilesetId`.

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>Query's uitvoeren op gegevenssets met de WFS-API

 Op gegevenssets kunnen query's worden uitgevoerd met de [WFS-API](/rest/api/maps/wfs). Met de WFS-API kunt u een query uitvoeren voor het opvragen van functieverzamelingen, een specifieke verzameling of een specifieke functie met een **functie-id**. De functie wordt door middel van de **functie -id** uniek geïdentificeerd binnen de gegevensset. Deze wordt bijvoorbeeld gebruikt om te bepalen welke functiestatus in een bepaalde statusset moet worden bijgewerkt.

1. Selecteer in de Postman-toepassing de optie **New**. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraagnaam** in en selecteer een verzameling. Klik op **Opslaan**.

2. Voer een **GET**-aanvraag uit om een lijst met de verzamelingen in uw gegevensset te bekijken. Vervang `<dataset-id>` door uw `datasetId`. Gebruik de primaire sleutel van Azure Maps in plaats van de tijdelijke aanduiding. De aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. De antwoordheader is in de GeoJSON-indeling en bevat alle verzamelingen uit de gegevensset. Voor dit voorbeeld wordt omwille van de duidelijkheid alleen de `unit`-verzameling weergegeven. Zie [WFS-API voor het beschrijven van verzamelingen](/rest/api/maps/wfs/collectiondescriptionpreview) voor een voorbeeld dat alle verzamelingen bevat. Als u meer wilt weten over een verzameling, kunt u op een van de URL's in het `link`-element klikken.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Voer een **GET**-aanvraag uit voor de functieverzamelingen van het type `unit`.  Vervang `{datasetId}` door uw `datasetId`. Gebruik de primaire sleutel van Azure Maps in plaats van de tijdelijke aanduiding. De hoofdtekst van het antwoord bevat alle functies van de `unit`-verzameling. De aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. Kopieer de `id` van de functie voor een eenheidsfunctie met stijlkenmerken die dynamisch kunnen worden gewijzigd.  De `id` van de functie wordt in de volgende sectie gebruikt, omdat de bezettingsstatus en de temperatuur van de eenheid dynamisch kunnen worden bijgewerkt. In het volgende voorbeeld is UNIT26 de `id` van de functie. De stijlkenmerken van deze functie worden statussen genoemd. De functie wordt gebruikt om een statusset te maken.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Functiestatusset maken

1. Selecteer in de Postman-toepassing de optie **New**. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **aanvraagnaam** in en selecteer een verzameling. Klik op **Opslaan**.

2. Voer een **POST**-aanvraag uit voor de [API voor het maken van statussets](/rest/api/maps/featurestate/createstatesetpreview). Gebruik de `datasetId` van de gegevensset die de status bevat die u wilt wijzigen. De aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Stel in de **headers** van de **POST**-aanvraag `Content-Type` in op `application/json`. Geef in de **hoofd tekst** de onbewerkte JSON-stijlen op om wijzigingen aan te brengen in de `occupied` en de `temperature` *status*. Wanneer u klaar bent, klikt u op **Send**.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. Kopieer de `statesetId` vanuit hoofdtekst van het antwoord.

5. Voer een **POST**-aanvraag uit om de status bij te werken: Geef de statesetId en de `ID` van de functie door met uw Azure Maps-abonnementssleutel. De aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. Stel in de **headers** van de **POST**-aanvraag `Content-Type` in op `application/json`. Kopieer en plak de JSON uit het onderstaande voorbeeld in de **hoofdtekst** van de **POST**-aanvraag.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > De update wordt alleen opgeslagen als het tijdstempel van de aanvraag later is dan het tijdstempel van de vorige aanvraag. Elke sleutelnaam die eerder tijdens het maken is geconfigureerd, kan worden doorgegeven.

7. Wanneer een update is voltooid, ontvangt u een HTTP-statuscode van het type `200 OK`. Als u [dynamische opmaak hebt geïmplementeerd](indoor-map-dynamic-styling.md) voor een indoorplattegrond, wordt de update op het opgegeven tijdstempel op de plattegrond weergegeven.

Met de [API voor het ophalen van functiestatussen](/rest/api/maps/featurestate/getstatespreview) kunt u de status van een functie ophalen met behulp van de `ID` van de functie. U kunt de statusset en de bijbehorende resources ook verwijderen. Dit doet u met behulp van de [API voor het verwijderen van functiestatussen](/rest/api/maps/featurestate/deletestatesetpreview).

Zie [Creator Indoor Maps](creator-indoor-maps.md) voor meer informatie over de verschillende Azure Maps Creator-services (preview) die in dit artikel zijn besproken.

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van de module voor indoorplattegronden raadpleegt u

> [!div class="nextstepaction"]
> [De module Indoor Maps gebruiken](how-to-use-indoor-module.md)