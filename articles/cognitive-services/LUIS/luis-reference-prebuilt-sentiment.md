---
title: Sentiment-analyse-LUIS
titleSuffix: Azure Cognitive Services
description: Als sentiment-analyse is geconfigureerd, omvat de LUIS JSON-antwoord sentiment analyse.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 2e8e5a127741625fde7910aaabd421836148fc35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018664"
---
# <a name="sentiment-analysis"></a>Sentimentanalyse
Als sentiment-analyse is geconfigureerd, omvat de LUIS JSON-antwoord sentiment analyse. Meer informatie over sentiment analyse vindt u in de [Text Analytics](../text-analytics/index.yml) -documentatie.

LUIS maakt gebruik van Text Analytics v2. 

## <a name="resolution-for-sentiment"></a>Oplossing voor sentiment

Sentiment-gegevens is een score tussen 1 en 0 die de positieve waarde (dichter bij 1) of negatief (dichter bij 0) sentiment van de gegevens aangeeft.

#### <a name="english-language"></a>[Engelse taal](#tab/english)

Wanneer de cultuur is `en-us` , is de reactie:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Andere talen](#tab/other-languages)

Voor alle andere cult uren is de reactie:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).