---
title: Azure IoT-apparaatbeheer met IoT-extensie voor Azure CLI | Microsoft Docs
description: Gebruik de IoT-extensie voor het Azure CLI-hulpprogramma voor Azure IoT Hub-apparaatbeheer, met de directe methoden en de beheeropties voor de gewenste eigenschappen van de dubbel.
author: chrissie926
manager: ''
keywords: azure iot device management, azure iot hub device management, device management iot, iot hub device management
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: a5bc7e195efd62f430fdf2aa0cb606dbcff79528
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567193"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>De IoT-extensie voor Azure CLI gebruiken voor Azure IoT Hub apparaatbeheer

![End-to-end-diagram](media/iot-hub-get-started-e2e-diagram/2.png)

In dit artikel leert u hoe u de IoT-extensie voor Azure CLI gebruikt met verschillende beheeropties op uw ontwikkelmachine. [De IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) is een opensource-IoT-extensie die wordt toegevoegd aan de mogelijkheden van [de Azure CLI.](/cli/azure/overview) De Azure CLI bevat opdrachten voor interactie met Azure Resource Manager en beheer-eindpunten. U kunt bijvoorbeeld Azure CLI gebruiken om een Azure-VM of een IoT-hub te maken. Met een CLI-extensie kan een Azure-service de Azure CLI uitbreiden, zodat u toegang hebt tot aanvullende servicespecifieke mogelijkheden. De IoT-extensie biedt IoT-ontwikkelaars opdrachtregeltoegang tot alle IoT Hub, IoT Edge en IoT Hub Device Provisioning Service mogelijkheden.

| Beheeroptie          | Taak  |
|----------------------------|-----------|
| Directe methoden             | Laat een apparaat fungeren als het starten of stoppen van het verzenden van berichten of het opnieuw opstarten van het apparaat.                                        |
| Gewenste dubbeleigenschappen    | Zet een apparaat in bepaalde staat, zoals het instellen van een LED op groen of het instellen van het telemetrie-verzendinterval op 30 minuten.         |
| Gerapporteerde dubbeleigenschappen   | Haal de gerapporteerde status van een apparaat op. Het apparaat meldt bijvoorbeeld dat de LED nu gaat knipperen.                                    |
| Tweelingtags                  | Apparaatspecifieke metagegevens opslaan in de cloud. Bijvoorbeeld de implementatielocatie van een verkoopautomaat.                         |
| Query's voor apparaattwee        | Query's uitvoeren op alle apparaat twins om deze tweelingen met willekeurige voorwaarden op te halen, zoals het identificeren van de apparaten die beschikbaar zijn voor gebruik. |

Zie Richtlijnen voor [apparaat-naar-cloudcommunicatie](iot-hub-devguide-d2c-guidance.md) en Richtlijnen voor communicatie van cloud naar apparaat voor gedetailleerde uitleg over de verschillen en richtlijnen [voor het gebruik van deze opties.](iot-hub-devguide-c2d-guidance.md)

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat (metagegevens, configuraties en voorwaarden) zijn opgeslagen. IoT Hub houdt een apparaat dubbel aan voor elk apparaat dat er verbinding mee maakt. Zie Aan de slag met apparaattweeling voor meer informatie [over apparaattweeling.](iot-hub-node-node-twin-getstarted.md)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Vereisten

* Voltooi de [zelfstudie Raspberry Pi Online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelfstudies over apparaten. U kunt bijvoorbeeld naar [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) gaan met node.jsof naar een van de quickstarts [Telemetrie](quickstart-send-telemetry-dotnet.md) verzenden. Deze artikelen hebben betrekking op de volgende vereisten:

  * Een actief Azure-abonnement.
  * Een Azure IoT-hub onder uw abonnement.
  * Een clienttoepassing die berichten naar uw Azure IoT-hub verzendt.

* Zorg ervoor dat uw apparaat wordt uitgevoerd met de clienttoepassing tijdens deze zelfstudie.

* [Python 2.7x of Python 3.x](https://www.python.org/downloads/)

* De Azure CLI. Zie Azure CLI installeren als u [deze wilt installeren.](/cli/azure/install-azure-cli) Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az –version` om de versie te valideren.

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

## <a name="device-twin-desired-properties"></a>Gewenste eigenschappen van apparaat dubbel

Stel een gewenste eigenschapsinterval in = 3000 door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Deze eigenschap kan van uw apparaat worden gelezen.

## <a name="device-twin-reported-properties"></a>Gerapporteerde eigenschappen van apparaat dubbel

Haal de gerapporteerde eigenschappen van het apparaat op door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Een van de gerapporteerde eigenschappen van de dubbel is $metadata.$lastUpdated, waarin wordt vermeld wanneer de gerapporteerde eigenschappenset voor de apparaat-app voor het laatst is bijgewerkt.

## <a name="device-twin-tags"></a>Tags van apparaattwee

Geef de tags en eigenschappen van het apparaat weer door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Voeg een veldrol = temperature&humidity toe aan het apparaat door de volgende opdracht uit te voeren:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Query's voor apparaattwee

Voer de volgende opdracht uit om een query uit te voeren op apparaten met de tag role = 'temperature&humidity':

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Voer de volgende opdracht uit om een query uit te voeren op alle apparaten, met uitzondering van apparaten met de tag rol = 'temperatuur&vochtigheid':

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u apparaat-naar-cloud-berichten bewaakt en cloud-naar-apparaat-berichten verzendt tussen uw IoT-apparaat en Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]