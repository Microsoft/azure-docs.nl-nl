---
title: Azure IoT-Apparaatbeheer met IoT-extensie voor Azure CLI | Microsoft Docs
description: Gebruik de IoT-extensie voor Azure CLI-hulp programma voor Azure IoT Hub Apparaatbeheer, met de methoden direct en de gewenste eigenschappen beheer opties.
author: chrissie926
manager: ''
keywords: Apparaatbeheer van Azure IOT, Azure IOT hub-Apparaatbeheer, beheer van IOT-apparaten in IOT hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 499c0c065cb38313c98ed9412ab1f08d70dbc654
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199523"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>De IoT-extensie voor Azure CLI gebruiken voor Azure IoT Hub Apparaatbeheer

![End-to-end-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[De IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) is een open-source IOT-extensie die wordt toegevoegd aan de mogelijkheden van de [Azure cli](/cli/azure/overview). De Azure CLI bevat opdrachten voor interactie met Azure Resource Manager-en beheer eindpunten. U kunt bijvoorbeeld Azure CLI gebruiken om een Azure VM of een IoT-hub te maken. Een CLI-uitbrei ding stelt een Azure-service in staat om de Azure CLI te verbeteren, zodat u toegang hebt tot aanvullende servicespecifieke mogelijkheden. De IoT-extensie biedt IoT-ontwikkel aars opdracht regel toegang tot alle IoT Hub, IoT Edge en IoT Hub Device Provisioning Service mogelijkheden.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Beheer optie          | Taak  |
|----------------------------|-----------|
| Directe methoden             | Zorg ervoor dat een apparaat functioneert, zoals het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Dubbele gewenste eigenschappen    | Plaats een apparaat in bepaalde staten, zoals het instellen van een LED op groen of het instellen van het verzend interval van de telemetrie naar 30 minuten.         |
| Dubbele gerapporteerde eigenschappen   | De gerapporteerde status van een apparaat ophalen. Zo rapporteert het apparaat dat de LED nu knippert.                                    |
| Dubbele Tags                  | Sla apparaatspecifieke meta gegevens op in de Cloud. Bijvoorbeeld de locatie van de implementatie van een computer.                         |
| Dubbele query's van het apparaat        | Voer een query uit op alle apparaatdubbels om deze apparaatdubbels met wille keurige voor waarden op te halen, zoals het identificeren van de apparaten die beschikbaar zijn voor gebruik. |

Zie voor meer gedetailleerde informatie over de verschillen en richt lijnen over het gebruik van deze opties [apparaat-naar-Cloud communicatie richtlijnen](iot-hub-devguide-d2c-guidance.md) en [Cloud-naar-apparaat-communicatie richtlijnen](iot-hub-devguide-c2d-guidance.md).

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat (metagegevens, configuraties en voorwaarden) zijn opgeslagen. IoT Hub persistent voor elk apparaat dat verbinding maakt met het apparaat. Zie [aan de slag met apparaat apparaatdubbels](iot-hub-node-node-twin-getstarted.md)voor meer informatie over apparaatdubbels.

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u de IoT-extensie voor Azure CLI gebruikt met verschillende beheer opties op uw ontwikkel computer.

## <a name="what-you-do"></a>Wat u doet

Voer Azure CLI en de IoT-extensie voor Azure CLI uit met verschillende beheer opties.

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de zelf studie [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelf studies van het apparaat. bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze items gelden voor de volgende vereisten:

  - Een actief Azure-abonnement.
  - Een Azure IoT hub onder uw abonnement.
  - Een client toepassing die berichten verzendt naar uw Azure IoT hub.

* Zorg ervoor dat uw apparaat wordt uitgevoerd met de client toepassing tijdens deze zelf studie.

* [Python 2.7 x of python 3. x](https://www.python.org/downloads/)

* De Azure CLI. Als u de app wilt installeren, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli). Uw Azure CLI-versie moet mini maal 2.0.70 of hoger zijn. Gebruik `az –version` om de versie te valideren.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Installeer de IoT-extensie. De eenvoudigste manier is `az extension add --name azure-iot` uit te voeren. [In het Leesmij-bestand bij de IoT-extensie](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) worden verschillende manieren voor het installeren van de extensie beschreven.

## <a name="sign-in-to-your-azure-account"></a>Aanmelden bij uw Azure-account

Meld u aan bij uw Azure-account door de volgende opdracht uit te voeren:

```azurecli
az login
```

## <a name="direct-methods"></a>Directe methoden

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Dubbele gewenste eigenschappen van apparaat

Stel een gewenste eigenschaps interval = 3000 in door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Deze eigenschap kan worden gelezen vanaf uw apparaat.

## <a name="device-twin-reported-properties"></a>Dubbele gerapporteerde eigenschappen van het apparaat

De gerapporteerde eigenschappen van het apparaat ophalen door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Een van de dubbele gerapporteerde eigenschappen is $metadata. $lastUpdated, waarin de laatst ingestelde eigenschaps van de apparaat-app wordt weer gegeven.

## <a name="device-twin-tags"></a>Dubbele Tags voor apparaten

Geef de tags en eigenschappen van het apparaat weer door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Voeg een veld Role = Tempe ratuur&vochtigheid toe aan het apparaat door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Dubbele query's van het apparaat

Query's uitvoeren op apparaten met een tag van Role = ' Tempe ratuur&vochtigheid ' door de volgende opdracht uit te voeren:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Query's uitvoeren op alle apparaten behalve die met een tag van Role = ' Tempe ratuur&vochtigheid ' door de volgende opdracht uit te voeren:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u apparaat-naar-Cloud-berichten kunt bewaken en Cloud-naar-apparaat-berichten kunt verzenden tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]