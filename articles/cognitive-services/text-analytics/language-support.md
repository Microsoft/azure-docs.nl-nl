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
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 180de56e3c158802460d2ff995041e8572d4dcd7
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97844973"
---
# <a name="text-analytics-api-v3-language-support"></a>Ondersteuning voor Text Analytics-API v3-taal 

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment-analysis)

| Taal              | Taalcode | v2-ondersteuning | v3-ondersteuning | De versie van het v3-model wordt gestart: |              Opmerkingen |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` ook geaccepteerd |
| Chinese-Traditional   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Deens               |     `da`      |     ✓      |            |                            |                    |
| Nederlands                 |     `nl`      |     ✓      |            |                            |                    |
| Engels               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Fins               |     `fi`      |     ✓      |            |                            |                    |
| Frans                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Duits                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grieks                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italiaans               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japans              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreaans                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Pools                |     `pl`      |     ✓      |            |                            |                    |
| Portugees (Brazilië)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` ook geaccepteerd |
| Russisch               |     `ru`      |     ✓      |            |                            |                    |
| Spaans               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Zweeds               |     `sv`      |     ✓      |            |                            |                    |
| Turks               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Opinie-analyse (alleen v 3.1-Preview-versie)

| Taal              | Taalcode | Starten met versie van v3-model: |              Opmerkingen |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Engels               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[NER (Herkenning van benoemde entiteiten)](#tab/named-entity-recognition)

> [!NOTE]
> * NER v3 ondersteunt momenteel alleen Engelse en Spaanse talen. Als u NER v3 aanroept met een andere taal, retourneert de API v 2.1-resultaten, op voor waarde dat de taal wordt ondersteund in versie 2,1.
> * v 2.1 retourneert alleen de volledige set beschik bare entiteiten voor het Engels, vereenvoudigd Chinees, Frans, Duits en Spaans.  De entiteiten ' persoon ', ' locatie ' en ' organisatie ' worden geretourneerd voor de andere ondersteunde talen.

| Taal               | Taalcode | v 2.1-ondersteuning | v3-ondersteuning | Starten met versie van v3-model: |       Opmerkingen        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabisch                |     `ar`      |     ✓      |            |                                 |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |            |                                 | `zh` ook geaccepteerd |
| Chinese-Traditional   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Tsjechisch                 |     `cs`      |     ✓      |            |                                 |                    |
| Deens                |     `da`      |     ✓      |            |                                 |                    |
| Nederlands                 |     `nl`      |     ✓      |            |                                 |                    |
| Engels                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Fins               |     `fi`      |     ✓      |            |                                 |                    |
| Frans                 |     `fr`      |     ✓      |            |                                 |                    |
| Duits                 |     `de`      |     ✓      |            |                                 |                    |
| Hebreeuws                |     `he`      |     ✓      |            |                                 |                    |
| Hongaars             |     `hu`      |     ✓      |            |                                 |                    |
| Italiaans               |     `it`      |     ✓      |            |                                 |                    |
| Japans              |     `ja`      |     ✓      |            |                                 |                    |
| Koreaans                |     `ko`      |     ✓      |            |                                 |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb` ook geaccepteerd |
| Pools                |     `pl`      |     ✓      |            |                                 |                    |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` ook geaccepteerd |
| Russisch              |     `ru`      |     ✓      |            |                                 |                    |
| Spaans               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Zweeds               |     `sv`      |     ✓      |            |                                 |                    |
| Turks               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/key-phrase-extraction)

> [!NOTE]
> Model versies van Sleuteltermextractie vóór 2020-07-01 hebben een limiet van 64 tekens. Deze limiet is niet aanwezig in latere model versies.

