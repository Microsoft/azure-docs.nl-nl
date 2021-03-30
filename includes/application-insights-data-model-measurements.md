---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175793"
---
Verzameling aangepaste metingen. Gebruik deze verzameling om de benoemde meting te rapporteren die is gekoppeld aan het telemetrie-item. Typische gebruiks voorbeelden zijn:
- de grootte van de nettolading van de telemetrie van de afhankelijkheid
- het aantal wachtrij-items dat is verwerkt door de aanvraag-telemetrie
- de tijd die de klant heeft genomen bij het volt ooien van de stap in de telemetrie van de wizard stap voltooiings gebeurtenis.

U kunt een query uitvoeren op [aangepaste metingen](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) in Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Aangepaste metingen zijn gekoppeld aan het telemetrie-item waarvan ze deel uitmaken. Ze zijn onderworpen aan steek proeven met het telemetrie-item met deze metingen. Gebruik [metrische telemetrie](../articles/azure-monitor/app/api-custom-events-metrics.md)om een meting met een waarde onafhankelijk van andere telemetrie-typen bij te houden.

Maximale sleutel lengte: 150
