---
title: Oplossingen bouwen voor Azure IoT Central | Microsoft Docs
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. Dit artikel bevat een overzicht van het bouwen van geïntegreerde oplossingen met IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417806"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central Solution Builder-hand leiding

*Dit artikel is van toepassing op oplossingen bouwers.*

Met een IoT Central-toepassing kunt u miljoenen apparaten tijdens hun levenscyclus bewaken en beheren. Deze hand leiding is voor oplossingen bouwers die IoT Central gebruiken om geïntegreerde oplossingen te bouwen. Met een IoT Central-toepassing kunt u apparaten beheren, telemetrie van apparaten analyseren en integreren met andere back-end-services.

Een oplossings bouwer:

- Hiermee configureert u Dash boards en weer gaven in de IoT Central web-gebruikers interface.
- Maakt gebruik van de ingebouwde regels en analyse Programma's voor het afleiden van zakelijke inzichten van de verbonden apparaten.
- Maakt gebruik van de mogelijkheden voor het exporteren van gegevens en regels om IoT Central te integreren met andere back-end-services.

## <a name="configure-dashboards-and-views"></a>Dash boards en weer gaven configureren

Een IoT Central toepassing kan een of meer Dash boards hebben die Opera tors gebruiken om de toepassing weer te geven en ermee te werken. Als opbouw functie voor oplossingen kunt u het standaard dashboard aanpassen en speciale Dash boards maken:

- Zie [sjablonen voor branche gerichte](concepts-app-templates.md#industry-focused-templates)voor een aantal voor beelden van aangepaste Dash boards.
- Zie [meerdere Dash boards maken en beheren](howto-create-personal-dashboards.md) en [het toepassings dashboard configureren](howto-add-tiles-to-your-dashboard.md)voor meer informatie over Dash boards.

Wanneer een apparaat verbinding maakt met een IoT Central, wordt het apparaat gekoppeld aan een apparaatprofiel voor het apparaattype. Een apparaatprofiel heeft aanpas bare weer gaven die een operator gebruikt voor het beheren van afzonderlijke apparaten. Als oplossings ontwikkelaar kunt u de beschik bare weer gaven voor een apparaattype maken en aanpassen. Zie [weer gaven toevoegen](howto-set-up-template.md#add-views)voor meer informatie.

## <a name="use-built-in-rules-and-analytics"></a>Ingebouwde regels en analyse gebruiken

Een oplossings ontwikkelaar kan regels toevoegen aan een IoT Central toepassing die aanpas bare acties uitvoert. Regels evalueren voor waarden op basis van gegevens die afkomstig zijn van een apparaat om te bepalen wanneer een actie moet worden uitgevoerd. Zie voor meer informatie over regels:

- [Zelfstudie: Een regel maken en meldingen instellen in uw Azure IoT Central-toepassing](tutorial-create-telemetry-rules.md)
- [Webhook-acties maken voor regels in Azure IoT Central](howto-create-webhooks.md)
- [Meerdere acties groeperen om uit te voeren vanuit een of meer regels](howto-use-action-groups.md)

IoT Central heeft ingebouwde analyse mogelijkheden die een operator kan gebruiken om de gegevens stromen van de verbonden apparaten te analyseren. Zie [How to use Analytics om apparaatgegevens te analyseren](howto-create-analytics.md)voor meer informatie.

## <a name="integrate-with-other-services"></a>Integreren met andere services

Als opbouw functie voor oplossingen kunt u de mogelijkheden voor het exporteren van gegevens en regels in IoT Central gebruiken om te integreren met andere services. Raadpleeg voor meer informatie:

- [IoT-gegevens exporteren naar Cloud bestemmingen met behulp van gegevens export](howto-export-data.md)
- [Werk stromen gebruiken om uw Azure IoT Central-toepassing te integreren met andere Cloud Services](howto-configure-rules-advanced.md)
- [Azure IoT Central uitbreiden met aangepaste regels met behulp van Stream Analytics, Azure Functions en SendGrid](howto-create-custom-rules.md)
- [Azure IoT Central uitbreiden met aangepaste analyses met behulp van Azure Databricks](howto-create-custom-analytics.md)
- [Uw Azure IoT Central-gegevens visualiseren en analyseren in een Power BI dash board](howto-connect-powerbi.md)

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het gebruik van IoT Central, kunt u de volgende stappen uitvoeren om de quickstarts te proberen, te beginnen met [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md).
