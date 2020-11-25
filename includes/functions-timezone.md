---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028408"
---
De standaardtijd zone die wordt gebruikt in de CRON-expressies is Coordinated Universal Time (UTC). Als u de CRON-expressie op basis van een andere tijd zone wilt gebruiken, maakt u een app-instelling voor de functie-app met de naam `WEBSITE_TIME_ZONE` . 

De waarde van deze instelling is afhankelijk van het besturings systeem en het plan waarop de functie-app wordt uitgevoerd.

|Besturingssysteem |Plannen |Waarde |
|-|-|-|
| **Windows** |Alles | Stel de waarde in op de naam van de gewenste tijd zone die is opgegeven door de tweede regel van elk paar dat wordt opgegeven door de Windows-opdracht `tzutil.exe /L` |
| **Linux** |Premium<br/>Toegewezen |Stel de waarde in op de naam van de gewenste tijd zone, zoals weer gegeven in de [TZ-data base](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` wordt momenteel niet ondersteund in het verbruiks abonnement voor Linux.

Eastern time in de Verenigde Staten (vertegenwoordigd door `Eastern Standard Time` (Windows) of `America/New_York` (Linux)) gebruikt in dat geval utc-05:00 tijdens de standaard tijd en UTC-04:00 tijdens de zomer tijd. Als u wilt dat een timer trigger wordt geactiveerd om 10:00 uur Eastern time elke dag, maakt u een app-instelling voor uw functie `WEBSITE_TIME_ZONE` -app met de naam, stelt u de waarde in op `Eastern Standard Time` (Windows) of `America/New_York` (Linux) en gebruikt u vervolgens de volgende NCRONTAB-expressie: 

```
"0 0 10 * * *"
``` 

Wanneer u `WEBSITE_TIME_ZONE` de tijd gebruikt, wordt het tijdstip gewijzigd in de specifieke tijd zone, inclusief zomer tijd en wijzigingen in de standaard tijd.
