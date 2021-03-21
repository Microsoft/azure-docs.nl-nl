---
title: Basis concepten voor AI-verrijking
titleSuffix: Azure Cognitive Search
description: Extractie van inhoud, verwerking van natuurlijke taal (NLP) en afbeeldings verwerking worden gebruikt voor het maken van Doorzoek bare inhoud in azure Cognitive Search indexen met zowel vooraf gedefinieerde cognitieve vaardig heden als aangepaste AI-algoritmen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.custom: references_regions
ms.openlocfilehash: 22d7c1bbe03d8b3c0e3b6026c9bac039f0651548
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037248"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>AI-verrijking in azure Cognitive Search

AI-verrijking is een uitbrei ding van [Indexeer functies](search-indexer-overview.md) die kunnen worden gebruikt voor het extra heren van tekst uit afbeeldingen, blobs en andere ongestructureerde gegevens bronnen. Met verrijking en extractie kunt u inhoud in indexerings objecten zoeken, hetzij een [zoek index](search-what-is-an-index.md) ofwel een [kennis archief](knowledge-store-concept-intro.md). 

Extractie en verrijking worden geïmplementeerd met *cognitieve vaardig heden* die zijn gekoppeld aan de door de Indexeer functie gestuurde pijp lijn. U kunt ingebouwde vaardig heden van micro soft gebruiken of externe verwerking insluiten in een [*aangepaste vaardigheid*](cognitive-search-create-custom-skill-example.md) die u maakt. Voor beelden van een aangepaste vaardigheid zijn een aangepaste entity module of een classificatie van documenten die gericht zijn op een specifiek domein, zoals financiën, weten schappelijke publicaties of medicijnen.

De ingebouwde vaardig heden vallen onder de volgende categorieën: 

+ Vaardig heden voor de **verwerking van natuurlijke taal** zijn onder andere [entiteits herkenning](cognitive-search-skill-entity-recognition.md), [taal detectie](cognitive-search-skill-language-detection.md), [extractie van sleutel zinnen](cognitive-search-skill-keyphrases.md), tekst manipulatie, [sentiment detectie](cognitive-search-skill-sentiment.md)en [PII-detectie](cognitive-search-skill-pii-detection.md). Met deze vaardig heden wordt ongestructureerde tekst toegewezen als Doorzoek bare en filter bare velden in een index.

+ Vaardig heden voor de **verwerking van afbeeldingen** zijn onder andere [optische teken herkenning (OCR)](cognitive-search-skill-ocr.md) en het identificeren van [visuele functies](cognitive-search-skill-image-analysis.md), zoals gezichts detectie, afbeeldings interpretatie, afbeeldings herkenning (beroemde personen en bezienswaardigheden) of kenmerken zoals afbeeldings stand. Deze vaardig heden maken tekst representaties van afbeeldings inhoud, waardoor deze kan worden doorzocht met behulp van de query mogelijkheden van Azure Cognitive Search.

![Diagram van verrijkings pijplijn](./media/cognitive-search-intro/cogsearch-architecture.png "overzicht van verrijkings pijplijn")

Ingebouwde vaardig heden in azure Cognitive Search zijn gebaseerd op vooraf getrainde machine learning modellen in Cognitive Services-API's: [Computer Vision](../cognitive-services/computer-vision/index.yml) en [Text Analytics](../cognitive-services/text-analytics/overview.md). U kunt een Cognitive Services resource koppelen als u deze resources tijdens de verwerking van inhoud wilt gebruiken.

De verwerking van natuurlijke taal en afbeelding wordt toegepast tijdens de fase van gegevens opname, met resultaten die deel uitmaken van de samen stelling van een document in een Doorzoek bare index in azure Cognitive Search. Gegevens worden als een Azure-gegevensset gedistribueerd en vervolgens gepusht via een indexerings pijplijn met behulp van de [ingebouwde vaardig heden](cognitive-search-predefined-skills.md) die u nodig hebt.  

## <a name="feature-availability"></a>Beschikbaarheid van functies

AI-verrijking is beschikbaar in regio's waar Azure Cognitive Services ook beschikbaar zijn.  U kunt de huidige Beschik baarheid van AI-verrijking controleren op de pagina [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=search) .  AI-verrijking is beschikbaar in alle ondersteunde regio's, met uitzonde ring van:

