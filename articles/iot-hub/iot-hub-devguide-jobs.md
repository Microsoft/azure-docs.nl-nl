---
title: Meer informatie over Azure IoT Hub Jobs | Microsoft Docs
description: 'Ontwikkelaars handleiding: taken plannen die moeten worden uitgevoerd op meerdere apparaten die zijn verbonden met uw IoT-hub. Taken kunnen Tags en gewenste eigenschappen bijwerken en directe methoden aanroepen op meerdere apparaten.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81730106"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Taken op meerdere apparaten plannen

Azure IoT Hub maakt een aantal bouw stenen mogelijk, zoals [dubbele eigenschappen van het apparaat en Tags](iot-hub-devguide-device-twins.md) en [directe methoden](iot-hub-devguide-direct-methods.md). Meestal kunnen back-end-apps apparaat beheerders en-Opera tors gebruiken om IoT-apparaten bulksgewijs en op een gepland tijdstip bij te werken en te communiceren. Met taken worden dubbele updates en directe methoden voor apparaten uitgevoerd op een geplande periode. Een operator zou bijvoorbeeld een back-end-app gebruiken waarmee een taak wordt gestart en bijgehouden voor het opnieuw opstarten van een set apparaten in Build 43 en Floor 3 op een moment dat de bewerkingen van het gebouw niet worden onderbroken.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Overweeg het gebruik van taken wanneer u de voortgang van een van de volgende activiteiten op een set apparaten wilt plannen en bijhouden:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

## <a name="job-lifecycle"></a>Taak levenscyclus

Taken worden geïnitieerd door de back-end van de oplossing en worden onderhouden door IoT Hub. U kunt een taak initiëren via een service gerichte URI ( `PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30` ) en een query uitvoeren op een uitvoering van een taak via een service gerichte URI ( `GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30` ). Als u de status van actieve taken wilt vernieuwen nadat een taak is gestart, voert u een taak query uit.

> [!NOTE]
> Wanneer u een taak initieert, kunnen eigenschaps namen en-waarden alleen in de volgende gevallen een door ons ASCII afdruk bare alfanumerieke waarde bevatten: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Taken voor het uitvoeren van directe methoden

Het volgende code fragment toont de HTTPS 1,1-aanvraag gegevens voor het uitvoeren van een [directe methode](iot-hub-devguide-direct-methods.md) op een set apparaten met behulp van een taak:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

De query voorwaarde kan zich ook op één apparaat-ID of op een lijst met apparaat-Id's bevindt, zoals wordt weer gegeven in de volgende voor beelden:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IOT hub query taal](iot-hub-devguide-query-language.md) bevat IOT hub query taal in aanvullende details.

Het volgende code fragment toont de aanvraag en het antwoord op een geplande taak voor het aanroepen van een directe methode met de naam testMethod op alle apparaten op contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Taken voor het bijwerken van dubbele eigenschappen van het apparaat

Het volgende code fragment toont de HTTPS 1,1-aanvraag Details voor het bijwerken van dubbele eigenschappen van apparaten met behulp van een taak:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> De eigenschap *updateTwin* vereist een geldige ETAG-overeenkomst; bijvoorbeeld `etag="*"` .

Het volgende code fragment toont de aanvraag en het antwoord op een geplande taak voor het bijwerken van de dubbele eigenschappen van het apparaat voor een test apparaat op contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Query's uitvoeren voor de voortgang van taken

In het volgende code fragment worden de HTTPS 1,1-aanvraag Details voor het uitvoeren van query's voor taken weer gegeven:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

De continuationToken is afkomstig van het antwoord.

U kunt een query uitvoeren voor de status van de taak uitvoering op elk apparaat met behulp van de [IOT hub query taal voor apparaatdubbels, Jobs en bericht routering](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Eigenschappen van taken

De volgende lijst bevat de eigenschappen en bijbehorende beschrijvingen die kunnen worden gebruikt bij het uitvoeren van query's op taken of taak resultaten.

| Eigenschap | Beschrijving |
| --- | --- |
| **jobId** |De door de toepassing meegeleverde ID voor de taak. |
| **startTime** |De door de toepassing aangelegde start tijd (ISO-8601) voor de taak. |
| **Tijd** |IoT Hub gegeven datum (ISO-8601) voor wanneer de taak is voltooid. Alleen geldig nadat de taak de status voltooid heeft bereikt. |
| **type** |Typen taken: |
| | **scheduleUpdateTwin**: een taak die wordt gebruikt om een set gewenste eigenschappen of labels bij te werken. |
| | **scheduleDeviceMethod**: een taak die wordt gebruikt om een apparaat methode aan te roepen voor een set apparaat apparaatdubbels. |
| **status** |Huidige status van de taak. Mogelijke waarden voor status: |
| | **in behandeling**: gepland en wachten om te worden opgehaald door de taak service. |
| | **gepland**: in de toekomst gepland voor een tijd. |
| | **uitvoeren**: huidige actieve taak. |
| | **geannuleerd**: de taak is geannuleerd. |
| | **mislukt**: taak mislukt. |
| | **voltooid**: de taak is voltooid. |
| **deviceJobStatistics** |Statistieken over de uitvoering van de taak. |
| | **deviceJobStatistics** eigenschappen: |
| | **deviceJobStatistics. deviceCount**: aantal apparaten in de taak. |
| | **deviceJobStatistics. failedCount**: aantal apparaten waarop de taak is mislukt. |
| | **deviceJobStatistics. succeededCount**: het aantal apparaten waarop de taak is voltooid. |
| | **deviceJobStatistics. runningCount**: het aantal apparaten dat op dit moment de taak uitvoert. |
| | **deviceJobStatistics. pendingCount**: het aantal apparaten dat in behandeling is om de taak uit te voeren. |

### <a name="additional-reference-material"></a>Extra referentie materiaal

Andere naslag onderwerpen in de IoT Hub ontwikkelaars handleiding zijn:

* [IOT hub-eind punten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eind punten die elke IOT-hub beschikbaar maakt voor runtime-en beheer bewerkingen.

* Met [beperking en quota's](iot-hub-devguide-quotas-throttling.md) worden de quota's beschreven die van toepassing zijn op de IOT hub-service en het beperkings gedrag dat u kunt verwachten wanneer u de service gebruikt.

* Met de [sdk's van Azure IOT-apparaat en-service](iot-hub-devguide-sdks.md) worden de diverse sdk's voor de taal weer gegeven die u kunt gebruiken bij het ontwikkelen van zowel apparaat-als service-apps die communiceren met IOT hub.

* [IOT hub query taal voor apparaatdubbels, taken en bericht routering](iot-hub-devguide-query-language.md) beschrijft de IOT hub query taal. Gebruik deze query taal om informatie op te halen van IoT Hub over de apparaatdubbels en taken van uw apparaat.

* [IOT hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IOT hub ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende IoT Hub zelf studie als u een aantal van de concepten wilt uitproberen die in dit artikel worden beschreven:

* [Taken plannen en uitzenden](iot-hub-node-node-schedule-jobs.md)