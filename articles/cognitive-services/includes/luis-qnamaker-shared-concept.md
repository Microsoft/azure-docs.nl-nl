---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: 78b93c05621754ae499e4ae8ca6b66c5bbfaad1b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028115"
---
Cognitive Services biedt twee services voor de verwerking van natuurlijke taal, [Language Understanding](../luis/what-is-luis.md) en [QnA Maker](../qnamaker/overview/overview.md), elk met een ander doel. Begrijpen wanneer u elke service gebruikt en hoe ze elkaar in de compliment hebben.

Met natuurlijke taal verwerking (NLP) kan uw client toepassing, zoals een chat-bot, samen werken met uw gebruikers, met behulp van natuurlijke taal. Een gebruiker voert een zin of zin in. De tekst van de gebruiker kan een slechte grammatica, spelling en lees tekens bevatten. De cognitieve service kan toch door de gebruikers zin worden gebruikt, waardoor de chat gegevens worden geretourneerd die de gebruiker nodig heeft.

## <a name="cognitive-services-with-nlp"></a>Cognitive Services met NLP

Language Understanding (LUIS) en QnA Maker bieden NLP. De client toepassing verzendt tekst in natuurlijke taal. De service gebruikt de tekst, verwerkt deze en retourneert een resultaat.

## <a name="when-to-use-each-service"></a>Wanneer moet elke service worden gebruikt

Language Understanding (LUIS) en QnA Maker verschillende problemen op te lossen. LUIS bepaalt het doel van de tekst van een gebruiker (ook wel een utterance genoemd), terwijl QnA Maker het antwoord op de tekst van een gebruiker (ook wel een query genoemd) bepaalt.

Als u de juiste service wilt kiezen, moet u weten wat de gebruikers tekst van de client toepassing is en welke informatie de client toepassing moet verkrijgen van de cognitieve service.

Als uw chat-bot de tekst ontvangt `How do I get to the Human Resources building on the Seattle North campus?` , gebruikt u de onderstaande grafiek om te begrijpen hoe elke service met de tekst werkt.

|Service|Client toepassing bepaalt|
|--|--|
|LUIS|**Hiermee wordt de bedoeling** van de tekst van de gebruiker bepaald: de service retourneert niet het antwoord op de vraag. Deze tekst wordt bijvoorbeeld geclassificeerd als overeenkomend met het `FindLocation` doel.<br>|
|QnA Maker|**Retourneert het antwoord op de vraag** van een aangepaste Knowledge Base. Deze tekst wordt bijvoorbeeld bepaald als een vraag met het statische tekst antwoord van  `Get on the #9 bus and get off at Franklin street` .|
|||

> [!div class="mx-imgBorder"]
> ![Infographic om te bepalen wanneer u LUIS moet gebruiken en wanneer u QnA Maker wilt gebruiken](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>Wanneer gebruikt u LUIS?

Gebruik LUIS als u de bedoeling van een uiting wilt weten als onderdeel van een proces in de chatbot. Als u doorgaat met de voorbeeld tekst, `How do I get to the Human Resources building on the Seattle North campus?` , wanneer u weet dat de gebruiker zich kan bezig houden met het vinden van een locatie, kunt u de details van de utterance (die is opgetrokken met entiteiten) door geven aan een andere service, zoals een Transport Server, om het antwoord te krijgen.

U hoeft LUIS en QnA Maker niet te combi neren om intentie te bepalen.

U kunt de twee services voor deze utterance combi neren als de chat-bot de tekst moet verwerken op basis van bedoelingen en entiteiten (met behulp van LUIS), maar ook het specifieke statische tekst antwoord (met behulp van QnA Maker) te vinden.

## <a name="when-do-you-use-qna-maker"></a>Wanneer gebruikt u QnA Maker?

Gebruik QnA Maker wanneer u een statische Knowledge Base met antwoorden hebt. Deze Knowledge Base is aangepast aan uw behoeften, die u hebt ingebouwd met documenten zoals PDF’s en URL’s.

U kunt door gaan met het voor beeld utterance, `How do I get to the Human Resources building on the Seattle North campus?` , de tekst verzenden als een query naar uw gepubliceerde QnA Maker-service en het beste antwoord ontvangen.

U hoeft LUIS en QnA Maker niet te combi neren om het antwoord op de vraag te bepalen.

U kunt de twee services voor deze utterance combi neren als de chat-bot de tekst moet verwerken op basis van bedoelingen en entiteiten (met behulp van LUIS), maar ook het antwoord kunt vinden (met behulp van QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Beide services gebruiken wanneer de Knowledge Base niet is voltooid

Als u uw QnA Maker Knowledge Base bouwt, maar u weet dat het onderwerp domein wordt gewijzigd (zoals tijdige informatie), kunt u LUIS en QnA Maker Services combi neren. Zo kunt u de gegevens in uw Knowledge Base gebruiken, maar ook LUIS gebruiken om te bepalen wat de bedoeling van een gebruiker is. Zodra de client toepassing het voor nemen heeft, kan deze relevante gegevens van een andere bron aanvragen.

Uw client toepassing moet zowel LUIS als QnA Maker reacties op scores bewaken. Als de Score van QnA Maker onder een bepaalde drempel waarde ligt, gebruikt u de intentie en entiteits gegevens die zijn geretourneerd door LUIS om de informatie over te brengen naar een service van derden.

U kunt door gaan met de voorbeeld tekst, `How do I get to the Human Resources building on the Seattle North campus?` dus dat QnA Maker een lage betrouwbaarheids Score retourneert. Gebruik het intentie resultaat van LUIS `FindLocation` en alle geëxtraheerde entiteiten, zoals `Human Resources building` en `Seattle North campus` , om deze gegevens naar een toewijzings-of zoek service voor een ander antwoord te verzenden.

U kunt dit antwoord van derden voor validatie weer geven aan de gebruiker. Zodra u de goed keuring van de gebruiker hebt, kunt u terugkeren naar QnA Maker om de informatie toe te voegen om uw kennis te verg Roten.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Gebruik beide services als uw chat-bot meer informatie nodig heeft

Als uw chat-bot meer informatie nodig heeft dan een van beide services biedt, kunt u een beslissings structuur door lopen door beide services te gebruiken en beide antwoorden in de client toepassing te verwerken.

Gebruik het bot Framework **[Dispatch cli](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** -hulp programma om een proces te bouwen waarmee u met beide services kunt werken. Met dit hulp programma bouwt u een top-LUIS-app van intenties die worden verzonden tussen LUIS en QnA Maker als onderliggende apps. Meer [informatie](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0) over de integratie met LUIS, QnA maker en bot Framework.

Gebruik het voor beeld van bot Builder, **NLP met verzen ding**, in [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) of [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), om dit type chat-bot te implementeren.

## <a name="best-practices"></a>Aanbevolen procedures

Aanbevolen procedures voor elke service implementeren:

* Best practices voor [Luis](../luis/luis-concept-best-practices.md)
* Aanbevolen procedures [QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Zie ook

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Verzending CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Voor beelden van bot-Framework](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot service](/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Azure bot-emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot Framework Web Chat](https://github.com/microsoft/BotFramework-WebChat)