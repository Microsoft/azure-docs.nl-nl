---
title: Migratie handleiding voor v2 van de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u uw toepassingen kunt verplaatsen om versie 3 van de Text Analytics-API te gebruiken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 31cba884cff490d43341a4c52f24c32b1e253e42
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967326"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migreren naar versie 3. x van de Text Analytics-API

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Als u versie 2,1 van de Text Analytics-API gebruikt, helpt dit artikel u bij het upgraden van uw toepassing tot het gebruik van versie 3. x. Versie 3,0 is algemeen beschikbaar en introduceert nieuwe functies zoals uitgebreide [entity Recognition (ner)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) en [model versie beheer](concepts/model-versioning.md). Er is ook een preview-versie van v 3.1 (v 3.1-Preview. x) beschikbaar, waarmee u functies zoals [opinie Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)kunt toevoegen. De modellen die in v2 worden gebruikt, ontvangen geen toekomstige updates. 

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment-analysis)

## <a name="feature-changes"></a>Functie wijzigingen 

Sentimentanalyse in versie 2,1 retourneert sentiment scores tussen 0 en 1 voor elk document dat naar de API wordt verzonden, met scores dichter bij 1 en dat meer positieve sentiment. In plaats daarvan retourneert versie 3 sentiment-labels (zoals ' positief ' of ' negatief ') voor zowel de zinnen als het document als geheel en de bijbehorende vertrouwens scores. 

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar het v3-eind punt voor sentiment analyse. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . U moet de toepassing ook bijwerken om de sentiment-labels te gebruiken die in de [API-reactie](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)worden geretourneerd. 

Zie de referentie documentatie voor voor beelden van het JSON-antwoord.
* [Versie 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Versie 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Versie 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[NER en entiteits koppeling](#tab/named-entity-recognition)

## <a name="feature-changes"></a>Functie wijzigingen

> [!NOTE] 
> Op dit moment worden [v3-entiteits categorieën](named-entity-types.md) alleen geretourneerd op Engelse en Spaanse tekst. De API retourneert versie 2,1-resultaten voor aanvragen in andere talen, op voor waarde dat deze worden ondersteund in versie 2,1.

In versie 2,1 gebruikt de Text Analytics-API één eind punt voor benoemde entiteits herkenning (NER) en entiteits koppelingen. Versie 3 biedt uitgebreide detectie van benoemde entiteiten en maakt gebruik van afzonderlijke eind punten voor NER en aanvragen voor het koppelen van entiteiten. Vanaf v 3.1-Preview. 1 kan NER ook persoonlijke `pii` informatie en status gegevens detecteren `phi` . 

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar de V3-eind punten voor NER en/of entiteits koppeling.

Entiteiten koppelen
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

U moet uw toepassing ook bijwerken om de [entiteits categorieën](named-entity-types.md) te gebruiken die in de [API-reactie](how-tos/text-analytics-how-to-entity-linking.md#view-results)worden geretourneerd.

Zie de referentie documentatie voor voor beelden van het JSON-antwoord.
* [Versie 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Versie 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Versie 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[Taaldetectie](#tab/language-detection)

## <a name="feature-changes"></a>Functie wijzigingen 

De functie voor taal detectie is niet gewijzigd in v3 buiten de eindpunt versie, maar het JSON-antwoord zal `ConfidenceScore` in plaats van worden opgenomen `score` . V3 retourneert ook slechts één taal in de uitvoer. 

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar het v3-eind punt voor taal detectie. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . U moet ook de toepassing bijwerken zodat deze wordt gebruikt `ConfidenceScore` in plaats van `score` in de [reactie](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)van de API. 

Zie de referentie documentatie voor voor beelden van het JSON-antwoord.
* [Versie 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Versie 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Versie 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>Functie wijzigingen 

De functie voor het uitpakken van sleutel woorden is niet gewijzigd in v3 buiten de eindpunt versie.

## <a name="steps-to-migrate"></a>Te migreren stappen

### <a name="rest-api"></a>REST-API

Als uw toepassing het REST API gebruikt, werkt u het aanvraag eindpunt bij naar het v3-eind punt voor het uitpakken van de sleutel woord groep. Bijvoorbeeld: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Zie de referentie documentatie voor voor beelden van het JSON-antwoord.
* [Versie 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Versie 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Versie 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

### <a name="client-libraries"></a>Clientbibliotheken

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>Zie ook

* [Wat is de Text Analytics-API?](overview.md)
* [Taalondersteuning](language-support.md)
* [Versiebeheer model](concepts/model-versioning.md)