| Taal              | Taalcode | v2-ondersteuning | v3-ondersteuning | Beschikbaar vanaf versie van v3-model: |       Opmerkingen        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Nederlands                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Engels               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Fins               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Frans                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Duits                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiaans               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japans              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreaans                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Noors (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` ook geaccepteerd |
| Pools                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugees (Brazilië)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugees (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` ook geaccepteerd |
| Russisch               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spaans               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Zweeds               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entiteiten koppelen](#tab/entity-linking)

| Taal | Taalcode | v2-ondersteuning | v3-ondersteuning | Beschikbaar vanaf versie van v3-model: | Opmerkingen |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Engels  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spaans  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Taaldetectie](#tab/language-detection)

De Text Analytics-API kan een breed scala aan talen, varianten, dialecten en bepaalde regionale/culturele talen detecteren en detecteerde talen retour neren met hun naam en code. Text Analytics Taaldetectie taal code parameters voldoen aan de [bcp-47](https://tools.ietf.org/html/bcp47) -standaard, met het meren deel van de [ISO-639-1-](https://www.iso.org/iso-639-language-codes.html) id's. 

Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u Taaldetectie proberen om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd is `unknown` .

| Taal | Taalcode | v3-ondersteuning | Beschikbaar vanaf versie van v3-model: |
|:-|:-:|:-:|:-:|
| Afrikaans | `af` | ✓ |  |
| Albanees | `sq` | ✓ |  |
| Arabisch | `ar` | ✓ |  |
| Armeens | `hy` | ✓ |  |
| Baskisch | `eu` | ✓ |  |
| Wit-Russisch | `be` | ✓ |  |
| Bengaals | `bn` | ✓ |  |
| Bosnisch | `bs` | ✓ | 2020-09-01 |
| Bulgaars | `bg` | ✓ |  |
| Birmaans | `my` | ✓ |  |
| Catalaans, Valenciaans | `ca` | ✓ |  |
| Centraal-Khmer | `km` | ✓ |  |
| Chinees | `zh` | ✓ |  |
| Chinees (vereenvoudigd) | `zh_chs` | ✓ |  |
| Chinees (traditioneel) | `zh_cht` | ✓ |  |
| Kroatisch | `hr` | ✓ |  |
| Tsjechisch | `cs` | ✓ |  |
| Deens | `da` | ✓ |  |
| Dari | `prs` | ✓ | 2020-09-01 |
| Divehi, Dhivehi, Maldivian | `dv` | ✓ |  |
| Nederlands, Vlaams | `nl` | ✓ |  |
| Engels | `en` | ✓ |  |
| Esperanto | `eo` | ✓ |  |
| Ests | `et` | ✓ |  |
| Fijisch | `fj` | ✓ | 2020-09-01 |
| Fins | `fi` | ✓ |  |
| Frans | `fr` | ✓ |  |
| Galicisch | `gl` | ✓ |  |
| Georgisch | `ka` | ✓ |  |
| Duits | `de` | ✓ |  |
| Grieks | `el` | ✓ |  |
| Gujarati | `gu` | ✓ |  |
| Haitian, Haitian Creole | `ht` | ✓ |  |
| Hebreeuws | `he` | ✓ |  |
| Hindi | `hi` | ✓ |  |
| Hmong Daw | `mww` | ✓ | 2020-09-01 |
| Hongaars | `hu` | ✓ |  |
| IJslands | `is` | ✓ |  |
| Indonesisch | `id` | ✓ |  |
| Inuktitut | `iu` | ✓ |  |
| Iers | `ga` | ✓ |  |
| Italiaans | `it` | ✓ |  |
| Japans | `ja` | ✓ |  |
| Kannada | `kn` | ✓ |  |
| Kazachs | `kk` | ✓ | 2020-09-01 |
| Koreaans | `ko` | ✓ |  |
| Koerdisch | `ku` | ✓ |  |
| Democratische | `lo` | ✓ |  |
| Latijnse | `la` | ✓ |  |
| Lets | `lv` | ✓ |  |
| Litouws | `lt` | ✓ |  |
| Macedonisch | `mk` | ✓ |  |
| Malagassisch | `mg` | ✓ | 2020-09-01 |
| Maleisisch | `ms` | ✓ |  |
| Malayalam | `ml` | ✓ |  |
| Maltees | `mt` | ✓ |  |
| Maori | `mi` | ✓ | 2020-09-01 |
| Mahrati | `mr` | ✓ | 2020-09-01 |
| Noors | `no` | ✓ |  |
| Noors (Nynorsk) | `nn` | ✓ |  |
| Odia | `or` | ✓ |  |
| Pashto, Pushto | `ps` | ✓ |  |
| Perzisch | `fa` | ✓ |  |
| Pools | `pl` | ✓ |  |
| Portugees | `pt` | ✓ |  |
| Punjabi, Panjabi | `pa` | ✓ |  |
| Queretaro Otomi | `otq` | ✓ | 2020-09-01 |
| Roemeens, Moldavian, Moldavië | `ro` | ✓ |  |
| Russisch | `ru` | ✓ |  |
| Samoaans | `sm` | ✓ | 2020-09-01 |
| Servisch | `sr` | ✓ |  |
| Sinhala, Sinhalese | `si` | ✓ |  |
| Slowaaks | `sk` | ✓ |  |
| Sloveens | `sl` | ✓ |  |
| Somalisch | `so` | ✓ |  |
| Spaans, Castilië | `es` | ✓ |  |
| Swahili | `sw` | ✓ |  |
| Zweeds | `sv` | ✓ |  |
| Tagalog | `tl` | ✓ |  |
| Tahitiaans | `ty` | ✓ | 2020-09-01 |
| Tamil | `ta` | ✓ |  |
| Telugu | `te` | ✓ |  |
| Thai | `th` | ✓ |  |
| Tongaans | `to` | ✓ | 2020-09-01 |
| Turks | `tr` | ✓ |  |
| Oekraïens | `uk` | ✓ |  |
| Urdu | `ur` | ✓ |  |
| Oezbeeks | `uz` | ✓ |  |
| Vietnamees | `vi` | ✓ |  |
| Welsh | `cy` | ✓ |  |
| Jiddisch | `yi` | ✓ |  |
| Yucateeks Maya | `yua` | ✓ |  |

---

## <a name="see-also"></a>Zie tevens

* [Wat is de Text Analytics-API?](overview.md)   
