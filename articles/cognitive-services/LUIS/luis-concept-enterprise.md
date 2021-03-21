---
title: Zakelijke concepten-LUIS
titleSuffix: Azure Cognitive Services
description: Inzicht in ontwerp concepten voor grote LUIS-apps of meerdere apps, waaronder LUIS en QnA Maker samen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 3f52dc502233cbab42ae5decff70b77c41c38e0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609682"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Bedrijfs strategieën voor een LUIS-app
Bekijk deze ontwerp strategieën voor uw bedrijfs-app.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Wanneer u verwacht dat LUIS-aanvragen voorbij het quotum vallen

LUIS heeft een maandelijks quotum en een quotum per seconde, gebaseerd op de prijs categorie van de Azure-resource. 

Als uw aanvraag frequentie voor de LUIS-app groter is dan het toegestane [quotum](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), kunt u het volgende doen:

* Breid de belasting naar meer LUIS-apps uit met [dezelfde app-definitie](#use-multiple-apps-with-same-app-definition). Dit omvat, eventueel, het uitvoeren van LUIS uit een [container](luis-container-howto.md). 
* [Meerdere sleutels](#assign-multiple-luis-keys-to-same-app) maken en toewijzen aan de app. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Meerdere apps met dezelfde app-definitie gebruiken
Exporteer de oorspronkelijke LUIS-app en importeer de app vervolgens weer in afzonderlijke apps. Elke app heeft een eigen App-ID. Wanneer u publiceert, maakt u in plaats van dezelfde sleutel in alle apps te gebruiken een afzonderlijke sleutel voor elke app. Het verdelen van de belasting over alle apps, zodat er geen enkele app wordt overspoeld. Voeg [Application Insights](./luis-csharp-tutorial-bf-v4.md) toe om het gebruik te bewaken. 

Zorg ervoor dat de Voorspellings voorspelling tussen de eerste en tweede intentie breed genoeg is om dezelfde hoofd intentie te verkrijgen voor alle apps en dat LUIS niet wordt verward, waardoor er verschillende resultaten zijn tussen apps voor kleine variaties in uitingen. 

Bij het trainen van deze apps op hetzelfde niveau, moet u ervoor zorgen dat [alle gegevens worden getraind](luis-how-to-train.md#train-with-all-data).

Wijs één app toe als de hoofd server. Alle uitingen die worden voorgesteld voor controle, moeten worden toegevoegd aan de hoofd-app en vervolgens worden teruggezet naar alle andere apps. Dit is een volledige export van de app of het laden van de gelabelde uitingen van de Master naar de onderliggende items. Het laden kan worden uitgevoerd vanaf de [Luis](luis-reference-regions.md) -website of de ontwerp-API voor [één utterance](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) of voor een [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Plan een periodieke beoordeling, bijvoorbeeld elke twee weken, van het [eind punt uitingen](luis-how-to-review-endpoint-utterances.md) voor het actieve leer proces en vervolgens opnieuw te trainen en opnieuw te publiceren. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Meerdere LUIS-sleutels aan dezelfde app toewijzen
Als uw LUIS-app meer eindpunt treffers ontvangt dan met de quota van uw enkele sleutel, kunt u meer sleutels maken en toewijzen aan de LUIS-app. Een Traffic Manager of load balancer maken voor het beheren van de eindpunt query's in de eindpunt sleutels. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Wanneer de monolithische-app een onjuiste intentie retourneert
Als uw app een breed scala aan gebruikers uitingen moet voors pellen, kunt u overwegen het [Verzend model](#dispatch-tool-and-model)te implementeren. Als u een monolithische-app opsplitst, kan LUIS zich richten op de detectie van de intenties in plaats van de intenties in de bovenliggende app en de onderliggende apps te verwarren. 

Plan een periodieke [beoordeling van het uitingen](luis-how-to-review-endpoint-utterances.md) voor het actieve leer proces, bijvoorbeeld elke twee weken, en voer vervolgens opnieuw een Train en opnieuw uit. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Wanneer u meer dan 500 intenties nodig hebt
Stel dat u een Office-assistent ontwikkelt met meer dan 500 intenties. Als de 200-intenties betrekking hebben op het plannen van vergaderingen, zijn er voor 200 meer informatie over herinneringen, 200 het ophalen van gegevens over collega's, en 200 zijn voor het verzenden van e-mail, groeps intentie, zodat elke groep zich in één app bevindt. vervolgens maakt u een app op het hoogste niveau met elk doel. Gebruik het [Verzend model](#dispatch-tool-and-model) om de app op het hoogste niveau te bouwen. Wijzig vervolgens uw bot om de trapsgewijze oproep te gebruiken, zoals wordt weer gegeven in de [zelf studie over het verzend model](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Wanneer u meerdere LUIS-en QnA Maker-apps moet combi neren
Als u meerdere LUIS-en QnA Maker-apps hebt die moeten reageren op een bot, gebruikt u het [Verzend model](#dispatch-tool-and-model) om de app op het hoogste niveau te bouwen.  Wijzig vervolgens uw bot om de trapsgewijze oproep te gebruiken, zoals wordt weer gegeven in de [zelf studie over het verzend model](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Hulp programma en model voor verzen ding
Gebruik het opdracht regel programma voor [verzen ding][dispatch-tool] , gevonden in [BotBuilder-hulpprogram MA'S](https://github.com/Microsoft/botbuilder-tools) om meerdere LUIS en/of QnA Maker apps te combi NEREN in een bovenliggende Luis-app. Met deze aanpak kunt u een bovenliggend domein met alle onderwerpen en andere onderliggende domeinen in afzonderlijke apps hebben. 

![Conceptuele afbeelding van de verzend architectuur](./media/luis-concept-enterprise/dispatch-architecture.png)

Het bovenliggende domein wordt vermeld in LUIS met een versie die `Dispatch` in de lijst met apps wordt genoemd. 

De chat-bot ontvangt de utterance en verzendt deze vervolgens naar de bovenliggende LUIS-app voor voor spellingen. De eerste voorspelde intentie van de bovenliggende app bepaalt welke LUIS onderliggende app het volgende wordt genoemd. De chat-bot verzendt de utterance naar de onderliggende app voor een specifiekere voor spelling.

Meer informatie over hoe deze hiërarchie van aanroepen wordt gedaan vanuit de Bot Builder v4 [-verzender-toepassing-zelf studie](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limieten voor intenties in het verzend model
Een Dispatch-toepassing heeft 500 verzend bronnen, gelijk aan 500 intenties, als het maximum. 

## <a name="more-information"></a>Meer informatie

* [Bot Framework SDK](https://github.com/Microsoft/botframework)
* [Zelf studie over verzend modellen](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs)
* [Verzending CLI](https://github.com/Microsoft/botbuilder-tools)
* Voor beeld van verspreidings model-bot- [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [testen van een batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: /azure/bot-service/bot-builder-tutorial-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool