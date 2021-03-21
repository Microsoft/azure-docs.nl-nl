---
title: Uw app plannen-QnA Maker
description: Meer informatie over het plannen van uw QnA Maker-app. Begrijp hoe QnA Maker werkt en communiceert met andere Azure-Services en enkele Knowledge Base-concepten.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e20679c3999f7ece1f6d3ed47a241cfd9dab9236
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214742"
---
# <a name="plan-your-qna-maker-app"></a>Uw QnA Maker-app plannen

Als u uw QnA Maker-app wilt plannen, moet u weten hoe QnA Maker werkt en samenwerkt met andere Azure-Services. U moet ook een solide begrijpt van Knowledge Base-concepten hebben.

## <a name="azure-resources"></a>Azure-resources

Elke [Azure-resource](azure-resources.md#resource-purposes) die is gemaakt met QnA Maker heeft een specifiek doel. Elke resource heeft zijn eigen doel, limieten en [prijs categorie](azure-resources.md#pricing-tier-considerations). Het is belang rijk dat u de functie van deze resources begrijpt, zodat u die kennis in uw plannings proces kunt gebruiken.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

| Resource | Doel |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) resource | Voor spelling en query's ontwerpen |
| [Cognitive Search](azure-resources.md#cognitive-search-resource) resource | Gegevens opslag en zoeken |
| [Resource en app-plan service resource app service](azure-resources.md#app-service-and-app-service-plan) | Eindpunt queryvoorspelling |
| [Application Insights](azure-resources.md#application-insights) resource | Telemetrie voor spelling query |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

| Resource | Doel |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) resource | Ontwerpen, query Voorspellings eindpunt en telemetrie|
| [Cognitive Search](azure-resources.md#cognitive-search-resource) resource | Gegevens opslag en zoeken |

---
### <a name="resource-planning"></a>Resource planning

De gratis laag, `F0` , van elke resource werkt en kan zowel de ontwerp-als query Voorspellings ervaring bieden. U kunt deze laag gebruiken om te leren ontwerpen en voor spellingen van query's. Wanneer u naar een productie-of Live scenario gaat, moet u de resource selectie opnieuw evalueren.

#### <a name="qna-maker-resource"></a>QnA Maker resource

Eén QnA Maker resource kan meerdere Knowledge bases hosten. Het aantal kennis grondslagen wordt bepaald door het aantal ondersteunde indexen van de Cognitive Search prijs categorie. Meer informatie over de [relatie tussen indexen en kennis bases](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Grootte van kennis basis en door Voer

Wanneer u een echte app bouwt, moet u voldoende resources plannen voor de grootte van uw kennis basis en voor de verwachte aanvragen voor de voor spelling van query's.

Een Knowledge Base-grootte wordt bepaald door:
* Limieten voor de prijs categorie voor [Cognitive Search resources](../../../search/search-limits-quotas-capacity.md)
* [QnA Maker limieten](../limits.md)

De aanvraag voor de voor spelling van de Knowledge Base-query wordt bepaald door het web-app-abonnement en de web-app. Raadpleeg de [Aanbevolen instellingen](azure-resources.md#recommended-settings) om uw prijs categorie te plannen.

### <a name="resource-sharing"></a>Delen van resources

Als u al een aantal van deze resources in gebruik hebt, kunt u overwegen resources te delen. Bekijk welke resources [kunnen worden gedeeld](azure-resources.md#share-services-with-qna-maker) met het memorandum dat resource deling een geavanceerd scenario is.

Alle Knowledge bases die zijn gemaakt in dezelfde QnA Maker resource delen hetzelfde **test** query Voorspellings eindpunt.

### <a name="understand-the-impact-of-resource-selection"></a>Meer informatie over de gevolgen van het selecteren van resources

De juiste resource selectie betekent dat uw Knowledge Base antwoorden op query voorspellingen heeft beantwoord.

Als uw Knowledge Base niet naar behoren werkt, is het meestal een probleem met een onjuist resource beheer.

Voor een onjuiste resource selectie moet worden onderzocht om te bepalen welke [resource moet worden gewijzigd](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Kennis bases

Een kennis database is rechtstreeks verbonden met de QnA Maker resource. Het bevat de paren van de vraag en het antwoord (QnA) die worden gebruikt voor het beantwoorden van aanvragen voor de voor spelling van query's.

### <a name="language-considerations"></a>Taal overwegingen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

De eerste Knowledge Base die is gemaakt op uw QnA Maker resource stelt de taal voor de resource in. U kunt slechts één taal voor een QnA Maker bron hebben.

U kunt uw QnA Maker resources structureren per taal of u kunt [Translator](../../translator/translator-info-overview.md) gebruiken om een query van een andere taal te wijzigen in de taal van de Knowledge Base voordat u de query naar het eind punt voor query voorspelling verzendt.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

U kunt nu kennissen hebben in verschillende talen binnen dezelfde QnA Maker resource. Wanneer u de eerste Knowledge Base maakt, kunt u kiezen of u de resource wilt gebruiken voor Knowledge bases in één taal of in meerdere talen.

![QnA Maker Managed (preview) multi-Knowledge Base selecteren](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

> [!NOTE]
> Als u taal instellingen per Knowledge Base inschakelt, kunt u niet zoveel kennis grondslagen in uw QnA Maker-resource maken. Voor [meer informatie over de beperkingen voor taal instellingen](./azure-resources.md).

---

### <a name="ingest-data-sources"></a>Gegevens bronnen opnemen

U kunt een van de volgende opgenomen [gegevens bronnen](../Concepts/data-sources-and-content.md) gebruiken om een Knowledge Base te maken:

* Open bare URL
* Persoonlijke share point-URL
* File

Met het opname proces worden [ondersteunde inhouds typen](../reference-document-format-guidelines.md) geconverteerd om te worden geprijsd. Alle verdere bewerking van het *antwoord* geschiedt met prijs verlaging. Nadat u een Knowledge Base hebt gemaakt, kunt u [QnA-paren](question-answer-set.md) in de QnA Maker Portal bewerken met [Rich Text-ontwerp](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Overwegingen voor gegevens indeling

Omdat de uiteindelijke indeling van een QnA-paar wordt geprijsd, is het belang rijk om te begrijpen hoe de [korting wordt geboden](../reference-markdown-format.md).

Gekoppelde installatie kopieën moeten beschikbaar zijn vanaf een open bare URL om te worden weer gegeven in het deel venster test van de QnA Maker portal of in een client toepassing. QnA Maker biedt geen verificatie voor inhoud, met inbegrip van installatie kopieën.

### <a name="bot-personality"></a>Bot-persoonlijkheid

Voeg een bot-persoonlijkheid toe aan uw Knowledge Base met [Chit-Chat](../how-to/chit-chat-knowledge-base.md). Deze persoonlijkheid bevindt zich in de antwoorden die zijn opgenomen in een bepaalde gespreks Toon, zoals *professioneel* en *vriendelijk*. Deze Chit-Chat is een set met de volgende stem, die u kunt toevoegen, bewerken en verwijderen.

Een bot-persoonlijkheid wordt aanbevolen als uw bot verbinding maakt met uw Knowledge Base. U kunt ervoor kiezen om chit te gebruiken in uw Knowledge Base, zelfs als u ook verbinding maakt met andere services, maar u moet nalezen hoe de bot service communiceert om te weten of het juiste architectuur ontwerp is voor uw gebruik.

### <a name="conversation-flow-with-a-knowledge-base"></a>Conversatie stroom met een Knowledge Base

De conversatie stroom begint meestal met een aanhef van een gebruiker, zoals `Hi` of `Hello` . Uw Knowledge Base kan beantwoorden met een algemeen antwoord, zoals `Hi, how can I help you` , en kan ook een selectie van vervolg vragen stellen om de conversatie voort te zetten.

Ontwerp uw gesprek stroom met een lus, zodat een gebruiker weet hoe uw bot moet worden gebruikt en niet wordt afgebroken door de bot in de conversatie. [Opvolgings prompts](../how-to/multiturn-conversation.md) bieden een koppeling tussen QnA-paren, die de conversatie stroom toestaan.

### <a name="authoring-with-collaborators"></a>Ontwerpen met samen werkers

Mede werkers kunnen andere ontwikkel aars zijn die de volledige ontwikkelings stack van de Knowledge Base-toepassing delen of kunnen worden beperkt tot alleen het ontwerpen van de kennis database.

Knowledge Base-ontwerp ondersteunt diverse op rollen gebaseerde toegangs machtigingen die u in de Azure Portal toepast om het bereik van de mogelijkheden van een samen werker te beperken.

## <a name="integration-with-client-applications"></a>Integratie met client toepassingen

De integratie met client toepassingen wordt gerealiseerd door een query naar het runtime-eind punt voor de voor spelling te verzenden. Er wordt een query naar uw specifieke Knowledge Base verzonden met een SDK of op REST gebaseerde aanvraag naar het eind punt van de Web-App van uw QnA Maker.

Als u een client aanvraag correct wilt verifiëren, moet de client toepassing de juiste referenties en de Knowledge Base-ID verzenden. Als u een Azure Bot Service gebruikt, configureert u deze instellingen als onderdeel van de bot-configuratie in de Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Conversatie stroom in een client toepassing

De conversatie stroom in een client toepassing, zoals een Azure-bot, vereist mogelijk functionaliteit voor en na interactie met de Knowledge Base.

Ondersteunt uw client toepassing gesprek stroom door alternatieve manieren te bieden voor het verwerken van vervolg vragen of met inbegrip van Chit-Chit? Als dit het geval is, ontwerpt u deze vroege en zorgt u ervoor dat de query van de client toepassing correct wordt verwerkt door een andere service of wanneer deze naar uw kennis database wordt verzonden.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Verzen ding tussen QnA Maker en Language Understanding (LUIS)

Een client toepassing kan verschillende functies bieden, waarvan er slechts één wordt beantwoord door een Knowledge Base. Andere functies moeten nog steeds inzicht hebben in de tekst van de conversatie en betekenis hiervan ophalen.

Een gemeen schappelijke client toepassings architectuur is om zowel QnA Maker als [Language Understanding (Luis)](../../LUIS/what-is-luis.md) samen te gebruiken. LUIS biedt de tekst classificatie en-extractie voor elke query, met inbegrip van andere services. QnA Maker vindt u antwoorden van uw Knowledge Base.

In een dergelijk scenario met een [gedeelde architectuur](../choose-natural-language-processing-service.md) wordt de [verzen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) ding tussen de twee services uitgevoerd door het verzend programma van bot Framework.

### <a name="active-learning-from-a-client-application"></a>Actief leren vanuit een client toepassing

QnA Maker maakt gebruik van _actief leren_ om uw Knowledge Base te verbeteren door alternatieve vragen te stellen aan een antwoord. De client toepassing is verantwoordelijk voor een deel van dit [actief onderwijs](../How-To/use-active-learning.md). Via gesprek prompts kan de client toepassing bepalen dat de Knowledge Base een antwoord heeft geretourneerd dat niet nuttig is voor de gebruiker en kan een beter antwoord vaststellen. De client toepassing moet die gegevens naar de Knowledge Base verzenden om de kwaliteit van de voor spelling te verbeteren.

### <a name="providing-a-default-answer"></a>Een standaard antwoord opgeven

Als uw Knowledge Base geen antwoord heeft gevonden, wordt het _standaard antwoord_ geretourneerd. Dit antwoord kan worden geconfigureerd op de pagina **instellingen** in de QnA Maker portal of in de [api's](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Dit standaard antwoord wijkt af van het standaard antwoord van Azure bot. U kunt het standaard antwoord voor uw Azure-bot configureren in de Azure Portal als onderdeel van de configuratie-instellingen. Deze wordt geretourneerd wanneer niet aan de Score drempel wordt voldaan.

## <a name="prediction"></a>Voorspelling

De voor spelling is het antwoord van uw Knowledge Base en bevat meer informatie dan alleen het antwoord. Als u een antwoord op een query wilt ontvangen, gebruikt u de [GenerateAnswer-API](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Schommelingen van de Voorspellings Score

Een score kan worden gewijzigd op basis van verschillende factoren:

* Aantal antwoorden dat u hebt aangevraagd als reactie op [GenerateAnswer](query-knowledge-base.md) met `top` eigenschap
* Diverse beschik bare alternatieve vragen
* Filteren op meta gegevens
* De query is verzonden naar `test` of de `production` Knowledge Base

Er is een [rang orde van twee fasen](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
- Cognitive Search-eerste positie. Stel het aantal _antwoorden_ in dat Maxi maal genoeg is en dat de beste antwoorden door Cognitive Search worden geretourneerd en vervolgens worden door gegeven aan de QnA Maker ranker.
- QnA Maker-tweede positie. Pas parametrisatie en machine learning toe om het beste antwoord te bepalen.

### <a name="service-updates"></a>Service-updates

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Pas de [meest recente runtime-updates](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) toe om service-updates automatisch te beheren.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

In QnA Maker Managed (preview) wordt de runtime beheerd door de QnA Maker-service zelf. Daarom zijn service-updates niet van toepassing.

---

### <a name="scaling-throughput-and-resiliency"></a>Schalen, door Voer en tolerantie

Schalen, door Voer en tolerantie worden bepaald door de Azure- [resources](../how-to/set-up-qnamaker-service-azure.md), hun prijs categorieën en de bijbehorende architectuur, zoals [Traffic Manager](../how-to/configure-QnA-Maker-resources.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analytics met Application Insights

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Alle query's voor uw Knowledge Base worden opgeslagen in Application Insights. Gebruik onze [meest voorkomende query's](../how-to/get-analytics-knowledge-base.md) om inzicht te krijgen in uw metrische gegevens.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

In de beheerde implementatie wordt telemetrie aangeboden via de [Azure Monitor-service](../../../azure-monitor/index.yml). Gebruik onze [meest voorkomende query's](../how-to/get-analytics-knowledge-base.md) om inzicht te krijgen in uw metrische gegevens.


---

## <a name="development-lifecycle"></a>Levenscyclus van ontwikkeling

De [ontwikkelings levenscyclus](development-lifecycle-knowledge-base.md) van een Knowledge Base is actief: het bewerken, testen en publiceren van uw kennis database.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Knowledge Base-ontwikkeling van QnA Maker paren

Uw QnA-paren moeten zijn ontworpen en ontwikkeld op basis van het gebruik van uw client toepassing.

Elk paar kan het volgende bevatten:
* Meta gegevens kunnen worden gefilterd bij het uitvoeren van query's zodat u uw QnA-paren kunt labelen met aanvullende informatie over de bron, de inhoud, de indeling en het doel van uw gegevens.
* Vervolg vragen: helpt u bij het bepalen van een pad via uw kennis database, zodat de gebruiker bij het juiste antwoord komt.
* Alternatieve vragen: belang rijk zodat Zoek resultaten kunnen worden vergeleken met je antwoord in verschillende vormen van de vraag. [Actieve Learning suggesties](../How-To/use-active-learning.md) veranderen in alternatieve vragen.

### <a name="devops-development"></a>DevOps-ontwikkeling

Voor het ontwikkelen van een Knowledge Base voor het invoegen van een DevOps-pijp lijn moet de Knowledge Base tijdens batch tests worden geïsoleerd.

Een Knowledge Base deelt de Cognitive Search index met alle andere kennis grondslagen op de QnA Maker resource. Hoewel de Knowledge Base is geïsoleerd per partitie, kan het delen van de index een verschil in de Score veroorzaken in vergelijking met de gepubliceerde kennis database.

Als u _dezelfde score_ wilt hebben op de `test` en `production` kennis bases, kunt u een QnA Maker resource isoleren in één kennis database. In deze architectuur hoeft de resource alleen live te zijn als de geïsoleerde batch-test.

## <a name="next-steps"></a>Volgende stappen

* [Azure-resources](../how-to/set-up-qnamaker-service-azure.md)
* [Vraag-en-antwoord-paren](question-answer-set.md)
