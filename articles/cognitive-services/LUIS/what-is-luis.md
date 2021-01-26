---
title: Wat is Language Understanding (LUIS)?
description: 'Language Understanding (LUIS): een API-cloudservice waarbij machine learning wordt toegepast op natuurlijke spreektaal om betekenissen te voorspellen en informatie te extraheren.'
keywords: Azure, kunstmatige intelligentie, ai, natuurlijke taalverwerking, nlp, natuurlijk taalbegrip, nlu, LUIS, AI met gespreksfuncties, AI-chatbot, nlp AI, Azure LUIS
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/23/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f21a2cc8218dde8b0cf7203a1fcb1f5cfb9424a2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790864"
---
# <a name="what-is-language-understanding-luis"></a>Wat is Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) is een cloudgebaseerde AI met gespreksfuncties die aangepaste machine-learningintelligentie toepast op de natuurlijke spreektaal van een gebruiker in tekstvorm om daar de algemene betekenis van te voorspellen en relevante detailinformatie uit te destilleren.

Een clienttoepassing voor LUIS is een gesprekstoepassing waarin een gebruiker in natuurlijke taal communiceert om een taak te voltooien. Voorbeelden van clienttoepassingen zijn sociale media-apps, AI chatbots en met spraak bestuurde bureaubladtoepassingen.

