---
title: Azure IoT Hub als Event Grid bron
description: In dit artikel vindt u de eigenschappen en het schema voor Azure IoT Hub-gebeurtenissen. Hiermee worden de beschik bare gebeurtenis typen, een voorbeeld gebeurtenis en gebeurtenis eigenschappen weer gegeven.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 7e1c480bd2a662a2ee3418b35dc9c3b50d412a60
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185832"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Azure IoT Hub als Event Grid bron
In dit artikel vindt u de eigenschappen en het schema voor Azure IoT Hub-gebeurtenissen. Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's. 

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

### <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Azure IoT Hub verzendt de volgende gebeurtenis typen:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Gepubliceerd wanneer een apparaat wordt geregistreerd bij een IoT-hub. |
| Microsoft.Devices.DeviceDeleted | Gepubliceerd wanneer een apparaat wordt verwijderd uit een IoT-hub. | 
| Microsoft.Devices.DeviceConnected | Gepubliceerd wanneer een apparaat is verbonden met een IoT-hub. |
| Microsoft.Devices.DeviceDisconnected | Gepubliceerd wanneer een apparaat wordt losgekoppeld van een IoT-hub. | 
| Microsoft.Devices.DeviceTelemetry | Gepubliceerd wanneer een telemetrie-bericht wordt verzonden naar een IoT-hub. |

### <a name="example-event"></a>Voorbeeld gebeurtenis

Het schema voor DeviceConnected-en DeviceDisconnected-gebeurtenissen heeft dezelfde structuur. Deze voorbeeld gebeurtenis toont het schema van een gebeurtenis die optreedt wanneer een apparaat is verbonden met een IoT-hub:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

De gebeurtenis DeviceTelemetry wordt geactiveerd wanneer een telemetrie-gebeurtenis wordt verzonden naar een IoT Hub. Hieronder ziet u een voor beeld van een schema voor deze gebeurtenis.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Het schema voor DeviceCreated-en DeviceDeleted-gebeurtenissen heeft dezelfde structuur. Deze voorbeeld gebeurtenis toont het schema van een gebeurtenis die optreedt wanneer een apparaat wordt geregistreerd bij een IoT-hub:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Gebeurtenis eigenschappen

Alle gebeurtenissen bevatten dezelfde gegevens op het hoogste niveau: 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| Type | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| gegevens | object | IoT Hub gebeurtenis gegevens.  |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Voor alle IoT Hub gebeurtenissen bevat het gegevens object de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| hubName | tekenreeks | De naam van de IoT Hub waarop het apparaat is gemaakt of verwijderd. |
| deviceId | tekenreeks | De unieke id van het apparaat. Deze hoofdletter gevoelige teken reeks kan Maxi maal 128 tekens lang zijn, en ondersteunt ASCII 7-bits alfanumerieke tekens plus de volgende speciale tekens: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |

De inhoud van het gegevens object verschilt voor elke uitgever van de gebeurtenis. 

