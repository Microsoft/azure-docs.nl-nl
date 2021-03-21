---
title: IoT Plug en Play-modellen gebruiken in een oplossing | Microsoft Docs
description: Als oplossings bouwer leert u hoe u IoT Plug en Play-modellen kunt gebruiken in uw IoT-oplossing.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7e0d93d3fd179d672b49967c575bbfb9c0fc5de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213688"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>IoT Plug en Play-modellen gebruiken in een IoT-oplossing

In dit artikel wordt beschreven hoe u in een IoT-oplossing model-ID van een IoT-Plug en Play apparaat identificeert en vervolgens de model definitie ervan ophaalt.

Er zijn twee algemene categorieën van een IoT-oplossing:

- Een *oplossing* die speciaal is gebouwd, werkt met een bekende set modellen voor de IOT Plug en Play-apparaten die verbinding maken met de oplossing. U gebruikt deze modellen bij het ontwikkelen van de oplossing.

- Een *model* gerichte oplossing kan worden gebruikt met het model van een IoT-Plug en Play apparaat. Het bouwen van een model gerichte oplossing is complexer, maar het voor deel is dat uw oplossing werkt met apparaten die in de toekomst kunnen worden toegevoegd. Een op modellen gebaseerde IoT-oplossing haalt een model op en gebruikt dit om te bepalen welke telemetrie, eigenschappen en opdrachten het apparaat implementeert.

Voor het gebruik van een IoT Plug en Play model, een IoT-oplossing:

1. Hiermee wordt de model-ID aangegeven van het model dat is geïmplementeerd door de IoT-Plug en Play apparaat, module of IoT Edge module die met de oplossing is verbonden.

1. Maakt gebruik van de model-ID voor het ophalen van de model definitie van het verbonden apparaat vanuit een model opslagplaats of aangepaste opslag.

## <a name="identify-model-id"></a>Model-ID identificeren

Wanneer een IoT Plug en Play-apparaat verbinding maakt met IoT Hub, wordt de model-ID geregistreerd van het model dat wordt geïmplementeerd met IoT Hub.

IoT Hub meldt de oplossing met de apparaat model-ID als onderdeel van de verbindings stroom van het apparaat.

Een oplossing kan de model-ID van de IoT-Plug en Play apparaat verkrijgen door een van de volgende drie methoden te gebruiken:

### <a name="get-device-twin-api"></a>Dubbele API voor apparaat ophalen

De oplossing kan gebruikmaken van de [dubbele API Get-apparaat](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) om de model-id van de IoT-Plug en Play apparaat op te halen.

> [!TIP]
> Gebruik [ModuleClient. getTwin](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin)voor modules en IOT Edge modules.

In het volgende dubbele reactie fragment `modelId` van een apparaat bevat de model-id van een IOT-Plug en Play apparaat:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Digitale dubbele API ophalen

De oplossing kan de ophalen van de [digitale dubbele](/rest/api/iothub/service/digitaltwin/getdigitaltwin) API gebruiken om de model-id op te halen van het model dat is geïmplementeerd door het IOT Plug en Play-apparaat.

In het volgende digitale dubbele-reactie fragment `$metadata.$model` bevat de model-id van een IoT Plug en Play-apparaat:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>Waarschuwing voor digitale dubbele wijzigings gebeurtenis

Een verbinding met een apparaat resulteert in een [digitale dubbele wijzigings gebeurtenis](concepts-digital-twin.md#digital-twin-change-events) melding. Een oplossing moet zich abonneren op deze gebeurtenis melding. Zie [IOT hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie over het inschakelen van route ring voor digitale dubbele gebeurtenissen.

De oplossing kan gebruikmaken van de gebeurtenis die wordt weer gegeven in het volgende code fragment voor meer informatie over het IoT Plug en Play-apparaat dat verbinding maakt en de model-ID krijgt:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>Een model definitie ophalen

Een oplossing maakt gebruik van de model-ID hierboven om de bijbehorende model definitie op te halen.

Een oplossing kan de model definitie ophalen met behulp van een van de volgende opties:

### <a name="model-repository"></a>Modelopslagplaats

Oplossingen kunnen de [model opslagplaats](concepts-model-repository.md) gebruiken om modellen op te halen. De apparaat-Builders of de oplossings bouwers moeten hun modellen vooraf uploaden naar de opslag plaats, zodat de oplossing ze kan ophalen.

Voer de volgende stappen uit nadat u de model-ID voor een nieuwe apparaat-verbinding hebt geïdentificeerd:

1. Haal de model definitie op met behulp van de model-ID uit de model opslagplaats. Zie [device model-opslag plaats](concepts-model-repository.md)voor meer informatie.

1. Met de model definitie van het verbonden apparaat kunt u de mogelijkheden van het apparaat opsommen.

1. Met de genummerde mogelijkheden van het apparaat kunt u gebruikers toestaan om te [communiceren met het apparaat](quickstart-service.md).

### <a name="custom-store"></a>Aangepast archief

Oplossingen kunnen deze model definities opslaan in een lokaal bestands systeem, in een open bare bestands opslag of een aangepaste implementatie gebruiken.

Voer de volgende stappen uit nadat u de model-ID voor een nieuwe apparaat-verbinding hebt geïdentificeerd:

1. Haal de model definitie op met behulp van de model-ID uit uw aangepaste opslag.

1. Met de model definitie van het verbonden apparaat kunt u de mogelijkheden van het apparaat opsommen. 

1. Met de genummerde mogelijkheden van het apparaat kunt u gebruikers toestaan om te [communiceren met het apparaat](quickstart-service.md).  

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u IoT Plug en Play-modellen integreert in een IoT-oplossing, kunt u de volgende stappen volgen:

- [Interactie met een apparaat vanuit uw oplossing](quickstart-service.md)
- [IoT digitale dubbele REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
