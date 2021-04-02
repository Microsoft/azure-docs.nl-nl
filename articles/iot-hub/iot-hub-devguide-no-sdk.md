---
title: Ontwikkelen zonder Azure IoT SDK | Microsoft Docs
description: 'Ontwikkelaars handleiding: informatie over en koppelingen naar onderwerpen die u kunt gebruiken om apps voor apparaten en back-end-apps te bouwen zonder een Azure IoT-SDK te gebruiken.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461707"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Ontwikkelen zonder gebruik te maken van een Azure IoT Hub SDK

In dit onderwerp vindt u nuttige informatie en koppelingen voor ontwikkel aars die apparaat-of back-end-apps willen ontwikkelen zonder de Azure IoT-Sdk's te gebruiken.

Micro soft adviseert sterk een Azure IoT SDK te gebruiken. De Azure IoT-apparaten en-service-Sdk's worden gepubliceerd op veel populaire platforms. De Sdk's bieden een gebruiks vriendelijke laag die veel van de complexiteit van het onderliggende communicatie protocol afhandelt, met inbegrip van de verbinding met het apparaat en de nieuwe verbinding en het beleid voor opnieuw proberen. De Sdk's worden regel matig bijgewerkt met de nieuwste functies die worden weer gegeven door IoT Hub en beveiligings updates. Met behulp van de Sdk's kunt u de ontwikkelings tijd en-tijd die aan code onderhoud zijn besteed, beperken. Zie [Azure IOT-apparaat en-service-sdk's](iot-hub-devguide-sdks.md)voor meer informatie over de Azure IOT sdk's. Voor meer informatie over de voor delen van het gebruik van een Azure IoT SDK raadpleegt [u de voor delen van het gebruik van de Azure IOT sdk's en Valk uilen om te voor komen dat u](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) een blog bericht maakt.

Hoewel IoT Hub ondersteuning biedt voor AMQP, AMQP over websockets, HTTPS, MQTT en MQTT over websockets voor communicatie met apparaten, raden we u aan MQTT te gebruiken als uw apparaat dit ondersteunt.

## <a name="development-prerequisites"></a>Vereisten voor de ontwikkeling

Voordat u met de ontwikkeling begint, moet u beschikken over een grondige kennis van IoT Hub en de functies die u voor het apparaat of de back-end-app wilt implementeren. Hier volgt een zeer korte lijst met onderwerpen die u moet kennen:

* Zorg ervoor dat u bekend bent met de eind punten die door IoT Hub worden weer gegeven en de protocollen die op elk eind punt worden ondersteund. Zie [IOT hub-eind punten](iot-hub-devguide-endpoints.md)voor meer informatie.

* Wanneer een keuze van het protocol is betrokken bij apparaat-apps, raden we u ten zeerste aan gebruik te maken van MQTT. Voordat u een protocol kiest, moet u er echter wel voor zorgen dat u de beperkingen kent die door elk van beide worden opgelegd. Zie [een communicatie protocol kiezen](iot-hub-devguide-protocols.md)voor meer informatie.

* Zie [toegang tot IOT hub beheren](iot-hub-devguide-security.md)voor meer informatie over verificatie met IOT hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Hulp bij verschillende protocollen

Voor hulp bij het gebruik van de volgende protocollen zonder een Azure IoT SDK:

* Apparaten of back-end-apps op **AMQP**, Zie [AMQP-ondersteuning](iot-hub-amqp-support.md).

* Apparaat-apps op **MQTT**, Zie [MQTT-ondersteuning](iot-hub-mqtt-support.md). In het grootste deel van dit onderwerp wordt het MQTT-protocol rechtstreeks behandeld. Het bevat ook informatie over het gebruik van de [IOT MQTT-voorbeeld opslagplaats](https://github.com/Azure-Samples/IoTMQTTSample). Deze opslag plaats bevat C-voor beelden die gebruikmaken van de Mosquitto-bibliotheek van de eclips om berichten te verzenden naar IoT Hub.

* Apparaten of back-end-apps op **https**, raadpleegt u de [Azure IOT hub rest-api's](/rest/api/iothub/). Houd er rekening mee dat u geen [gebruik kunt maken van X](#development-prerequisites). 509 certificerings instantie (CA) verificatie met https.

Voor apparaten is het raadzaam om MQTT te gebruiken als uw apparaat dit ondersteunt.

## <a name="next-steps"></a>Volgende stappen

* [MQTT-ondersteuning](iot-hub-mqtt-support.md)