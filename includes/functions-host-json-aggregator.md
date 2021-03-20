---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92167646"
---
Hiermee geeft u op hoeveel functieaanroepen worden geaggregeerd wanneer u [meetwaarden voor Application Insights berekent](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Eigenschap |Standaard  | Beschrijving |
|---------|---------|---------| 
|batchSize|1000|Maximaal aantal aanvragen om samen te voegen.| 
|flushTimeout|00:00:30|Maximale tijdsperiode om samen te voegen.| 

Functieaanroepen worden geaggregeerd wanneer de eerste van de twee limieten is bereikt.
