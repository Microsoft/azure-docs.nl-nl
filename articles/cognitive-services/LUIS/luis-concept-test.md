---
title: Uw LUIS-app testen
titleSuffix: Azure Cognitive Services
description: Testen is het proces van het leveren van voor beeld-uitingen aan LUIS en het verkrijgen van een antwoord op LUIS-herkende intenties en entiteiten.
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b33f765e936d7c0db301a5b2fcf38ccaf137f771
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787550"
---
# <a name="testing-example-utterances-in-luis"></a>Test voorbeeld uitingen in LUIS

Testen is het proces van het leveren van voor beeld-uitingen aan LUIS en het verkrijgen van een antwoord op LUIS-herkende intenties en entiteiten. 

U kunt LUIS interactief testen, één utterance tegelijk of een set uitingen opgeven. Tijdens het testen kunt u de reactie van het huidige actieve model vergelijken met de Voorspellings reactie van het gepubliceerde model. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Wat is een score bij het testen?
Zie de concepten van de [Voorspellings Score](luis-concept-prediction-score.md) voor meer informatie over Voorspellings scores.

## <a name="interactive-testing"></a>Interactief testen
Interactieve tests worden uitgevoerd vanuit het **test** paneel van de Luis-Portal. U kunt een utterance invoeren om te zien hoe intenties en entiteiten worden geïdentificeerd en gescoord. Als LUIS de intenties en entiteiten niet voordicteert zoals u verwacht op een utterance in het deel venster testen, kopieert u deze naar de pagina **intentie** als een nieuwe utterance. Vervolgens labelt u de onderdelen van die utterance voor entiteiten en traint u LUIS. 

## <a name="batch-testing"></a>Batchgewijs testen
Zie [batch testen](./luis-how-to-batch-test.md) als u meer dan één utterance per keer wilt testen.

## <a name="endpoint-testing"></a>Eindpunt tests
U kunt met behulp van het [eind punt](luis-glossary.md#endpoint) testen met Maxi maal twee versies van uw app. Als uw hoofd-of live-versie van uw app is ingesteld als het **productie** -eind punt, voegt u een tweede versie toe aan het **staging** -eind punt. Deze benadering biedt u drie versies van een utterance: het huidige model in het test venster van de [Luis](luis-reference-regions.md) -website en de twee versies van de twee verschillende eind punten. 

Alle eindpunt tests tellen mee voor uw gebruiks quota. 

## <a name="do-not-log-tests"></a>Geen tests vastleggen in logboek
Als u een eind punt test en niet wilt dat de utterance wordt geregistreerd, moet u de `logging=false` query reeks configuratie gebruiken.

## <a name="where-to-find-utterances"></a>Waar vind ik uitingen?
LUIS slaat alle geregistreerde uitingen op in het query logboek, dat u kunt downloaden op de LUIS-Portal op de lijst pagina met **apps** , evenals de Luis- [ontwerp-api's](https://go.microsoft.com/fwlink/?linkid=2092087). 

Alle uitingen LUIS zijn niet zeker van de lijst op de pagina **[endpoint uitingen](luis-how-to-review-endpoint-utterances.md)** van de [Luis](luis-reference-regions.md) -website. 

## <a name="remember-to-train"></a>Vergeet niet om te trainen
[Train](luis-how-to-train.md) Luis nadat u wijzigingen hebt aangebracht in het model. Wijzigingen in de LUIS-app worden niet weer gegeven in de test totdat de app is getraind. 

## <a name="best-practices"></a>Aanbevolen procedures
Lees de [Aanbevolen procedures](luis-concept-best-practices.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [testen](luis-interactive-test.md) van uw uitingen.