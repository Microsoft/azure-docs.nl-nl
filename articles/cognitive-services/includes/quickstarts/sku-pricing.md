---
title: SKU's en prijzen voor Cognitive Services
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 8cc4bc6907f83ce062fed82dde17815fc4debd67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719936"
---
Bekijk de onderstaande lijst met SKU's en prijsinformatie. 

#### <a name="multi-service"></a>Multi-service

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Meerdere services. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/) voor meer informatie.            | `CognitiveServices`     |


#### <a name="vision"></a>Vision

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - Voorspelling | `CustomVision.Prediction` |
| Custom Vision - Training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |

#### <a name="search"></a>Search

| Service            | Soort                  |
|--------------------|-----------------------|
| Bing Automatische suggesties   | `Bing.Autosuggest.v7` |
| Bing Aangepaste zoekopdrachten | `Bing.CustomSearch`   |
| Bing Entiteiten zoeken | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing Spellingcontrole   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Spraak

| Service            | Soort                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Spraakherkenning | `SpeakerRecognition` |

#### <a name="language"></a>Taal

| Service            | Soort                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Tekstanalyse     | `TextAnalytics`     |
| Tekstomzetting   | `TextTranslation`   |

#### <a name="decision"></a>Besluit

| Service           | Soort               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Prijscategorieën en facturering

Prijscategorieën (en het bedrag dat in rekening wordt gebracht) zijn gebaseerd op het aantal transacties dat u verzendt met behulp van uw verificatiegegevens. Voor elke prijsklasse wordt het volgende gespecificeerd:
* het maximumaantal toegestane transacties per seconde (TPS).
* servicefuncties die zijn ingeschakeld binnen de prijscategorie.
* kosten voor een vooraf gedefinieerd aantal transacties. Als u boven dit aantal uitkomt, worden er extra kosten in rekening gebracht zoals gespecificeerd in de [prijsinformatie](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor uw service.

> [!NOTE]
> Veel van de Cognitive Services hebben een gratis laag die u kunt gebruiken om de service te proberen. Als u de gratis laag wilt gebruiken, gebruikt u `F0` als de SKU voor uw resource.