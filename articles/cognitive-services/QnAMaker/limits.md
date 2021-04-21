---
title: Limieten en grenzen - QnA Maker
description: QnA Maker heeft metalimieten voor delen van de knowledge base en service. Het is belangrijk om uw knowledge base binnen deze limieten te houden om te testen en te publiceren.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: ad498575b029f918538909a9b5b2d52c71c1389c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816364"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker en grenzen van knowledge base

QnA Maker de onderstaande limieten zijn [](../../search/search-limits-quotas-capacity.md) een combinatie van de limieten Azure Cognitive Search prijscategorie en de [limieten QnA Maker prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). U moet beide limieten kennen om te begrijpen hoeveel knowledge bases u per resource kunt maken en hoe groot elke knowledge base kan groeien.

## <a name="knowledge-bases"></a>Knowledge bases

Het maximum aantal Knowledge Bases is gebaseerd op Azure Cognitive Search [laaglimieten](../../search/search-limits-quotas-capacity.md).

|**Azure Cognitive Search laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximum aantal gepubliceerde Knowledge Bases dat is toegestaan|2|14|49|199|199|2,999|

 Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 knowledge bases publiceren (1 index per gepubliceerde Knowledge Base). De vijftiende index, `testkb` , wordt gebruikt voor alle Knowledge Bases voor het maken en testen.

## <a name="extraction-limits"></a>Extractielimieten

### <a name="file-naming-constraints"></a>Beperkingen voor bestandsnaamgeving

Bestandsnamen mogen niet de volgende tekens bevatten:

|Gebruik geen teken|
|--|
|Enkele aangave `'`|
|Dubbele aangave `"`|

### <a name="maximum-file-size"></a>Maximale bestandsgrootte

|Indeling|Maximale bestandsgrootte (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximum aantal bestanden

Het maximum aantal bestanden dat kan worden geëxtraheerd en de maximale bestandsgrootte is gebaseerd op de limieten QnA Maker **[de prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

> [!NOTE]
> QnA Maker beheerd (preview) is een gratis service zonder limieten voor het aantal bronnen dat kan worden toegevoegd. Doorvoer is momenteel begrensd op 10 transacties per seconde voor zowel beheer-API's als voorspellings-API's.

### <a name="maximum-number-of-deep-links-from-url"></a>Maximum aantal dieptekoppelingen van URL

Het maximum aantal dieptekoppelingen dat kan worden verkend voor extractie van QnA's van een URL-pagina is **20**.

## <a name="metadata-limits"></a>Limieten voor metagegevens

Metagegevens worden weergegeven als een sleutel-waardepaar op basis van tekst, zoals `product:windows 10` . Deze wordt in kleine gevallen opgeslagen en vergeleken.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Op Azure Cognitive Search prijscategorie

Het maximum aantal metagegevensvelden per Knowledge Base is gebaseerd op uw **[Azure Cognitive Search laaglimieten](../../search/search-limits-quotas-capacity.md)**.

|**Azure Cognitive Search laag** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximumaantal metagegevensvelden per QnA Maker service (voor alle KB's)|1000|100*|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Op naam en waarde

De lengte en acceptabele tekens voor de naam en waarde van de metagegevens worden in de volgende tabel vermeld.

|Item|Toegestane chars|Regex-patroon komt overeen|Maximum aantal chars|
|--|--|--|--|
|Naam (sleutel)|Kunt<br>alfanumeriek (letters en cijfers)<br>`_` (onderstrepingsteken)<br> Mag geen spaties bevatten.|`^[a-zA-Z0-9_]+$`|100|
|Waarde|Hiermee staat u alles toe behalve<br>`:` (dubbele punt)<br>`|` (verticale pijp)<br>Er is slechts één waarde toegestaan.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Knowledge Base inhoudslimieten instellen
Algemene limieten voor de inhoud in de knowledge base:
* Lengte van antwoordtekst: 25.000 tekens
* Lengte van vraagtekst: 1000 tekens
* Lengte van de tekst van de metagegevenssleutel: 100 tekens
* Lengte van de tekst van de metagegevenswaarde: 500 tekens
* Ondersteunde tekens voor metagegevensnaam: alfabeten, cijfers en `_`
* Ondersteunde tekens voor metagegevenswaarde: Alle behalve `:` en `|`
* Lengte van bestandsnaam: 200
* Ondersteunde bestandsindelingen: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Maximum aantal alternatieve vragen: 300
* Maximum aantal vraag-antwoordparen: is afhankelijk van de **[Azure Cognitive Search gekozen](../../search/search-limits-quotas-capacity.md#document-limits)** laag. Een vraag-en-antwoordpaar wordt gekoppeld aan een document Azure Cognitive Search index.
* URL-/HTML-pagina: 1 miljoen tekens

## <a name="create-knowledge-base-call-limits"></a>Aanroeplimieten voor Knowledge Base maken:
Deze staan voor de limieten voor elke actie voor het maken van een knowledge base; Dat wil zeggen dat u op *KB maken klikt* of de Api CreateKnowledgeBase aanroept.
* Aanbevolen maximum aantal alternatieve vragen per antwoord: 300
* Maximum aantal URL's: 10
* Maximum aantal bestanden: 10
* Maximumaantal toegestane QnA's per aanroep: 1000

## <a name="update-knowledge-base-call-limits"></a>Aanroeplimieten voor Knowledge Base bijwerken
Deze staan voor de limieten voor elke updateactie; dat wil zeggen, klikken op *Opslaan en trainen* of de UpdateKnowledgeBase-API aanroepen.
* Lengte van elke bronnaam: 300
* Aanbevolen maximum aantal alternatieve vragen toegevoegd of verwijderd: 300
* Maximum aantal metagegevensvelden dat is toegevoegd of verwijderd: 10
* Maximum aantal URL's dat kan worden vernieuwd: 5
* Maximumaantal toegestane QnA's per aanroep: 1000

## <a name="add-unstructured-file-limits"></a>Niet-gestructureerde bestandslimieten toevoegen

> [!NOTE]
> * Als u grotere bestanden wilt gebruiken dan de limiet toestaat, kunt u het bestand ops delen in kleinere bestanden voordat u ze naar de API verstuurt. 

Deze vertegenwoordigen de limieten wanneer ongestructureerde bestanden worden gebruikt voor het maken van *KB* of het aanroepen van de CreateKnowledgeBase-API:
* Lengte van het bestand: de eerste 32000 tekens worden geëxtrah
* Maximaal 3 antwoorden per bestand.

## <a name="prebuilt-question-answering-limits"></a>Vooraf gebouwde limieten voor het beantwoorden van vragen

> [!NOTE]
> * Als u grotere documenten wilt gebruiken dan de limiet toestaat, kunt u de tekst in kleinere stukken tekst ops delen voordat u ze naar de API verstuurt. 
> * Een document bestaat uit één tekenreeks van teksttekens.  

Deze vertegenwoordigen de limieten wanneer vooraf gebouwde API wordt gebruikt om een *antwoord te* genereren of de GenerateAnswer-API aan te roepen:
* Aantal documenten: 5
* Maximale grootte van één document: 5120 tekens
* Maximaal 3 antwoorden per document.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over wanneer en hoe u [de serviceprijscategorie wijzigt.](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)
