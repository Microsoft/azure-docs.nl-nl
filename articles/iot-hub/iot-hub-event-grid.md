---
title: Azure IoT Hub en Event Grid | Microsoft Docs
description: Gebruik Azure Event Grid om processen te activeren op basis van acties die in IoT Hub plaatsvinden.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: 0e0ca8a787145fb40087a2d99be85607404eebfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152129"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reageren op IoT Hub gebeurtenissen met behulp van Event Grid om acties te activeren

Azure IoT Hub kan worden geïntegreerd met Azure Event Grid zodat u gebeurtenismeldingen kunt verzenden naar andere services en downstreamprocessen kunt activeren. Configureer uw zakelijke toepassingen om op IoT Hub-gebeurtenissen te letten zodat u op een betrouwbare, schaalbare en veilige manier op kritieke gebeurtenissen kunt reageren. U kunt bijvoorbeeld een toepassing maken waarmee een database wordt bijgewerkt, een orderticket wordt gemaakt en een e-mailmelding wordt bezorgd wanneer een nieuw IoT-apparaat wordt geregistreerd bij uw IoT-hub.

[Azure Event grid](../event-grid/overview.md) is een volledig beheerde service voor gebeurtenis routering die gebruikmaakt van een model voor publiceren en abonneren. Event Grid heeft ingebouwde ondersteuning voor Azure-Services, zoals [Azure functions](../azure-functions/functions-overview.md) en [Azure Logic apps](../logic-apps/logic-apps-overview.md), en kan gebeurtenis waarschuwingen leveren aan niet-Azure-Services met behulp van webhooks. Zie [Een inleiding tot Azure Event Grid](../event-grid/overview.md) voor een volledige lijst met gebeurtenisverwerkers die Event Grid ondersteunt.

