---
title: Typen Url's die worden ondersteund voor import-QnA Maker
description: Begrijpen hoe de typen Url's worden gebruikt voor het importeren en maken van QnA-paren.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91776694"
---
# <a name="urls-supported-for-importing-documents"></a>Url's die worden ondersteund voor het importeren van documenten

Begrijpen hoe de typen Url's worden gebruikt voor het importeren en maken van QnA-paren.

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

## <a name="support-urls"></a>Url's voor ondersteuning

QnA Maker kunt semi-gestructureerde ondersteunings webpagina's verwerken, zoals webartikelen waarin wordt beschreven hoe u een bepaalde taak uitvoert, hoe u een bepaald probleem opspoort en oplost en wat de aanbevolen procedures voor een bepaald proces zijn. Extractie werkt het beste voor inhoud die een duidelijke structuur met hiërarchische koppen heeft.

> [!NOTE]
> Uitpakken voor ondersteunings artikelen is een nieuwe functie en is in vroege stadia. Het werkt het beste voor eenvoudige pagina's, die goed zijn gestructureerd en geen complexe kop-en voet teksten bevatten.

![QnA Maker ondersteunt de extractie van semi-gestructureerde webpagina's waarbij een duidelijke structuur wordt weer gegeven met hiërarchische koppen](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)