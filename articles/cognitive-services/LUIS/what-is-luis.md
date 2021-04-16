---
title: overzicht Language Understanding (LUIS)
description: 'Language Understanding (LUIS): een API-cloudservice waarbij machine learning wordt toegepast op natuurlijke spreektaal om betekenissen te voorspellen en informatie te extraheren.'
keywords: Azure, kunstmatige intelligentie, ai, natuurlijke taalverwerking, nlp, natuurlijk taalbegrip, nlu, LUIS, AI met gespreksfuncties, AI-chatbot, nlp AI, Azure LUIS
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503763"
---
# <a name="what-is-language-understanding-luis"></a>Wat is Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) is een cloudgebaseerde AI met gespreksfuncties die aangepaste machine-learningintelligentie toepast op de natuurlijke spreektaal van een gebruiker in tekstvorm om daar de algemene betekenis van te voorspellen en relevante detailinformatie uit te destilleren. LUIS biedt toegang via de [aangepaste portal,](https://www.luis.ai) [API's][endpoint-apis] en [SDK-clientbibliotheken.](client-libraries-rest-api.md)

Volg voor de eerste keer dat gebruikers zich aanmelden bij de [](luis-get-started-create-app.md) [LUIS-portal](sign-in-luis-portal.md "aanmelden bij de LUIS-portal") Om aan de slag te gaan, kunt u vooraf gebouwde domein-apps van LUIS proberen of uw [app bouwen.](get-started-portal-build-app.md)

Deze documentatie bevat de volgende artikeltypen:  

* [**Quickstarts zijn**](luis-get-started-create-app.md) aan de slag-instructies om u te begeleiden bij het indienen van aanvragen bij de service.  
* [**Instructiegidsen**](luis-how-to-start-new-app.md) bevatten instructies voor het gebruik van de service op specifiekere of aangepaste manieren.  
* [**Concepten**](artificial-intelligence.md) bieden uitgebreide uitleg over de servicefunctionaliteit en -functies.  
* [**Zelfstudies**](tutorial-intents-only.md) zijn langere handleidingen die laten zien hoe u de service kunt gebruiken als onderdeel van bredere bedrijfsoplossingen.  

## <a name="what-does-luis-offer"></a>Wat biedt LUIS? 

* **Eenvoud:** LUIS offloadt u van de noodzaak van in-house AI-expertise of van eerdere machine learning kennis. Met slechts een paar klikken kunt u uw eigen conversationele AI-toepassing bouwen. U kunt uw aangepaste toepassing bouwen door een van onze [quickstarts](get-started-portal-build-app.md)te volgen of u kunt een van onze vooraf gemaakte [domein-apps](luis-get-started-create-app.md) gebruiken.
* **Beveiliging, privacy en naleving:** LUIS wordt mogelijk gemaakt door de Azure-infrastructuur en biedt beveiliging, privacy en naleving van ondernemingsklassen. Uw gegevens blijven van u; u kunt uw gegevens op elk moment verwijderen. Uw gegevens worden versleuteld terwijl ze in de opslag zijn. Meer informatie hierover vindt u [hier](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy).
* **Integratie:** integreer uw LUIS-app eenvoudig met andere Microsoft-services, zoals [Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)en [Speech-service.](../Speech-Service/quickstarts/intent-recognition.md)


## <a name="luis-scenarios"></a>LUIS-scenario's
* [Een zakelijke](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot)gespreksbot bouwen: in deze referentiearchitectuur wordt beschreven hoe u een zakelijke conversationele bot (chatbot) bouwt met behulp van de Azure Bot Framework.
* [Commerce Chatbot:](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot)samen stellen de Azure Bot Service en Language Understanding-service ontwikkelaars in staat om gespreksinterfaces te maken voor verschillende scenario's zoals bankieren, reizen en entertainment.
* [IoT-apparaten](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant)beheren met behulp van een spraakassistent: maak naadloze gespreksinterfaces met al uw apparaten die via internet toegankelijk zijn, van uw aangesloten televisie of apparaat tot apparaten in een aangesloten elektriciteitscentrale.


## <a name="application-development-life-cycle"></a>Levenscyclus van toepassingsontwikkeling

![Levenscyclus van luis-app-ontwikkeling](./media/luis-overview/luis-dev-lifecycle.png "Levenscyclus van LUIS-toepassingsdevelooment")

-   **Plannen:** identificeer de scenario's waarin gebruikers uw toepassing kunnen gebruiken. De acties en relevante informatie definiëren die moet worden herkend.
-   **Bouwen:** gebruik uw ontwerpresource om uw app te ontwikkelen. Begin met het definiëren [van intenties](luis-concept-intent.md) [en entiteiten.](luis-concept-entity-types.md) Voeg vervolgens [trainings-utterances toe](luis-concept-utterance.md) voor elke intentie. 
-   **Testen en verbeteren:** begin met het testen van uw model met andere utterances om een idee te krijgen van hoe de app zich gedraagt en u kunt bepalen of er verbetering nodig is. U kunt uw toepassing verbeteren door deze [best practices te volgen.](luis-concept-best-practices.md) 
-   **Publiceren:** implementeer uw app voor voorspelling en bevraag het eindpunt met behulp van uw voorspellingsresource. Meer informatie over ontwerp- en voorspellingsresources kunt u [hier vinden.](luis-how-to-azure-subscription.md#luis-resources) 
-   **Verbinding** maken: maak verbinding met andere services, zoals [Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)en [Speech-service.](../Speech-Service/quickstarts/intent-recognition.md) 
-   **Verfijnen:** [Eindpunt-utterances controleren om](luis-concept-review-endpoint-utterances.md) uw toepassing te verbeteren met praktijkvoorbeelden

Meer informatie over het plannen en bouwen van uw [toepassing kunt u hier vinden.](luis-how-plan-your-app.md)

## <a name="next-steps"></a>Volgende stappen

* [Wat is er nieuw](whats-new.md "Nieuw") bij de service en de documentatie
* [Een LUIS-app bouwen](tutorial-intents-only.md)
* [API-naslaginformatie][endpoint-apis]
* [Aanbevolen procedures](luis-concept-best-practices.md)
* [Resources voor ontwikkelaars](developer-reference-resource.md "Bronnen voor ontwikkelaars") voor LUIS.
* [Plan uw app](luis-how-plan-your-app.md "Uw app plannen") met [intenties](luis-concept-intent.md "intents") en [entiteiten](luis-concept-entity-types.md "entiteiten").

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
