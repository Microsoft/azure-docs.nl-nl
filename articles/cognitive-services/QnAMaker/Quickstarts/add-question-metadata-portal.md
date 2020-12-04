---
title: Vragen en antwoorden toevoegen in QnA Maker-portal
description: Dit artikel laat zien hoe u vraag-en-antwoordparen kunt toevoegen met metagegevens zodat uw gebruikers het juiste antwoord op hun vraag kunnen vinden.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 53c0afa1cdb8c9920875b7ba694339107714bd54
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462197"
---
# <a name="add-questions-and-answer-with-qna-maker-portal"></a>Vragen en antwoorden toevoegen met QnA Maker-portal

Nadat er een Knowledge Base is gemaakt, voegt u vraag-en-antwoord-paren (QnA) toe met metagegevens voor het filteren van het antwoord. De vragen in de volgende tabel gaan over de limieten voor Azure-servicelimieten, maar elk ervan heeft te maken met een andere Azure-zoekservice.

<a name="qna-table"></a>

|Paar|Vragen|Antwoord|Metagegevens|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Nadat er metagegevens zijn toegevoegd aan een QnA-paar, kan de clienttoepassing:

* Antwoorden opvragen die alleen overeenkomen met bepaalde metagegevens.
* Alle antwoorden ontvangen, maar de antwoorden nabewerken afhankelijk van de metagegevens voor elk antwoord.


## <a name="prerequisites"></a>Vereisten

* Voltooi de [vorige quickstart](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Aanmelden bij de QnA Maker-portal

1. Meld u aan bij de [QnA Maker-portal](https://www.qnamaker.ai).

1. Selecteer uw bestaande Knowledge Base in de [vorige quickstart](./create-publish-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Aanvullende vragen met een alternatieve formulering toevoegen

De huidige Knowledge Base bevat de QnA-paren voor het oplossen van problemen van QnA Maker. Deze paren zijn gemaakt toen de URL werd toegevoegd aan de Knowledge Base tijdens het maken.

Toen deze URL werd geïmporteerd, is er slechts één vraag met één antwoord gemaakt. In deze procedure voegt u aanvullende vragen toe.

1. Gebruik op de pagina **Edit** (Bewerken) het zoekvak boven de vraag-en-antwoord-paren om de vraag `How large a knowledge base can I create?` te vinden

1. Selecteer in de kolom **Question** (Vraag) **+ Add alternative phrasing** (Alternatieve formulering toevoegen) en voeg de nieuwe formuleringen uit de volgende tabel toe.

    |Alternatieve formulering|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Selecteer **Save and train** (Opslaan en trainen) om de Knowledge Base opnieuw te trainen.

1. Selecteer **Test** en voer vervolgens een vraag in die lijkt op een van de nieuwe alternatieve formuleringen, maar niet precies dezelfde woorden gebruikt:

    `What GB size can a knowledge base be?`

    Het juiste antwoord wordt geretourneerd in Markdown-indeling:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`

    Als u **Inspect** (Inspecteren) selecteert onder het geretourneerde antwoord, ziet u dat er meer antwoorden aan de vraag voldeden, maar niet met hetzelfde hoge betrouwbaarheidsniveau.

    Voeg niet elke mogelijke combinatie van alternatieve formuleringen toe. Wanneer u [actief leren](../how-to/improve-knowledge-base.md) van QnA Maker inschakelt, vindt dit de alternatieve formuleringen die uw Knowledge Base het beste helpen aan de behoeften van uw gebruikers te voldoen.

1. Selecteer nogmaals **Test** om het testvenster te sluiten.

## <a name="add-metadata-to-filter-the-answers"></a>Metagegevens toevoegen om de antwoorden te filteren

Door metagegevens toe te voegen aan vraag-en-antwoord-paren kan uw clienttoepassing gefilterde antwoorden opvragen. Dit filter wordt toegepast voordat de [eerste en tweede rankers](../concepts/query-knowledge-base.md#ranker-process) worden toegepast.

1. Voeg het tweede vraag-en-antwoord-paar uit de [eerste tabel in deze quickstart](#qna-table) toe, zonder de metagegevens, en ga door met de volgende stappen.

1. Selecteer **View options** (Weergaveopties) en vervolgens **Show metadata** (Metagegevens weergeven).

1. Selecteer **Add metadata tags** (Metagegevenstags toevoegen) voor het QnA-paar dat u zojuist hebt toegevoegd, en voeg de naam `service` en de waarde `search` toe. Dit ziet er zo uit: `service:search`.

1. Voeg nog een metagegevenstag toe, met de naam `link_in_answer` en de waarde `false`. Dit ziet er zo uit: `link_in_answer:false`.

1. Zoek het eerste antwoord in de tabel: `How large a knowledge base can I create?`.

1. Voeg metagegevensparen toe voor dezelfde twee metagegevenstags:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    U hebt nu twee vragen met dezelfde metagegevenstags met verschillende waarden.

1. Selecteer **Save and train** (Opslaan en trainen) om de Knowledge Base opnieuw te trainen.

1. Selecteer **Publish** (Publiceren) in het bovenste menu om naar de publicatiepagina te gaan.
1. Selecteer de knop **Publish** (Publiceren) om de huidige Knowledge Base naar het eindpunt te publiceren.
1. Nadat de Knowledge Base is gepubliceerd, gaat u door naar de volgende quickstart voor meer informatie over het genereren van een antwoord uit uw Knowledge Base.

## <a name="what-did-you-accomplish"></a>Wat hebt u gedaan?

U hebt uw Knowledge Base bewerkt ter ondersteuning van meer vragen en opgegeven naam-waardeparen om filteren te ondersteunen tijdens het zoeken naar het topantwoord of voor naverwerking nadat het antwoord of de antwoorden zijn geretourneerd.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de QnA Maker- en Bot-framework-resources in Azure Portal als u niet verdergaat met de volgende quickstart.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Antwoord ophalen met behulp van Postman of cURL](get-answer-from-knowledge-base-using-url-tool.md)