+ Australië - zuidoost
+ China - noord 2
+ Noorwegen - oost
+ Duitsland - west-centraal

Als uw zoek service zich in een van deze regio's bevindt, kunt u vaardig heden niet maken en gebruiken, maar alle andere functionaliteit van de zoek service is beschikbaar en volledig ondersteund.

## <a name="when-to-use-ai-enrichment"></a>Wanneer moet u AI-verrijking gebruiken?

U kunt overwegen ingebouwde cognitieve vaardig heden te gebruiken als uw onbewerkte inhoud ongestructureerde tekst, afbeeldings inhoud of inhoud bevat die taal detectie en-omzetting nodig heeft. Het Toep assen van AI via de ingebouwde cognitieve vaardig heden kan deze inhoud ontgrendelen, waardoor de waarde en het hulp programma in uw zoek-en data Science-apps worden verhoogd. 

Daarnaast kunt u overwegen een aangepaste vaardigheid toe te voegen als u een open source-, derden-of code van een andere partij hebt die u wilt integreren in de pijp lijn. Classificatie modellen die opvallende kenmerken van verschillende document typen identificeren, vallen in deze categorie, maar elk pakket dat waarde toevoegt aan uw inhoud, kan worden gebruikt.

### <a name="more-about-built-in-skills"></a>Meer informatie over ingebouwde vaardig heden

Een [vaardig](cognitive-search-defining-skillset.md) heden die is geassembleerd met behulp van ingebouwde vaardig heden, is goed geschikt voor de volgende toepassings scenario's:

+ Gescande documenten (JPEG) waarvoor u Zoek opdrachten in volledige tekst wilt uitvoeren. U kunt een OCR-vaardigheid (Optical Character Recognition) koppelen om tekst uit JPEG-bestanden te identificeren, uit te pakken en op te nemen.

+ Pdf's met gecombineerde afbeelding en tekst. Tekst in Pdf's kan worden geëxtraheerd tijdens het indexeren zonder het gebruik van stappen voor het verrijken, maar het toevoegen van afbeeldingen en natuurlijke taal verwerking levert vaak een beter resultaat op dan een standaard indexering.

+ Meertalige inhoud waartegen u taal detectie en mogelijk tekst omzetting wilt Toep assen.

+ Ongestructureerde of semi-gestructureerde documenten met inhoud met inherente betekenis of context die in het grotere document is verborgen. 

  Blobs in het bijzonder bevatten vaak een grote inhoud die in één veld wordt verpakt. Door de vaardig heden van afbeeldingen en natuurlijke taal verwerking aan een Indexeer functie toe te voegen, kunt u nieuwe informatie maken die extant is in de onbewerkte inhoud, maar niet op andere wijze als DISTINCT-velden. Sommige kant-en-klare cognitieve vaardig heden die u kunnen helpen: sleutel woordgroepen extractie, sentiment analyse en entiteits herkenning (personen, organisaties en locaties).

  Daarnaast kunnen ingebouwde vaardig heden ook worden gebruikt om inhoud te herstructureren met behulp van tekst splitsen, samen voegen en vorm bewerkingen.

### <a name="more-about-custom-skills"></a>Meer informatie over aangepaste vaardig heden

Aangepaste vaardig heden kunnen complexere scenario's ondersteunen, zoals het herkennen van formulieren of het detecteren van aangepaste entiteiten met behulp van een model dat u opgeeft en inpakt in de [aangepaste vaardigheids webinterface](cognitive-search-custom-skill-interface.md). Enkele voor beelden van aangepaste vaardig heden zijn [formulieren Recognizer](../cognitive-services/form-recognizer/overview.md), integratie van de [Bing entiteiten zoeken-API](./cognitive-search-create-custom-skill-example.md)en [aangepaste entiteits herkenning](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="steps-in-an-enrichment-pipeline"></a>Stappen in een verrijkings pijplijn <a name="enrichment-steps"></a>

