---
title: Docker pull voor de Sentimentanalyse container
titleSuffix: Azure Cognitive Services
description: Docker pull-opdracht voor Sentimentanalyse container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564616"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker pull voor de Sentimentanalyse v3-container

De container sentimentanalyse v3 is beschikbaar in verschillende talen. Gebruik de onderstaande opdracht om de container voor de Engelse container te downloaden. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Als u de container voor een andere taal wilt downloaden, vervangt `en` u door een van de onderstaande taalcodes. 

| Text Analytics Container | Taalcode |
|--|--|
| Chinese-Simplified    |   `zh-hans`   |
| Chinese-Traditional   |   `zh-hant`   |
| Nederlands                 |     `nl`      |
| Engels               |     `en`      |
| Frans                |     `fr`      |
| Duits                |     `de`      |
| Hindi                 |    `hi`       |
| Italiaans               |     `it`      |
| Japans              |     `ja`      |
| Koreaans                |     `ko`      |
| Noors (Bokmål)   |     `no`      |
| Portugees (Brazilië)   |    `pt-BR`    |
| Portugees (Portugal) |    `pt-PT`    |
| Spaans               |     `es`      |
| Turks               |     `tr`      |

Zie voor een volledige beschrijving van de beschikbare tags voor de Text Analytics containers [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
