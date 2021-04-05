---
title: Bestaande IoT-hub gebruiken met Device simulatie oplossing-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de apparaat simulatie oplossings versneller configureert voor het gebruik van een bestaande IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009361"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Een bestaande IoT-hub gebruiken met de oplossingsverbetering voor apparaatsimulatie

Wanneer u Device simulatie implementeert, kunt u er ook voor kiezen om een IoT-hub te implementeren voor gebruik in uw simulatie. Met deze optie implementeert u een [IOT-hub uit de S2-laag met één schaal eenheid](../iot-hub/iot-hub-scaling.md). Als u deze optionele IoT-hub implementeert, kunt u nog steeds een andere IoT Hub instellen voor een simulatie uitvoering.

Als u ervoor kiest om de optionele IoT Hub niet te implementeren, moet u uw eigen hub gebruiken voor simulaties die u uitvoert.

Als u geen IoT-hub hebt, kunt u altijd een nieuwe maken op basis van de [Azure Portal](https://portal.azure.com).

Als u een al bestaande IoT-hub wilt gebruiken, hebt u de connection string nodig voor het **iothubowner** -beleid voor gedeelde toegang. U kunt deze connection string ophalen via de [Azure Portal](https://portal.azure.com):

1. Klik op de configuratie pagina van de hub in de portal op **beleid voor gedeelde toegang**.

1. Klik op **iothubowner**.

1. Kopieer de primaire of secundaire connection string.

[![Verbindingstekenreeks ophalen](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Gebruik de connection string die u hebt gekopieerd tijdens het configureren van de simulatie:

![Simulatie configureren](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u een bestaande IoT-hub kunt gebruiken in een simulatie. Vervolgens wilt u weten hoe u [een geavanceerd model kunt maken](iot-accelerators-device-simulation-advanced-device.md) voor een simulatie.
