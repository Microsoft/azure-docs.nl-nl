---
title: 'Architectuurconcepten in Azure IoT Central: zonnepaneel | Microsoft Docs'
description: In dit artikel worden de belangrijkste concepten besproken die betrekking hebben op de architectuur van de Azure IoT Central-bewakingsapp voor zonnepanelen.
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: cd35381e4c2cdb849662ad134cfbef8229707eed
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516631"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central: architectuur van de zonnepaneel-app

Dit artikel bevat een overzicht van de sjabloonarchitectuur van de bewakings-app voor zonnepanelen. In het onderstaande diagram ziet u een veelgebruikte architectuur voor een zonnepaneel-app in Azure die gebruikmaakt van het IoT Central-platform.

> [!div class="mx-imgBorder"]
> ![architectuur van slimme meters](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Deze architectuur bestaat uit de volgende onderdelen. Voor sommige toepassingen zijn mogelijk niet alle hier vermelde onderdelen nodig.

## <a name="solar-panels-and-connectivity"></a>Zonnepanelen en connectiviteit

Zonnepanelen zijn een van de belangrijkste bronnen van duurzame energie. Afhankelijk van het type zonnepaneel en de opstelling, kunt u deze verbinden met behulp van gateways of andere tussenliggende apparaten en eigen systemen. Mogelijk moet u een IoT Central-apparaatbrug bouwen om apparaten aan te sluiten die niet rechtstreeks kunnen worden verbonden. De IoT Central-apparaatbrug is een opensource-oplossing. De volledige details vindt u [hier](../core/howto-build-iotc-device-bridge.md). 

## <a name="iot-central-platform"></a>IoT Central-platform
Azure IoT Central is een platform dat het bouwen van IoT-oplossingen kan vereenvoudigen en waarmee u de overhead en kosten van het beheren, bedienen en ontwikkelen van IoT kunt verminderen. Met IoT Central kunt u eenvoudig uw Internet of Things (IoT)-assets op schaal aansluiten, bewaken en beheren. Nadat u de zonnepanelen hebt verbonden met IoT Central, maakt de app-sjabloon gebruik van ingebouwde functies, zoals apparaatmodellen, opdrachten en dashboards. De app-sjabloon maakt ook gebruik van IoT Central-opslag voor 'warm path'-scenario's, zoals het in vrijwel realtime bewaken van meetgegevens, en tevens analyses, regels en visualisatie.


## <a name="extensibility-options-to-build-with-iot-central"></a>Uitbreidingsopties die met IoT Central kunnen worden gebouwd
Het IoT Central-platform biedt twee uitbreidingsopties: Continue gegevensexport en API's. De klanten en partners kunnen kiezen uit deze opties op afhankelijk van hoe ze hun oplossingen aan hun behoeften willen aanpassen. Zo heeft een van onze partners CDE geconfigureerd met Azure Data Lake Storage (ADLS). Ze maken gebruik van ADLS voor langetermijnretentie van gegevens en andere 'cold-path'-scenario's voor opslag, zoals batchverwerking, controle en rapportagedoeleinden. 

## <a name="next-steps"></a>Volgende stappen

* Nu u kennis hebt genomen van de architectuur, kunt u [gratis een zonnepaneel-app maken](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Zie [Overzicht van IoT Central](../index.yml) voor meer informatie over IoT Central
