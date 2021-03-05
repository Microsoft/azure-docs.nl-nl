---
title: Azure percept AI-modellen
description: Meer informatie over de AI-modellen die beschikbaar zijn voor prototypen en implementatie
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: d0cdabb3b22d642a7903810181106b09c549e1a2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218159"
---
# <a name="azure-percept-ai-models"></a>Azure percept AI-modellen

Met Azure percept kunt u AI-modellen rechtstreeks ontwikkelen en implementeren in azure percept DK vanuit [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Model implementatie maakt gebruik van [Azure IOT hub](https://azure.microsoft.com/services/iot-hub/) en [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>AI-voorbeeld modellen

Azure percept Studio bevat voorbeeld modellen voor de volgende toepassingen:

- detectie van personen
- voertuig detectie
- algemene object detectie
- detectie van producten op plank

Met vooraf getrainde modellen is geen code ring of trainings gegevens verzameling vereist. Implementeer uw gewenste model in azure percept DK vanuit de portal en open de video stroom van de Devkit om het model dezicht in actie te zien. U kunt de telemetrie van het model niet gebruiken via het hulp programma [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) .

## <a name="reference-solutions"></a>Referentieoplossingen

Er is ook een [referentie oplossing](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) voor het tellen van personen beschikbaar. Deze referentie oplossing is een open-source AI-toepassing die op Edge gebaseerde personen oplevert die worden geteld met door de gebruiker gedefinieerde zone invoer/afsluit gebeurtenissen. De video-en AI-uitvoer van het on-premise edge-apparaat is egressed naar [Azure data Lake](https://azure.microsoft.com/solutions/data-lake/), met de gebruikers interface die als een Azure-website wordt uitgevoerd. AI-deinterferentie wordt aangeboden door een open-source AI-model voor het detecteren van personen.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Vooraf ontwikkelde oplossings-gif van ruimtelijke analyse.":::

## <a name="custom-no-code-solutions"></a>Aangepaste oplossingen zonder code

Met Azure percept Studio kunt u aangepaste [visie](./tutorial-nocode-vision.md) -en spraak oplossingen ontwikkelen, maar geen code ring vereist.

Voor aangepaste Vision-oplossingen zijn zowel object detectie als classificatie AI-modellen beschikbaar. U kunt uw trainings afbeeldingen gewoon uploaden en labelen, die rechtstreeks kunnen worden gemaakt met het percept Vision-SoM van Azure percept DK, indien gewenst. Model training en evaluatie kunnen eenvoudig worden uitgevoerd in [Custom Vision](https://www.customvision.ai/), die deel uitmaakt van [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview).

Voor aangepaste spraak oplossingen zijn sjablonen voor spraak assistenten momenteel beschikbaar voor de volgende toepassingen:

- Horeca: Hotel Room, ingericht met op spraak beheerde slimme apparaten.
- Gezondheids zorg: Care-faciliteit die is uitgerust met met spraak beheerde smart-apparaten.
- Inventaris: inventarisatie-hub die is uitgerust met op spraak beheerde smart-apparaten.
- Automobiel: automobiel hub, uitgerust met op spraak beheerde slimme apparaten.

De vooraf gemaakte tref woorden en opdrachten voor spraak assistenten zijn rechtstreeks beschikbaar via de portal. Aangepaste tref woorden en opdrachten kunnen worden gemaakt en getraind in [Speech Studio](https://speech.microsoft.com/), dat ook deel uitmaakt van Azure Cognitive Services.

## <a name="advanced-development"></a>Geavanceerde ontwikkeling

Raadpleeg [Azure PERCEPT DK Advanced Development github](https://github.com/microsoft/azure-percept-advanced-development) voor actuele hulp, zelf studies en voor beelden voor zaken als:

* Een aangepast AI-model naar het apparaat brengen
* Een model bijwerken dat al wordt ondersteund met overboeking Learning
* En meer
