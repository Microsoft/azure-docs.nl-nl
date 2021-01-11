---
title: Gegevens exporteren uit Azure IoT Central (verouderd) | Microsoft Docs
description: Gegevens exporteren uit uw Azure IoT Central-toepassing naar Azure Event Hubs, Azure Service Bus en Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: d4c099d29a843b4c354ffb218887dc7ffab51771
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065436"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>IoT-gegevens exporteren naar Cloud bestemmingen met behulp van gegevens export (verouderd)

> [!Note]
> In dit artikel worden de verouderde functies voor gegevens export in IoT Central beschreven.
>
> - Zie [IOT-gegevens exporteren naar Cloud bestemmingen met behulp van gegevens export](./howto-export-data.md)voor meer informatie over de nieuwste functies voor het exporteren van gegevens.
> - Zie voor meer informatie over de verschillen tussen de functies voor het exporteren van de voorbeeld gegevens en verouderde gegevens export de [vergelijkings tabel](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export).

In dit artikel wordt beschreven hoe u de functie voor gegevens export kunt gebruiken in azure IoT Central. Met deze functie kunt u uw gegevens continu exporteren naar **azure Event hubs**, **Azure service bus** of **Azure Blob Storage** -exemplaren. Gegevens export maakt gebruik van de JSON-indeling en kan informatie over telemetrie, apparaatgegevens en apparaatinstellingen bevatten. De geëxporteerde gegevens gebruiken voor:

- Inzichten en analyse van warme paden. Deze optie omvat het activeren van aangepaste regels in Azure Stream Analytics, het activeren van aangepaste werk stromen in Azure Logic Apps, of het door geven via Azure Functions door lopen om te worden getransformeerd.
- Analyse van koude paden zoals trainings modellen in de analyse van Azure Machine Learning of lange termijn in micro soft Power BI.

> [!Note]
> Wanneer u het exporteren van gegevens inschakelt, worden er vanaf dat moment alleen gegevens opgehaald. Op dit moment kunnen geen gegevens worden opgehaald voor een tijdstip waarop het exporteren van gegevens is uitgeschakeld. Als u meer historische gegevens wilt behouden, schakelt u de optie gegevens export voor tijdig in.

## <a name="prerequisites"></a>Vereisten

U moet een beheerder zijn in uw IoT Central-toepassing of machtigingen voor het exporteren van gegevens hebben.

## <a name="set-up-export-destination"></a>Export bestemming instellen

Het export doel moet bestaan voordat u uw gegevens export kunt configureren.

### <a name="create-event-hubs-namespace"></a>De Event Hubs-naamruimte maken

Als u geen bestaande Event Hubs naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe event hubs naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Meer informatie vindt u in [Azure Event hubs docs](../../event-hubs/event-hubs-create.md).

2. Kies een abonnement. U kunt gegevens exporteren naar andere abonnementen die zich niet in hetzelfde abonnement bevinden als uw IoT Central-toepassing. In dit geval maakt u verbinding met behulp van een connection string.

3. Maak een Event Hub in uw Event Hubs naam ruimte. Ga naar uw naam ruimte en selecteer **+ Event hub** aan de bovenkant om een event hub-exemplaar te maken.

### <a name="create-service-bus-namespace"></a>Service Bus naam ruimte maken

