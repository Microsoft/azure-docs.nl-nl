---
title: Wat is Custom Vision?
titleSuffix: Azure Cognitive Services
description: Leer hoe u de Azure Custom Vision-service kunt gebruiken om aangepaste AI-classificaties voor afbeeldingen en objectdetecties te bouwen in de Azure-cloud.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: afbeeldingsherkenning, afbeeldings-id, app voor afbeeldingsherkenning, Custom Vision
ms.openlocfilehash: 12877f2d43f9b8f864871e5a5ab050aa0eeb61e2
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814598"
---
# <a name="what-is-custom-vision"></a>Wat is Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Custom Vision is een service voor het herkennen van afbeeldingen waarmee u uw eigen afbeeldings-id’s kunt bouwen, implementeren en verbeteren. Met een afbeeldings-id worden labels (die klassen of objecten vertegenwoordigen) toegepast op afbeeldingen, op basis van de bijbehorende visuele kenmerken. Anders dan bij de [Computer Vision](../computer-vision/overview.md)-service kunt u met Custom Vision de labels opgeven en aangepaste modellen trainen om deze te detecteren.

## <a name="what-it-does"></a>Wat het doet

De Custom Vision Service gebruikt een Machine Learning-algoritme om afbeeldingen te analyseren. U, als ontwikkelaar, dient groepen afbeeldingen in die de betreffende kenmerken al dan niet hebben. U voorziet de afbeeldingen op het moment van inzending zelf van een label. Vervolgens wordt het algoritme getraind op basis van deze gegevens en berekent het algoritme de eigen nauwkeurigheid aan de hand van dezelfde afbeeldingen. Wanneer het algoritme is getraind, kunt u het model testen, opnieuw trainen en uiteindelijk gebruiken in uw app voor afbeeldingsherkenning om nieuwe afbeeldingen te classificeren. U kunt het model zelf ook exporteren voor offlinegebruik.

### <a name="classification-and-object-detection"></a>Classificatie- en objectdetectie

Custom Vision-functionaliteit kan worden onderverdeeld in twee functies. Met **afbeeldingsclassificatie** worden een of meer labels toegepast op een afbeelding. **Objectdetectie** is vergelijkbaar, maar hiermee worden ook de coördinaten van de afbeelding geretourneerd waar de toegepaste labels kunnen worden gevonden.

### <a name="optimization"></a>Optimalisatie

De Custom Vision-service is geoptimaliseerd om grote verschillen tussen afbeeldingen snel te herkennen, zodat u uw model kunt gaan prototypen met een kleine hoeveelheid gegevens. 50 afbeeldingen per label is meestal al een goed begin. De service is echter niet optimaal geschikt voor het detecteren van subtiele verschillen in afbeeldingen (bijvoorbeeld het detecteren van kleine fouten in scenario’s voor kwaliteitscontrole).

Daarnaast kunt u kiezen uit verschillende Custom Vision-algoritmen die zijn geoptimaliseerd voor afbeeldingen met bepaalde onderwerpen&mdash;bijvoorbeeld oriëntatiepunten of detailhandelsartikelen. Zie de handleiding [Een classificatie bouwen](getting-started-build-a-classifier.md) of de handleiding [Een objectdetector bouwen](get-started-build-detector.md) voor meer informatie.

## <a name="what-it-includes"></a>Samenstelling van Content Moderator

De Custom Vision Service is beschikbaar als een set met systeemeigen SDK’s of via een webinterface op de [Custom Vision-website](https://customvision.ai/). U kunt een model maken, testen en trainen via een van de interfaces of beide samen gebruiken.

![Custom Vision-website in een Chrome-browservenster](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle Cognitive Services, dienen ontwikkelaars die de Custom Vision-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Volg de handleiding [Een classificatie bouwen](getting-started-build-a-classifier.md) om aan de slag te gaan met Custom Vision in de webportal, of voltooi een [quickstart](quickstarts/image-classification.md) om de basisscenario’s in code te implementeren.