Een verrijkings pijplijn is gebaseerd op [*Indexeer functies*](search-indexer-overview.md). Indexeer functies vullen een index op basis van veld-naar-veld Toewijzingen tussen de index en uw gegevens bron voor het kraken van documenten. Vaardig heden, die nu zijn gekoppeld aan Indexeer functies, onderscheppen en verrijkt documenten volgens de vakkennisset (en) die u definieert. Zodra de index is geïndexeerd, hebt u toegang tot inhoud via Zoek opdrachten via alle [query typen die door Azure Cognitive Search worden ondersteund](search-query-overview.md).  Als u geen ervaring hebt met Indexeer functies, begeleidt deze sectie u stapsgewijs door de stappen.

### <a name="step-1-connection-and-document-cracking-phase"></a>Stap 1: de fase verbinding en document opkraken

Aan het begin van de pijp lijn hebt u ongestructureerde tekst-of niet-tekst inhoud (zoals afbeeldingen, gescande documenten of JPEG-bestanden). Gegevens moeten aanwezig zijn in een Azure Data Storage-service die toegankelijk is voor een Indexeer functie. Indexeer functies kunnen bron documenten ' barsten ' om tekst uit de bron gegevens op te halen. Het kraken van documenten is het proces van het uitpakken of maken van tekst inhoud van niet-tekst bronnen tijdens het indexeren.

![De fase voor het kraken van documenten](./media/cognitive-search-intro/document-cracking-phase-blowup.png "document kraken")

 De volgende bronnen worden ondersteund: Azure Blob-opslag, Azure-tabel opslag, Azure SQL Database en Azure Cosmos DB. Inhoud op basis van tekst kan worden geëxtraheerd uit de volgende bestands typen: Pdf's, Word, Power Point, CSV-bestanden. Zie [ondersteunde indelingen](search-howto-indexing-azure-blob-storage.md#SupportedFormats)voor de volledige lijst. Het indexeren vergt enige tijd met een kleine, representatieve gegevensset en bouwt deze vervolgens incrementeel op als uw oplossing is gerijpt.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Stap 2: cognitieve vaardig heden en verrijkings fase

Verrijking wordt uitgevoerd met *cognitieve vaardig heden* die atomische bewerkingen uitvoeren. Als u bijvoorbeeld een PDF hebt gekraakt, kunt u entiteits herkenning, taal detectie of sleutel woordgroepen extractie Toep assen om nieuwe velden in uw index te maken die niet standaard beschikbaar zijn in de bron. Samen wordt de verzameling van de vaardig heden die in uw pijp lijn wordt gebruikt, een *vakkennis* genoemd.  

![Verrijkings fase](./media/cognitive-search-intro/enrichment-phase-blowup.png "verrijkings fase")

Een vaardig heden is gebaseerd op [ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) of [aangepaste vaardig heden](cognitive-search-create-custom-skill-example.md) die u verstrekt en maakt verbinding met de vakkennisset. Een vaardighedenset kan mini maal of zeer complex zijn en bepaalt niet alleen het type verwerking, maar ook de volg orde van de bewerkingen. Een vaardig heden plus de veld toewijzingen die zijn gedefinieerd als onderdeel van een Indexeer functie, geeft volledig de verrijkings pijplijn op. Zie [een vaardig heden definiëren](cognitive-search-defining-skillset.md)voor meer informatie over het samen stellen van al deze onderdelen.

Intern genereert de pijp lijn een verzameling verrijkte documenten. U kunt bepalen welke delen van de verrijkte documenten moeten worden toegewezen aan Indexeer bare velden in uw zoek index. Als u bijvoorbeeld de sleutel woord extractie en de vaardig heden voor entiteits herkenning hebt toegepast, worden deze nieuwe velden onderdeel van het verrijkte document en kunnen ze worden toegewezen aan velden in uw index. Zie [annotaties](cognitive-search-concept-annotations-syntax.md) voor meer informatie over invoer/uitvoer-vormingen.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Een knowledgeStore-element toevoegen om verrijkingen op te slaan

[Search rest API-Version = 2020-06-30](/rest/api/searchservice/) breidt vaardig heden uit met een `knowledgeStore` definitie die een Azure Storage-verbinding en projecties biedt die beschrijven hoe de verrijkingen worden opgeslagen. Dit is een aanvulling op uw index. In een Standard AI-pijp lijn zijn verrijkte documenten onwaar, die alleen tijdens het indexeren worden gebruikt en vervolgens worden verwijderd. In het kennis archief worden verrijkte documenten bewaard. Zie [Knowledge Store](knowledge-store-concept-intro.md)voor meer informatie.

