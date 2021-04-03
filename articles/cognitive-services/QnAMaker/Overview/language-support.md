---
title: Taal ondersteuning-QnA Maker
titleSuffix: Azure Cognitive Services
description: Een lijst met cultuur, natuurlijke talen die door QnA Maker worden ondersteund voor uw Knowledge Base. Maak geen talen in dezelfde Knowledge Base.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97605056"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Taal ondersteuning voor een QnA Maker resource en kennis bases

In dit artikel worden de taal ondersteunings opties voor QnA Maker resources en Knowledge bases beschreven. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

De taal voor de service wordt geselecteerd wanneer u de eerste Knowledge Base maakt in de resource. Alle aanvullende kennis grondslagen in de resource moeten zich in dezelfde taal bekomen. 

De taal bepaalt de relevantie van de resultaten QnA Maker worden uitgevoerd als reactie op gebruikers query's. De QnA Maker resource en alle kennis grondslagen in die resource ondersteunen één taal. De ene taal is nodig om de beste antwoord resultaten te bieden voor een query.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

In QnA Maker beheerd, kunt u taal instellingen maken op basis van het individuele kennis niveau. Deze instelling kan alleen worden ingeschakeld met de kennis basis van de service. Eenmaal ingesteld, kunnen de taal instellingen voor de service niet meer worden gewijzigd. 

Als u taal instellingen selecteert die specifiek zijn voor kennis, is het toegestaan om kennis gebaseerd te maken voor verschillende talen in de service zelf. 

---

## <a name="single-language-per-resource"></a>Eén taal per resource

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Overweeg de volgende:

* Een QnA Maker-service en alle kennis slagen ondersteunen slechts één taal.
* De taal wordt expliciet ingesteld wanneer de eerste kennis basis van de service wordt gemaakt.
* De taal wordt bepaald op basis van de bestanden en Url's die worden toegevoegd wanneer de Knowledge Base wordt gemaakt.
* De taal kan niet worden gewijzigd voor andere kennis grondslagen in de service.
* De taal wordt door de Cognitive Search-service (Rank #1) en de QnA Maker-service (Rank #2) gebruikt om het beste antwoord op een query te genereren.

# <a name="qnamaker-managed-preview"></a>[Beheerde QnAMaker (preview-versie)](#tab/v2)
![Taal instelling in door QnA Maker beheerd](../media/language-support/language-setting-managed.png)

Als u **het selectie vakje niet inschakelt om de taal instelling per kennis-basis in te scha kelen**, moet u rekening houden met het volgende: 
* Een QnA Maker-service en alle Knowledge bases ondersteunen slechts één taal.
* De taal wordt expliciet ingesteld wanneer de eerste kennis basis van de service wordt gemaakt
* De taal wordt bepaald op basis van de bestanden en Url's die worden toegevoegd wanneer de Knowledge Base wordt gemaakt.
* De taal kan niet worden gewijzigd voor andere kennis grondslagen in de service
* De taal wordt door de Cognitive Search-service (Rank #1) en de QnA Maker-service (Rank #2) gebruikt voor het genereren van het beste antwoord op een query

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Ondersteuning van meerdere talen in één QnA Maker resource

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)
Deze functionaliteit wordt niet ondersteund in onze huidige, algemeen beschik bare (GA) stabiele release. Bekijk QnA Maker die worden beheerd om deze functionaliteit te testen. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)
* Wanneer u de eerste Knowledge Base maakt in uw service, kunt u kiezen voor het inschakelen van de taal instelling per Knowledge Base. Schakel het selectie vakje in om kennis grondslagen te maken die horen bij verschillende talen binnen één service.
* De taal instelling kan niet worden gewijzigd voor de service, nadat de eerste Knowledge Base is gemaakt.
* Als u taal instellingen inschakelt die specifiek zijn voor elke kennis database, dan moet u in plaats van één test index voor de service één test index per Knowledge Base hebben. 

![Taal instelling in door QnA Maker beheerd](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Ondersteuning van meerdere talen in één kennis database

Als u een Knowledge Base-systeem moet ondersteunen dat verschillende talen bevat, kunt u het volgende doen:

* Gebruik de [Translator-service](../../translator/translator-info-overview.md) om een vraag in één taal te vertalen voordat u de vraag naar uw kennis database verzendt. Zo kunt u zich richten op de kwaliteit van één taal en de kwaliteit van de alternatieve vragen en antwoorden.
* Maak een QnA Maker resource en een kennis database in die resource voor elke taal. Hierdoor kunt u afzonderlijke alternatieve vragen en antwoord tekst beheren die voor elke taal meer worden genuanced. Dit biedt veel meer flexibiliteit, maar vereist veel hogere onderhouds kosten wanneer de vragen of antwoorden in alle talen worden gewijzigd.


## <a name="languages-supported"></a>Ondersteunde talen

De volgende lijst bevat de talen die worden ondersteund voor een QnA Maker bron. 

| Taal |
|--|
| Arabisch |
| Armeens |
| Bengaals |
| Baskisch |
| Bulgaars |
| Catalaans |
| Chinese_Simplified |
| Chinese_Traditional |
| Kroatisch |
| Tsjechisch |
| Deens |
| Nederlands |
| Engels |
| Ests |
| Fins |
| Frans |
| Galicisch |
| Duits |
| Grieks |
| Gujarati |
| Hebreeuws |
| Hindi |
| Hongaars |
| IJslands |
| Indonesisch |
| Iers |
| Italiaans |
| Japans |
| Kannada |
| Koreaans |
| Lets |
| Litouws |
| Malayalam |
| Maleisisch |
| Noors |
| Pools |
| Portugees |
| Punjabi |
| Roemeens |
| Russisch |
| Serbian_Cyrillic |
| Serbian_Latin |
| Slowaaks |
| Sloveens |
| Spaans |
| Zweeds |
| Tamil |
| Telugu |
| Thai |
| Turks |
| Oekraïens |
| Urdu |
| Vietnamees |

## <a name="query-matching-and-relevance"></a>Overeenkomende treffers en relevantie van query's
QnA Maker is afhankelijk van de [taal analyse functies van Azure Cognitive Search](/rest/api/searchservice/language-support) voor het leveren van resultaten.

Hoewel de mogelijkheden van Azure Cognitive Search op pari zijn voor ondersteunde talen, heeft QnA Maker een extra rangorde die zich boven de Azure Search-resultaten bevindt. In dit classificatie model gebruiken we enkele speciale semantische en op woord gebaseerde functies in de volgende talen.

|Talen met extra rangorde|
|--|
|Chinees|
|Tsjechisch|
|Nederlands|
|Engels|
|Frans|
|Duits|
|Hongaars|
|Italiaans|
|Japans|
|Koreaans|
|Pools|
|Portugees|
|Spaans|
|Zweeds|

Deze extra classificatie is een interne werking van de QnA Makerer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Taal selecteren](../index.yml)