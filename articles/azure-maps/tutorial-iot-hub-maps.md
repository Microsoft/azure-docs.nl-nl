---
title: 'Zelfstudie: Ruimtelijke IoT-analyse implementeren | Microsoft Azure Maps'
description: Zelfstudie over het integreren van IoT Hub met API's van de Microsoft Azure Maps-service
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 91556087cd3e7ccd85550c721f70b8608fdc4ffd
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168447"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Zelfstudie: Ruimtelijke IoT-analyse implementeren met behulp van Azure Maps

In een IoT-scenario is het gebruikelijk om relevante gebeurtenissen die zich in ruimte en tijd voordoen, vast te leggen en bij te houden. Voorbeelden omvatten wagenparkbeheer, assets bijhouden, mobiliteit en Smart City-toepassingen. Deze zelfstudie begeleidt u door een oplossing waarmee het verplaatsen van tweedehandshuurauto's wordt getraceerd, met behulp van de Azure Maps-API's.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een Azure-opslagaccount maken om traceringsgegevens voor auto's te registreren.
> * Een geofence uploaden naar de Azure Maps Data-service (preview) met behulp van de Data Upload-API.
> * Een hub maken in Azure IoT Hub en een apparaat registreren.
> * Een functie maken in Azure Functions, waarbij u bedrijfslogica implementeert op basis van de ruimtelijke analyse van Azure Maps.
> * U abonneren op telemetriegebeurtenissen voor IoT-apparaten vanuit de Azure-functie via Azure Event Grid.
> * De telemetriegebeurtenissen filteren met behulp van berichtroutering van IoT Hub.

