---
title: De model versie opgeven in Text Analytics v3
titleSuffix: Azure Cognitive Services
description: Meer informatie over het opgeven van het Text Analytics-API model dat wordt gebruikt voor uw gegevens.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: ee3a56fed87f493d79796c0d91e5324c94e87eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599354"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Model versie beheer in de Text Analytics-API

Met versie 3 van de Text Analytics-API kunt u de model versie kiezen die wordt gebruikt voor uw gegevens. Gebruik de optionele `model-version` para meter om de versie van het model in uw API-aanvragen te selecteren. Bijvoorbeeld: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Als deze para meter niet is opgegeven, wordt de API standaard ingesteld op de laatste stabiele versie. 

## <a name="available-versions"></a>Beschik bare versies

Gebruik de onderstaande tabel om te bepalen welke model versies worden ondersteund door elk gehoste eind punt.


| Eindpunt                        | Ondersteunde versies                                     | nieuwste versie |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`  | `2021-01-15`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |
| `/entities/health`              | `2021-03-01`                           | `2021-03-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Meer informatie over de updates voor deze modellen vindt u in [Wat is er nieuw](../whats-new.md).

## <a name="text-analytics-for-health"></a>Text Analytics voor status

De [Text Analytics voor de status](../how-tos/text-analytics-for-health.md) container maakt gebruik van afzonderlijke model versies dan de bovenstaande API-eind punten.  Houd er rekening mee dat er slechts één model versie beschikbaar is per container installatie kopie.

| Eindpunt                        | Tag container installatie kopie                     | Model versie |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.015370001-onprem-amd64` of de nieuwste          | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Text Analytics](../overview.md)
* [Sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Herkenning van entiteiten](../how-tos/text-analytics-how-to-entity-linking.md)
