---
title: Ontwikkelaars handleiding voor apparaten (C)-IoT Plug en Play | Microsoft Docs
description: Beschrijving van IoT-Plug en Play voor ontwikkel aars van C-apparaten
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 3aa236570e518b142adb8382387a8cdea4fc08a0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453277"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Ontwikkelaars handleiding voor IoT Plug en Play-apparaten

IoT Plug en Play biedt u de mogelijkheid om slimme apparaten te bouwen die hun mogelijkheden adverteren voor Azure IoT-toepassingen. IoT Plug en Play-apparaten hoeven niet hand matig te worden geconfigureerd wanneer een klant deze verbindt met IoT-Plug en Play toepassingen.

Een Smart Device kan rechtstreeks worden geïmplementeerd, [modules](../iot-hub/iot-hub-devguide-module-twins.md)gebruiken of [IOT Edge modules](../iot-edge/about-iot-edge.md)gebruiken.

In deze hand leiding worden de basis stappen beschreven die nodig zijn om een apparaat, module of IoT Edge-module te maken die volgt op de [IoT Plug en Play-conventies](../iot-pnp/concepts-convention.md).

Voer de volgende stappen uit om een IoT-Plug en Play apparaat, module of IoT Edge module te bouwen:

1. Zorg ervoor dat het apparaat gebruikmaakt van het MQTT-of MQTT over websockets-protocol om verbinding te maken met Azure IoT Hub.
1. Maak een [DTDL-model (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) om uw apparaat te beschrijven. Zie [onderdelen begrijpen in IoT Plug en Play-modellen](concepts-components.md)voor meer informatie.
1. Werk uw apparaat of module bij om de `model-id` verbinding met het apparaat te maken.
1. Telemetrie, eigenschappen en opdrachten implementeren met behulp van de [IoT Plug en Play-conventies](concepts-convention.md)

Zodra de implementatie van uw apparaat of module is voltooid, gebruikt u [Azure IOT Explorer](howto-use-iot-explorer.md) om te controleren of het apparaat de IOT Plug en Play-conventies volgt.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over IoT Plug en Play Device Development, zijn hier enkele aanvullende bronnen:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Apparaat-SDK voor C](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](/rest/api/iothub/device)
- [Model onderdelen](concepts-components.md)
- [De DTDL-hulpprogram ma's voor ontwerpen installeren en gebruiken](howto-use-dtdl-authoring-tools.md)
- [Ontwikkelaars handleiding voor IoT Plug en Play-service](concepts-developer-guide-service.md)