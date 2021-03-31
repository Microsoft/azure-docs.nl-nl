---
title: Documentatie koppelingen voor AI-verrijking
titleSuffix: Azure Cognitive Search
description: Een aantekeningen lijst met artikelen, zelf studies, voor beelden en blog berichten die zijn gerelateerd aan de workloads voor AI-verrijking in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935344"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Documentatie bronnen voor AI-verrijking in azure Cognitive Search

AI-verrijking is een invoeg toepassing voor indexering op basis van indexeringen waarmee latente informatie in niet-tekst bronnen en niet-gedifferentieerde tekst wordt getransformeerd in volledige tekst Doorzoek bare inhoud in azure Cognitive Search. 

Voor ingebouwde verwerking worden de vooraf getrainde AI-modellen in Cognitive Services intern aangeroepen om de analyses uit te voeren. U kunt aangepaste modellen ook integreren met behulp van Azure Machine Learning, Azure Functions of andere benaderingen.

Hier volgt een geconsolideerde lijst van de documentatie voor AI-verrijking.

## <a name="concepts"></a>Concepten

+ [AI-verrijkingen](cognitive-search-concept-intro.md)
+ [Vaardighedensets](cognitive-search-working-with-skillsets.md)
+ [Foutopsporingssessies](cognitive-search-debug-session.md)
+ [Knowledge Stores](knowledge-store-concept-intro.md)
+ [Projecties](knowledge-store-projection-overview.md)
+ [Incrementele verrijking (hergebruik van een in cache opgeslagen verrijkt document)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Praktische instructies

+ [Snelstartgids: een cognitieve vaardigheids maken in de Azure Portal](cognitive-search-quickstart-blob.md)
+ [Zelf studie: verrijkte indexering met AI](cognitive-search-tutorial-blob.md)
+ [Zelf studie: wijzigingen in uw vaardig heden vaststellen, herstellen en door voeren met debug-sessies](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Knowledge Stores

+ [Snelstartgids: een kennis archief maken in de Azure Portal](knowledge-store-create-portal.md)
+ [Een kennisarchief maken met behulp van REST en Postman](knowledge-store-create-rest.md)
+ [Een kennis archief met Storage Explorer weer geven](knowledge-store-view-storage-explorer.md)
+ [Verbinding maken met een kennis archief met Power BI](knowledge-store-connect-power-bi.md)
+ [Voor beelden van projectie (verrijkingen vorm geven en exporteren)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Aangepaste vaardig heden (Geavanceerd)

+ [Een aangepaste vaardigheden Interface definiëren](cognitive-search-custom-skill-interface.md)
+ [Voor beeld: een aangepaste vaardigheid maken met behulp van Azure Functions (en Bing Entity Search Api's)](cognitive-search-create-custom-skill-example.md)
+ [Voor beeld: een aangepaste vaardigheid maken met behulp van python](cognitive-search-custom-skill-python.md)
+ [Voor beeld: een aangepaste vaardigheid maken met behulp van de formulier Recognizer](cognitive-search-custom-skill-form.md) 
+ [Voor beeld: een aangepaste vaardigheid maken met behulp van Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Instructies

+ [Een Cognitive Services-resource toevoegen](cognitive-search-attach-cognitive-services.md)
+ [Een set vaardigheden definiëren](cognitive-search-defining-skillset.md)
+ [verwijzen naar aantekeningen in een vaardig heden](cognitive-search-concept-annotations-syntax.md)
+ [Velden toewijzen aan een index](cognitive-search-output-field-mapping.md)
+ [Informatie uit installatiekopieën verwerken en extraheren](cognitive-search-concept-image-scenarios.md)
+ [Caching configureren voor incrementele verrijking](search-howto-incremental-index.md)
+ [Een Azure Cognitive Search-index opnieuw samen stellen](search-howto-reindex.md)
+ [Ontwerptips](cognitive-search-concept-troubleshooting.md)
+ [Veelvoorkomende fouten en waarschuwingen](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Verwijzingen voor vaardigheden

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
  + [Micro soft. skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Micro soft. skills. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Micro soft. skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Micro soft. skills. Text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Micro soft. skills. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Micro soft. skills. Text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Micro soft. skills. Text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Micro soft. skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Micro soft. skills. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Micro soft. skills. Vision. OcrSkill](cognitive-search-skill-ocr.md)
  + [Micro soft. skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Micro soft. skills. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Aangepaste vaardigheden
  + [Micro soft. skills. custom. AmlSkill](cognitive-search-aml-skill.md)
  + [Micro soft. skills. custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Afgeschafte vaardigheden](cognitive-search-skill-deprecated.md)
  + [Micro soft. skills. Text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API's

+ [REST API](/rest/api/searchservice/)
  + [Vaardig heden maken (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Indexeer functie maken (API-Version = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Zie ook

+ [Azure Cognitive Search REST API](/rest/api/searchservice/)
+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Wat is Azure Cognitive Search?](search-what-is-azure-search.md)