## <a name="prerequisites"></a>Vereisten

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. [Maak een Azure Maps-account](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie.

4. [Maak een resourcegroep.](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) In deze zelfstudie noemen we de resourcegroep *ContosoRental*, maar u kunt elke gewenste naam kiezen.

5. Download het [C#-project rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

In deze zelfstudie wordt gebruikgemaakt van de toepassing [Postman](https://www.postman.com/), maar u kunt ook een andere API-ontwikkelomgeving kiezen.

## <a name="use-case-rental-car-tracking"></a>Use-case: huurauto traceren

Stel dat een autoverhuurbedrijf locatiegegevens, reisafstand en actieve status voor de huurauto's van het bedrijf wil registreren. Het bedrijf wil deze informatie ook opslaan telkens wanneer een auto de correcte geautoriseerde geografische regio verlaat.

De huurauto's zijn uitgerust met IoT-apparaten die regelmatig telemetriegegevens naar IoT Hub verzenden. De telemetrie bevat de huidige locatie en geeft aan of de motor van de auto loopt. Het apparaatlocatieschema houdt zich aan het IoT [Plug and Play-schema voor georuimtelijke gegeven](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Het schema voor de apparaattelemetrie van de huurauto ziet eruit als de volgende JSON-code:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

In deze zelfstudie volgt u maar één voertuig. Nadat de Azure-services zijn ingesteld, moet u het [C#-project rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) downloaden om de voertuigsimulator uit te voeren. Het hele proces, van gebeurtenis tot uitvoering van de functie, wordt samengevat in de volgende stappen:

1. Het apparaat in het voertuig verzendt telemetriegegevens naar IoT Hub.

2. Als de motor van de auto loopt, publiceert de hub de telemetriegegevens in Event Grid.

3. Er wordt een Azure-functie geactiveerd vanwege het bijbehorende gebeurtenisabonnement op telemetriegebeurtenissen van het apparaat.

4. Met de functie worden de locatiecoördinaten van het voertuig, de gebeurtenistijd en de apparaat-id geregistreerd. Vervolgens wordt de [Spatial Geofence Get API](/rest/api/maps/spatial/getgeofence) gebruikt om te bepalen of de auto zich buiten de geofence heeft begeven. Als de auto buiten de grenzen van de geofence is geweest, worden de locatiegegevens die zijn ontvangen van de gebeurtenis, met de functie opgeslagen in een blobcontainer. Via de functie wordt ook de [omgekeerde zoekopdracht voor adressen](/rest/api/maps/search/getsearchaddressreverse) uitgevoerd om de locatiecoördinaten om te zetten in een straatnaam; deze wordt vervolgens opgeslagen bij de andere locatiegegevens voor het apparaat.

In het volgende diagram ziet u een overzicht op hoog niveau van het systeem.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagram van systeemoverzicht.":::

In de volgende afbeelding is het geofence-gebied blauw gemarkeerd. De route van de huurauto wordt aangegeven met een groene lijn.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Afbeelding van geofence-route.":::

## <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

Om de traceringsgegevens over schendingen van de auto op te slaan, maakt u een [algemeen v2-opslagaccount](../storage/common/storage-account-overview.md#general-purpose-v2-accounts) in de resourcegroep. Als u nog geen resourcegroep hebt gemaakt, volgt u de instructies in [Een resourcegroep maken](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). In deze zelfstudie geeft u de resourcegroep de naam *ContosoRental*.

Volg de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal) om een opslagaccount te maken. In deze zelfstudie geeft u het opslagaccount de naam *contosorentalstorage*, maar over het algemeen kunt u elke gewenste naam kiezen.

Wanneer u het opslagaccount hebt gemaakt, moet u een container maken om logboekgegevens in op te slaan.

1. Ga naar het zojuist gemaakte opslagaccount. Selecteer de koppeling **Containers** in de sectie **Essentials**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Schermopname van containers voor blobopslag.":::

2. Selecteer **+ Container** in de linkerbovenhoek. Aan de rechterkant van de browser wordt een deelvenster weergegeven. Geef de container de naam *contoso-rental-logs* en selecteer **Maken**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Schermopname van een blobcontainer maken.":::

3. Ga naar het deelvenster **Toegangssleutels** in uw opslagaccount en kopieer de waarden bij **Opslagaccountnaam** en **Sleutel** in de sectie **key1**. U hebt deze beide waarden nodig in de sectie 'Een Azure-functie maken en een Event Grid-abonnement toevoegen'.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Schermopname van de naam en sleutel van het opslagaccount kopiëren.":::

## <a name="upload-a-geofence"></a>Een geofence uploaden

Gebruik nu de [Postman-app](https://www.getpostman.com) om [de geofence te uploaden](./geofence-geojson.md) naar Azure Maps. De geofence definieert het toegestane geografische gebied voor het huurvoertuig. U gebruikt de geofence in de Azure-functie om te bepalen of een auto is verplaatst buiten het geofence-gebied.

Volg deze stappen om de geofence te uploaden met behulp van de Azure Maps Data Upload-API: 

1. Open de Postman-app en selecteer **Nieuw**. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken). Geef de verzameling een naam en selecteer **Maken**.

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Aanvraag** in het venster **Nieuwe maken** en voer een aanvraagnaam in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

3. Selecteer de HTTP-methode **POST** op het tabblad Builder en voer de volgende URL in om de geofence te uploaden naar de Data Upload-API. Vergeet niet om `{subscription-key}` te vervangen door de primaire abonnementssleutel.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    De waarde `geojson` naast de parameter `dataFormat` in het URL-pad vertegenwoordigt de indeling van de gegevens die worden geüpload.

4. Selecteer **Hoofdtekst** > **onbewerkt** voor de invoerindeling en kies **JSON** in de vervolgkeuzelijst. [Open het JSON-gegevensbestand](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) en kopieer het bestand in de sectie met de hoofdtekst. Selecteer **Verzenden**.

5. Selecteer **Verzenden** en wacht totdat de aanvraag is verwerkt. Wanneer de aanvraag is voltooid, gaat u naar het tabblad **Kopteksten** van het antwoord. Kopieer de waarde van de **Location**-sleutel (Locatie), de `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Maak een **GET** HTTP-aanvraag op de `status URL` om de status van de API-aanroep te controleren. U moet uw primaire abonnementssleutel toevoegen aan de URL voor authenticatie. De **GET**-aanvraag moet lijken op de volgende URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Wanneer de HTTP-aanvraag **GET** met succes is uitgevoerd, retourneert deze een `resourceLocation`. De `resourceLocation` bevat de unieke `udid` voor de geüploade inhoud. Kopieer deze `udid` voor later gebruik in deze zelfstudie.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

IoT Hub maakt veilige en betrouwbare tweerichtingscommunicatie mogelijk tussen een IoT-toepassing en de beheerde apparaten. Voor deze zelfstudie wilt u informatie ontvangen van het apparaat in het voertuig om zo de locatie van de huurauto te bepalen. In deze sectie maakt u een IoT-hub in de resourcegroep *ContosoRental*. Deze hub is verantwoordelijk voor het publiceren van de telemetriegebeurtenissen van het apparaat.

> [!NOTE]
> De mogelijkheid om telemetriegebeurtenissen van apparaten in Event Grid te publiceren, is momenteel in openbare preview. Deze functie is beschikbaar in alle regio's, met uitzondering van de volgende: US - oost, US - west, Europa - west, Azure Government, Azure China 21Vianet en Microsoft Azure Duitsland.

Als u een IoT-hub wilt maken in de resourcegroep *ContosoRental*, volgt u de stappen in [Een IoT-hub maken](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub).

## <a name="register-a-device-in-your-iot-hub"></a>Een apparaat registreren in uw IoT-hub

Apparaten kunnen geen verbinding maken met de IoT-hub, tenzij ze zijn geregistreerd in het IoT Hub-identiteitsregister. Hier maakt u één apparaat met de naam *InVehicleDevice*. Als u het apparaat wilt maken en registreren in uw IoT-hub, volgt u de stappen in [Een nieuw apparaat registreren in de IoT-hub](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub). Zorg ervoor dat u de primaire verbindingsreeks van het apparaat kopieert. U hebt deze later nodig.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Een functie maken en een Event Grid-abonnement toevoegen

Azure Functions is een serverloze rekenservice waarmee u kleine codefragmenten (functies) kunt uitvoeren zonder dat u de rekeninfrastructuur expliciet hoeft in te richten of te beheren. Zie [Azure Functions](../azure-functions/functions-overview.md)voor meer informatie.

Een functie wordt geactiveerd via een bepaalde gebeurtenis. Hier maakt u een functie die wordt geactiveerd met een Event Grid-trigger. Maak de relatie tussen de trigger en de functie door een gebeurtenisabonnement te maken voor de telemetriegebeurtenissen van het IoT Hub-apparaat. Wanneer een telemetriegebeurtenis van het apparaat optreedt, wordt de functie aangeroepen als eindpunt, en ontvangt deze de relevante gegevens voor het apparaat dat u eerder hebt geregistreerd in IoT Hub.

Hier ziet u de [C#-scriptcode die uw functie bevat](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Stel nu uw Azure-functie in.

1. Selecteer **Een resource maken** in het dashboard van Azure Portal. Typ in het tekstvak: **Functie-app**. Selecteer **Functie-app** > **Maken**.

1. Geef de functie-app een naam op de aanmaakpagina van **Functie-app**. Selecteer onder **Resourcegroep** de optie **ContosoRental** in de vervolgkeuzelijst. Selecteer **.NET Core** als **Runtimestack**. Selecteer onderaan de pagina **Volgende: Hosting>** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Schermopname van een functie-app maken.":::

1. Selecteer voor **Opslagaccount** het opslagaccount dat u hebt gemaakt in [Een Azure-opslagaccount maken](#create-an-azure-storage-account). Selecteer **Controleren + maken**.

1. Bekijk de details van de functie-app en selecteer **Maken**.

1. Wanneer de app is gemaakt, voegt u er een functie aan toe. Ga naar functie app. Selecteer het deelvenster **Functies**. Selecteer **+ Toevoegen** bovenaan de pagina. Het deelvenster met functiesjablonen wordt weergegeven. Schuif omlaag in het deelvenster en selecteer **Azure Event Grid-trigger**.

     >[!IMPORTANT]
    > De namen van de sjablonen **Azure Event Hub-trigger** en **Azure Event Grid-trigger** lijken op elkaar. Zorg ervoor dat u de sjabloon **Azure Event Grid-trigger** selecteert.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Schermopname van een functie maken.":::

1. Geef een naam op voor de functie. In deze zelfstudie gebruikt u de naam *GetGeoFunction*, maar over het algemeen kunt u elke gewenste naam gebruiken. Selecteer **Functie maken**.

1. Selecteer in het linkermenu het deelvenster **Code + testen**. Kopieer en plak het [C#-script](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) in het codevenster.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopiëren/schermopname van code plakken in het functievenster.":::

1. Vervang de volgende parameters in de C#-code:
    * Vervang **SUBSCRIPTION_KEY** door de primaire abonnementssleutel van uw Azure Maps-account.
    * Vervang **UDID** door de `udid` van de geofence die u hebt geüpload in [Een geofence uploaden](#upload-a-geofence).
    * De functie `CreateBlobAsync` in het script maakt een blob per gebeurtenis in het gegevensopslagaccount. Vervang de **ACCESS_KEY**, **ACCOUNT_NAME** en **STORAGE_CONTAINER_NAME** door de toegangssleutel, accountnaam en gegevensopslagcontainer van je opslagaccount. Deze waarden zijn gegenereerd tijdens het maken van het opslagaccount in [Een Azure-opslagaccount maken](#create-an-azure-storage-account).

1. Selecteer in het linkermenu het deelvenster **Integratie**. Selecteer **Event Grid-trigger** in het diagram. Typ een naam voor de trigger, bijvoorbeeld *eventGridEvent* en selecteer **Event Grid-abonnement maken**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Schermopname van gebeurtenisabonnement toevoegen.":::

1. Vul de abonnementsgegevens in. Geef het gebeurtenisabonnement een naam. Selecteer bij **Gebeurtenisschema** de optie **Gebeurtenisrasterschema**. Selecteer bij **Onderwerptypen** de optie **Azure IoT Hub-accounts**. Selecteer bij **Resourcegroep** de resourcegroep die u hebt gemaakt aan het begin van deze zelfstudie. Selecteer bij **Resource** de IoT-hub die u hebt gemaakt in 'Een Azure IoT-hub maken'. Selecteer bij **Filteren op gebeurtenistype** de optie **Apparaattelemetrie**.

   Nadat u deze opties hebt gekozen, ziet u dat **Onderwerptype** is gewijzigd in **IoT Hub**. Voor **Systeemonderwerpnaam** kunt u dezelfde naam gebruiken als voor uw resource. Selecteer ten slotte in de sectie **Eindpuntgegevens** de optie **Een eindpunt selecteren**. Accepteer alle instellingen en selecteer **Selectie bevestigen**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Schermopname van gebeurtenisabonnement maken.":::

1. Controleer de instellingen. Zorg ervoor dat het eindpunt de functie opgeeft die u in het begin van deze sectie hebt gemaakt. Selecteer **Maken**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Schermopname van bevestiging voor Gebeurtenisabonnement maken.":::

1. Nu bent u terug bij het paneel **Trigger bewerken**. Selecteer **Opslaan**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Gebeurtenissen filteren met behulp van berichtroutering van IoT Hub

Wanneer u een Event Grid-abonnement toevoegt aan de Azure-functie, wordt automatisch een berichtroute gemaakt in de opgegeven IoT-hub. Met berichtroutering kunt u verschillende gegevenstypen naar meerdere eindpunten routeren. Je kunt bijvoorbeeld telemetrieberichten van apparaten, levenscyclusgebeurtenissen van apparaten en dubbele wijzigingen van apparaten routeren. Zie [IoT Hub-berichtroutering gebruiken](../iot-hub/iot-hub-devguide-messages-d2c.md) voor meer informatie.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Schermopname van berichtroutering in IoT Hub.":::

In het voorbeeldscenario wilt u alleen berichten ontvangen wanneer de huurauto wordt verplaatst. Maak een routeringsquery om de gebeurtenissen te filteren waarbij de eigenschap `Engine` gelijk is aan **ON**. Als u een routeringsquery wilt maken, selecteert u de route **RouteToEventGrid** en vervangt u de **Routing-query** door **"Engine = 'ON'"** . Selecteer vervolgens **Opslaan**. De IoT-hub publiceert nu alleen apparaattelemetrie als de motor is ingeschakeld.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Schermopname van routeringsberichten filteren.":::

>[!TIP]
>Er zijn verschillende manieren om een query uit te voeren op IoT-apparaat-naar-cloud-berichten. Raadpleeg [IoT Hub-berichtroutering gebruiken](../iot-hub/iot-hub-devguide-routing-query-syntax.md) voor meer informatie over syntaxis van berichtroutering.

## <a name="send-telemetry-data-to-iot-hub"></a>Stuur telemetriegegevens naar IoT Hub

Wanneer de Azure-functie actief is, kunt u telemetriegegevens verzenden naar de IoT-hub, vanwaaruit ze naar Event Grid worden gerouteerd. Gebruik een C#-applicatie om locatiegegevens te simuleren voor een in-voertuig apparaat van een huurauto. U hebt .NET Core SDK 2.1.0 of hoger nodig op de ontwikkelcomputer om de toepassing uit te voeren. Volg deze stappen om gesimuleerde telemetriegegevens naar de IoT-hub te verzenden:

1. Als u dit nog niet hebt gedaan, downloadt u het C#-project [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

2. Open het bestand `simulatedCar.cs` in een teksteditor naar keuze en vervang de waarde van de `connectionString` door de waarde die u hebt opgeslagen toen u het apparaat registreerde. Sla de wijzigingen in het bestand op.

3. Zorg ervoor dat .NET Core op je computer is geïnstalleerd. Ga in uw lokale terminalvenster naar de hoofdmap van het C#-project en voer de volgende opdracht uit om de vereiste pakketten voor gesimuleerde apparaattoepassing te installeren:

    ```cmd/sh
    dotnet restore
    ```

4. Voer in dezelfde terminal de volgende opdracht uit om de simulatietoepassing voor huurauto's te bouwen en uit te voeren:

    ```cmd/sh
    dotnet run
    ```


  Je lokale terminal zou er als volgt uit moeten zien.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Schermopname van terminaluitvoer.":::

Als u nu de blobopslagcontainer opent, ziet u vier blobs voor locaties waar het voertuig zich buiten de geofence bevond.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Schermopname van blobs in een container weergeven.":::

Op de volgende kaart worden vier locatiepunten van het voertuig weergegeven die buiten de geofence vallen. Elke locatie is met regelmatige tijdsintervallen geregistreerd.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Schermopname van kaart met schendingen.":::

## <a name="explore-azure-maps-and-iot"></a>Azure Maps en IoT verkennen

Ga voor meer informatie over het verkennen van de Azure Maps API's die in deze zelfstudie worden gebruikt naar:

* [Zoekadres omkeren](/rest/api/maps/search/getsearchaddressreverse)
* [Geofence downloaden](/rest/api/maps/spatial/getgeofence)

Voor een volledige lijst van Azure Maps REST API's, bekijk:

* [Azure Maps REST API's](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Voor een lijst met apparaten die Azure-gecertificeerd zijn voor IoT ga je naar:

* [Azure-gecertificeerde apparaten](https://devicecatalog.azure.com/)

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources die moeten worden opgeruimd.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het verzenden van apparaat-naar-cloud-telemetrie en omgekeerd naar:


> [!div class="nextstepaction"]
> [Telemetrie verzenden vanaf een apparaat](../iot-hub/quickstart-send-telemetry-dotnet.md)