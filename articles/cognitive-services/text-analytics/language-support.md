---
title: Taalondersteuning - Text Analytics API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen die worden ondersteund door de Text Analytics API. In dit artikel wordt uitgelegd welke talen worden ondersteund voor elke bewerking.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: c0d91f803822e018f4363bb78d9138e2efe16f8a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531435"
---
# <a name="text-analytics-api-v3-language-support"></a>Text Analytics api v3-taalondersteuning 

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment-analysis)

| Taal              | Taalcode | v3-ondersteuning | Versie van v3-model starten: |              Notities |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` ook geaccepteerd |
| Chinese-Traditional   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Nederlands                 |     `nl`      |     ✓      |         2019-10-01        |                    |
| Engels               |     `en`      |     ✓      |         2019-10-01         |                    |
| Frans                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Duits                |     `de`      |     ✓      |         2019-10-01         |                    |
| Hindi                 |    `hi`       |     ✓      |         2020-04-01         |                    |
| Italiaans               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japans              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Koreaans                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` ook geaccepteerd |
| Spaans               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turks               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Meningmining (alleen v3.1-preview)

| Taal              | Taalcode | Vanaf versie v3-model: |              Notities |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Engels               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Herkenning van benoemde entiteiten (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Alleen de entiteiten 'Persoon', 'Locatie' en 'Organisatie' worden geretourneerd voor talen die zijn gemarkeerd met *.

| Taal               | Taalcode | v3-ondersteuning | Vanaf versie v3-model: |       Notities        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabisch                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` ook geaccepteerd |
| Chinese-Traditional   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Tsjechisch                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Deens                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Nederlands                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Engels                |     `en`      |     ✓      |               2019-10-01        |                    |
| Fins               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Frans                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Duits                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Hebreeuws                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Hongaars             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italiaans               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japans              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreaans                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Noors (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` ook geaccepteerd |
| Pools                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` ook geaccepteerd |
| Russisch              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Spaans               |     `es`      |     ✓       |               2020-04-01        |                    |
| Zweeds               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turks               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/key-phrase-extraction)

| Taal              | Taalcode |  v3-ondersteuning | Beschikbaar vanaf versie v3-model: |       Notities        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Deens                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Nederlands                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Engels               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Fins               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Frans                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Duits                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Italiaans               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Japans              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Koreaans                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` ook geaccepteerd |
| Pools                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portugees (Portugal) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` ook geaccepteerd |
| Russisch               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Spaans               |     `es`      |     ✓      |                2019-10-01                 |                    |
| Zweeds               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entiteiten koppelen](#tab/entity-linking)

| Taal | Taalcode |  v3-ondersteuning | Beschikbaar vanaf versie v3-model: | Notities |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Engels  |     `en`      |     ✓      |                2019-10-01                 |       |
| Spaans  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Persoonlijk identificeerbare informatie (PII)](#tab/pii)

| Taal               | Taalcode | v3-ondersteuning | Vanaf versie v3-model: |       Notities        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` ook geaccepteerd |
| Engels                |     `en`      |     ✓      |               2020-07-01        |                    |
| Frans                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Duits                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Italiaans               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japans              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreaans                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` ook geaccepteerd |
| Spaans               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Taaldetectie](#tab/language-detection)

De Text Analytics-API kan een breed scala aan talen, varianten, dialecten en sommige regionale/culturele talen detecteren en gedetecteerde talen retourneren met hun naam en code. Text Analytics Taaldetectie taalcodeparameters voldoen aan de [BCP-47-standaard,](https://tools.ietf.org/html/bcp47) met de meeste ervan conform [ISO-639-1-id's.](https://www.iso.org/iso-639-language-codes.html) 

Als u inhoud hebt uitgedrukt in een minder vaak gebruikte taal, kunt u proberen Taaldetectie om te zien of er een code wordt retourneert. Het antwoord voor talen die niet kunnen worden gedetecteerd, is `unknown` .

| Taal | Taalcode | v3-ondersteuning | Beschikbaar vanaf versie v3-model: |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albanees|`sq`|✓|    |
|Amharic|`am`|✓|2021-01-05|
|Arabisch|`ar`|✓|    |
|Armeens|`hy`|✓|    |
|Assamees|`as`|✓|2021-01-05|
|Azerbeidzjaanse|`az`|✓|2021-01-05|
|Baskisch|`eu`|✓|    |
|Wit-Russisch|`be`|✓|    |
|Bengaals|`bn`|✓|    |
|Bosnisch|`bs`|✓|2020-09-01|
|Bulgaars|`bg`|✓|    |
|Birmese|`my`|✓|    |
|Catalaans|`ca`|✓|    |
|Central Khmer|`km`|✓|    |
|Chinees|`zh`|✓|    |
|Chinees (vereenvoudigd)|`zh_chs`|✓|    |
|Chinees (traditioneel)|`zh_cht`|✓|    |
|Corsicaanse|`co`|✓|2021-01-05|
|Kroatisch|`hr`|✓|    |
|Tsjechisch|`cs`|✓|    |
|Deens|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Nederlands|`nl`|✓|    |
|Engels|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Ests|`et`|✓|    |
|Fijisch|`fj`|✓|2020-09-01|
|Fins|`fi`|✓|    |
|Frans|`fr`|✓|    |
|Galicisch|`gl`|✓|    |
|Georgisch|`ka`|✓|    |
|Duits|`de`|✓|    |
|Grieks|`el`|✓|    |
|Gujarati|`gu`|✓|    |
|Haïtiaanse|`ht`|✓|    |
|Hausa|`ha`|✓|2021-01-05|
|Hebreeuws|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Hongaars|`hu`|✓|    |
|IJslands|`is`|✓|    |
|Igbo|`ig`|✓|2021-01-05|
|Indonesisch|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Iers|`ga`|✓|    |
|Italiaans|`it`|✓|    |
|Japans|`ja`|✓|    |
|Javaans|`jv`|✓|2021-01-05|
|Kannada|`kn`|✓|    |
|Kazachs|`kk`|✓|2020-09-01|
|Kinyarwanda|`rw`|✓|2021-01-05|
|Kirghiz|`ky`|✓|2021-01-05|
|Koreaans|`ko`|✓|    |
|Koerdische|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latijnse|`la`|✓|    |
|Lets|`lv`|✓|    |
|Litouws|`lt`|✓|    |
|Luxemburgs|`lb`|✓|2021-01-05|
|Macedonische|`mk`|✓|    |
|Malagassisch|`mg`|✓|2020-09-01|
|Maleisisch|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltees|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Mahrati|`mr`|✓|2020-09-01|
|Mongools|`mn`|✓|2021-01-05|
|Nepalees|`ne`|✓|2021-01-05|
|Noors|`no`|✓|    |
|Noors (Nynorsk)|`nn`|✓|    |
|Odia|`or`|✓|    |
|Pasht|`ps`|✓|    |
|Perzisch|`fa`|✓|    |
|Pools|`pl`|✓|    |
|Portugees|`pt`|✓|    |
|Punjabi|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Roemeens|`ro`|✓|    |
|Russisch|`ru`|✓|    |
|Samoaans|`sm`|✓|2020-09-01|
|Servisch|`sr`|✓|    |
|Shona|`sn`|✓|2021-01-05|
|Sindhi|`sd`|✓|2021-01-05|
|Sinhala|`si`|✓|    |
|Slowaaks|`sk`|✓|    |
|Sloveens|`sl`|✓|    |
|Somalische|`so`|✓|    |
|Spaans|`es`|✓|    |
|Sundanese|`su`|✓|2021-01-05|
|Swahili|`sw`|✓|    |
|Zweeds|`sv`|✓|    |
|Philipijns|`tl`|✓|    |
|Tahitiaans|`ty`|✓|2020-09-01|
|Tajik|`tg`|✓|2021-01-05|
|Tamil|`ta`|✓|    |
|Tataars|`tt`|✓|2021-01-05|
|Telugu|`te`|✓|    |
|Thai|`th`|✓|    |
|Tibetaanse|`bo`|✓|2021-01-05|
|Tigrinya|`ti`|✓|2021-01-05|
|Tongaans|`to`|✓|2020-09-01|
|Turks|`tr`|✓|2021-01-05|
|Turkmen|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Yoruba|`yo`|✓|2021-01-05|
|Zulu|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Zie ook

* [Wat is de Text Analytics-API?](overview.md)   
