---
title: 'Architectuurconcepten in Azure IoT Central: slimme meter | Microsoft Docs'
description: In dit artikel worden de belangrijkste concepten besproken die betrekking hebben op de architectuur van de Azure IoT Central-sjabloon voor de energie-app
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 48dd95acf725e57d63f85c54c97b641935b049b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832365"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central: architectuur van de app voor een slimme meter

Dit artikel bevat een overzicht van de sjabloonarchitectuur van de bewakings-app voor de slimme meter. In het onderstaande diagram ziet u een veelgebruikte app-architectuur voor een slimme meter in Azure die gebruikmaakt van het IoT Central-platform.

> [!div class="mx-imgBorder"]
> ![architectuur van slimme meters](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Deze architectuur bestaat uit de volgende onderdelen. Voor sommige oplossingen zijn mogelijk niet alle hier vermelde onderdelen nodig.

## <a name="smart-meters-and-connectivity"></a>Slimme meters en connectiviteit 

Een slimme meter is een van de belangrijkste apparaten op het gebied van energieapparaten. Een slimme meter registreert en communiceert gegevens over het energieverbruik naar hulpprogramma's voor bewaking en andere toepassingen, zoals de facturering en vraagrespons. Op basis van het type meter kan met behulp van gateways of andere tussenliggende apparaten of systemen, zoals de randapparaten en hoofdsystemen, verbinding worden gemaakt met IoT Central. Bouw een IoT Central-apparaatbrug om apparaten te verbinden die niet rechtstreeks kunnen worden verbonden. De IoT Central-apparaatbrug is een opensource-oplossing. De volledige details vindt u [hier](../core/howto-build-iotc-device-bridge.md). 

## <a name="iot-central-platform"></a>IoT Central-platform

Azure IoT Central is een platform dat het bouwen van IoT-oplossingen kan vereenvoudigen en waarmee u de overhead en kosten van het beheren, bedienen en ontwikkelen van IoT kunt verminderen. Met IoT Central kunt u eenvoudig uw Internet of Things (IoT)-assets op schaal aansluiten, bewaken en beheren. Nadat u de slimme meter hebt verbonden met IoT Central, maakt de app-sjabloon gebruik van ingebouwde functies, zoals apparaatmodellen, opdrachten en dashboards. De app-sjabloon maakt ook gebruik van IoT Central-opslag voor 'warm path'-scenario's, zoals het in vrijwel realtime bewaken van meetgegevens, en tevens analyses, regels en visualisatie. 

## <a name="extensibility-options-to-build-with-iot-central"></a>Uitbreidingsopties die met IoT Central kunnen worden gebouwd
Het IoT Central-platform biedt twee uitbreidingsopties: Continue gegevensexport en API's. De klanten en partners kunnen kiezen uit deze opties op afhankelijk van hoe ze hun oplossingen aan hun behoeften willen aanpassen. Zo heeft een van onze partners CDE geconfigureerd met Azure Data Lake Storage (ADLS). Ze maken gebruik van ADLS voor langetermijnretentie van gegevens en andere 'cold-path'-scenario's voor opslag, zoals batchverwerking, controle en rapportagedoeleinden. 

## <a name="next-steps"></a>Volgende stappen

* Nu u kennis hebt genomen van de architectuur, kunt u [gratis een app voor een slimme meter maken](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Zie [Overzicht van IoT Central](../index.yml) voor meer informatie over IoT Central
