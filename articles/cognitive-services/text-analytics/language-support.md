---
title: Taal ondersteuning-Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen die worden ondersteund door de Text Analytics-API. In dit artikel wordt uitgelegd welke talen voor elke bewerking worden ondersteund.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: f6a109c10491ad2eabb12069157e9e6f394bc1f4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736607"
---
# <a name="text-analytics-api-v3-language-support"></a>Ondersteuning voor Text Analytics-API v3-taal 

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment-analysis)

| Taal              | Taalcode | v3-ondersteuning | De versie van het v3-model wordt gestart: |              Notities |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` ook geaccepteerd |
| Chinese-Traditional   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Engels               |     `en`      |     ✓      |         2019-10-01         |                    |
| Frans                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Duits                |     `de`      |     ✓      |         2019-10-01         |                    |
| Italiaans               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japans              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Koreaans                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` ook geaccepteerd |
| Spaans               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turks               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Opinie-analyse (alleen v 3.1-Preview-versie)

| Taal              | Taalcode | Starten met versie van v3-model: |              Notities |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Engels               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Herkenning van benoemde entiteiten (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Er worden alleen entiteiten van ' persoon ', ' locatie ' en ' organisatie ' geretourneerd voor talen die zijn gemarkeerd met *.

| Taal               | Taalcode | v3-ondersteuning | Starten met versie van v3-model: |       Notities        |
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

| Taal              | Taalcode |  v3-ondersteuning | Beschikbaar vanaf versie van v3-model: |       Notities        |
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

| Taal | Taalcode |  v3-ondersteuning | Beschikbaar vanaf versie van v3-model: | Notities |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Engels  |     `en`      |     ✓      |                2019-10-01                 |       |
| Spaans  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Persoonlijk identificeer bare informatie (PII)](#tab/pii)

| Taal               | Taalcode | v3-ondersteuning | Starten met versie van v3-model: |       Notities        |
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

De Text Analytics-API kan een breed scala aan talen, varianten, dialecten en bepaalde regionale/culturele talen detecteren en detecteerde talen retour neren met hun naam en code. Text Analytics Taaldetectie taal code parameters voldoen aan de [bcp-47](https://tools.ietf.org/html/bcp47) -standaard, met het meren deel van de [ISO-639-1-](https://www.iso.org/iso-639-language-codes.html) id's. 

Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u Taaldetectie proberen om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd is `unknown` .

| Taal | Taalcode | v3-ondersteuning | Beschikbaar vanaf versie van v3-model: |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albanees|`sq`|✓|    |
|Amhaars|`am`|✓|2021-01-05|
|Arabisch|`ar`|✓|    |
|Armeens|`hy`|✓|    |
|Assamees|`as`|✓|2021-01-05|
|Azerbeidzjan|`az`|✓|2021-01-05|
|Baskisch|`eu`|✓|    |
|Wit-Russisch|`be`|✓|    |
|Bengaals|`bn`|✓|    |
|Bosnisch|`bs`|✓|2020-09-01|
|Bulgaars|`bg`|✓|    |
|Birmaans|`my`|✓|    |
|Catalaans|`ca`|✓|    |
|Centraal-Khmer|`km`|✓|    |
|Chinees|`zh`|✓|    |
|Chinees (vereenvoudigd)|`zh_chs`|✓|    |
|Chinees (traditioneel)|`zh_cht`|✓|    |
|Corsicaans|`co`|✓|2021-01-05|
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
|Haitian|`ht`|✓|    |
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
|Kirgisische|`ky`|✓|2021-01-05|
|Koreaans|`ko`|✓|    |
|Koerdisch|`ku`|✓|    |
|Democratische|`lo`|✓|    |
|Latijnse|`la`|✓|    |
|Lets|`lv`|✓|    |
|Litouws|`lt`|✓|    |
|Luxemburgs|`lb`|✓|2021-01-05|
|Macedonisch|`mk`|✓|    |
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
|Somalisch|`so`|✓|    |
|Spaans|`es`|✓|    |
|Soendanees|`su`|✓|2021-01-05|
|Swahili|`sw`|✓|    |
|Zweeds|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitiaans|`ty`|✓|2020-09-01|
|Tadzjieks|`tg`|✓|2021-01-05|
|Tamil|`ta`|✓|    |
|Tataars|`tt`|✓|2021-01-05|
|Telugu|`te`|✓|    |
|Thai|`th`|✓|    |
|Tibetaanse|`bo`|✓|2021-01-05|
|Tigrinya|`ti`|✓|2021-01-05|
|Tongaans|`to`|✓|2020-09-01|
|Turks|`tr`|✓|2021-01-05|
|Turkmeens|`tk`|✓|2021-01-05|
|Xhosa|`xh`|✓|2021-01-05|
|Yoruba|`yo`|✓|2021-01-05|
|Zulu|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Zie ook

* [Wat is de Text Analytics-API?](overview.md)   