Als u geen bestaande Service Bus naam ruimte hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [nieuwe service bus naam ruimte in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Meer informatie vindt u in [Azure service bus docs](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Kies een abonnement. U kunt gegevens exporteren naar andere abonnementen die zich niet in hetzelfde abonnement bevinden als uw IoT Central-toepassing. In dit geval maakt u verbinding met behulp van een connection string.

3. Als u een wachtrij of onderwerp wilt maken om naar te exporteren, gaat u naar uw Service Bus-naam ruimte en selecteert u **+ wachtrij** of **+ onderwerp**.

Wanneer u Service Bus als een export bestemming kiest, moeten de wacht rijen en onderwerpen geen sessies of duplicaten detectie zijn ingeschakeld. Als een van deze opties is ingeschakeld, arriveert sommige berichten niet in uw wachtrij of onderwerp.

### <a name="create-storage-account"></a>Een opslagaccount maken

Als u geen bestaand Azure Storage-account hebt om naar te exporteren, voert u de volgende stappen uit:

1. Maak een [Nieuw opslag account in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Meer informatie over het maken van nieuwe [Azure Blob Storage-accounts](../../storage/blobs/storage-quickstart-blobs-portal.md) of [Azure data Lake Storage v2-opslag accounts](../../storage/common/storage-account-create.md). Gegevens export kan alleen gegevens schrijven naar opslag accounts die ondersteuning bieden voor blok-blobs. De volgende lijst bevat de bekende compatibele opslag account typen:

    |Prestatie niveau|Accounttype|
    |-|-|
    |Standard|Algemeen v2|
    |Standard|Algemeen v1|
    |Standard|Blob Storage|
    |Premium|Blob-opslag blok keren|

2. Maak een container in uw opslag account. Ga naar uw opslagaccount. Selecteer onder **BLOB**-service **Bladeren door blobs**. Selecteer **+ container** aan de bovenkant om een nieuwe container te maken.

## <a name="set-up-data-export"></a>Gegevens export instellen

Nu u een bestemming hebt voor het exporteren van gegevens, voert u de volgende stappen uit om het exporteren van gegevens in te stellen.

1. Meld u aan bij uw IoT Central-toepassing.

2. Selecteer in het linkerdeel venster **gegevens export**.

    > [!Tip]
    > Als het exporteren van **gegevens** niet in het linkerdeel venster wordt weer gegeven, bent u niet gemachtigd om de gegevens export in uw app te configureren. Neem contact op met een beheerder om het exporteren van gegevens in te stellen.

3. Selecteer de knop **+ Nieuw** . Kies een van de **azure Blob Storage**, **azure Event hubs**, **Azure service bus wachtrij** of het **Azure service bus onderwerp** als bestemming voor de export. Het maximum aantal uitvoer bewerkingen per toepassing is vijf.

4. Voer een naam in voor de export. Selecteer in de vervolg keuzelijst uw **naam ruimte** of **Voer een Connection String** in.

    - U ziet alleen opslag accounts, Event Hubs naam ruimten en Service Bus naam ruimten in hetzelfde abonnement als uw IoT Central-toepassing. Als u wilt exporteren naar een bestemming buiten dit abonnement, kiest u **een Connection String invoeren** en gaat u naar stap 6.
    - Voor apps die zijn gemaakt met behulp van het prijs plan gratis, is de enige manier om de gegevens export te configureren via een connection string. Apps voor het gratis prijs plan hebben geen bijbehorend Azure-abonnement.

    ![Nieuwe event hub maken](media/howto-export-data-legacy/export-event-hub.png)

5. Kies een Event Hub, wachtrij, onderwerp of container in de vervolg keuzelijst.

6. Beschrijving Als u **een connection string invoert**, wordt er een nieuw vak weer gegeven waarin u uw Connection String kunt plakken. Ga als volgt te connection string:

    - Event Hubs of Service Bus gaat u naar de naam ruimte in de Azure Portal:
        - Een connection string gebruiken voor de volledige naam ruimte:
            1. Onder **instellingen** selecteert u **beleid voor gedeelde toegang**
            2. Maak een nieuwe sleutel of kies een bestaande sleutel die machtigingen voor **verzenden** heeft.
            3. De primaire of secundaire connection string kopiëren
        - Als u connection string wilt gebruiken voor een specifiek Event Hub exemplaar of Service Bus-wachtrij of-onderwerp, gaat u naar **entiteiten > Event hubs** of **entiteiten** > **onderwerpen**>. Kies een specifiek exemplaar en volg de bovenstaande stappen om een connection string op te halen.
    - Opslag account, gaat u naar het opslag account in de Azure Portal:
        - Alleen verbindings reeksen voor het hele opslag account worden ondersteund. Verbindings reeksen die zijn beperkt tot één container, worden niet ondersteund.
          1. Selecteer onder **instellingen** **toegangs sleutels**
          2. Kopieer de Key1-connection string of de Key2-connection string

    Plak de connection string. Typ de naam van het exemplaar of de hoofdletter gevoelige **container**.

7. Kies onder te **exporteren gegevens** de typen gegevens die u wilt exporteren door het type in te stellen **op on**.

8. Als u gegevens export wilt inschakelen, schakelt u de **ingeschakelde** wissel knop **in**. Selecteer **Opslaan**.

9. Na enkele minuten worden uw gegevens weer gegeven in de bestemming die u hebt gekozen.

## <a name="export-contents-and-format"></a>Inhoud en indeling exporteren

Geëxporteerde telemetriegegevens bevatten het volledige bericht dat uw apparaten naar IoT Central zijn verzonden, niet alleen de telemetrie-waarden zelf. Gegevens van geëxporteerde apparaten bevatten wijzigingen in eigenschappen en meta gegevens van alle apparaten, en geëxporteerde Apparaatinstellingen bevatten wijzigingen in alle sjablonen voor apparaten.

Voor Event Hubs en Service Bus worden gegevens in bijna realtime geëxporteerd. De gegevens bevindt zich in de `body` eigenschap en bevindt zich in JSON-indeling. Hieronder vindt u voor beelden.

Bij Blob Storage worden gegevens eenmaal per minuut geëxporteerd, waarbij elk bestand met de batch wijzigingen sinds het laatste geëxporteerde bestand is. Geëxporteerde gegevens worden in drie mappen in JSON-indeling geplaatst. De standaard paden in uw opslag account zijn:

- Telemetrie: _{container}/{app-id}/Telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Apparaten: _{container}/{App-ID}/devices/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Apparaatinstellingen: _{container}/{app-id}/deviceTemplates/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_

Als u door de geëxporteerde bestanden in de Azure Portal wilt bladeren, gaat u naar het bestand en selecteert u het tabblad **BLOB bewerken** .

## <a name="telemetry"></a>Telemetrie

Voor Event Hubs en Service Bus, IoT Central een nieuw bericht snel exporteren nadat het bericht van een apparaat is ontvangen. Elk geëxporteerd bericht bevat het volledige bericht het apparaat dat is verzonden met de eigenschap Body in de JSON-indeling.

Voor Blob-opslag worden berichten per minuut in batch verzonden en geëxporteerd. De geëxporteerde bestanden gebruiken dezelfde indeling als de bericht bestanden die worden geëxporteerd door [IOT hub bericht routering](../../iot-hub/tutorial-routing.md) naar Blob Storage.

> [!NOTE]
> Zorg ervoor dat uw apparaten berichten verzenden met `contentType: application/JSON` en `contentEncoding:utf-8` (of) voor Blob Storage `utf-16` `utf-32` . Raadpleeg de [documentatie van IOT hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) voor een voor beeld.

Het apparaat dat de telemetrie heeft verzonden, wordt vertegenwoordigd door de apparaat-ID (Zie de volgende secties). Als u de namen van de apparaten wilt ophalen, gegevens van apparaten wilt exporteren en elk bericht wilt correleren met behulp van de **connectionDeviceId** die overeenkomt met de **deviceId** van het apparaat-bericht.

In het volgende voor beeld wordt een bericht weer gegeven dat is ontvangen van een Event Hub of Service Bus wachtrij of onderwerp:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Dit bericht bevat niet de apparaat-ID van het verzendende apparaat.

Als u de apparaat-ID uit de bericht gegevens in een Azure Stream Analytics query wilt ophalen, gebruikt u de functie [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) . Zie de query in [Azure IOT Central uitbreiden met aangepaste regels met behulp van stream Analytics, Azure functions en SendGrid](./howto-create-custom-rules.md)voor een voor beeld.

Gebruik [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)om de apparaat-id op te halen in een Azure Databricks of Apache Spark werk ruimte. Zie de Databricks-werk ruimte in [Azure IOT Central uitbreiden met aangepaste analyses met behulp van Azure Databricks](./howto-create-custom-analytics.md)voor een voor beeld.

In het volgende voor beeld ziet u een record die is geëxporteerd naar Blob Storage:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Apparaten

Elk bericht of record in een moment opname vertegenwoordigt een of meer wijzigingen aan een apparaat en het apparaat en de Cloud eigenschappen sinds het laatste geëxporteerde bericht. Het bericht bevat het volgende:

- `id` van het apparaat in IoT Central
- `displayName` van het apparaat
- ID van de Device-sjabloon in `instanceOf`
- `simulated` vlag, True als het apparaat een gesimuleerd apparaat is
- `provisioned` vlag, True als het apparaat is ingericht
- `approved` vlag, True als het apparaat is goedgekeurd voor het verzenden van gegevens
- Eigenschaps waarden
- `properties` waarden van het apparaat en de Cloud eigenschappen opnemen

Verwijderde apparaten worden niet geëxporteerd. Er zijn momenteel geen indica toren voor het exporteren van berichten voor verwijderde apparaten.

Voor Event Hubs en Service Bus verzendt IoT Central berichten met apparaatgegevens naar uw Event Hub-of Service Bus-wachtrij of-onderwerp in bijna realtime.

Voor Blob Storage wordt een nieuwe moment opname met alle wijzigingen sinds de laatste geschreven, eenmaal per minuut geëxporteerd.

In het volgende voorbeeld bericht ziet u informatie over apparaten en eigenschappen van gegevens in een Event Hub of Service Bus wachtrij of onderwerp:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Deze moment opname is een voorbeeld bericht waarin de gegevens van apparaten en eigenschappen in Blob Storage worden weer gegeven. Geëxporteerde bestanden bevatten één regel per record.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Apparaatsjablonen

Elk bericht of momentopname record vertegenwoordigt een of meer wijzigingen aan een sjabloon van een gepubliceerd apparaat sinds het laatste geëxporteerde bericht. De informatie die in elk bericht of record wordt verzonden, omvat:

- `id` van de apparaatprofiel die overeenkomt met de `instanceOf` van de bovenstaande apparaten-stream
- `displayName` van de sjabloon voor het apparaat
- Het apparaat `capabilityModel` met inbegrip van de definities van de `interfaces` telemetrie, eigenschappen en opdrachten
- `cloudProperties` definities
- Onderdrukkingen en initiële waarden, inline met de `capabilityModel`

Verwijderde Apparaatinstellingen worden niet geëxporteerd. Er zijn momenteel geen indica toren in geëxporteerde berichten voor verwijderde Apparaatinstellingen.

Voor Event Hubs en Service Bus verzendt IoT Central berichten met gegevens van een apparaataccount naar uw Event Hub-of Service Bus-wachtrij of-onderwerp in bijna realtime.

Voor Blob Storage wordt een nieuwe moment opname met alle wijzigingen sinds de laatste geschreven, eenmaal per minuut geëxporteerd.

In dit voor beeld wordt een bericht weer gegeven over device-sjabloon gegevens in Event Hub of Service Bus wachtrij of onderwerp:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

In dit voor beeld wordt een bericht weer gegeven met apparaat-en eigenschappen gegevens in Blob Storage. Geëxporteerde bestanden bevatten één regel per record.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Wijzigings waarschuwing voor gegevens indeling

> [!Note]
> De gegevens indeling van de telemetrie-stroom wordt niet beïnvloed door deze wijziging. Alleen de apparaten en apparaatnamen van de gegevens stromen worden beïnvloed.

Als u een bestaande gegevens export in uw preview-toepassing hebt met de *apparaten* en de streams voor *apparaat-sjablonen* ingeschakeld, werkt u de export op **30 juni 2020** bij. Deze vereiste geldt voor export naar Azure Blob Storage, Azure Event Hubs en Azure Service Bus.

Vanaf 3 februari 2020 heeft alle nieuwe exports in toepassingen met apparaten en apparaatinstellingen ingeschakeld de hierboven beschreven gegevens indeling. Alle exports die vóór deze datum zijn gemaakt, blijven op de oude gegevens indeling tot en met 30 juni 2020, op het moment dat deze exports automatisch worden gemigreerd naar de nieuwe gegevens indeling. De nieuwe gegevens indeling komt overeen met het [apparaat](/rest/api/iotcentral/devices/get), de [apparaat-eigenschap](/rest/api/iotcentral/devices/getproperties), de eigenschap van de [apparaat-Cloud](/rest/api/iotcentral/devices/getcloudproperties)en de object [sjabloon](/rest/api/iotcentral/devicetemplates/get) objecten in de IOT Central open bare API.

Voor **apparaten** zijn er belang rijke verschillen tussen de oude gegevens indeling en de nieuwe gegevens indeling:
- `@id` voor het apparaat wordt verwijderd, `deviceId` wordt de naam gewijzigd in `id` 
- `provisioned` markering is toegevoegd om de inrichtings status van het apparaat te beschrijven
- `approved` markering is toegevoegd om de goedkeurings status van het apparaat te beschrijven
- `properties` inclusief apparaat-en Cloud eigenschappen, komt overeen met entiteiten in de open bare API

Voor **apparaatprofielen** zijn de verschillen tussen de oude gegevens indeling en de nieuwe gegevens indeling als volgt:

- `@id` de naam van de sjabloon voor het apparaat is gewijzigd in `id`
- `@type` de naam van de apparaatprofiel wordt gewijzigd in `types` en is nu een matrix

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Apparaten (indeling afgeschaft op 3 februari 2020)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Apparaatbeheer (indeling afgeschaft op 3 februari 2020)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u uw gegevens exporteert naar Azure Event Hubs, Azure Service Bus en Azure Blob-opslag, gaat u verder met de volgende stap:

> [!div class="nextstepaction"]
> [Aangepaste analyses uitvoeren met Databricks](./howto-create-custom-analytics.md)