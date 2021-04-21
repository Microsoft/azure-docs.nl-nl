---
title: Erotische, racy, gory content - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van inhoud voor volwassenen in afbeeldingen met behulp van Computer Vision API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceef604fe07a11be89376e26c6fecc49298ebacf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778855"
---
# <a name="detect-adult-content"></a>Inhoud voor volwassenen detecteren

Computer Vision kunnen inhoud voor volwassenen detecteren in afbeeldingen, zodat ontwikkelaars de weergave van deze afbeeldingen in hun software kunnen beperken. Inhoudsvlaggen worden toegepast met een score tussen nul en één, zodat ontwikkelaars de resultaten kunnen interpreteren op basis van hun eigen voorkeuren.

> [!NOTE]
> Veel van deze functionaliteit wordt aangeboden door de [Azure Content Moderator service.](../content-moderator/overview.md) Zie dit alternatief voor oplossingen voor strengere scenario's voor inhoudsbeheer, zoals tekstbeheer en werkstromen voor menselijke beoordeling.

## <a name="content-flag-definitions"></a>Definities van inhoudsvlag

De classificatie 'volwassenen' bevat verschillende categorieën:

- **Afbeeldingen** voor volwassenen zijn expliciet seksueel van aard en tonen vaak nuditeit en seksueel getinte handelingen.
- **Racy** images are sexually suggestive in nature and often contain less sexually explicit content than images tagged as **Adult**.
- **Bloede afbeeldingen** tonen bloed/bloed.

## <a name="use-the-api"></a>De API gebruiken

U kunt inhoud voor volwassenen detecteren met de [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Wanneer u de waarde van toevoegt aan de `Adult` queryparameter **visualFeatures,** retourneert de API drie Booleaanse eigenschappen , en in het &mdash; `isAdultContent` `isRacyContent` `isGoryContent` &mdash; JSON-antwoord. De methode retourneert ook bijbehorende eigenschappen , en die betrouwbaarheidsscores tussen nul en één voor &mdash; `adultScore` elke respectieve categorie `racyScore` `goreScore` &mdash; vertegenwoordigen.

- [Snelstart: De Computer Vision-clientbibliotheek gebruiken](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
