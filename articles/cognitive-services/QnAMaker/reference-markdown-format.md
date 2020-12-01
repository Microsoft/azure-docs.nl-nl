---
title: Indeling voor prijs verlaging-QnA Maker
description: Hieronder ziet u de lijst met indelingen voor prijs opgaven die u kunt gebruiken in de antwoord tekst van QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352266"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>De indeling voor prijs verlaging wordt ondersteund in QnA Maker antwoord tekst

QnA Maker worden antwoord tekst opgeslagen als prijs opgeven. Er zijn veel soorten prijs verlaging. Gebruik deze verwijzing om ervoor te zorgen dat de antwoord tekst wordt geretourneerd en correct wordt weer gegeven.

Gebruik de **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -zelf studie om uw prijs verlaging te valideren. De zelf studie heeft een **try it** -functie voor het snel kopiëren/plakken van validatie.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Wanneer gebruik ik RTF-bewerking versus korting

Met [RTF-bewerking](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) van antwoorden kunt u, als auteur, een opmaak werkbalk gebruiken om snel tekst te selecteren en op te maken.

De prijs verlaging is een beter hulp middel wanneer u inhoud automatisch moet genereren om kennis te maken van de Knowledge bases die als onderdeel van een CI/CD-pijp lijn of voor [batch tests](./index.yml)moeten worden geïmporteerd.

## <a name="supported-markdown-format"></a>Ondersteunde indeling voor prijs verlaging

Hieronder ziet u de lijst met indelingen voor prijs opgaven die u kunt gebruiken in de antwoord tekst van QnA Maker.

|Doel|Indeling|Voorbeeld voor Markdown|Rendering<br>zoals weer gegeven in de chat-bot|
|--|--|--|--|
Een nieuwe regel tussen twee zinnen.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![nieuwe lijn tussen twee zinnen opmaken](./media/qnamaker-concepts-datasources/format-newline.png)|
|Headers van H1 tot H6, het aantal `#` geeft aan welke koptekst u wilt. 1 `#` is de H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![indeling met kopteksten voor korting](./media/qnamaker-concepts-datasources/format-headers.png)<br>![notatie met de koppen H1 tot en met H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Cursief |`*text*`|`How do I create a bot with *QnA Maker*?`|![opmaken met cursief](./media/qnamaker-concepts-datasources/format-italics.png)|
|Sterk (vet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![indeling met sterke markering voor vet](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL voor koppeling|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![indeling voor URL (Hyper Link)](./media/qnamaker-concepts-datasources/format-url.png)|
|* URL voor de open bare afbeelding|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![indeling voor URL van open bare afbeelding ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Doorhalen|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![opmaak voor door halen](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Vet en cursief|`***text**_`|`How can I create a _*_QnA Maker_*_ bot?`|![opmaak voor vet en cursief](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Vetgedrukte URL voor koppeling|`[_*text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![notatie voor vetgedrukte URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Cursieve URL voor koppeling|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![notatie voor cursieve URL](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Symbolen voor kortings symbool voor Escape|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Indeling voor de symbolen voor de kortings verlaging.](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Geordende lijst|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>In het voor gaande voor beeld wordt gebruikgemaakt van automatische nummering in korting.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>In het vorige voor beeld wordt gebruikgemaakt van expliciete nummering.|![indeling voor geordende lijst](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Ongeordende lijst|`\n * item1 \n * item2`<br>of<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![indeling voor niet-geordende lijst](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Geneste lijsten|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>U kunt bestelde en niet-geordende lijsten samen nesten. Het tabblad, `\t` , geeft het inspring niveau van het onderliggende element aan.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![indeling voor geneste niet-geordende lijst](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![indeling voor geneste geordende lijst](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker de afbeelding niet op enigerlei manier verwerken. Het is de rol van de client toepassing om de installatie kopie weer te geven.

Als u inhoud wilt toevoegen met behulp van de Knowledge Base-Api's update/replace en de inhoud/het bestand bevat HTML-tags, kunt u de HTML in het bestand behouden door te zorgen dat het openen en sluiten van de tags wordt geconverteerd in de gecodeerde indeling.

| HTML behouden  | Vertegenwoordiging in de API-aanvraag  | Representatie in KB |
|-----------|---------|-------------------------|
| Ja | \&lt; br \& gt; | &lt;br&gt; |
| Ja | \&lt; h3 \& gt; koptekst \& lt;/h3 \& gt; | &lt;H3 &gt; header &lt; /H3&gt; |

CR LF (\r\n) wordt ook geconverteerd naar \n in de KB. LF (\n) wordt bewaard. Als u een escape reeks als een \t of \n wilt escapepen, kunt u back slash gebruiken, bijvoorbeeld: ' \\ \\ r \\ \\ n ' en ' \\ \\ t '

## <a name="next-steps"></a>Volgende stappen

Controleer de [Bestands indelingen](reference-tsv-format-batch-testing.md)voor batch tests.