![Conceptuele afbeelding van 3 clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Conceptuele afbeelding van 3 clienttoepassingen die werken met Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>LUIS gebruiken in een chatbot

<a name="Accessing-LUIS"></a>

Wanneer de LUIS-app is gepubliceerd, worden uitingen (tekst) door een clienttoepassing verzonden naar de eindpunt-[API][endpoint-apis] voor verwerking van natuurlijke taal in LUIS en worden de resultaten ontvangen als JSON-antwoorden. Een algemene clienttoepassing voor LUIS is een chatbot.


![Conceptuele afbeelding van LUIS die met Chat bot werkt om gebruikerstekst te voorspellen met begrip van natuurlijk taalgebruik (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Conceptuele afbeelding van LUIS die met Chat bot werkt om gebruikerstekst te voorspellen met begrip van natuurlijk taalgebruik (NLP)")

|Stap|Bewerking|
|:--|:--|
|1|De clienttoepassing verzendt een _uiting_ van de gebruiker (tekst in eigen woorden), zoals "Ik wil mijn contactpersoon van de afdeling HR bellen", naar het eindpunt van LUIS als een HTTP-aanvraag.|
|2|Met LUIS kunt u uw eigen taalmodellen maken om intelligentie toe te voegen aan uw toepassing. Taalmodellen op basis van machine learning nemen de ongestructureerde invoertekst en retourneren een antwoord in JSON-indeling met een top-intentie, `HRContact`. Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Ook andere gegevens, zoals de _Contact Type_, kunnen worden geëxtraheerd.|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen voor het afhandelen van de aanvragen van de gebruiker. Deze beslissingen kunnen zijn gebaseerd op een beslissingsstructuur in de code van het botframework en aanroepen naar andere services. |

De LUIS-app levert intelligence zodat er slimme keuzen kunnen worden gemaakt in de clienttoepassing. Die keuzen worden niet gemaakt in LUIS.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>NLU (natuurlijk taalbegrip)

[LUIS biedt kunstmatige intelligentie (AI)](artificial-intelligence.md "LUIS biedt kunstmatige intelligentie (AI)") in de vorm van NLU, een subset van AI voor natuurlijke taalverwerking.

Uw LUIS-app bevat een domein-specifiek model voor natuurlijke taal. U kunt de LUIS-app starten met een vooraf gedefinieerd domeinmodel, uw eigen model bouwen of onderdelen van een vooraf gedefinieerd domein combineren met uw eigen aangepaste gegevens.

* **Vooraf gedefinieerd model** LUIS bevat veel vooraf gedefinieerde domeinmodellen, inclusief intenties, uitingen en vooraf gedefinieerde entiteiten. U kunt de vooraf gedefinieerde entiteiten ook gebruiken zonder de intenties en uitingen van het vooraf gedefinieerde model. [Vooraf gedefinieerde domeinmodellen](./howto-add-prebuilt-models.md "Modellen voor vooraf gemaakt domein") bevatten alles wat u nodig hebt om LUIS snel te kunnen gaan gebruiken.

* **Aangepast model** LUIS biedt u een aantal manieren om uw eigen aangepaste modellen e identificeren, inclusief intenties en entiteiten. Entiteiten zijn machine learning-entiteiten, specifieke of letterlijke entiteiten, en een combinatie van machine learning en letterlijk.

Leer meer over [NLP AI](artificial-intelligence.md "NLP") en het specifieke gebied van NLU voor LUIS.

## <a name="step-1-design-and-build-your-model"></a>Stap 1: Uw model ontwerpen en bouwen

Ontwerp uw model met categorieën gebruikersintenties, ook wel **[intenties](luis-concept-intent.md "intents")** genoemd. Voor elke intentie zijn voorbeelden van **[uitingen](luis-concept-utterance.md "utterances")** van gebruikers vereist. Elke uiting kan verschillende gegevens bevatten. Deze moeten worden geëxtraheerd met [machine learning-entiteiten](luis-concept-entity-types.md#effective-machine-learned-entities "machine learning-entiteiten").

|Voorbeeld van een uiting van een gebruiker|Intentie|Geëxtraheerde gegevens|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|13:00 uur, Bob|

U bouwt het model op met de [ontwerp](https://go.microsoft.com/fwlink/?linkid=2092087 "authoring")-API's of met de **[LUIS-portal](https://www.luis.ai "LUIS-portal")**, of beide. Leer meer over bouwen met behulp van de [portal](get-started-portal-build-app.md "portal") en de [SDK-clientbibliotheken](./client-libraries-rest-api.md?pivots=rest-api "SDK-clientbibliotheken").

## <a name="step-2-get-the-query-prediction"></a>Stap 2: De queryvoorspelling ophalen

Nadat het model van uw app is getraind en gepubliceerd naar het eindpunt, worden uitingen door een clienttoepassing (zoals een chatbot) verzonden naar de voorspellende [eindpunt](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint")-API. De API past het model toe op de uiting om deze te analyseren en reageert met de voorspellingsresultaten in JSON-indeling.

Het minimale JSON-antwoord van het eindpunt bevat de query-uiting en de belangrijkste score-intentie. Ook andere gegevens kunnen worden geëxtraheerd, zoals de volgende **Contact Type**-entiteit en het algemene gevoel.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Stap 3: Voorspellingen van het model verbeteren

Nadat uw LUIS-app is gepubliceerd en uitingen van echte gebruikers ontvangt, zorgt LUIS voor [actief leren](luis-concept-review-endpoint-utterances.md "actief leren") van eindpuntuitingen om de nauwkeurigheid van de voorspellingen te verbeteren. Beoordeel deze suggesties als onderdeel van uw gewone onderhoud in de levenscyclus van de ontwikkeling.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Levenscyclus van ontwikkeling en hulpprogramma's
LUIS biedt hulpprogramma's, versiebeheer en samenwerking met andere LUIS-auteurs voor integratie in de volledige [ontwikkelingscyclus](luis-concept-app-iteration.md "levenscyclus van ontwikkeling").

Language Understanding (LUIS) kan als REST-API worden geïntegreerd met elk product, elke service en elk framework met een HTTP-aanvraag. LUIS biedt voor diverse belangrijke programmeertalen ook clientbibliotheken (SDK's). Lees meer over de aangeboden [resources voor ontwikkelaars](developer-reference-resource.md "bronnen voor ontwikkelaars").

Hulpprogramma's om LUIS snel en eenvoudig met een bot te gebruiken:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI") Het NPM-pakket biedt creatie en voorspelling met een zelfstandig opdrachtregelhulpprogramma of een import.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") LUISGen is een hulpprogramma voor het genereren van sterk getypeerde broncode voor C# en TypeScript op basis van een geëxporteerd LUIS-model.
* [Dispatcher](https://aka.ms/dispatch-tool "Dispatch") - met behulp van het verdeelprogrammamodel kunnen verschillende LUIS- en QnA Maker-apps worden gebruikt vanuit een bovenliggende app.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown is een opdrachtregelprogramma dat helpt om taalmodellen voor uw bot te beheren.

## <a name="integrate-with-a-bot"></a>Integratie met een bot

Gebruik de [Azure Bot-service](/azure/bot-service/ "Azure Bot Service") met het [Microsoft Bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") om een chatbot te bouwen en te implementeren. Gebruik het grafische interfaceprogramma [Composer](/composer/ "Composer") of [actieve voorbeelden van bots](https://github.com/microsoft/BotBuilder-Samples "werkende botvoorbeelden") die voor de belangrijkste botscenario's zijn ontworpen, om te ontwerpen en te ontwikkelen.

## <a name="integrate-with-other-cognitive-services"></a>Integreren met andere Cognitive Services

Andere cognitieve services die kunnen worden gebruikt in combinatie met LUIS:
* Met [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") kunnen verschillende soorten tekst worden gecombineerd in een kennisdatabase met vragen en antwoorden.
* [Spraakservice](../Speech-Service/overview.md "Speech Service") - hiermee kunt u gesproken taalaanvragen converteren naar tekst.

LUIS biedt functionaliteit van Text Analytics als onderdeel van uw bestaande LUIS-resources. Deze functionaliteit biedt onder andere [stemmingsanalyse](luis-how-to-publish-app.md#configuring-publish-settings "sentimentanalyse") en [extractie van sleutelwoorden](luis-reference-prebuilt-keyphrase.md "sleuteltermextractie") via de vooraf gebouwde keyPhrase-entiteit.

## <a name="learn-with-the-quickstarts"></a>Leren met behulp van quickstarts

Gebruik de praktische quickstarts in de [portal](get-started-portal-build-app.md "portal") en de [SDK-clientbibliotheken](./client-libraries-rest-api.md?pivots=rest-api "SDK-clientbibliotheken") om meer te leren over LUIS.


## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

[Gebruik LUIS-containers](luis-container-howto.md) om API-functies on-premises te implementeren. Deze Docker-containers stellen u in staat om de service dichter bij uw gegevens te brengen voor naleving, beveiliging en andere operationele redenen.

## <a name="next-steps"></a>Volgende stappen

* [Wat is er nieuw](whats-new.md "Nieuwe functies") bij de service en de documentatie
* [Plan uw app](luis-how-plan-your-app.md "Uw app plannen") met [intenties](luis-concept-intent.md "intents") en [entiteiten](luis-concept-entity-types.md "entiteiten").
* [Voer een query uit op het voorspellingseindpunt](luis-get-started-get-intent-from-browser.md "Voer een query uit op het voorspellingseindpunt").
* [Resources voor ontwikkelaars](developer-reference-resource.md "Bronnen voor ontwikkelaars") voor LUIS.

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/