Het gegevens object bevat de volgende eigenschappen voor **apparaten die zijn verbonden met** het apparaat en de verbinding met het **apparaat** IOT hub gebeurtenissen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| moduleId | tekenreeks | De unieke id van de module. Dit veld wordt alleen uitgevoerd voor module apparaten. Deze hoofdletter gevoelige teken reeks kan Maxi maal 128 tekens lang zijn, en ondersteunt ASCII 7-bits alfanumerieke tekens plus de volgende speciale tekens: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |
| deviceConnectionStateEventInfo | object | Informatie over de gebeurtenis van de verbindings status van het apparaat
| sequenceNumber | tekenreeks | Een getal waarmee u de volg orde kunt bepalen van gebeurtenissen die zijn verbonden met het apparaat of de verbinding met het apparaat. De meest recente gebeurtenis heeft een Volg nummer dat hoger is dan de vorige gebeurtenis. Dit aantal kan worden gewijzigd door meer dan 1, maar is strikt toeneemt. Zie een [Volg nummer gebruiken](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Voor de **telemetrie** van het apparaat IOT hub gebeurtenis bevat het gegevens object het apparaat-naar-Cloud bericht in de [IOT hub-bericht indeling](../iot-hub/iot-hub-devguide-messages-construct.md) en heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| body | tekenreeks | De inhoud van het bericht van het apparaat. |
| properties | tekenreeks | Toepassings eigenschappen zijn door de gebruiker gedefinieerde teken reeksen die aan het bericht kunnen worden toegevoegd. Deze velden zijn optioneel. |
| systeem eigenschappen | tekenreeks | [Systeem eigenschappen](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) helpen de inhoud en bron van de berichten te identificeren. Het telemetrie-bericht van een apparaat moet een geldige JSON-indeling hebben waarvoor het content type is ingesteld op JSON en contentEncoding is ingesteld op UTF-8 in de eigenschappen van het bericht systeem. Als deze niet is ingesteld, worden de berichten in IoT Hub geschreven in de indeling basis 64-code ring.  |

Het gegevens object van het apparaat dat is **gemaakt** en het **apparaat is verwijderd** IOT hub gebeurtenissen bevat de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| dubbel | object | Informatie over het apparaat, namelijk de Cloud representatie van meta gegevens van toepassings apparaten. | 
| deviceID | tekenreeks | De unieke id van het apparaat dubbele. | 
| ETAG | tekenreeks | Een validator om consistentie van updates op een apparaat te garanderen. Elke ETAG is gegarandeerd uniek per apparaat-twee. |  
| deviceEtag| tekenreeks | Een validator voor het garanderen van consistentie van updates voor een apparaat-REGI ster. Elke deviceEtag is gegarandeerd uniek volgens het REGI ster van het apparaat. |
| status | tekenreeks | Hiermee wordt aangegeven of het dubbele apparaat is ingeschakeld of uitgeschakeld. | 
| statusUpdateTime | tekenreeks | De ISO8601-tijds tempel van de laatste dubbele status update voor het apparaat. |
| connectionState | tekenreeks | Hiermee wordt aangegeven of het apparaat is verbonden of verbroken. | 
| lastActivityTime | tekenreeks | De ISO8601-tijds tempel van de laatste activiteit. | 
| cloudToDeviceMessageCount | geheel getal | Aantal berichten van de Cloud naar het apparaat dat naar dit apparaat wordt verzonden. | 
| authenticationType | tekenreeks | Verificatie type dat wordt gebruikt voor dit apparaat: ofwel `SAS` , `SelfSigned` of `CertificateAuthority` . |
| x509Thumbprint | tekenreeks | De vinger afdruk is een unieke waarde voor het x509-certificaat, die vaak wordt gebruikt om een bepaald certificaat te vinden in een certificaat archief. De vinger afdruk wordt dynamisch gegenereerd met het SHA1-algoritme en bestaat niet fysiek in het certificaat. | 
| primaryThumbprint | tekenreeks | Primaire vinger afdruk voor het x509-certificaat. |
| secondaryThumbprint | tekenreeks | Secundaire vinger afdruk voor het x509-certificaat. | 
| versie | geheel getal | Een geheel getal dat elke keer dat het apparaat wordt bijgewerkt, wordt verhoogd. |
| locatie | object | Een gedeelte van de eigenschappen dat alleen kan worden geschreven door de back-end van de toepassing en door het apparaat kan worden gelezen. | 
| CDE | object | Een deel van de eigenschappen dat alleen door het apparaat kan worden geschreven en door de back-end van de toepassing kan worden gelezen. |
| lastUpdated | tekenreeks | De ISO8601-tijds tempel van de laatste eigenschaps update voor het apparaat. | 

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel  |Beschrijving  |
|---------|---------|
| [E-mailmeldingen over gebeurtenissen van Azure IoT Hub verzenden met Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Een logische app verzendt een e-mail melding wanneer een apparaat wordt toegevoegd aan uw IoT Hub. |
| [Reageren op IoT Hub gebeurtenissen met behulp van Event Grid om acties te activeren](../iot-hub/iot-hub-event-grid.md) | Overzicht van het integreren van IoT Hub met Event Grid. |
| [De verbinding met het apparaat is verbonden en de verbroken gebeurtenissen van het apparaat](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Laat zien hoe u gebeurtenissen van de verbindings status van apparaten kunt ordenen. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](overview.md) voor een inleiding tot Azure Event Grid.
* Zie [reageren op IOT hub gebeurtenissen met Event grid om acties te activeren](../iot-hub/iot-hub-event-grid.md)voor meer informatie over de samen werking tussen IoT Hub en Event grid.