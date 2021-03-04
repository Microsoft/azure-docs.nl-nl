---
title: Query's tussen meerdere services tussen Azure Monitor en Azure Data Explorer (preview-versie)
description: Query's uitvoeren op Azure Data Explorer gegevens via Azure Log Analytics-hulpprogram ma's omgekeerd, zodat u al uw gegevens op één plek kunt samen voegen en analyseren.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 7a259af17943643e722633592e53f219726c4437
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031205"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Query's voor meerdere services, Azure Monitor en Azure Data Explorer (preview-versie)
Query's tussen meerdere services en [Azure Data Explorer](/azure/data-explorer/), [Application Insights](../app/app-insights-overview.md)en [log Analytics](../logs/data-platform-logs.md)maken.
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Query's voor Azure Monitor en Azure Data Explorer meerdere services
Met deze ervaring kunt u [query's voor meerdere services maken tussen Azure Data Explorer en Azure monitor](/azure/data-explorer/query-monitor-data) en [query's voor meerdere services maken tussen Azure Monitor en Azure Data Explorer](./azure-monitor-data-explorer-proxy.md).

Bijvoorbeeld (query's uitvoeren op Azure Data Explorer vanuit Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Als de buitenste query een query uitvoert in een tabel in de werk ruimte en vervolgens lid wordt van een andere tabel in een Azure Data Explorer-cluster (in dit geval clustername = Help, DATABASENAME = voor beelden) met behulp van een nieuwe ' ADX ()-functie, zoals hoe u hetzelfde kunt doen om een andere werk ruimte te doorzoeken vanuit query tekst.

> [!NOTE]
> * De mogelijkheid om Azure Monitor gegevens uit Azure Data Explorer op te vragen, rechtstreeks vanuit Azure Data Explorer client hulpprogramma's of indirect door een query uit te voeren op een Azure Data Explorer-cluster, is in de preview-modus.
> * Neem contact op met het team voor [Query's tussen services](mailto:adxproxy@microsoft.com) .

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Query exporteren Log Analytics gegevens uit Azure Blob Storage-account

Bij het exporteren van gegevens uit Azure Monitor naar een Azure Storage-account is een lage Bewaar periode en de mogelijkheid om Logboeken opnieuw toe te wijzen aan verschillende regio's.

Gebruik Azure Data Explorer om query's uit te voeren op gegevens die zijn geëxporteerd uit uw Log Analytics-werk ruimten. Eenmaal geconfigureerd, worden ondersteunde tabellen die vanuit uw werk ruimten worden verzonden naar een Azure-opslag account, beschikbaar als gegevens bron voor Azure-Data Explorer. [Query's van Azure monitor geëxporteerde gegevens met behulp van Azure Data Explorer (preview)](../logs/azure-data-explorer-query-storage.md).

[Azure Data Explorer query vanuit de opslag stroom](media\azure-data-explorer-query-storage\exported-data-query.png)

>[!tip] 
> * Als u alle gegevens uit uw Log Analytics-werk ruimte wilt exporteren naar een Azure-opslag account of Event Hub, gebruikt u de functie Log Analytics werkruimte gegevens exporteren van Azure Monitor Logboeken. [Zie log Analytics werkruimte gegevens exporteren in azure monitor (preview)](/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:
* [query's voor meerdere services maken tussen Azure Data Explorer en Azure monitor](/azure/data-explorer/query-monitor-data). Azure Monitor gegevens opvragen vanuit Azure Data Explorer
* [query's voor meerdere services maken tussen Azure monitor en Azure Data Explorer](./azure-monitor-data-explorer-proxy.md). Query's uitvoeren op Azure Data Explorer gegevens vanuit Azure Monitor
* [Log Analytics werkruimte gegevens exporteren in azure monitor (preview)](/azure/data-explorer/query-monitor-data). Koppel en zoek aan Azure Blob Storage-account met Log Analytics geëxporteerde gegevens.