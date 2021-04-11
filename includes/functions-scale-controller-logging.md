---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081048"
---
| Eigenschap | Beschrijving |
|--|--|
|**`<DESTINATION>`**| De bestemming waarnaar de logboeken worden verzonden. Geldige waarden zijn `AppInsights` en `Blob` .<br/>Wanneer u gebruikt `AppInsights` , moet u ervoor zorgen dat [Application Insights is ingeschakeld in uw functie-app](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration).<br/>Wanneer u de bestemming instelt op `Blob` , worden logboeken gemaakt in een BLOB-container met `azure-functions-scale-controller` de naam in het standaard opslag account dat is ingesteld in de `AzureWebJobsStorage` toepassings instelling. |
|**`<VERBOSITY>`** | Hiermee geeft u het niveau van logboek registratie op. Ondersteunde waarden zijn `None` , `Warning` en `Verbose` .<br/>Als deze instelling `Verbose` is ingesteld op, registreert de schaal controller een reden voor elke wijziging in het aantal werk nemers, evenals informatie over de triggers die in deze beslissingen zijn opgenomen. Uitgebreide logboeken bevatten trigger waarschuwingen en de hashes die worden gebruikt door de triggers vóór en nadat de schaal controller wordt uitgevoerd. |

> [!TIP]
> Houd er wel bij dat de logboek registratie van de schaal controller is ingeschakeld. Dit is van invloed op de [mogelijke kosten voor het bewaken van uw functie-app](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Overweeg logboek registratie in te scha kelen totdat u voldoende gegevens hebt verzameld om te begrijpen hoe de schaal controller gedraagt en vervolgens uit te scha kelen.