### <a name="step-3-search-index-and-query-based-access"></a>Stap 3: zoek index en op query's gebaseerde toegang

Wanneer de verwerking is voltooid, hebt u een zoek index die bestaat uit verrijkte documenten, met volledige tekst doorzoekbaar in azure Cognitive Search. Bij [het uitvoeren van een query op de index](search-query-overview.md) wordt uitgelegd hoe ontwikkel aars en gebruikers toegang hebben tot de verrijkte inhoud die door de pijp lijn wordt gegenereerd. 

![Index met zoek pictogram](./media/cognitive-search-intro/search-phase-blowup.png "Index met zoek pictogram")

De index is net als andere die u kunt maken voor Azure Cognitive Search: u kunt een aanvulling vormen op aangepaste analyse functies, query's voor fuzzy zoeken uitvoeren, een gefilterde zoek opdracht toevoegen, of experimenteren met Score profielen om de vorm van de zoek resultaten te wijzigen.

Indexen worden gegenereerd op basis van een index schema waarmee de velden, kenmerken en andere constructies worden gedefinieerd die zijn gekoppeld aan een specifieke index, zoals Score profielen en synoniemen. Zodra een index is gedefinieerd en ingevuld, kunt u incrementeel indexeren om nieuwe en bijgewerkte bron documenten op te halen. Bepaalde wijzigingen moeten volledig opnieuw worden opgebouwd. U moet een kleine gegevensset gebruiken totdat het schema ontwerp stabiel is. Zie [How to rebuild an index](search-howto-reindex.md) (Een index herbouwen) voor meer informatie.

**Controle lijst: een typische werk stroom**

1. Deel uw Azure-bron gegevens in een representatieve steek proef. Het indexeren vergt enige tijd met een kleine, representatieve gegevensset en bouwt deze vervolgens incrementeel op als uw oplossing is gerijpt.

1. Maak een [gegevens bron object](/rest/api/searchservice/create-data-source) in azure Cognitive Search om een Connection String op te geven voor het ophalen van gegevens.

1. Maak een [vaardig heden](/rest/api/searchservice/create-skillset) met verrijkings stappen.

1. Definieer het [index schema](/rest/api/searchservice/create-index). De verzameling *velden* bevat velden uit de bron gegevens. U moet ook extra velden uitstuben voor het opslaan van gegenereerde waarden voor inhoud die tijdens verrijking is gemaakt.

1. Definieer de [Indexeer functie](/rest/api/searchservice/create-indexer) die verwijst naar de gegevens bron, de vaardig heden en de index.

1. Voeg in de Indexeer functie *outputFieldMappings* toe. In deze sectie wordt de uitvoer van de vaardig heden (in stap 3) toegewezen aan de invoer velden in het index schema (in stap 4).

1. *Maak Create Indexing* -aanvraag die u zojuist hebt gemaakt (een post-aanvraag met een indexers definitie in de hoofd tekst van de aanvraag) om de Indexeer functie in azure Cognitive Search te drukken. Deze stap bestaat uit het uitvoeren van de Indexeer functie en het aanroepen van de pijp lijn.

1. Voer query's uit om de resultaten te evalueren en de code te wijzigen om vaardig heden, schema of Indexeer configuratie bij te werken.

1. [Stel de Indexeer functie opnieuw](search-howto-reindex.md) in voordat u de pijp lijn opnieuw bouwt.

## <a name="next-steps"></a>Volgende stappen

+ [Uitgebreide documentatie koppelingen voor AI-verrijking](cognitive-search-resources-documentation.md)
+ [Voor beeld: een aangepaste vaardigheid maken voor AI-verrijking (C#)](cognitive-search-create-custom-skill-example.md)
+ [Quick Start: AI-verrijking in een portal door lopen](cognitive-search-quickstart-blob.md)
+ [Zelf studie: meer informatie over de AI-verrijkings Api's](cognitive-search-tutorial-blob.md)
+ [Kennisarchief](knowledge-store-concept-intro.md)
+ [Een kennis archief maken in REST](knowledge-store-create-rest.md)
+ [Tips voor probleemoplossing](cognitive-search-concept-troubleshooting.md)
