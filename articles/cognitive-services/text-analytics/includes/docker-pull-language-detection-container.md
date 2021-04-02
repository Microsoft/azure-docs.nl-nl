---
title: Docker-pull voor de Taaldetectie container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Taaldetectie container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906090"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-pull voor de Taaldetectie container

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [taaldetectie](https://go.microsoft.com/fwlink/?linkid=2018759) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
