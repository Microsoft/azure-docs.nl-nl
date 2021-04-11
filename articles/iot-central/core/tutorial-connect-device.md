---
title: 'Zelfstudie: een generieke client-app verbinden met Azure IoT Central | Microsoft Docs'
description: In deze zelfstudie wordt uitgelegd hoe u, als apparaatontwikkelaar, een apparaat met een C-, C#-, Java-, JavaScript- of Python-client-app verbindt met uw Azure IoT Central-toepassing. U kunt de automatisch gegenereerde apparaatsjabloon wijzigen door weergaven toe te voegen die een operator in staat stellen om te communiceren met een verbonden apparaat.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: bbf94b6e000d5c082debd6a0d41a8d62b8b3f26e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491068"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Zelfstudie: Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

In deze zelfstudie wordt uitgelegd hoe u, als apparaatontwikkelaar, een clienttoepassing verbindt met uw Azure IoT Central-toepassing. Met de toepassing wordt het gedrag van een temperatuur controller apparaat gesimuleerd. Wanneer de toepassing verbinding maakt met IoT Central, wordt de model-ID van het apparaat model van de temperatuur controller verzonden. IoT Central gebruikt de model-id om het apparaatmodel op te halen en een sjabloon voor u te maken. U voegt aanpassing en weergaven toe aan de apparaatsjabloon zodat een operator met een apparaat kan communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De apparaatcode maken, deze uitvoeren en kijken hoe deze verbinding maakt met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie bekijken die vanaf het apparaat wordt verzonden.
> * Aangepaste weergaven toevoegen aan een apparaatsjabloon.
> * De sjabloon van het apparaat publiceren.
> * Een weergave gebruiken om apparaateigenschappen te beheren.
> * Een opdracht aanroepen om het apparaat te beheren.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Onbewerkte gegevens weergeven

Als apparaatontwikkelaar kunt u de weergave **Onbewerkte gegevens** gebruiken om de onbewerkte gegevens te onderzoeken die via uw apparaat worden verzonden naar IoT Central:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="De weergave Onbewerkte gegevens":::

In deze weergave kunt u de kolommen selecteren die u wilt weergeven en een tijdsbereik instellen. In de kolom niet- **gemodelleerde gegevens** worden apparaatgegevens weer gegeven die niet overeenkomen met een eigenschap of telemetrie in de sjabloon voor het apparaat.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

Als u liever wilt doorgaan met de set zelfstudies over IoT Central en meer wilt leren over het bouwen van een IoT Central-oplossing, raadpleegt u:

> [!div class="nextstepaction"]
> [Een gateway-apparaatsjabloon maken](./tutorial-define-gateway-device-type.md)

U, als apparaatontwikkelaar, kent nu de basisbeginselen van het maken van een apparaat met behulp van Java. Hierna volgen enkele voorgestelde volgende stappen:

* Lees [Wat zijn apparaatsjablonen?](./concepts-device-templates.md) voor meer informatie over de rol van apparaatsjablonen wanneer u uw apparaatcode implementeert.
* Lees [Verbinding maken met Azure IoT Central](./concepts-get-connected.md) voor meer informatie over het registreren van apparaten met IoT Central en hoe IoT Central apparaatverbindingen beveiligt.
* Zie [Telemetrie-, eigenschap- en opdracht-payloads](concepts-telemetry-properties-commands.md) voor meer informatie over de gegevens die door het apparaat worden uitgewisseld met IoT Central.
* Lees de [Handleiding voor ontwikkelaars van IoT Plug en Play-apparaten](../../iot-pnp/concepts-developer-guide-device.md).
