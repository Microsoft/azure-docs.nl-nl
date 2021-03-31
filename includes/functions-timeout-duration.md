---
title: bestand opnemen
description: bestand opnemen
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "77198323"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Time-outduur voor Function-app 

De time-outduur van een Function-app wordt gedefinieerd door de eigenschap `functionTimeout` in het projectbestand [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout). In de volgende tabel ziet u de standaard- en maximumwaarden in minuten voor beide abonnementen en de verschillende runtimeversies:

| Plannen | Runtime-versie | Standaard | Maximum |
|------|---------|---------|---------|
| Verbruik | 1.x | 5 | 10 |
| Verbruik | 2.x | 5 | 10 |
| Verbruik | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Onbeperkt |
| Premium | 2.x | 30 | Onbeperkt |
| Premium | 3.x | 30 | Onbeperkt |
| App Service | 1.x | Onbeperkt | Onbeperkt |
| App Service | 2.x | 30 | Onbeperkt |
| App Service | 3.x | 30 | Onbeperkt |

> [!NOTE] 
> Ongeacht de time-outinstelling van de Function-app is 230 seconden de maximale tijdsduur die een door HTTP geactiveerde functie heeft om te reageren op een aanvraag. Dit komt door de [standaard time-out voor inactiviteit van Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Overweeg voor langere verwerkingstijden het [asynchrone Durable Functions-patroon](../articles/azure-functions/durable/durable-functions-overview.md#async-http) te gebruiken of [stel het werk uit en stuur onmiddellijk een antwoord terug](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
