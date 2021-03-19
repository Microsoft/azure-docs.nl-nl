---
title: Levens cyclus van Knowledge Base-QnA Maker
description: QnA Maker leert het beste in een iteratieve cyclus van model wijzigingen, utterance-voor beelden, publiceren en verzamelen van gegevens uit eindpunt query's.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e52e7151bc30a19bd6f6041d52effdd799a87c99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91776966"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Levens cyclus van de Knowledge Base in QnA Maker
QnA Maker leert het beste in een iteratieve cyclus van model wijzigingen, utterance-voor beelden, publiceren en verzamelen van gegevens uit eindpunt query's.

![Ontwerpcyclus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Een QnA Maker-KB maken
QnA Maker Knowledge Base-eind punt (KB) biedt een beste antwoord op een gebruikers query op basis van de inhoud van de KB. Het maken van een kennis database is een eenmalige actie voor het instellen van een inhouds opslagplaats met vragen, antwoorden en gekoppelde meta gegevens. U kunt een KB maken door vooraf bestaande inhoud te verkennen, zoals de volgende bronnen:

- Pagina met veelgestelde vragen
- Product handleidingen
- Q-A-paren

Meer informatie over het [maken van een Knowledge Base](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>De Knowledge Base testen en bijwerken

De Knowledge Base is klaar om te worden getest nadat deze is gevuld met inhoud, hetzij op basis van een hand leiding hetzij door automatische extractie. Interactieve tests kunnen worden uitgevoerd in de QnA Maker Portal, via het **test** paneel. U voert algemene gebruikers query's in. Vervolgens controleert u of de antwoorden met de juiste reactie en een voldoende betrouwbaarheids Score worden geretourneerd.


* **Zo kunt u lage betrouwbaarheids scores herstellen**: alternatieve vragen toevoegen.
* **Wanneer een query niet de [standaard reactie](../How-to/change-default-answer.md)retourneert**: nieuwe antwoorden toevoegen aan de juiste vraag.

Deze nauw keurige lus van test-update gaat door totdat u tevreden bent met de resultaten. Meer informatie over het [testen van uw Knowledge Base](../How-To/test-knowledge-base.md).

Gebruik voor grote Kb's geautomatiseerde tests met de [generateAnswer-API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) en de `isTest` eigenschap Body, die de Knowledge Base doorzoekt `test` in plaats van de gepubliceerde kennis database.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>De knowledge base publiceren
Wanneer u klaar bent met het testen van de Knowledge Base, kunt u deze publiceren. Publiceren duwt de nieuwste versie van de geteste Knowledge Base naar een speciale Azure Cognitive Search-index die de **gepubliceerde** kennis database vertegenwoordigt. Hiermee wordt ook een eindpunt gemaakt dat kan worden aangeroepen in uw toepassing of chatbot.

Als gevolg van de publicatie-actie, hebben alle verdere wijzigingen die zijn aangebracht in de test versie van de Knowledge Base geen invloed op de gepubliceerde versie. De gepubliceerde versie is mogelijk Live in een productie toepassing.

Elk van deze kennis grondslagen kan afzonderlijk worden getest. Met behulp van de Api's kunt u de test versie van de Knowledge Base richten `isTest` op de eigenschap Body in de generateAnswer-aanroep.

Meer informatie over het [publiceren van uw Knowledge Base](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Gebruik bewaken
Als u de chat logboeken van uw service wilt kunnen registreren, moet u Application Insights inschakelen wanneer u [uw QnA Maker-service maakt](../How-To/set-up-qnamaker-service-azure.md).

U kunt verschillende analyses van uw service gebruik krijgen. Meer informatie over hoe u Application Insights kunt gebruiken om [analyses voor uw QnA Maker-service](../How-To/get-analytics-knowledge-base.md)op te halen.

Op basis van wat u leert van uw analyse, moet u [de juiste updates voor uw Knowledge Base](../How-To/edit-knowledge-base.md)maken.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versie beheer voor gegevens in uw Knowledge Base

Versie beheer voor gegevens wordt gegeven via de functies voor importeren/exporteren op de pagina **instellingen** van de QnA Maker Portal.

U kunt een back-up maken van een Knowledge Base door de Knowledge Base te exporteren in of in een `.tsv` `.xls` indeling. Als u dit bestand hebt geëxporteerd, neemt u dit op als onderdeel van de regel matige controle van broncode beheer.

Als u terug wilt gaan naar een specifieke versie, moet u het bestand importeren van het lokale systeem. Een geëxporteerde Knowledge Base **mag** alleen worden gebruikt via importeren op de pagina **instellingen** . Het kan niet worden gebruikt als een bestands-of URL-document gegevens bron. Hiermee worden vragen en antwoorden die momenteel in de Knowledge Base staan vervangen door de inhoud van het geïmporteerde bestand.

## <a name="test-and-production-knowledge-base"></a>Test-en productie Knowledge Base
Een kennis database is de opslag plaats van vragen en antwoorden sets die zijn gemaakt, onderhouden en worden gebruikt via QnA Maker. Elke QnA Maker resource kan meerdere kennis slagen bevatten.

Een kennis database heeft twee statussen: *testen* en *gepubliceerd*.

### <a name="test-knowledge-base"></a>Knowledge Base testen

De *test kennis basis* is de versie die momenteel wordt bewerkt en opgeslagen. De test versie is getest op nauw keurigheid en voor volledigheid van reacties. Wijzigingen die zijn aangebracht in de test Knowledge Base, hebben geen invloed op de eind gebruiker van uw toepassing of chat-bot. De test kennis basis staat bekend als `test` in de HTTP-aanvraag. De `test` kennis is beschikbaar in het deel venster met de interactieve **test** van de QnA maker van de portal.

### <a name="production-knowledge-base"></a>Productie Knowledge Base

De *gepubliceerde kennis database* is de versie die wordt gebruikt in uw chat-bot of-toepassing. Als u een kennis database publiceert, wordt de inhoud van de test versie opgenomen in de gepubliceerde versie. De gepubliceerde kennis database is de versie die door de toepassing via het eind punt wordt gebruikt. Zorg ervoor dat de inhoud juist en goed is getest. De gepubliceerde kennis database staat bekend als `prod` in de HTTP-aanvraag.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Suggesties voor actieve trainingen](./active-learning-suggestions.md)