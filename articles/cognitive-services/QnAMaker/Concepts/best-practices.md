---
title: Aanbevolen procedures-QnA Maker
description: Gebruik deze aanbevolen procedures om uw Knowledge Base te verbeteren en betere resultaten te bieden aan de eind gebruikers van uw toepassing/chat-bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 77d4a1cad4843578419be54465a06e87834dd186
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104515"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Aanbevolen procedures voor een QnA Maker Knowledge Base

De [Knowledge Base-ontwikkelings levenscyclus](../Concepts/development-lifecycle-knowledge-base.md) helpt u bij het beheren van uw KB van begin tot eind. Gebruik deze aanbevolen procedures om uw Knowledge Base te verbeteren en betere resultaten te bieden aan de eind gebruikers van uw client toepassing of chat-bot.

## <a name="extraction"></a>Extractie

De QnA Maker-service verbetert de algoritmen die QnAs extra heren uit inhoud en breiden de lijst met ondersteunde bestands-en HTML-indelingen voortdurend uit. Volg de [richt lijnen](../index.yml) voor gegevens extractie op basis van uw document type.

Over het algemeen moeten pagina's met veelgestelde vragen zelfstandig zijn en niet worden gecombineerd met andere informatie. Product handleidingen moeten duidelijke kopteksten en bij voor keur een index pagina bevatten.

### <a name="configuring-multi-turn"></a>Multi-turn configureren

[Maak uw Knowledge Base](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) met meerdere Schakel extractie ingeschakeld. Als uw Knowledge Base wel of geen vraag hiërarchie ondersteunt, kan deze hiërarchie worden geëxtraheerd uit het document of worden gemaakt nadat het document is geëxtraheerd.

## <a name="creating-good-questions-and-answers"></a>Goede vragen en antwoorden maken

### <a name="good-questions"></a>Goede vragen

De beste vragen zijn eenvoudig. Bekijk het sleutel woord of de woord groep voor elke vraag en maak een eenvoudige vraag voor die sleutel woord of woord groep.

Voeg zoveel alternatieve vragen toe als u nodig hebt, maar behoud de aanpassingen eenvoudig. Het toevoegen van meer woorden of frasen die geen deel uitmaken van het belangrijkste doel van de vraag, helpt niet bij het QnA Maker vinden van een overeenkomst.


### <a name="add-relevant-alternative-questions"></a>Relevante alternatieve vragen toevoegen

Uw gebruiker kan vragen invoeren met een spreek stijl `How do I add a toner cartridge to my printer?` of een zoek opdracht op tref woorden, zoals `toner cartridge` . De Knowledge Base moet beide stijlen van vragen hebben om het beste antwoord correct te kunnen retour neren. Als u niet zeker weet welke tref woorden een klant invoert, gebruikt u Application Insights gegevens voor het analyseren van query's.

### <a name="good-answers"></a>Goede antwoorden

