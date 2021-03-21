---
title: Hand leiding voor Azure IoT Central-Opera tors
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. Dit artikel bevat een overzicht van de operator rol in IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669934"
---
# <a name="iot-central-operator-guide"></a>IoT Central-operator handleiding

*Dit artikel is van toepassing op Opera tors.*

Met een IoT Central-toepassing kunt u miljoenen apparaten tijdens hun levenscyclus bewaken en beheren. Deze hand leiding is voor Opera tors die gebruikmaken van een IoT Central toepassing voor het beheren van IoT-apparaten.

Een operator:

- Bewaakt en beheert de apparaten die zijn verbonden met de toepassing.
- Hiermee worden problemen met apparaten oplost en opgelost.
- Richt nieuwe apparaten in.

## <a name="monitor-and-manage-devices"></a>Apparaten bewaken en beheren

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Scherm opname van de weer gave van een apparaat":::

Voor het bewaken van apparaten kan een operator de weer gaven van het apparaat gebruiken die zijn gedefinieerd door de oplossings functie als onderdeel van de sjabloon. Deze weer gaven kunnen telemetrie en eigenschaps waarden van apparaten weer geven. Een voor beeld is de weer gave **overzicht** die wordt weer gegeven op de vorige scherm afbeelding.

Voor meer gedetailleerde informatie kan een operator gebruikmaken van apparaatgroepen en de ingebouwde analyse functies. Zie [How to use Analytics om apparaatgegevens te analyseren](howto-create-analytics.md)voor meer informatie.

Als u afzonderlijke apparaten wilt beheren, kan een operator de weer gaven apparaat-en Cloud eigenschappen instellen en opdrachten op het apparaat aanroepen. Voor beelden zijn onder andere de weer gaven apparaat en **opdrachten** **beheren** in de vorige scherm afbeelding.

Als u apparaten in bulk wilt beheren, kan een operator taken maken en plannen. Met taken kunnen eigenschappen worden bijgewerkt en opdrachten op meerdere apparaten worden uitgevoerd. Zie [een taak maken en uitvoeren in uw Azure IOT Central-toepassing](howto-run-a-job.md)voor meer informatie.

## <a name="troubleshoot-and-remediate-issues"></a>Problemen oplossen en herstellen

De operator is verantwoordelijk voor de status van de toepassing en de bijbehorende apparaten. De [hand leiding](troubleshoot-connection.md) voor het oplossen van problemen helpt Opera tors bij het vaststellen en oplossen van veelvoorkomende problemen. Een operator kan de pagina **apparaten** gebruiken om apparaten te blok keren die niet goed lijken te zijn totdat het probleem is opgelost.

## <a name="add-and-remove-devices"></a>Apparaten toevoegen en verwijderen

De operator kan apparaten afzonderlijk of bulksgewijs toevoegen aan en verwijderen uit uw IoT Central-toepassing. Zie [apparaten beheren in uw Azure IOT Central-toepassing](howto-manage-devices.md)voor meer informatie.

## <a name="personalize"></a>Aanpassen

Opera tors kunnen persoonlijke Dash boards maken in een IoT Central-toepassing die koppelingen bevat naar de resources die ze het meest gebruiken. Zie [Dash boards beheren](howto-create-personal-dashboards.md#manage-dashboards)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het gebruik van IoT Central, kunt u de volgende stappen uitvoeren om de quickstarts te proberen, te beginnen met [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md).
