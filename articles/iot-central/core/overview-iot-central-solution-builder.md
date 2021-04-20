---
title: Oplossingen bouwen voor Azure IoT Central | Microsoft Docs
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. In dit artikel vindt u een overzicht van het bouwen van geïntegreerde oplossingen met IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e762b8c2e2d7f72b89629c520560b205cedcd036
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728552"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central handleiding voor het bouwen van oplossingen

*Dit artikel is van toepassing op oplossingsbouwers.*

Met een IoT Central-toepassing kunt u miljoenen apparaten tijdens hun levenscyclus bewaken en beheren. Deze handleiding is voor oplossingsbouwers die IoT Central gebruiken om geïntegreerde oplossingen te bouwen. Met IoT Central kunt u apparaten beheren, telemetriegegevens van apparaten analyseren en integreren met andere back-endservices.

Een bouwer van oplossingen:

- Hiermee configureert u dashboards en weergaven in IoT Central webinterface.
- Maakt gebruik van de ingebouwde regels en analysehulpprogramma's om zakelijke inzichten af te leiden van de verbonden apparaten.
- Maakt gebruik van de mogelijkheden voor gegevensexport en regels om IoT Central te integreren met andere back-endservices.

## <a name="configure-dashboards-and-views"></a>Dashboards en weergaven configureren

Een IoT Central kan een of meer dashboards hebben die operators gebruiken om de toepassing weer te geven en te gebruiken. Als bouwer van oplossingen kunt u het standaarddashboard aanpassen en gespecialiseerde dashboards maken:

- Zie Branchegerichte sjablonen voor enkele voorbeelden van aangepaste [dashboards.](concepts-app-templates.md#industry-focused-templates)
- Zie Meerdere [dashboards](howto-create-personal-dashboards.md) maken en beheren en Het toepassingsdashboard configureren voor meer informatie over [dashboards.](howto-add-tiles-to-your-dashboard.md)

Wanneer een apparaat verbinding maakt met een IoT Central, wordt het apparaat gekoppeld aan een apparaatsjabloon voor het apparaattype. Een apparaatsjabloon heeft aanpasbare weergaven die een operator gebruikt om afzonderlijke apparaten te beheren. Als oplossingsontwikkelaar kunt u de beschikbare weergaven voor een apparaattype maken en aanpassen. Zie Weergaven toevoegen [voor meer informatie.](howto-set-up-template.md#add-views)

## <a name="use-built-in-rules-and-analytics"></a>Ingebouwde regels en analyses gebruiken

Een oplossingsontwikkelaar kan regels toevoegen aan een IoT Central toepassing die aanpasbare acties uitvoeren. Regels evalueren voorwaarden, op basis van gegevens die afkomstig zijn van een apparaat, om te bepalen wanneer een actie moet worden uitgevoerd. Zie voor meer informatie over regels:

- [Zelfstudie: Een regel maken en meldingen instellen in uw Azure IoT Central-toepassing](tutorial-create-telemetry-rules.md)
- [Webhook-acties maken voor regels in Azure IoT Central](howto-create-webhooks.md)
- [Meerdere acties groepen om uit te voeren vanuit een of meer regels](howto-use-action-groups.md)

IoT Central beschikt over ingebouwde analysemogelijkheden die een operator kan gebruiken om de gegevensstroom van de verbonden apparaten te analyseren. Zie Analyse gebruiken om apparaatgegevens te analyseren [voor meer informatie.](howto-create-analytics.md)

## <a name="integrate-with-other-services"></a>Integreren met andere services

Als bouwer van oplossingen kunt u de mogelijkheden voor gegevensexport en regels in IoT Central integreren met andere service. Raadpleeg voor meer informatie:

- [IoT-gegevens exporteren naar cloudbestemmingen met behulp van gegevensexport](howto-export-data.md)
- [Gegevens voor IoT Central](howto-transform-data.md)
- [Werkstromen gebruiken om uw Azure IoT Central te integreren met andere cloudservices](howto-configure-rules-advanced.md)
- [Azure IoT Central uitbreiden met aangepaste regels met behulp van Stream Analytics, Azure Functions en SendGrid](howto-create-custom-rules.md)
- [Azure IoT Central uitbreiden met aangepaste analyse met behulp van Azure Databricks](howto-create-custom-analytics.md)
- [Uw gegevens visualiseren Azure IoT Central analyseren in een Power BI dashboard](howto-connect-powerbi.md)

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het gebruik van IoT Central, kunt u de volgende stappen uitvoeren om de quickstarts te proberen, te beginnen met [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md).
