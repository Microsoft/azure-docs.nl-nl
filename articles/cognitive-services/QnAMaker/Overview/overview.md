---
title: Wat is de QnA Maker-service?
description: QnA Maker is een cloud-NLP-service voor het eenvoudig maken van een natuurlijke gesprekslaag over uw gegevens. Het kan worden gebruikt om het meest geschikte antwoord te vinden voor een bepaalde invoer in natuurlijke taal, vanuit uw aangepaste Knowledge Base (KB) aan informatie.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 01/22/2021
ms.custom: cog-serv-seo-aug-2020
keywords: qna maker, chatbots met weinig code, gesprekken met meerdere paden
ms.openlocfilehash: 16f30801d52aa46184c4eb394f3d1cfebecd4be6
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98700712"
---
# <a name="what-is-qna-maker"></a>Wat is QnA Maker?

QnA Maker is een cloud-NLP-service (Natuurlijke taalverwerking) waarmee u een natuurlijke gesprekslaag kunt maken over uw gegevens. Het wordt gebruikt om het meest geschikte antwoord te vinden voor invoer van uw aangepaste Knowledge Base (KB) aan informatie.

QnA Maker wordt vaak gebruikt voor het bouwen van pratende clienttoepassingen, waaronder social media-toepassingen, chatbots en desktoptoepassingen met spraakfunctionaliteit.

## <a name="when-to-use-qna-maker"></a>Wanneer kunt u QnA Maker gebruiken

* **Wanneer u statische informatie hebt** - Gebruik QnA Maker als u statische informatie hebt in uw Knowledge Base van antwoorden. Deze Knowledge Base is aangepast aan uw behoeften, die u hebt opgebouwd met documenten zoals [PDF’s en URL’s](../index.yml).
* **Wanneer u hetzelfde antwoord wilt geven op een aanvraag, vraag of opdracht** - wanneer verschillende gebruikers dezelfde vraag verzenden, wordt hetzelfde antwoord geretourneerd.
* **Als u statische informatie wilt filteren op basis van metagegevens** - [metagegevens](../how-to/metadata-generateanswer-usage.md)-tags toevoegen om extra filteropties te bieden die relevant zijn voor de gebruikers van uw clienttoepassing en de informatie. Algemene metagegevens bevatten [smalltalk](../how-to/chit-chat-knowledge-base.md), inhoudstype of indeling, doel van de inhoud en de recentheid van de inhoud.
* **Wanneer u een bot-gesprek wat statische informatie bevat, wilt beheren**, gebruikt uw Knowledge Base de gesprekstekst of opdracht van de gebruiker en beantwoordt deze. Als het antwoord deel uitmaakt van een vooraf bepaalde gespreksstroom die in uw Knowledge Base wordt weergegeven met [context met meerdere paden](../how-to/multiturn-conversation.md), kan de bot deze stroom eenvoudig leveren.

## <a name="what-is-a-knowledge-base"></a>Wat is een Knowledge Base?

QnA Maker [importeert uw inhoud](../index.yml) in een Knowledge Base met vraag- en antwoordparen. Het importeerproces extraheert informatie over de relatie tussen de onderdelen van uw gestructureerde en semi-gestructureerde inhoud om relaties tussen de vraag- en antwoordparen te suggereren. U kunt deze vraag- en antwoordparen bewerken of nieuwe paren toevoegen.

De inhoud van het vraag- en antwoordpaar bevat:
* Alle andere vormen van de vraag
* Metagegevens-tags die worden gebruikt om antwoordkeuzes te filteren tijdens de zoekopdracht
* Vervolgprompts om de verfijning van de zoekopdracht voor te zetten

