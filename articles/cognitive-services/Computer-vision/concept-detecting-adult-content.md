---
title: Volwassene, ongepaste, benchmarks-inhoud-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van inhoud voor volwassenen in afbeeldingen met behulp van de Computer Vision-APi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cc8a4508ceeda245fbc10a81e16f3ecf05284c7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013621"
---
# <a name="detect-adult-content"></a>Inhoud voor volwassenen detecteren

Computer Vision kunnen materiaal voor volwassenen in afbeeldingen detecteren zodat ontwikkel aars de weer gave van deze installatie kopieën in hun software kunnen beperken. Inhouds vlaggen worden toegepast met een score tussen nul en één zodat ontwikkel aars de resultaten kunnen interpreteren op basis van hun eigen voor keuren.

> [!NOTE]
> Veel van deze functionaliteit wordt aangeboden door de [Azure content moderator](../content-moderator/overview.md) -service. Bekijk dit alternatief voor oplossingen voor meer strenge toezicht scenario's voor inhoud, zoals tekst toezicht en Human Review-werk stromen.

## <a name="content-flag-definitions"></a>Definities van inhouds markeringen

In de categorie ' volwassene ' zijn er verschillende categorieën:

- **Volwassene** installatie kopieën worden gedefinieerd als die van een expliciete seksuele aard zijn en vaak worden weer gegeven als naaktheid en seksuele daden.
- **Ongepaste** -installatie kopieën worden gedefinieerd als installatie kopieën die expliciet worden voorgesteld en bevatten vaak minder expliciete inhoud dan afbeeldingen die als **volwassene** worden gelabeld.
- **Benchmarks** -installatie kopieën worden gedefinieerd als die van Gore.

## <a name="use-the-api"></a>De API gebruiken

U kunt inhoud voor volwassenen detecteren met de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Wanneer u de waarde van `Adult` aan de query parameter **visualFeatures** toevoegt, retourneert de API drie Booleaanse eigenschappen &mdash; `isAdultContent` , `isRacyContent` en `isGoryContent` &mdash; in het bijbehorende JSON-antwoord. De-methode retourneert ook de bijbehorende eigenschappen &mdash; `adultScore` , `racyScore` en `goreScore` &mdash; die betrouw bare scores vertegenwoordigen tussen nul en één voor elke betreffende categorie.

- [Snelstartgids: een afbeelding analyseren (.NET SDK)](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Quick Start: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)