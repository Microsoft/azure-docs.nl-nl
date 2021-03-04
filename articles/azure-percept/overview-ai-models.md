---
title: Azure percept AI-modellen
description: Meer informatie over de AI-modellen die beschikbaar zijn voor prototypen en implementatie
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 28a8de231f179cf69342da81e6a2ae1989d2a5d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041583"
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

## <a name="pre-built-solutions"></a>Vooraf gebouwde oplossingen

Er is ook een [vooraf gemaakte ruimtelijke analyse oplossing voor personen detectie](https://github.com/george-moore/Santa-Cruz-AI-App) beschikbaar. De vooraf ontwikkelde oplossing is een open-source AI-toepassing die op Edge gebaseerde mensen oplevert met door de gebruiker gedefinieerde zone invoer/afsluit gebeurtenissen. De video-en AI-uitvoer van het on-premise edge-apparaat is egressed naar [Azure data Lake](https://azure.microsoft.com/solutions/data-lake/), met de gebruikers interface die als een Azure-website wordt uitgevoerd. AI-deinterferentie wordt aangeboden door een open-source AI-model voor het detecteren van personen.

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

Voor geavanceerde ontwikkel aars voert de beschik bare [Jupyter-notebook](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) overdrachts training uit met behulp van een vooraf getraind tensor flow model (MobileNetSSDV2Lite) in python met een aangepaste gegevensset voor object detectie. Het notitie blok maakt gebruik van externe Compute-exemplaren via [Azure machine learning](https://azure.microsoft.com/services/machine-learning/#product-overview) en kan worden uitgevoerd in de Cloud met behulp van de AzureML-portal of lokaal in [Visual Studio code](https://code.visualstudio.com/).

Ook inbegrepen zijn een aantal nuttige python- [scripts](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) voor het beheren van gegevens sets en het [installatie programma dev tools pack](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md), waarmee alle hulpprogram ma's die nodig zijn voor het ontwikkelen van een geavanceerde AI-oplossing, worden geïnstalleerd en geconfigureerd.
