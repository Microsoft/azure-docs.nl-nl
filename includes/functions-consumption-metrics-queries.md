---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92168104"
---
#### <a name="determine-memory-usage"></a>Geheugen gebruik bepalen 

Onder **bewaking** selecteert u **Logboeken (analyse)**, kopieert u de volgende telemetrie-query en plakt u deze in het query venster en selecteert u **uitvoeren**. Met deze query wordt het totale geheugen gebruik voor elke steekproef periode geretourneerd.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

De resultaten zien eruit als in het volgende voor beeld:

| UTC-tijds tempel \[\]          | naam          | waarde       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 uur | Privé-bytes | 209.932.288 |
| 9/12/2019, 1:06:14 \. 994 uur | Privé-bytes | 212.189.184 |
| 9/12/2019, 1:06:30 \. 010 | Privé-bytes | 231.714.816 |
| 9/12/2019, 1:07:15 \. 040 uur | Privé-bytes | 210.591.744 |
| 9/12/2019, 1:12:16 \. 285 uur | Privé-bytes | 216.285.184 |
| 9/12/2019, 1:12:31 \. 376 uur | Privé-bytes | 235.806.720 |

#### <a name="determine-duration"></a>Duur bepalen 

Azure Monitor worden metrische gegevens op het niveau van de resource bijgehouden, wat voor functies de functie-app is. Application Insights-integratie verzendt metrische gegevens per functie. Hier volgt een voor beeld van een analyse query om de gemiddelde duur van een functie op te halen:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| naam                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| Queue trigger AvgDurationMs | 16 \. 087                     |
| Queue trigger MaxDurationMs | 90 \. 249                     |
| Queue trigger MinDurationMs | 8 \. 522                      |
