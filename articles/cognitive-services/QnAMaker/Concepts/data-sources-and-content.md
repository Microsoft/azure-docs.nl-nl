---
title: Gegevens bronnen en inhouds typen-QnA Maker
description: Meer informatie over het importeren van vraag-en antwoord paren uit gegevens bronnen en ondersteunde inhouds typen, waaronder veel standaard gestructureerde documenten, zoals PDF, DOCX en TXT-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99556037"
---
# <a name="importing-from-data-sources"></a>Importeren uit gegevensbronnen

Een kennis database bestaat uit vraag-en antwoord paren in open bare Url's en bestanden.

## <a name="data-source-locations"></a>Gegevens bron locaties

Inhoud wordt in een Knowledge Base van een gegevens bron gebracht. Gegevens bron locaties zijn **open bare url's of bestanden**, waarvoor geen verificatie is vereist.

[Share Point-bestanden](../how-to/add-sharepoint-datasources.md)die zijn beveiligd met verificatie, zijn de uitzonde ring. Share point-resources moeten bestanden, niet webpagina's zijn. Als de URL eindigt met een webextensie zoals. ASPX, kan deze niet worden geïmporteerd in QnA Maker vanuit SharePoint.

## <a name="chit-chat-content"></a>Chit-Chat inhoud

De Chit-Chat-inhoudset wordt aangeboden als een volledige inhouds bron voor inhoud in verschillende talen en gespreks stijlen. Dit kan een begin punt zijn voor de persoonlijkheid van uw bot en bespaart u de tijd en kosten voor het schrijven van deze bestanden. Meer informatie [over het toevoegen van Chit](../how-to/chit-chat-knowledge-base.md) aan uw Knowledge Base.

## <a name="structured-data-format-through-import"></a>Gestructureerde gegevens indeling via importeren

Als u een Knowledge Base importeert, wordt de inhoud van de bestaande Knowledge Base vervangen. Voor het importeren is een gestructureerd `.tsv` bestand vereist dat vragen en antwoorden bevat. Deze informatie helpt bij het QnA Maker groeperen van de vraag-antwoord paren en het kenmerk ervan aan een bepaalde gegevens bron.

| Vraag  | Antwoord  | Bron| Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redactionele|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Indeling voor gestructureerde multi-draaiing via importeren

U kunt de multi-turn-conversaties maken in een `.tsv` bestands indeling. De indeling biedt u de mogelijkheid om de multi-turn-conversaties te maken door eerdere chat logboeken te analyseren (met andere processen, niet met QnA Maker) en vervolgens het bestand te maken `.tsv` via Automation. Importeer het bestand om de bestaande Knowledge Base te vervangen.

> [!div class="mx-imgBorder"]
> ![Conceptueel model van 3 niveaus van de multi-turn-vraag](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

De kolom voor een multi-turn `.tsv` , specifiek voor meerdere zetten, is een **prompt**. Een voor beeld `.tsv` , weer gegeven in Excel, toont de gegevens die moeten worden opgenomen om de multi-turn-kinderen te definiëren:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

De **display order** is numeriek en de **weergave** tekst is Text die geen prijs verlaging mag bevatten.

> [!div class="mx-imgBorder"]
> ![Voor beeld van een multi-turn-vraag zoals weer gegeven in Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exporteren als voor beeld

Als u niet zeker weet hoe u uw QnA-paar in het `.tsv` bestand kunt vertegenwoordigen:
* Gebruik dit [Download bare voor beeld van github](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Of maak het paar in de QnA Maker Portal, sla de Knowledge Base op en exporteer deze vervolgens naar een voor beeld van hoe het paar vertegenwoordigt.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Inhouds typen van documenten die u aan een kennis database kunt toevoegen
Inhouds typen bevatten veel standaard gestructureerde documenten, zoals PDF, DOC en TXT.

### <a name="file-and-url-data-types"></a>Bestands-en URL-gegevens typen

De volgende tabel bevat een overzicht van de typen inhoud en bestands indelingen die door QnA Maker worden ondersteund.

|Brontype|Type inhoud| Voorbeelden|
|--|--|--|
|URL|Veelgestelde vragen<br> (Plat, met secties of met een start pagina voor onderwerpen)<br>Ondersteunings pagina's <br> (Procedures voor één pagina, artikelen, problemen oplossen, enz.)|[Veelgestelde vragen](../troubleshooting.md), <br>[Veelgestelde vragen met koppelingen](https://www.microsoft.com/en-us/software-download/faq),<br> [Veelgestelde vragen met de introductie pagina van de onderwerpen](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Ondersteunings artikel](./best-practices.md)|
|PDF/DOC|Faq's<br> Product handleiding,<br> Brochures<br> Waarop<br> Flyer-beleid,<br> Ondersteunings handleiding,<br> Gestructureerde QnA,<br> Enzovoort.|**Zonder meerdere zetten**<br>[Gestructureerde QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> Voor [beeld-Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Voorbeeld semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> Voor [beeld-white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Meerdere zetten**:<br>[Surface Pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-voor delen (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Voor delen van Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Structured QnA-bestand<br> (inclusief RTF-, HTML-ondersteuning)|**Zonder meerdere scha kelen**:<br>[Voor beeld van QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Meerdere zetten**:<br>[Gestructureerd eenvoudig FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface laptop-FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Structured QnA-bestand|[Voor beeld van Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Als u verificatie voor uw gegevens bron nodig hebt, kunt u de volgende methoden voor het ophalen van die inhoud in QnA Maker:

* Down load het bestand hand matig en importeer het in QnA Maker
* Het bestand toevoegen vanaf de geauthenticeerde [share point-locatie](../how-to/add-sharepoint-datasources.md)

### <a name="url-content"></a>URL-inhoud

Er kunnen twee typen documenten worden geïmporteerd via **URL** in QnA Maker:

* Url's voor veelgestelde vragen
* Url's voor ondersteuning

Elk type geeft een verwachte indeling aan.

### <a name="file-based-content"></a>Op bestanden gebaseerde inhoud

U kunt bestanden toevoegen aan een Knowledge Base van een open bare bron of uw lokale bestands systeem in de [QnA Maker Portal](https://www.qnamaker.ai).

### <a name="content-format-guidelines"></a>Richtlijnen voor contentindelingen

Meer informatie over de [richt lijnen](../reference-document-format-guidelines.md) voor de indeling van de verschillende bestanden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [bewerken van QnAs](../how-to/edit-knowledge-base.md).
