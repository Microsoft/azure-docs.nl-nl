---
title: Update van het apparaat voor IoT Hub netwerk vereisten | Microsoft Docs
description: Bij het bijwerken van het apparaat voor IoT Hub worden verschillende netwerk poorten gebruikt voor verschillende doel einden.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558377"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Poorten die worden gebruikt bij het bijwerken van het apparaat voor IoT Hub
ADU maakt gebruik van diverse netwerk poorten voor verschillende doel einden.

## <a name="default-ports"></a>Standaard poorten

Doel|Poortnummer |
---|---
Downloaden van netwerken/Cdn's  | 80 (HTTP-protocol)
Downloaden van MCC/Cdn's | 80 (HTTP-protocol)
Verbinding met ADU-agent met Azure IoT Hub  | 8883 (MQTT-Protocol)

## <a name="use-azure-iot-hub-supported-protocols"></a>Ondersteunde protocollen van Azure IoT Hub gebruiken
De ADU-agent kan worden aangepast om een van de ondersteunde Azure-IoT Hub protocollen te gebruiken.

Meer [informatie](../iot-hub/iot-hub-devguide-protocols.md) over de huidige lijst met ondersteunde protocollen.