![Voorbeeld van vraag en antwoord met metagegevens](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Nadat u uw Knowledge Base hebt gepubliceerd, verstuurt een clienttoepassing de vraag van een gebruiker naar uw eindpunt. Uw QnA Maker-service verwerkt de vraag en reageert met het beste antwoord.

## <a name="create-a-chat-bot-programmatically"></a>Programmatisch een chatbot maken

Zodra een Knowledge Base van QnA Maker is gepubliceerd, stuurt een clienttoepassing een vraag naar uw Knowledge Base-eindpunt en ontvangt de resultaten als een JSON-antwoord. Een veelvoorkomende clienttoepassing voor QnA Maker is een chatbot.

![Een vraag stellen aan een bot en antwoord krijgen vanuit de inhoud van de Knowledge Base](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Stap|Bewerking|
|:--|:--|
|1|De clienttoepassing stuurt de _vraag_ van de gebruiker (tekst in hun eigen woorden), ‘Hoe kan ik op een programmatische manier mijn Knowledge Base bijwerken?’ naar uw Knowledge Base-eindpunt.|
|2|QnA Maker maakt gebruik van de getrainde Knowledge Base om het juiste antwoord te geven en eventuele vervolgprompts die kunnen worden gebruikt voor het verfijnen van de zoekactie naar het beste antwoord. QnA Maker retourneert een reactie in JSON-indeling.|
|3|De clienttoepassing maakt gebruik van het JSON-antwoord om beslissingen te nemen over het afhandelen van het gesprek. Deze beslissingen kunnen bestaan uit het tonen van het beste antwoord en het weergeven van meer keuzes om de zoekactie naar het beste antwoord te verfijnen. |
|||

## <a name="build-low-code-chat-bots"></a>Chatbots met weinig code bouwen

De QnA Maker-portal biedt de volledige ontwerpervaring van de Knowledge Base. U kunt documenten in hun huidige vorm importeren in uw Knowledge Base. Deze documenten (zoals een FAQ, de handleiding van een product, een spreadsheet of een webpagina) worden omgezet in vraag- en antwoordparen. Elk paar wordt geanalyseerd op vervolgprompts en verbonden met andere paren. De uiteindelijke _markdown_-indeling biedt ondersteuning voor uitgebreide presentaties, inclusief afbeeldingen en links.

Zodra de Knowledge Base is bewerkt, publiceert u de Knowledge Base naar een werkende [Azure-web-app-bot](https://azure.microsoft.com/services/bot-service/) zonder code te schrijven. Test uw bot in de [Azure Portal](https://portal.azure.com) of download deze en ga verder met ontwikkelen.

## <a name="high-quality-responses-with-layered-ranking"></a>Reacties van hoge kwaliteit met gelaagde classificatie

Het systeem van QnA Maker is een gelaagde classificatiebenadering. De gegevens worden opgeslagen in Azure Search, wat ook fungeert als de eerste classificatielaag. De beste resultaten van Azure Search worden vervolgens door het NLP-herclassificeringsmodel van QnA Maker gehaald om de uiteindelijke resultaten en betrouwbaarheidsscore te produceren.

## <a name="multi-turn-conversations"></a>Gesprekken met meerdere paden

QnA Maker biedt prompts met meerdere paden en actief leren om u te helpen uw basis vraag- en antwoordparen te verbeteren.

**Prompts met meerdere paden** geven u de mogelijkheid om vraag- en antwoordparen met elkaar te verbinden. Met deze verbinding kan de clienttoepassing een antwoord op het hoogste niveau geven en kunnen er meer vragen worden gesteld om de zoekopdracht te verfijnen voor een definitief antwoord.

Nadat de Knowledge Base vragen heeft ontvangen van gebruikers op het gepubliceerde eindpunt, past QnA Maker **actief leren** toe op deze praktijkvragen om wijzigingen in uw Knowledge Base voor te stellen om de kwaliteit te verbeteren.

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling

QnA Maker biedt creatie, training en publicatie samen met samenwerkingsmachtigingen voor integratie in de volledige levenscyclus van de ontwikkeling.

> [!div class="mx-imgBorder"]
> ![Conceptuele afbeelding van de ontwikkelingscyclus](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Een quickstart volgen

We bieden quickstarts in de populairste programmeertalen, die zijn ontworpen om u de basisontwerppatronen te leren en waarmee u in minder dan tien minuten code kunt uitvoeren. Zie de volgende lijst voor de quickstart voor elke functie.

* [Aan de slag met QnA Maker-clientbibliotheek](../quickstarts/quickstart-sdk.md)
* [Aan de slag met QnA Maker-portal](../quickstarts/create-publish-knowledge-base.md)

## <a name="next-steps"></a>Volgende stappen
QnA Maker biedt alles wat u nodig hebt om uw aangepaste Knowledge Base te bouwen, beheren en implementeren.

> [!div class="nextstepaction"]
> [Bekijk de meest recente wijzigingen](../whats-new.md)