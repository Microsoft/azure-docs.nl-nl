---
title: Ingebouwde tekst-en afbeeldings verwerking tijdens het indexeren
titleSuffix: Azure Cognitive Search
description: Gegevens extractie, natuurlijke taal, cognitieve vaardig heden voor afbeeldings verwerking Voeg semantiek en structuur toe aan onbewerkte inhoud in een Azure Cognitive Search-pijp lijn.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bde82a0ad50c2172e70337ad202b91af0a62c530
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547812"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Ingebouwde cognitieve vaardig heden voor tekst-en afbeeldings verwerking tijdens het indexeren (Azure Cognitive Search)

In dit artikel vindt u informatie over de cognitieve vaardig heden van Azure Cognitive Search die u in een vakkennisset kunt gebruiken om inhoud en structuur te extra heren. Een *cognitieve vaardigheid* is een module of bewerking waarmee inhoud op een of andere manier wordt getransformeerd. Vaak is het een onderdeel dat gegevens ophaalt of een structuur verkrijgt, waardoor we onze informatie over de invoer gegevens verg Roten. Bijna altijd is de uitvoer op basis van tekst. Een *vakkennisset* is het verzamelen van vaardig heden die de verrijkings pijplijn definiëren. 

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).
>
> De functie [incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) biedt u de mogelijkheid om een cache te bieden waarmee de Indexeer functie efficiënter kan werken bij het uitvoeren van alleen de cognitieve vaardig heden die nodig zijn als u uw vakkennisset in de toekomst wijzigt, zodat u tijd en geld bespaart.


## <a name="built-in-skills"></a>Ingebouwde vaardigheden

Verschillende vaardig heden zijn flexibel in wat ze gebruiken of produceren. Over het algemeen zijn de meeste vaardig heden gebaseerd op vooraf getrainde modellen, wat betekent dat u het model niet kunt trainen met uw eigen trainings gegevens. In de volgende tabel worden de vaardig heden van micro soft opgesomd en beschreven. 

| Vaardigheid | Beschrijving |
|-------|-------------|
|[Micro soft. skills. Text. CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen.|
| [Micro soft. skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | Deze vaardigheid maakt gebruik van een voortraind model voor het detecteren van belang rijke zinnen op basis van de term plaatsing, taal kundige regels, nabij de andere voor waarden en hoe ongebruikelijk de term binnen de bron gegevens valt. |
| [Micro soft. skills. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Deze vaardigheid maakt gebruik van een voortraind model om te detecteren welke taal wordt gebruikt (één taal-ID per document). Wanneer er meerdere talen worden gebruikt binnen dezelfde tekst segmenten, is de uitvoer de LCID van de taal die het meest in gebruik is.|
| [Micro soft. skills. Text. MergeSkill](cognitive-search-skill-textmerger.md) | Consolideert tekst van een verzameling velden in één veld.  |
| [Micro soft. skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Deze vaardigheid maakt gebruik van een voortraind model voor het instellen van entiteiten voor een vaste set categorieën: personen, locatie, organisatie, e-mails, Url's, datum/tijd-velden. |
| [Micro soft. skills. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Deze vaardigheid maakt gebruik van een voortraind model voor het extra heren van persoonlijke gegevens uit een bepaalde tekst. De vaardigheid biedt ook verschillende opties voor het maskeren van de gedetecteerde persoonlijke gegevens entiteiten in de tekst.  |
| [Micro soft. skills. Text. SentimentSkill](cognitive-search-skill-sentiment.md)  | Deze vaardigheid maakt gebruik van een pretraind model om positieve of negatieve sentiment te beoordelen op basis van een record. De Score ligt tussen 0 en 1. Er treden neutrale scores op voor de null-case wanneer sentiment niet kan worden gedetecteerd en voor tekst die als neutraal wordt beschouwd.  |
| [Micro soft. skills. Text. SplitSkill](cognitive-search-skill-textsplit.md) | Splitst tekst op pagina's zodat u inhoud stapsgewijs kunt verrijken of uitbreiden. |
| [Micro soft. skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Deze vaardigheid maakt gebruik van een voortraind model voor het vertalen van de invoer tekst in een groot aantal talen voor het gebruik van normalisatie-of lokalisatie-use cases. |
| [Micro soft. skills. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Deze vaardigheid maakt gebruik van een algoritme voor detectie van de afbeelding om de inhoud van een afbeelding te identificeren en een beschrijving van een tekst te genereren. |
| [Micro soft. skills. Vision. OcrSkill](cognitive-search-skill-ocr.md) | Optische teken herkenning. |
| [Micro soft. skills. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Staat filteren toe, het toewijzen van een standaard waarde en het samen voegen van gegevens op basis van een voor waarde.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extraheert inhoud uit een bestand binnen de verrijkings pijplijn. |
| [Micro soft. skills. util. ShaperSkill](cognitive-search-skill-shaper.md) | Wijst uitvoer toe aan een complex type (een gegevens type met meerdere delen, dat kan worden gebruikt voor een volledige naam, een adres van meerdere regels of een combi natie van achternaam en persoonlijke id). |
| [Micro soft. skills. custom. WebApiSkill](cognitive-search-custom-skill-web-api.md) | Hiermee kan een AI-verrijkings pijplijn worden uitgebreid door een HTTP-aanroep naar een aangepaste web-API te maken |
| [Micro soft. skills. custom. AmlSkill](cognitive-search-aml-skill.md) | Hiermee kan een AI-verrijkings pijplijn met een Azure Machine Learning model worden uitgebreid |


Zie [een aangepaste interface definiëren](cognitive-search-custom-skill-interface.md) voor meer informatie over het maken van een aangepaste [vaardigheid](cognitive-search-custom-skill-web-api.md). voor [Beeld: een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Zie ook

+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Definitie van aangepaste vaardigheden interface](cognitive-search-custom-skill-interface.md)
+ [Zelf studie: verrijkte indexering met AI](cognitive-search-tutorial-blob.md)
