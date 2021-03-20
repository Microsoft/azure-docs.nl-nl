---
title: Miniaturen met slimme bijgesneden-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het genereren van miniaturen voor installatie kopieën met behulp van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68945220"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Miniaturen met slimme bijsnijding genereren met Computer Vision

Een miniatuur is een weer gave met een verkleinde grootte van een afbeelding. Miniaturen worden gebruikt voor afbeeldingen en andere gegevens in een rendabelere, overzichtelijke manier. De Computer Vision-API gebruikt slimme bijsnijden, samen met het wijzigen van de grootte van de afbeelding, om intuïtieve miniaturen te maken voor een bepaalde afbeelding.

De Computer Vision algoritme voor het genereren van miniaturen werkt als volgt:

1. Verwijder de Afleidings elementen uit de afbeelding en Identificeer het _gedeelte van_ de &mdash; afbeelding waarin de hoofd objecten worden weer gegeven.
1. De afbeelding bijsnijden op basis van het geïdentificeerde _gedeelte van de interesse_.
1. Wijzig de hoogte-breedte verhouding zodat deze overeenkomt met de doel miniatuur dimensies.

## <a name="area-of-interest"></a>Interesse gebied

Wanneer u een installatie kopie uploadt, wordt deze door de Computer Vision-API geanalyseerd om het *interesse gebied* te bepalen. Deze regio kan vervolgens worden gebruikt om te bepalen hoe de afbeelding moet worden bijgesneden. De bijsnijd bewerking komt echter altijd overeen met de gewenste hoogte-breedte verhouding als er een is opgegeven.

U kunt ook de onbewerkte selectie kader coördinaten van hetzelfde *gebied van belang* halen door de **areaOfInterest** -API aan te roepen. U kunt deze informatie vervolgens gebruiken om de oorspronkelijke installatie kopie te wijzigen.

## <a name="examples"></a>Voorbeelden

De gegenereerde miniatuur kan sterk variëren, afhankelijk van wat u opgeeft voor hoogte, breedte en Slim bijsnijden, zoals wordt weer gegeven in de volgende afbeelding.

![Een berg afbeelding naast verschillende bijsnijd configuraties](./Images/thumbnail-demo.png)

In de volgende tabel ziet u typische miniaturen die zijn gegenereerd door Computer Vision voor de voorbeeld afbeeldingen. De miniaturen zijn gegenereerd voor een opgegeven doel hoogte en-breedte van 50 pixels, waarbij Slim bijsnijden is ingeschakeld.

| Installatiekopie | Voorbeeld |
|-------|-----------|
|![Buiten bergen in Zons ondergang met de silhouet van een persoon](./Images/mountain_vista.png) | ![Miniatuur van buiten bergen van een Zons ondergang met de silhouet van een persoon](./Images/mountain_vista_thumbnail.png) |
|![Een witte bloem met een groene achtergrond](./Images/flower.png) | ![Miniatuur weergave van Vision analyseren](./Images/flower_thumbnail.png) |
|![Een vrouw aan het dak van een Apartment-gebouw](./Images/woman_roof.png) | ![miniatuur van een vrouw aan het dak van een Apartment-gebouw](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [labelen van afbeeldingen](concept-tagging-images.md) en het [categoriseren van afbeeldingen](concept-categorizing-images.md).