De beste antwoorden zijn eenvoudige antwoorden, maar niet te eenvoudig. Gebruik geen antwoorden zoals `yes` en `no` . Als uw antwoord moet worden gekoppeld aan andere bronnen of als u een uitgebreide ervaring met media en koppelingen wilt bieden, gebruikt u [labels voor meta gegevens](../how-to/edit-knowledge-base.md#add-metadata) om onderscheid te maken tussen antwoorden. vervolgens [verzendt u de query](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) met de tags voor meta gegevens in de `strictFilters` eigenschap om de juiste antwoord versie te verkrijgen.

|Antwoord|Follup vragen|
|--|--|
|Schakel de Surface-laptop uit met de knop aan/uit op het toetsen bord.|* Toetsen combinaties die in de slaap stand worden gezet, afgesloten en opnieuw starten.<br>* Een oppervlakte laptop hard opstarten<br>* Het BIOS voor een Surface-laptop wijzigen<br>* Verschillen tussen de slaap stand, afsluiten en opnieuw opstarten|
|Klanten service is 24 uur per dag beschikbaar via telefoon-, Skype-en SMS-berichten.|* Neem contact op met de verkoop gegevens.<br> * Kantoor-en opslag locaties en-uren voor een persoonlijk bezoek.<br> * Accessoires voor een Surface-laptop.|

## <a name="chit-chat"></a>Chit-Chat
Voeg Chit-Chat toe aan uw bot om uw bot meer praten en aantrekkelijker te maken, met weinig moeite. U kunt eenvoudig Chit toevoegen van vooraf gedefinieerde persoonlijke gegevens wanneer u uw KB maakt en deze op elk gewenst moment wijzigen. Meer informatie over het [toevoegen van Chit-Chat aan uw KB](../How-To/chit-chat-knowledge-base.md).

Chit: chatten wordt ondersteund in [veel talen](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Een persoonlijkheid kiezen
Chit-Chat wordt ondersteund voor verschillende vooraf gedefinieerde persoonlijke voors:

|Persoonlijkheid |QnA Maker Gegevenssetbestand |
|---------|-----|
|Professional |[qna_chitchat_professional. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Weergave |[qna_chitchat_friendly. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Enthousiast |[qna_chitchat_enthusiastic. TSV](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

De antwoorden variëren van formeel naar informele en irreverent. U moet de persoonlijkheid selecteren die het dichtst in de buurt is uitgelijnd met de Toon die u voor uw bot wilt. U kunt de [gegevens sets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)bekijken en er een selecteren die als basis voor uw bot fungeert en vervolgens de antwoorden aanpassen.

### <a name="edit-bot-specific-questions"></a>Bot-specifieke vragen bewerken
Er zijn bepaalde bot-specifieke vragen die deel uitmaken van de Chit-Chat gegevens verzameling en die zijn ingevuld met algemene antwoorden. Wijzig deze antwoorden zodat ze het beste overeenkomen met de details van uw bot.

We raden u aan om de volgende Chit-Chat QnAs specifieker te maken:

* Wat is uw rol?
* Wat kunt u doen?
* How old are you?
* Wie heeft u gemaakt?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Aangepaste Chit-Chat toevoegen met een meta gegevens code

Als u uw eigen Chit-Chat QnA-paren toevoegt, moet u ervoor zorgen dat u meta gegevens toevoegt zodat deze antwoorden worden geretourneerd. De naam/waarde-paar van de meta gegevens is `editorial:chitchat` .

## <a name="searching-for-answers"></a>Antwoorden zoeken

GenerateAnswer-API maakt gebruik van beide vragen en het antwoord op zoek naar de beste antwoorden op de query van een gebruiker.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Vragen alleen zoeken wanneer antwoord niet relevant is

Gebruik de [`RankerType=QuestionOnly`](#choosing-ranker-type) Als u geen antwoorden wilt zoeken.

Een voor beeld hiervan is wanneer de Knowledge Base een catalogus van acroniemen is als vragen met hun volledige formulier als antwoord. De waarde van het antwoord kan niet helpen bij het zoeken naar het juiste antwoord.

## <a name="rankingscoring"></a>Rang schikking/Score
Zorg ervoor dat u het beste gebruik maakt van de classificatie functies die QnA Maker ondersteunt. Als u dit doet, wordt de kans dat een bepaalde gebruikers query wordt beantwoord met een juiste reactie verbeterd.

### <a name="choosing-a-threshold"></a>Een drempel waarde kiezen

De standaard [betrouwbaarheids Score](confidence-score.md) die wordt gebruikt als drempel waarde is 0, maar u kunt [de drempel waarde](confidence-score.md#set-threshold) voor uw KB wijzigen op basis van uw behoeften. Omdat elke KB afwijkt, moet u de drempel waarde testen en kiezen die het meest geschikt is voor uw KB.

### <a name="choosing-ranker-type"></a>Type rangorde kiezen
QnA Maker zoekt standaard naar vragen en antwoorden. Als u alleen vragen wilt doorzoeken, gebruikt u de `RankerType=QuestionOnly` in de hoofd tekst van de GenerateAnswer-aanvraag om een antwoord te genereren.

### <a name="add-alternate-questions"></a>Alternatieve vragen toevoegen
[Alternatieve vragen](../How-To/edit-knowledge-base.md) verbeteren de kans op een overeenkomst met een gebruikers query. Alternatieve vragen zijn handig wanneer er meerdere manieren zijn waarop dezelfde vraag kan worden gesteld. Dit kan wijzigingen bevatten in de sentence-structuur en de woord stijl.

|Oorspronkelijke query|Alternatieve query's|Wijziging|
|--|--|--|
|Is parkeer plaatsen beschikbaar?|Hebt u een auto Park?|Sentence-structuur|
 |Hallo|Yo<br>Dat is alles.|woord stijl of slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Tags voor meta gegevens gebruiken voor het filteren van vragen en antwoorden

[Meta gegevens](../How-To/edit-knowledge-base.md) voegen de mogelijkheid toe om een client toepassing te laten weten dat deze niet alle antwoorden mag hebben, maar in plaats daarvan de resultaten van een gebruikers query te beperken op basis van meta gegevenslabels. Het antwoord van de Knowledge Base kan variëren op basis van de meta gegevens code, zelfs als de query hetzelfde is. Bijvoorbeeld *' waar bevindt zich parkeren '* kan een ander antwoord hebben als de locatie van de restaurant vertakking anders is, dat wil zeggen, de meta gegevens zijn *Locatie: Seattle* versus *Locatie: Redmond*.

### <a name="use-synonyms"></a>Synoniemen gebruiken
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)
Hoewel er enige ondersteuning is voor synoniemen in de Engelse taal, gebruikt u hoofdletter gevoelige woord wijzigingen via de [aanpassings-API](/rest/api/cognitiveservices/qnamaker/alterations/replace) om synoniemen toe te voegen aan tref woorden die verschillende vormen hebben. Synoniemen worden toegevoegd op het QnA Maker service niveau en worden **gedeeld door alle kennis grondslagen in de service**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
Hoewel er enige ondersteuning is voor synoniemen in de Engelse taal, gebruikt u hoofdletter gevoelige woord wijzigingen via de [aanpassings-API](/rest/api/cognitiveservices/qnamaker/alterations/replace) om synoniemen toe te voegen aan tref woorden die verschillende vormen hebben. Synoniemen in QnA Maker Managed (preview) worden **toegevoegd per Knowledge Base**.

|Oorspronkelijk woord|Synoniemen|
|--|--|
|ze|kopen<br>net-bankieren<br>net bankieren|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Afzonderlijke woorden gebruiken om vragen te onderscheiden
Het classificatie algoritme van QnA Maker, dat overeenkomt met een gebruikers query met een vraag in de Knowledge Base, werkt het beste als elke vraag een andere behoefte behandelt. Herhaling van dezelfde woord set tussen vragen vermindert de kans dat het juiste antwoord wordt gekozen voor een bepaalde gebruikers query met deze woorden.

U kunt bijvoorbeeld twee afzonderlijke QnAs hebben met de volgende vragen:

|QnAs|
|--|
|waar bevindt zich de parkeer *locatie*|
|waar is de ATM- *locatie*|

Omdat deze twee QnAs worden aangeduid met zeer vergelijk bare woorden, kan deze soort gelijke scores worden veroorzaakt door veel gebruikers query's die zijn aangeduid als  *' waar is de `<x>` locatie '*. Probeer in plaats daarvan duidelijk te onderscheiden van query's zoals  *' waar is de parkeer partij '* en *' waar is de ATM '*, door woorden als ' locatie ' te vermijden, zodat u veel vragen in uw KB kunt vinden.

## <a name="collaborate"></a>Samenwerken
Met QnA Maker kunnen gebruikers samen werken aan een Knowledge Base. Gebruikers moeten toegang hebben tot de resource groep Azure QnA Maker om toegang te krijgen tot de kennis bases. Sommige organisaties willen het bewerken en onderhouden van de Knowledge Base mogelijk maken en nog steeds toegang tot hun Azure-resources kunnen beveiligen. Dit model voor redacteur-goed keuring wordt uitgevoerd door twee identieke [QnA Maker Services](../How-to/set-up-qnamaker-service-azure.md) in verschillende abonnementen in te stellen en er een te selecteren voor de bewerking voor het bewerken van de test cyclus. Zodra het testen is voltooid, wordt de inhoud van de Knowledge Base met een [import-export](../Tutorials/migrate-knowledge-base.md) proces overgebracht naar de QnA Maker-service van de goed keurder die de Knowledge Base uiteindelijk gaat publiceren en het eind punt bijwerkt.



## <a name="active-learning"></a>Actief leren

Met het [actieve leer proces](../How-to/use-active-learning.md) wordt geadviseerd om alternatieve vragen te stellen wanneer het een breed scala van kwaliteit en aantal op gebruikers gebaseerde query's heeft. Het is belang rijk om gebruikers query's van client toepassingen toe te staan om deel te nemen aan de feedback-lus van het actieve Learning zonder dat de censoren. Zodra vragen worden voorgesteld in de QnA Maker Portal, kunt u **[filteren op suggesties](../How-To/improve-knowledge-base.md)** en deze suggesties bekijken en accepteren of afwijzen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Knowledge Base bewerken](../How-to/edit-knowledge-base.md)
