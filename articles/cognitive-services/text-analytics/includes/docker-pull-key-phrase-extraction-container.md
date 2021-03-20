---
title: Docker-pull voor de Sleuteltermextractie container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Sleuteltermextractie container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90906023"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-pull voor de Sleuteltermextractie container

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [Sleuteltermextractie](https://go.microsoft.com/fwlink/?linkid=2018757) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
