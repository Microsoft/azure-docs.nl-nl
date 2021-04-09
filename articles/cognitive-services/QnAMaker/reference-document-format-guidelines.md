---
title: Richt lijnen voor het importeren van document indelingen-QnA Maker
description: Gebruik deze richt lijnen voor het importeren van documenten om de beste resultaten voor uw inhoud te krijgen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99549538"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Richt lijnen voor het opmaken van geïmporteerde documenten en Url's

Bekijk deze opmaak richtlijnen om de beste resultaten voor uw inhoud te krijgen.

## <a name="formatting-considerations"></a>Opmaak overwegingen

Nadat u een bestand of URL hebt geïmporteerd, worden QnA Maker geconverteerd en wordt de inhoud opgeslagen in de [indeling voor prijs verlaging](https://en.wikipedia.org/wiki/Markdown). Het conversie proces voegt nieuwe regels toe aan de tekst, zoals `\n\n` . Een kennis van de prijs verlaging helpt u bij het begrijpen van de geconverteerde inhoud en het beheren van uw Knowledge Base-inhoud.

Als u inhoud rechtstreeks in uw Knowledge Base toevoegt of bewerkt, gebruikt u de indeling voor **prijs verlaging** om inhoud met opmaak te maken of de inhoud van de prijs verlaging te wijzigen die al in het antwoord voor komt. QnA Maker ondersteunt een groot deel van de prijs verlaging voor uw inhoud. De client toepassing, zoals een chat-bot, ondersteunt echter mogelijk niet dezelfde set prijs notaties. Het is belang rijk om de weer gave van antwoorden van de client toepassing te testen.

Bekijk een volledige lijst met [inhouds typen en voor beelden](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).

## <a name="basic-document-formatting"></a>Elementaire document opmaak

QnA Maker herkent secties en subsecties en relaties in het bestand op basis van visuele aanwijzingen als:

* tekengrootte
* lettertype stijl
* nummeren
* kleuren

> [!NOTE]
> Er wordt momenteel geen ondersteuning geboden voor het extra heren van afbeeldingen van geüploade documenten.

### <a name="product-manuals"></a>Product handleidingen

Een hand leiding is doorgaans richt lijnen die bij een product worden geleverd. Het helpt de gebruiker bij het instellen, gebruiken, onderhouden en probleem oplossing van het product. Als QnA Maker een hand matige processen verwerkt, worden de koppen en subkoppen als vragen en de volgende inhoud als antwoorden geëxtraheerd. Bekijk [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)een voor beeld.

Hieronder ziet u een voor beeld van een hand leiding met een index pagina en hiërarchische inhoud

 ![Hand matig voor beeld van product voor een Knowledge Base](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extractie werkt het beste in hand boeken met een inhouds opgave en/of een index pagina en een duidelijke structuur met hiërarchische koppen.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brochures, richt lijnen, documenten en andere bestanden

Veel andere typen documenten kunnen ook worden verwerkt voor het genereren van QA-paren, mits ze een duidelijke structuur en indeling hebben. Dit zijn onder andere: brochures, richt lijnen, rapporten, White papers, weten schappelijke documenten, beleids regels, boeken, enzovoort. Bekijk [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)een voor beeld.

Hieronder ziet u een voor beeld van een semi-Structured doc, zonder een index:

 ![Semi-Structured document voor Azure Blob Storage](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Structured QnA-document

De indeling voor gestructureerde Question-Answers in document bestanden, bevindt zich in de vorm van afwisselende vragen en antwoorden per regel, één vraag per regel gevolgd door het antwoord op de volgende regel, zoals hieronder wordt weer gegeven:

```text
Question1

Answer1

Question2

Answer2
```

Hieronder ziet u een voor beeld van een gestructureerd QnA Word-document:

 ![Voor beeld van een Structured QnA-document voor een kennis database](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>Gestructureerde *txt*-, *TSV* -en *xls* -bestanden

QnAs in de vorm van gestructureerde *. txt*-, *TSV* -of *xls* -bestanden kunnen ook worden geüpload naar QnA Maker om een Knowledge Base te maken of uit te breiden.  Dit kan onbewerkte tekst zijn of kan inhoud bevatten in RTF of HTML.

| Vraag  | Antwoord  | Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Eventuele aanvullende kolommen in het bron bestand worden genegeerd.

#### <a name="example-of-structured-excel-file"></a>Voor beeld van een gestructureerd Excel-bestand

Hieronder ziet u een voor beeld van een gestructureerd QnA *. xls* -bestand met HTML-inhoud:

 ![Gestructureerd QnA Excel-voor beeld voor een Knowledge Base](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand

Hieronder ziet u een voor beeld van een gestructureerd QnA *. xls* -bestand, met verschillende alternatieve vragen voor één antwoord:

 ![Voor beeld van alternatieve vragen voor één antwoord in een Excel-bestand](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Nadat het bestand is geïmporteerd, bevindt het vraag-en antwoord paar zich in de Knowledge Base, zoals hieronder wordt weer gegeven:

 ![Scherm afbeelding van alternatieve vragen voor één antwoord dat in de Knowledge Base wordt geïmporteerd](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Gestructureerde gegevens indeling via importeren

Als u een Knowledge Base importeert, wordt de inhoud van de bestaande Knowledge Base vervangen. Voor het importeren is een Structured TSV-bestand met gegevens bron informatie vereist. Deze informatie helpt bij het QnA Maker groeperen van de vraag-antwoord paren en het kenmerk ervan aan een bepaalde gegevens bron.

| Vraag  | Antwoord  | Bron| Meta gegevens (1 sleutel: 1 waarde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redactionele|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Document opmaak met meerdere scha kelen

* Gebruik koppen en subkoppen om de hiërarchie aan te duiden. U kunt bijvoorbeeld H1 de bovenliggende QnA en H2 aanduiden om de QnA aan te duiden die als prompt moet worden beschouwd. Gebruik de kleine grootte van de kop om volgende hiërarchie aan te duiden. Gebruik geen stijl, kleur of een ander mechanisme om de structuur in uw document te impliceren, QnA Maker de prompts voor meerdere schakelingen niet uit te pakken.
* Het eerste teken van de kop moet worden gekapitaliseerd.
* Beëindig geen kop met een vraag teken, `?` .

**Voorbeeld documenten**:<br>[Surface Pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-voor delen (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Voor delen van Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>Url's voor veelgestelde vragen

QnA Maker kunnen webpagina's met veelgestelde vragen in drie verschillende vormen ondersteunen:

* Pagina's met veelgestelde vragen
* Pagina met veelgestelde vragen met koppelingen
* Pagina met veelgestelde vragen met een start pagina voor onderwerpen

### <a name="plain-faq-pages"></a>Pagina's met veelgestelde vragen

Dit is het meest voorkomende type FAQ-pagina waarin de antwoorden direct op de vragen op dezelfde pagina volgen.

Hieronder volgt een voor beeld van een pagina met veelgestelde vragen:

![Voor beeld van een pagina met veelgestelde vragen voor een kennis database](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Pagina met veelgestelde vragen met koppelingen

In dit type FAQ-pagina worden vragen samen geaggregeerd en worden ze gekoppeld aan antwoorden die zich in verschillende delen van dezelfde pagina of op verschillende pagina's bevinden.

Hieronder volgt een voor beeld van een pagina met veelgestelde vragen met koppelingen in secties die zich op dezelfde pagina bevinden:

 ![Pagina met veelgestelde vragen over sectie koppelingen voor een Knowledge Base](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Pagina bovenliggende onderwerpen koppelingen naar pagina's met onderliggende antwoorden

Dit type Veelgestelde vragen bevat een pagina onderwerpen waarin elk onderwerp wordt gekoppeld aan een bijbehorende set vragen en antwoorden op een andere pagina. QnA Maker verkent alle gekoppelde pagina's om de bijbehorende vragen & antwoorden uit te pakken.

Hieronder ziet u een voor beeld van een pagina met onderwerpen met koppelingen naar secties met veelgestelde vragen op verschillende pagina's.

 ![Voor beeld van een diep gaande pagina Veelgestelde vragen voor een Knowledge Base](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>Url's voor ondersteuning

QnA Maker kunt semi-gestructureerde ondersteunings webpagina's verwerken, zoals webartikelen waarin wordt beschreven hoe u een bepaalde taak uitvoert, hoe u een bepaald probleem opspoort en oplost en wat de aanbevolen procedures voor een bepaald proces zijn. Extractie werkt het beste voor inhoud die een duidelijke structuur met hiërarchische koppen heeft.

> [!NOTE]
> Uitpakken voor ondersteunings artikelen is een nieuwe functie en is in vroege stadia. Het werkt het beste voor eenvoudige pagina's, die goed zijn gestructureerd en geen complexe kop-en voet teksten bevatten.

![QnA Maker ondersteunt de extractie van semi-gestructureerde webpagina's waarbij een duidelijke structuur wordt weer gegeven met hiërarchische koppen](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>De Knowledge Base importeren en exporteren

**TSV-en xls-bestanden**, van geëxporteerde kennis grondslagen, kunnen alleen worden gebruikt door het importeren van de bestanden van de pagina **instellingen** in de QnA Maker Portal. Ze kunnen niet worden gebruikt als gegevens bronnen tijdens het maken van een Knowledge Base of vanuit het onderdeel **bestand toevoegen** of **+ URL** toevoegen op de pagina **instellingen** . 

Wanneer u de Knowledge Base via deze **TSV-en xls-bestanden** importeert, worden de QnA-paren toegevoegd aan de redactionele bron en niet de bronnen waaruit de QnAs zijn geëxtraheerd in de geëxporteerde kennis basis. 


## <a name="next-steps"></a>Volgende stappen

Een volledige lijst met [inhouds typen en voor beelden](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base) weer geven
