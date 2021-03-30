---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "70048997"
---
<!-- put the ## header in the file that includes this file -->

In deze sectie maakt u een apparaat-id in het identiteits register van uw IoT-hub. Een apparaat kan geen verbinding maken met een hub, tenzij het een vermelding bevat in het identiteits register. Zie de [IOT hub-ontwikkelaars handleiding](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations)voor meer informatie.

1. In het navigatie menu van de IoT-hub opent u **IOT-apparaten** en selecteert u vervolgens **Nieuw** om een apparaat toe te voegen aan uw IOT-hub.

    ![Apparaat-id maken in de portal](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. In **een apparaat maken** geeft u een naam op voor het nieuwe apparaat, zoals **myDeviceId**, en selecteert u **Opslaan**. Met deze actie maakt u een apparaat-id voor uw IoT-hub.

   ![Een nieuw apparaat toevoegen](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Nadat het apparaat is gemaakt, opent u het apparaat in de lijst in het deelvenster **IoT-apparaten**. Kopieer de **primaire verbindings reeks** om deze later te gebruiken.

    ![Apparaat connection string](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> In het id-register van IoT Hub worden alleen apparaat-id's opgeslagen waarmee veilig toegang tot de IoT-hub kan worden verkregen. De apparaat-id’s en sleutels worden opgeslagen en gebruikt als beveiligingsreferenties. Met de vlag voor ingeschakeld/uitgeschakeld kunt u toegang tot een afzonderlijk apparaat uitschakelen. Als uw toepassing andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke opslagmethode gebruiken. Zie [IOT hub ontwikkelaars handleiding](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie.
