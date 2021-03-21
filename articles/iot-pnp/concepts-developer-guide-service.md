---
title: Hand leiding voor service ontwikkelaars-IoT Plug en Play | Microsoft Docs
description: Beschrijving van IoT-Plug en Play voor service ontwikkelaars
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582728"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Ontwikkelaars handleiding voor IoT Plug en Play-service

IoT Plug en Play biedt u de mogelijkheid om slimme apparaten te bouwen die hun mogelijkheden adverteren voor Azure IoT-toepassingen. IoT Plug en Play-apparaten hoeven niet hand matig te worden geconfigureerd wanneer een klant deze verbindt met IoT-Plug en Play toepassingen.

Met IoT Plug en Play kunt u apparaten gebruiken die hun model-ID hebben aangekondigd bij uw IoT-hub. U kunt bijvoorbeeld rechtstreeks toegang krijgen tot de eigenschappen en opdrachten van een apparaat.

Als u een IoT-Plug en Play apparaat wilt gebruiken dat is verbonden met uw IoT-hub, een van de IoT-service-Sdk's:

## <a name="service-sdks"></a>Service-SDK's

Gebruik de Azure IoT-service-Sdk's in uw oplossing om te communiceren met apparaten en modules. U kunt bijvoorbeeld de service-Sdk's gebruiken om dubbele eigenschappen te lezen en bij te werken en opdrachten aan te roepen. Ondersteunde talen zijn C#, Java, Node.js en python.

Met de Sdk's van de service krijgt u toegang tot apparaatgegevens van een oplossing, zoals een bureau blad of webtoepassing. De service-Sdk's bevatten twee naam ruimten en object modellen die u kunt gebruiken om de model-ID op te halen:

- IOT hub-service-client. Met deze service wordt de model-ID als een dubbele eigenschap van een apparaat weer gegeven.

- Digital Apparaatdubbels-client. De nieuwe Digital Apparaatdubbels-API werkt op model voor [Digital Apparaatdubbels Definition Language (DTDL)](concepts-digital-twin.md) , zoals onderdelen, eigenschappen en opdrachten. De digitale twee Api's maken het gemakkelijker voor oplossingen bouwers om IoT-Plug en Play oplossingen te maken.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Volgende stappen

Nu u over Apparaatbeheer hebt geleerd, zijn hier enkele aanvullende bronnen:

- [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Apparaat-SDK voor C](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](/rest/api/iothub/device)
- [Model gids voor IoT Plug en Play](concepts-modeling-guide.md)
- [De DTDL-hulpprogram ma's voor ontwerpen installeren en gebruiken](howto-use-dtdl-authoring-tools.md)