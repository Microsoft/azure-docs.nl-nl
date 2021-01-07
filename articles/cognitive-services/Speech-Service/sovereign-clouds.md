---
title: Soevereine Clouds-Speech-Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van soevereine Clouds
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/26/2020
ms.author: alexeyo
ms.openlocfilehash: a1c3fcf868af76865eec9fa2be4f0fdb58074867
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964453"
---
# <a name="speech-services-in-sovereign-clouds"></a>Spraak Services in soevereine Clouds

## <a name="azure-government-united-states"></a>Azure Government (Verenigde Staten)

Alleen beschikbaar voor Amerikaanse overheids instanties en hun partners. Zie hier voor meer informatie [over](../../azure-government/documentation-government-welcome.md) Azure Government [.](../../azure-government/compare-azure-government-global-azure.md)

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Secties**
  - VS (overheid) - Arizona
  - VS (overheid) - Virginia
- **Beschik bare prijs Categorieën:**
  - Free (F0) en Standard (S0). Meer informatie vindt u [hier](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
- **Ondersteunde functies:**
  - Spraak naar tekst
    - Aangepaste spraak (akoestische model (AM) en taal model (LM) aanpassing)
      - [Speech Studio](https://speech.azure.us/)
  - Tekst naar spraak
  - Spraak conversie
- **Niet-ondersteunde functies:**
  - Neurale Voice
  - Aangepaste stem
- **Ondersteunde talen:**
  - Arabisch (AR-*)
  - Chinees (ZH-*)
  - Engels (en-*)
  - Frans (FR-*)
  - Duits (de-*)
  - Hindi (Hi-IN)
  - Koreaans (ko-KR)
  - Russisch (ru-RU)
  - Spaans (ES-*)

### <a name="endpoint-information"></a>Eindpunt gegevens

Deze sectie bevat informatie over spraak Services-eind punten voor het gebruik met [spraak-SDK](speech-sdk.md), [spraak-naar-tekst rest API](rest-speech-to-text.md)en [tekst-naar-spraak-rest API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Speech Services-REST API

Spraak Services REST API eind punten in Azure Government hebben de volgende indeling:

|  REST API type/bewerking | Eindpunt indeling |
|--|--|
| Toegangs token | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Spraak-naar-tekst REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [Spraak-naar-tekst REST API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [REST API voor tekst-naar-spraak](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Vervang door `<REGION_IDENTIFIER>` de id die overeenkomt met de regio van uw abonnement uit deze tabel:

|                     | Regio-id |
|--|--|
| **VS (overheid) - Arizona**  | `usgovarizona` |
| **VS (overheid) - Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Speech-SDK

Voor de Speech-SDK in soevereine Clouds moet u ' van host ' gebruiken voor het instantiëren van de `SpeechConfig` klasse of `--host` optie van de [spraak-cli](spx-overview.md). (U kunt ook ' vanaf eind punt ' instantiëren en `--endpoint` Optie voor spraak-CLI).

`SpeechConfig` klasse moet als volgt worden geïnstantieerd:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
De spraak-CLI moet als volgt worden gebruikt (Let op de `--host` optie):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Vervang door `subscriptionKey` de code van uw spraak bron. Vervang door `usGovHost` de expressie die overeenkomt met de vereiste service aanbieding en de regio van uw abonnement uit deze tabel:

|  Regio/service-aanbieding | Host-expressie |
|--|--|
| **VS (overheid) - Arizona** | |
| Spraak naar tekst | `wss://usgovarizona.stt.speech.azure.us` |
| Tekst naar spraak | `https://usgovarizona.tts.speech.azure.us` |
| **VS (overheid) - Virginia** | |
| Spraak naar tekst | `wss://usgovvirginia.stt.speech.azure.us` |
| Tekst naar spraak | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure China

Beschikbaar voor organisaties met een zakelijke aanwezigheid in China. Meer informatie over Azure China vindt u [hier.](/azure/china/overview-operations) 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Secties**
  - China - oost 2
- **Beschik bare prijs Categorieën:**
  - Free (F0) en Standard (S0). Meer informatie vindt u [hier](https://www.azure.cn/pricing/details/cognitive-services/index.html)
- **Ondersteunde functies:**
  - Spraak naar tekst
    - Aangepaste spraak (akoestische model (AM) en taal model (LM) aanpassing)
      - [Speech Studio](https://speech.azure.cn/)
  - Tekst naar spraak
  - Spraak conversie
- **Niet-ondersteunde functies:**
  - Neurale Voice
  - Aangepaste stem
- **Ondersteunde talen:**
  - Arabisch (AR-*)
  - Chinees (ZH-*)
  - Engels (en-*)
  - Frans (FR-*)
  - Duits (de-*)
  - Hindi (Hi-IN)
  - Koreaans (ko-KR)
  - Russisch (ru-RU)
  - Spaans (ES-*)

### <a name="endpoint-information"></a>Eindpunt gegevens

Deze sectie bevat informatie over spraak Services-eind punten voor het gebruik met [spraak-SDK](speech-sdk.md), [spraak-naar-tekst rest API](rest-speech-to-text.md)en [tekst-naar-spraak-rest API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>Speech Services-REST API

Spraak Services REST API eind punten in azure China hebben de volgende indeling:

|  REST API type/bewerking | Eindpunt indeling |
|--|--|
| Toegangs token | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Spraak-naar-tekst REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [Spraak-naar-tekst REST API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [REST API voor tekst-naar-spraak](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Vervang door `<REGION_IDENTIFIER>` de id die overeenkomt met de regio van uw abonnement uit deze tabel:

|                     | Regio-id |
|--|--|
| **China - oost 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Speech-SDK

Voor de Speech-SDK in soevereine Clouds moet u ' van host ' gebruiken voor het instantiëren van de `SpeechConfig` klasse of `--host` optie van de [spraak-cli](spx-overview.md). (U kunt ook ' vanaf eind punt ' instantiëren en `--endpoint` Optie voor spraak-CLI).

`SpeechConfig` klasse moet als volgt worden geïnstantieerd:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
De spraak-CLI moet als volgt worden gebruikt (Let op de `--host` optie):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Vervang door `subscriptionKey` de code van uw spraak bron. Vervang door `azCnHost` de expressie die overeenkomt met de vereiste service aanbieding en de regio van uw abonnement uit deze tabel:

|  Regio/service-aanbieding | Host-expressie |
|--|--|
| **China - oost 2** | |
| Spraak naar tekst | `wss://chinaeast2.stt.speech.azure.cn` |
| Tekst naar spraak | `https://chinaeast2.tts.speech.azure.cn` |