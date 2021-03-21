---
title: Correlatie-Id's toevoegen aan IoT-berichten met gedistribueerde tracering (vooraf)
description: Meer informatie over het gebruik van de functie voor gedistribueerde tracering om IoT-berichten te traceren in de Azure-Services die door uw oplossing worden gebruikt.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: adcbf4efc4dfaa7701c18440531327949640cb53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581996"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Azure IoT-apparaat-naar-Cloud-berichten traceren met gedistribueerde tracering (voor beeld)

Microsoft Azure IoT Hub ondersteunt momenteel gedistribueerde tracering als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub is een van de eerste Azure-Services ter ondersteuning van gedistribueerde tracering. Naarmate meer Azure-Services gedistribueerde tracering ondersteunen, kunt u IoT-berichten traceren in de Azure-Services die bij uw oplossing betrokken zijn. Zie [gedistribueerde tracering](../azure-monitor/app/distributed-tracing.md)voor een achtergrond van gedistribueerde tracering.

Door gedistribueerde tracering in te scha kelen voor IoT Hub hebt u de volgende mogelijkheden:

- Bewaak de stroom van elk bericht nauw keurig via IoT Hub met behulp van de [tracerings context](https://github.com/w3c/trace-context). Deze tracerings context bevat correlatie-Id's waarmee u gebeurtenissen van het ene onderdeel kunt correleren met gebeurtenissen van een ander onderdeel. Het kan worden toegepast voor een subset of alle IoT-apparaten met behulp van [device-twee](iot-hub-devguide-device-twins.md).
- Registreer de tracerings context automatisch naar [Azure monitor logboeken](monitor-iot-hub.md).
- Meet en begrijp de berichten stroom en latentie van apparaten tot IoT Hub en routerings eindpunten.
- Ga eerst na hoe u gedistribueerde tracering wilt implementeren voor de niet-Azure-Services in uw IoT-oplossing.

In dit artikel gebruikt u de [Azure IOT Device SDK voor C](iot-hub-device-sdk-c-intro.md) met gedistribueerde tracering. Ondersteuning voor gedistribueerde tracering wordt nog steeds uitgevoerd voor de andere Sdk's.

## <a name="prerequisites"></a>Vereisten

- De preview-versie van gedistribueerde tracering wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  - **Europa - noord**
  - **Azië - zuidoost**
  - **West US 2**

- In dit artikel wordt ervan uitgegaan dat u bekend bent met het verzenden van telemetrie-berichten naar uw IoT-hub. Zorg ervoor dat u de Snelstartgids voor het [verzenden van telemetrie C](quickstart-send-telemetry-c.md)hebt voltooid.

- Registreer een apparaat met uw IoT-hub (de stappen die beschikbaar zijn in elke Snelstartgids) en noteer de connection string.

- Installeer de meest recente versie van [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>IoT Hub configureren

In deze sectie configureert u een IoT Hub voor het registreren van gedistribueerde traceer kenmerken (correlatie-Id's en tijds tempels).

1. Ga in het [Azure Portal](https://portal.azure.com/)naar uw IOT-hub.

1. Schuif in het linkerdeel venster voor uw IoT-hub omlaag naar het gedeelte **bewaking** en klik op **Diagnostische instellingen**.

1. Klik op **Diagnostische instelling toevoegen**.

1. Voer in het veld **naam** een naam in voor een nieuwe diagnostische instelling. Bijvoorbeeld **DistributedTracingSettings**.

1. Kies een of meer van de volgende opties om te bepalen waar de logboek registratie wordt verzonden:

    - **Archiveren naar een opslag account**: Configureer een opslag account om de logboek gegevens te bevatten.
    - **Streamen naar een event hub**: configureer een event hub om de logboek gegevens te bevatten.
    - **Verzenden naar log Analytics**: Configureer een log Analytics-werk ruimte om de logboek gegevens te bevatten.

1. Selecteer in de sectie **logboek** de bewerkingen waarvoor u logboek registratie gegevens wilt.

    Zorg ervoor dat u **DistributedTracing** opneemt en een **Bewaar periode** configureert voor het aantal dagen dat de logboek registratie moet worden bewaard. De Bewaar periode van het logboek heeft invloed op de opslag kosten.

    ![Scherm opname van de categorie DistributedTracing voor IoT Diagnostic Settings](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Klik op **Opslaan** voor de nieuwe instelling.

1. Beschrijving Als u de berichten stroom naar verschillende locaties wilt weer geven, stelt [u routerings regels in op ten minste twee verschillende eind punten](iot-hub-devguide-messages-d2c.md).

Zodra de logboek registratie is ingeschakeld, registreert IoT Hub een logboek wanneer een bericht met geldige tracerings eigenschappen wordt aangetroffen in een van de volgende situaties:

- De berichten arriveren op de gateway van IoT Hub.
- Het bericht wordt verwerkt door de IoT Hub.
- Het bericht wordt doorgestuurd naar aangepaste eind punten. Route ring moet zijn ingeschakeld.

Zie IoT Hub en [gedistribueerde tracering in IOT hub-bron logboeken](monitor-iot-hub-reference.md#distributed-tracing-preview) [controleren](monitor-iot-hub.md) voor meer informatie over deze logboeken en de bijbehorende schema's.

## <a name="set-up-device"></a>Apparaat instellen

In deze sectie bereidt u een ontwikkel omgeving voor op gebruik met de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c). Vervolgens wijzigt u een van de voor beelden om gedistribueerde tracering in te scha kelen op de telemetrie-berichten van uw apparaat.

Deze instructies zijn voor het bouwen van het voor beeld in Windows. Zie voor andere omgevingen [de c SDK compileren of de](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) [voor verpakking van de c SDK voor platform-specifieke ontwikkeling](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>De bron code klonen en initialiseren

1. Installeer [' Desktop Development with C++ ' workload](/cpp/build/vscpp-step-0-installation?view=vs-2019) voor Visual Studio 2019. Visual Studio 2017 en 2015 worden ook ondersteund.

1. Installeer [cmake](https://cmake.org/). Zorg ervoor dat deze in uw `PATH` typt door te typen `cmake -version` vanaf een opdracht prompt.

1. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de nieuwste versie van de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats te klonen:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten uit vanuit de map `azure-iot-sdk-c`:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Als `cmake` u uw C++-compiler niet kunt vinden, kunt u tijdens het uitvoeren van de bovenstaande opdracht build-fouten krijgen. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Bewerk het voor beeld telemetrie verzenden om gedistribueerde tracering in te scha kelen

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Het voor beeld op GitHub ophalen</a>

1. Gebruik een editor om het `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` bron bestand te openen.

1. Zoek de declaratie van de `connectionString` constante:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Vervang de waarde van de `connectionString` constante door het apparaat Connection String u een notitie hebt gemaakt in de sectie [een apparaat registreren](./quickstart-send-telemetry-c.md#register-a-device) van de [Snelstartgids voor het verzenden van telemetrie C](./quickstart-send-telemetry-c.md).

1. Wijzig de `MESSAGE_COUNT` definitie in `5000` :

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Zoek de regel met code die aanroept `IoTHubDeviceClient_LL_SetConnectionStatusCallback` voor het registreren van een call back functie van een verbindings status vóór de lus bericht verzenden. Voeg code toe onder die regel zoals hieronder wordt weer gegeven om `IoTHubDeviceClient_LL_EnablePolicyConfiguration` het inschakelen van gedistribueerde tracering voor het apparaat aan te roepen:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    De `IoTHubDeviceClient_LL_EnablePolicyConfiguration` functie maakt beleids regels mogelijk voor specifieke IoTHub-functies die zijn geconfigureerd via [apparaat apparaatdubbels](./iot-hub-devguide-device-twins.md). Eenmaal `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` ingeschakeld met de bovenstaande regel code wordt met het tracerings gedrag van het apparaat gedistribueerde tracerings wijzigingen weer gegeven die zijn aangebracht op het apparaat dubbele.

1. Als u de voor beeld-app wilt blijven uitvoeren zonder gebruik te maken van al uw quota, voegt u aan het einde van de lus Send Message een vertraging toe van een seconde:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compileren en uitvoeren

1. Ga naar de project directory van *iothub_ll_telemetry_sample* in de map cmake ( `azure-iot-sdk-c/cmake` ) die u eerder hebt gemaakt en compileer het voor beeld:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Voer de toepassing uit. Het apparaat verzendt telemetrie ondersteuning voor gedistribueerde tracering.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Zorg ervoor dat de app wordt uitgevoerd. Bekijk eventueel het bericht dat wordt verzonden naar IoT Hub door te kijken naar het console venster.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Tijdelijke oplossing voor clients van derden

Het is **niet** eenvoudig om een voor beeld van de gedistribueerde tracerings functie te bekijken zonder de C SDK te gebruiken. Daarom wordt deze methode niet aanbevolen.

Eerst moet u alle IoT Hub protocol primitieven in uw berichten implementeren door de ontwikkelaars gids te volgen om [IOT hub berichten te maken en te lezen](iot-hub-devguide-messages-construct.md). Bewerk vervolgens de protocol eigenschappen in de MQTT/AMQP-berichten om toe te voegen `tracestate` als **systeem eigenschap**. Met name:

* Voor MQTT voegt `%24.tracestate=timestamp%3d1539243209` u toe aan het bericht onderwerp, waar `1539243209` moet worden vervangen door de aanmaak tijd van het bericht in de Unix-Time Stamp-indeling. Zie als voor beeld de implementatie [in de C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Voor AMQP, toevoegen `key("tracestate")` en `value("timestamp=1539243209")` als aantekening van een bericht. Zie [hier](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)voor een referentie-implementatie.

Voor het beheren van het percentage berichten dat deze eigenschap bevat, implementeert u logica om te Luis teren naar gebeurtenissen die in de cloud worden gestart, zoals dubbele updates.

## <a name="update-sampling-options"></a>Sampling opties bijwerken 

Als u het percentage berichten wilt wijzigen dat moet worden getraceerd vanuit de Cloud, moet u het apparaat dubbele bijwerken. U kunt dit op meerdere manieren doen, met inbegrip van de JSON-editor in portal en de IoT Hub Service-SDK. In de volgende subsecties vindt u voor beelden.

### <a name="update-using-the-portal"></a>Bijwerken met behulp van de portal

1. Ga in [Azure Portal](https://portal.azure.com/)naar uw IOT-hub en klik vervolgens op **IOT-apparaten**.

1. Klik op uw apparaat.

1. Zoek naar **gedistribueerde tracering inschakelen (preview)** en selecteer **inschakelen**.

    ![Gedistribueerde tracering inschakelen in Azure Portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Kies een **sampling frequentie** tussen 0% en 100%.

1. Klik op **Opslaan**.

1. Wacht een paar seconden en klik op **vernieuwen**. Als u het apparaat hebt bevestigd, wordt het pictogram synchroniseren weer gegeven.

1. Ga terug naar het console venster voor de app voor telemetrie-berichten. Berichten die worden verzonden met `tracestate` in de toepassings eigenschappen worden weer gegeven.

    ![Traceer status](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. Beschrijving Wijzig de sampling frequentie in een andere waarde en Bekijk de wijziging in de frequentie die berichten bevatten `tracestate` in de eigenschappen van de toepassing.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Bijwerken met Azure IoT Hub voor VS code

1. Installeer VS code en Installeer [hier](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)de nieuwste versie van Azure IOT hub voor VS code.

1. Open VS code en [stel IoT Hub Connection String](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites)in.

1. Vouw het apparaat uit en zoek naar de **instelling voor gedistribueerde tracering (preview)**. Klik daaronder op **Update gedistribueerde tracerings instelling (preview)** van SubNode.

    ![Gedistribueerde tracering inschakelen in azure IoT Hub-extensie](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Selecteer in het pop-upvenster **inschakelen** en druk vervolgens op Enter om 100 te bevestigen als sampling frequentie.

    ![Sampling-modus bijwerken](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Sampling frequentie bijwerken](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Bulksgewijs bijwerken voor meerdere apparaten

Voor het bijwerken van de configuratie van de gedistribueerde tracerings sampling voor meerdere apparaten, gebruikt u [automatische apparaatconfiguratie](./iot-hub-automatic-device-management.md). Zorg ervoor dat u dit dubbele schema volgt:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Elementnaam | Vereist | Type | Beschrijving |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Ja | Geheel getal | Op dit moment worden twee modus waarden ondersteund om steek proeven in en uit te scha kelen. `1` is ingeschakeld en `2` is uit. |
| `sampling_rate` | Ja | Geheel getal | Deze waarde is een percentage. Alleen waarden van `0` tot `100` (inclusief) zijn toegestaan.  |

## <a name="query-and-visualize"></a>Query's uitvoeren en visualiseren

Als u alle traceringen wilt weer geven die door een IoT Hub zijn geregistreerd, moet u een query uitvoeren op het logboek archief dat u in de diagnostische instellingen hebt geselecteerd. In deze sectie vindt u een aantal verschillende opties.

### <a name="query-using-log-analytics"></a>Query's uitvoeren met Log Analytics

Als u [log Analytics met resource logboeken](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)hebt ingesteld, kunt u in de categorie zoeken naar Logboeken `DistributedTracing` . Deze query bevat bijvoorbeeld alle traceer logboeken die zijn vastgelegd:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Voorbeeld logboeken, zoals weer gegeven door Log Analytics:

| TimeGenerated | OperationName | Categorie | Niveau | CorrelationId | DurationMs | Eigenschappen |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633 Z | DiagnosticIoTHubD2C | DistributedTracing | Informatief | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId": "AZ3166", "messageSize": "96", "callerLocalTimeUtc": "2018-02-22T03:27:28.633 Z", "calleeLocalTimeUtc": "2018-02-22T03:27:28.687 Z"} |
| 2018-02-22T03:28:38.633 Z | DiagnosticIoTHubIngress | DistributedTracing | Informatief | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled": "false", "parentSpanId": "0144d2590aacd909"} |
| 2018-02-22T03:28:48.633 Z | DiagnosticIoTHubEgress | DistributedTracing | Informatief | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType": "EventHub", "eind punt": "myEventHub", "parentSpanId": "0144d2590aacd909"} |

Zie [Logboeken voor gedistribueerde tracering van Azure IOT hub](monitor-iot-hub-reference.md#distributed-tracing-preview)voor meer informatie over de verschillende soorten logboeken.

### <a name="application-map"></a>Toepassingskaart

Als u de stroom van IoT-berichten wilt visualiseren, stelt u de voor beeld-app voor de toepassings toewijzing in. De voor beeld-app verzendt de gedistribueerde tracerings logboeken naar de [toepassings toewijzing](../azure-monitor/app/app-map.md) met behulp van een Azure-functie en een event hub.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Het voor beeld op GitHub ophalen</a>

In onderstaande afbeelding ziet u gedistribueerde tracering in de app-toewijzing met drie routerings eindpunten:

![IoT-gedistribueerde tracering in de app-kaart](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Meer informatie over gedistribueerde Azure IoT-tracering

### <a name="context"></a>Context

Veel IoT-oplossingen, waaronder onze eigen [referentie architectuur](/azure/architecture/reference-architectures/iot) (alleen Engels), volgen doorgaans een variant van de [micro service-architectuur](/azure/architecture/microservices/). Naarmate een IoT-oplossing complexer wordt, kunt u een dozijn of meer micro Services gebruiken. Deze micro Services kunnen al dan niet afkomstig zijn van Azure. Het herkennen van IoT-berichten die worden verwijderd of vertraagd, kan lastig worden. U hebt bijvoorbeeld een IoT-oplossing die gebruikmaakt van vijf verschillende Azure-Services en 1500 actieve apparaten. Elk apparaat verzendt 10 apparaat-naar-Cloud-berichten per seconde (voor een totaal van 15.000 berichten/seconde), maar u ziet dat uw web-app alleen 10.000 berichten per seconde ziet. Waar is het probleem? Hoe vindt u de culprit?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Gedistribueerd tracerings patroon in micro service-architectuur

Voor het opnieuw samen stellen van de stroom van een IoT-bericht over verschillende services moet elke service een *correlatie-id* door geven waarmee het bericht uniek wordt geïdentificeerd. Nadat de correlatie-Id's in een gecentraliseerd systeem zijn verzameld, kunt u de berichten stroom zien. Deze methode wordt het [gedistribueerde tracerings patroon](/azure/architecture/microservices/logging-monitoring#distributed-tracing)genoemd.

Micro soft draagt bij aan het gebruik van het standaard W3C-voor deel [voor gedistribueerde tracering](https://w3c.github.io/trace-context/)om de uitgebreide acceptatie van gedistribueerde tracering te ondersteunen.

### <a name="iot-hub-support"></a>Ondersteuning voor IoT Hub

Als de functie voor gedistribueerde tracering voor IoT Hub is ingeschakeld, volgt deze stroom:

1. Er wordt een bericht gegenereerd op het IoT-apparaat.
1. Het IoT-apparaat beslist (met de hulp van de Cloud) dat dit bericht moet worden toegewezen aan een tracerings context.
1. De SDK voegt een `tracestate` toe aan de bericht eigenschap met de tijds tempel voor het maken van het bericht.
1. Het IoT-apparaat verzendt het bericht naar IoT Hub.
1. Het bericht arriveert bij de IoT hub-gateway.
1. IoT Hub zoekt naar de `tracestate` in de bericht eigenschappen en controleert of het de juiste indeling heeft.
1. Als dit het geval is, genereert IoT Hub een wereld wijd uniek `trace-id` voor het bericht, een `span-id` voor de ' hop ' en worden ze door gegeven aan [IOT hub gedistribueerde traceer logboeken](monitor-iot-hub-reference.md#distributed-tracing-preview) onder de bewerking `DiagnosticIoTHubD2C` .
1. Zodra de bericht verwerking is voltooid, wordt er door IoT Hub een andere gegenereerd `span-id` en wordt deze samen met de bestaande `trace-id` onder de bewerking geregistreerd `DiagnosticIoTHubIngress` .
1. Als route ring is ingeschakeld voor het bericht, wordt het door IoT Hub naar het aangepaste eind punt geschreven en wordt er een andere logboek registratie `span-id` met dezelfde `trace-id` categorie `DiagnosticIoTHubEgress` .
1. De bovenstaande stappen worden herhaald voor elk gegenereerd bericht.

## <a name="public-preview-limits-and-considerations"></a>Limieten en overwegingen voor de open bare preview

- Het voor stel voor de W3C-tracerings context Standard is momenteel een Working-concept.
- Op dit moment is de enige ontwikkel taal die wordt ondersteund door de client-SDK C.
- De ondersteuning voor de dubbele Cloud-naar-apparaat-laag is niet beschikbaar voor [IOT hub Basic](iot-hub-scaling.md#basic-and-standard-tiers). IoT Hub meldt zich echter nog steeds aan Azure Monitor als er een goed opgebouwde tracerings context header wordt weer te zien.
- Om ervoor te zorgen dat een efficiënte bewerking wordt uitgevoerd, wordt door IoT Hub een beperking opgelegd voor de frequentie van logboek registratie die kan optreden als onderdeel van gedistribueerde tracering.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het algemene gedistribueerde tracerings patroon in micro Services, het [volgende: gedistribueerde tracering-architectuur patroon](https://microservices.io/patterns/observability/distributed-tracing.html)
- Zie [IOT-apparaten op schaal configureren en controleren](./iot-hub-automatic-device-management.md)om configuratie in te stellen voor het Toep assen van gedistribueerde tracerings instellingen op een groot aantal apparaten.
- Zie [Wat is Azure monitor?](../azure-monitor/overview.md)voor meer informatie over Azure monitor.
- Zie [Monitor IOT hub](monitor-iot-hub.md) voor meer informatie over het gebruik van Azure monitor met IOT HUb.