![Azure Event Grid architectuur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionale beschikbaarheid

De Event Grid-integratie is beschikbaar voor IoT-hubs in de regio's waar Event Grid wordt ondersteund. Zie voor de meest recente lijst met regio's [een inleiding tot Azure Event grid](../event-grid/overview.md).

## <a name="event-types"></a>Gebeurtenistypen

IoT Hub publiceert de volgende gebeurtenis typen:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Gepubliceerd wanneer een apparaat wordt geregistreerd bij een IoT-hub. |
| Microsoft.Devices.DeviceDeleted | Gepubliceerd wanneer een apparaat wordt verwijderd uit een IoT-hub. |
| Microsoft.Devices.DeviceConnected | Gepubliceerd wanneer een apparaat is verbonden met een IoT-hub. |
| Microsoft.Devices.DeviceDisconnected | Gepubliceerd wanneer een apparaat wordt losgekoppeld van een IoT-hub. |
| Microsoft.Devices.DeviceTelemetry | Gepubliceerd wanneer een telemetriebericht van een apparaat wordt verzonden naar een IoT-hub |

Gebruik de Azure Portal of de Azure CLI om te configureren welke gebeurtenissen van elke IoT-hub moeten worden gepubliceerd. Voor een voor beeld kunt u de zelf studie [gebruiken om e-mail meldingen over Azure IOT hub-gebeurtenissen te verzenden met Logic apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Gebeurtenisschema

IoT Hub gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in de levens cyclus van uw apparaat. U kunt een IoT Hub-gebeurtenis identificeren door te controleren of de eigenschap eventType begint met **Microsoft.Devices**. Zie het [Event grid-gebeurtenis schema](../event-grid/event-schema.md)voor meer informatie over het gebruik van Event grid gebeurtenis eigenschappen.

### <a name="device-connected-schema"></a>Met apparaat verbonden schema

In het volgende voor beeld ziet u het schema van een gebeurtenis die is verbonden met een apparaat:

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```



### <a name="device-telemetry-schema"></a>Telemetrie-schema van apparaat

Het telemetrie-bericht van een apparaat moet een geldige JSON-indeling hebben waarvoor het content type is ingesteld op **Application/JSON** en contentEncoding ingesteld op **UTF-8** in de eigenschappen van het bericht [systeem](iot-hub-devguide-routing-query-syntax.md#system-properties). Beide eigenschappen zijn niet hoofdletter gevoelig. Als de inhouds codering niet is ingesteld, schrijft IoT Hub de berichten in de indeling basis 64-code ring.

U kunt telemetrie-gebeurtenissen van apparaten verrijken voordat ze worden gepubliceerd naar Event Grid door het eind punt te selecteren als Event Grid. Zie voor meer informatie [overzicht van verrijkingen van berichten](iot-hub-message-enrichments-overview.md).

In het volgende voor beeld ziet u het schema van een telemetrie-gebeurtenis van een apparaat:

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

### <a name="device-created-schema"></a>Apparaat gemaakt schema

In het volgende voor beeld ziet u het schema van een gebeurtenis die is gemaakt met een apparaat:

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
      "deviceEtag":"null",
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


> [!WARNING]
> *Dubbele gegevens* die zijn gekoppeld aan een gebeurtenis voor het maken van een apparaat, zijn een standaard configuratie en *mogen* niet worden vertrouwd op `authenticationType` basis van de werkelijke en andere apparaateigenschappen in een nieuw apparaat. Voor `authenticationType` en andere apparaateigenschappen in een nieuw apparaat gebruikt u de API voor registreren in azure IOT sdk's.

Zie [Azure Event grid-gebeurtenis schema voor IOT hub](../event-grid/event-schema-iot-hub.md)voor een gedetailleerde beschrijving van elke eigenschap.

## <a name="filter-events"></a>Gebeurtenissen filteren

IoT Hub gebeurtenis abonnementen kunnen gebeurtenissen filteren op basis van gebeurtenis type, gegevens inhoud en onderwerp, wat de naam van het apparaat is.

Event Grid kunt [filteren](../event-grid/event-filtering.md) op gebeurtenis typen, onderwerpen en gegevens inhoud. Tijdens het maken van het Event Grid-abonnement kunt u zich abonneren op geselecteerde IoT-gebeurtenissen. De subject filters in Event Grid **werken op basis van (voor** voegsel) en **eindigt** op (achtervoegsel) overeenkomsten. Het filter gebruikt een `AND` operator, waardoor gebeurtenissen met een onderwerp die overeenkomen met het voor voegsel en het achtervoegsel aan de abonnee worden geleverd.

Het onderwerp van IoT-gebeurtenissen maakt gebruik van de volgende indeling:

```json
devices/{deviceId}
```

Met Event Grid kunt u ook kenmerken van elke gebeurtenis filteren, met inbegrip van de gegevens inhoud. Zo kunt u kiezen welke gebeurtenissen op basis van de inhoud van het telemetrie-bericht worden geleverd. Zie [Geavanceerde filters](../event-grid/event-filtering.md#advanced-filtering) om voor beelden te bekijken. Voor het filteren van de hoofd tekst van de telemetrie-berichten moet u het content type instellen op **Application/JSON** en ContentEncoding naar **UTF-8** in de eigenschappen van het bericht [systeem](./iot-hub-devguide-routing-query-syntax.md#system-properties). Beide eigenschappen zijn niet hoofdletter gevoelig.

Voor niet-telemetrie-gebeurtenissen zoals DeviceConnected, DeviceDisconnected, DeviceCreated en DeviceDeleted kan de Event Grid-filtering worden gebruikt bij het maken van het abonnement. Voor telemetrie-gebeurtenissen kunnen gebruikers naast het filteren in Event Grid ook filteren op apparaatdubbels, bericht eigenschappen en hoofd tekst via de routerings query voor berichten. 

Wanneer u zich abonneert op telemetrie-gebeurtenissen via Event Grid, maakt IoT Hub een standaard bericht route voor het verzenden van het gegevens bron type van de apparaat berichten naar Event Grid. Zie [IOT hub Message Routing](iot-hub-devguide-messages-d2c.md)(Engelstalig) voor meer informatie over het routeren van berichten. Deze route wordt weer gegeven in de portal onder IoT Hub > bericht routering. Er wordt slechts één route naar Event Grid gemaakt, ongeacht het aantal voor beeld-abonnementen dat voor telemetrie-gebeurtenissen is gemaakt. Als u bijvoorbeeld verschillende abonnementen met verschillende filters nodig hebt, kunt u de operator OR gebruiken in deze query's op dezelfde route. Het maken en verwijderen van de route wordt beheerd via het abonnement op telemetrie-gebeurtenissen via Event Grid. U kunt geen route naar Event Grid maken of verwijderen met behulp van IoT Hub-bericht routering.

Als u berichten wilt filteren voordat telemetriegegevens worden verzonden, kunt u uw [routerings query](iot-hub-devguide-routing-query-syntax.md)bijwerken. De routerings query kan alleen worden toegepast op de bericht tekst als de hoofd tekst JSON is. U moet ook het content type instellen op **Application/JSON** en ContentEncoding naar **UTF-8** in de [Eigenschappen](./iot-hub-devguide-routing-query-syntax.md#system-properties)van het bericht systeem.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Beperkingen voor gebeurtenissen bij verbinden van apparaat en verbreken van verbinding van apparaat

Als u gebeurtenissen wilt ontvangen voor de verbindings status van een apparaat, moet een apparaat een "C2D"-telemetrie verzenden of een ' bewerking voor het ontvangen van een bericht ' met IOT hub. Houd er echter rekening mee dat als een apparaat gebruikmaakt van het AMQP-protocol om verbinding te maken met IOT hub, u wordt aangeraden de bewerking ' C2D receive Message ' uit te voeren, anders worden de meldingen van de verbindings status mogelijk met enkele minuten uitgesteld. Als uw apparaat gebruikmaakt van het MQTT-protocol, blijft de C2D IoT Hub-koppeling geopend. Voor AMQP kunt u de koppeling C2D openen door de interface voor het ontvangen van async voor IoT Hub C# SDK of de [apparaatclient voor AMQP](iot-hub-amqp-support.md#device-client)aan te roepen.

De koppeling D2C is open als u telemetrie verzendt. 

Als de verbinding met het apparaat Flik keert, wat betekent dat het apparaat regel matig verbinding maakt en de verbinding verbreekt, worden er niet elke verbindings status verzonden, maar wordt de huidige verbindings status gepubliceerd die is gemaakt op een periodieke moment opname, waarna de Flik kering wordt vervolgd. Als u dezelfde verbindings status gebeurtenis met andere Volg nummers of verschillende verbindings status gebeurtenissen ontvangt, betekent dit dat er een wijziging is opgetreden in de verbindings status van het apparaat.

## <a name="tips-for-consuming-events"></a>Tips voor het gebruik van gebeurtenissen

Toepassingen die IoT Hub-gebeurtenissen verwerken, moeten de volgende aanbevolen procedures volgen:

* Meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, dus ga er niet van uit dat gebeurtenissen van een bepaalde bron afkomstig zijn. Controleer altijd het onderwerp van het bericht om er zeker van te zijn dat het afkomstig is van de IoT-hub die u verwacht.

* Ga er niet van uit dat alle gebeurtenissen die u ontvangt, de typen zijn die u verwacht. Controleer altijd het gebeurtenis type voordat het bericht wordt verwerkt.

* Berichten arriveren soms in de verkeerde volgorde of na een vertraging. Gebruik het veld ETAG om te begrijpen of uw informatie over objecten up-to-date is voor het apparaat dat is gemaakt of door een apparaat verwijderde gebeurtenissen.

## <a name="next-steps"></a>Volgende stappen

* [De zelf studie voor de IoT Hub-gebeurtenissen proberen](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Informatie over het rangschikken van gebeurtenissen ‘apparaat verbonden’ en ‘verbinding met apparaat verbroken’.](iot-hub-how-to-order-connection-state-events.md)

* [Meer informatie over Event Grid](../event-grid/overview.md)

* [De verschillen tussen de route ring IoT Hub gebeurtenissen en berichten vergelijken](iot-hub-event-grid-routing-comparison.md)

* [Meer informatie over het gebruik van IoT-telemetrie-gebeurtenissen voor het implementeren van IoT ruimtelijke analyse met behulp van Azure Maps](../azure-maps/tutorial-iot-hub-maps.md)