---
title: Volwassene, ongepaste, benchmarks-inhoud-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van inhoud voor volwassenen in afbeeldingen met behulp van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532615"
---
# <a name="detect-adult-content"></a>Inhoud voor volwassenen detecteren

Computer Vision kunnen materiaal voor volwassenen in afbeeldingen detecteren zodat ontwikkel aars de weer gave van deze installatie kopieën in hun software kunnen beperken. Inhouds vlaggen worden toegepast met een score tussen nul en één zodat ontwikkel aars de resultaten kunnen interpreteren op basis van hun eigen voor keuren.

> [!NOTE]
> Veel van deze functionaliteit wordt aangeboden door de [Azure content moderator](../content-moderator/overview.md) -service. Bekijk dit alternatief voor oplossingen voor meer strenge toezicht scenario's voor inhoud, zoals tekst toezicht en Human Review-werk stromen.

## <a name="content-flag-definitions"></a>Definities van inhouds markeringen

De classificatie ' volwassene ' bevat verschillende categorieën:

- Installatie kopieën voor **volwassenen** zijn expliciet seksuele aard en bevatten vaak naakte en seksuele handelingen.
- **Ongepaste** -installatie kopieën zijn expliciet suggesties en bevatten vaak minder expliciete inhoud dan afbeeldingen die als **volwassen** worden gelabeld.
- **Benchmarks** -installatie kopieën tonen bloed/Gore.

## <a name="use-the-api"></a>De API gebruiken

U kunt inhoud voor volwassenen detecteren met de API voor het [analyseren van afbeeldingen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Wanneer u de waarde van `Adult` aan de query parameter **visualFeatures** toevoegt, retourneert de API drie Booleaanse eigenschappen &mdash; `isAdultContent` , `isRacyContent` en `isGoryContent` &mdash; in het bijbehorende JSON-antwoord. De-methode retourneert ook de bijbehorende eigenschappen &mdash; `adultScore` , `racyScore` en `goreScore` &mdash; die betrouw bare scores vertegenwoordigen tussen nul en één voor elke betreffende categorie.

- [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
