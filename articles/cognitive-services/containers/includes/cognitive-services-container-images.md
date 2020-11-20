---
title: Container opslagplaatsen en installatie kopieën
services: cognitive-services
author: aahill
manager: nitinme
description: Twee tabellen die de container registers, opslag plaatsen en namen van installatie kopieën vertegenwoordigen voor alle cognitieve service aanbiedingen.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 2c2ebe9b419100163ae55c1be85dd1464904e841
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978426"
---
### <a name="container-repositories-and-images"></a>Container opslagplaatsen en installatie kopieën

De onderstaande tabellen geven een lijst van de beschik bare container installatie kopieën die door Azure Cognitive Services worden aangeboden. Zie voor een volledige lijst met alle beschik bare namen van container installatie kopieën en de beschik bare labels [Cognitive Services container-afbeeldings codes](../container-image-tags.md). 

#### <a name="generally-available"></a>Algemeen beschikbaar 

De micro soft Container Registry (MCR) syndicateert alle algemeen beschik bare containers voor Cognitive Services. De containers zijn ook rechtstreeks beschikbaar vanuit de [docker-hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Zie [Luis-containers uitvoeren en installeren](../../LUIS/luis-container-howto.md) voor meer informatie.

**Tekstanalyse**

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|
| Sentimentanalyse v3 (Engels) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Sentimentanalyse v3 (Spaans) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Sentimentanalyse v3 (Frans) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Sentimentanalyse v3 (Italiaans) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Sentimentanalyse v3 (Duits) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Sentimentanalyse v3 (Chinees-vereenvoudigd) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Sentimentanalyse v3 (Chinees-traditioneel) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Sentimentanalyse v3 (Japans) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Sentimentanalyse v3 (Portugees) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Sentimentanalyse v3 (Nederlands) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Zie [Text Analytics containers uitvoeren en installeren](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) voor meer informatie.

**Anomaliedetectie** 

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|
| Anomalie detectie | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Zie voor meer informatie [over het uitvoeren en installeren van anomalie detectie containers](../../anomaly-detector/anomaly-detector-container-howto.md) .

**Speech Service**

> [!NOTE]
> Als u spraak containers wilt gebruiken, moet u een [online aanvraag formulier](https://aka.ms/csgate)volt ooien.

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|
| [Spraak naar tekst](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Custom Speech-naar-tekst](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Tekst-naar-spraak](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>Preview ' niet-gegated ' 

De volgende preview-containers zijn openbaar beschikbaar. Micro soft Container Registry (MCR) publiceert alle openbaar beschik bare niet-gegateeerde containers voor Cognitive Services. De containers zijn ook rechtstreeks beschikbaar vanuit de [docker-hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>Voor beeld ' gated '

Voorheen werden gehoste preview-containers gehost op de `containerpreview.azurecr.io` opslag plaats. Vanaf september 22 2020 worden deze containers (met uitzonde ring van Text Analytics voor de status) gehost op de micro soft-Container Registry (MCR) en hoeven ze niet te worden gedownload met behulp van de opdracht docker login. Als u de container wilt gebruiken, moet u het volgende doen:

1. Vul een [aanvraag formulier](https://aka.ms/csgate) in met uw Azure-abonnements-id en gebruikers scenario. 
2. Na goed keuring downloadt u de container van de MCR. 
3. Gebruik de sleutel en het eind punt van een geschikte Azure-resource om de container tijdens runtime te verifiëren. 

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | V 2.0 lezen | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | V 3.1 lezen | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Computer Vision](../../computer-vision/spatial-analysis-container.md) | Ruimtelijke analyse | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=ctts) | Aangepaste tekst-naar-spraak | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=lid) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=ntts) | Tekst-naar-spraak Neural | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Text Analytics voor status](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Text Analytics voor status | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |