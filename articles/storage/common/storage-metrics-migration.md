---
title: Van Opslaganalyse meet waarden verplaatsen naar Azure Monitor metrische gegevens | Microsoft Docs
description: Meer informatie over het overstappen van Opslaganalyse metrische gegevens (klassieke metrische gegevens) naar metrische gegevens in Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: ff4c3c5ae8629879fca6e9e683a9c77d0e2f144b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596105"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Overstappen op metrische gegevens in Azure Monitor

Op **31 augustus 2023** worden Opslaganalyse metrische gegevens, ook wel *klassieke metrische gegevens* genoemd, buiten gebruik gesteld. Zie de [officiële aankondiging](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/) voor meer informatie. Als u gebruikmaakt van klassieke metrische gegevens, moet u vóór die datum overstappen op metrische gegevens in Azure Monitor. Dit artikel helpt u bij het maken van de overstap. 

## <a name="steps-to-complete-the-transition"></a>Stappen voor het volt ooien van de overgang

Als u wilt overstappen op metrische gegevens in Azure Monitor, raden we u aan de volgende aanpak te volgen.

1. Meer informatie over enkele van de [belangrijkste verschillen](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) tussen de klassieke metrische gegevens en metrische gegevens in azure monitor. 

2. Een lijst samen stellen met de klassieke metrische gegevens die u momenteel gebruikt.

3. Bepaal [welke meet waarden in azure monitor](#metrics-mapping-between-old-metrics-and-new-metrics) dezelfde gegevens leveren als de metrische waarden die u momenteel gebruikt. 
   
4. Maak [grafieken](/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) of [Dash boards](/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) om metrische gegevens weer te geven.

   > [!NOTE]
   > Metrische gegevens in Azure Monitor zijn standaard ingeschakeld, zodat u niets hoeft te doen om het vastleggen van metrische gegevens te starten. U moet echter grafieken of Dash boards maken om deze metrische gegevens weer te geven. 
 
5. Als u waarschuwings regels hebt gemaakt die zijn gebaseerd op de metrische gegevens voor opslag, kunt u [waarschuwings regels maken](../../azure-monitor/alerts/alerts-overview.md) die zijn gebaseerd op metrische gegevens in azure monitor. 

6. Wanneer u al uw metrische gegevens in Azure Monitor kunt zien, kunt u de klassieke logboek registratie uitschakelen. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Klassieke metrische gegevens versus metrische gegevens in Azure Monitor

In deze sectie worden enkele belang rijke verschillen tussen deze twee metrische platformen beschreven.

Het belangrijkste verschil is het beheren van metrische gegevens. Klassieke metrische gegevens worden beheerd door Azure Storage terwijl de metrische gegevens in Azure Monitor door Azure Monitor worden beheerd. Met de klassieke metrische gegevens Azure Storage worden metrische waarden verzameld, geaggregeerd en opgeslagen in tabellen die zich in het opslag account bevinden. Met metrische gegevens in Azure Monitor Azure Storage naar de Azure Monitor back-end verzonden. Azure Monitor biedt een uniforme bewakings ervaring met gegevens uit de Azure Portal en gegevens die worden opgenomen. 

Klassieke metrische gegevens worden verzonden en opgeslagen in een Azure-opslag account. Azure Monitor metrieken kunnen naar meerdere locaties worden verzonden. Een opslag account kan een van deze locaties zijn, maar dit is niet vereist.  

Voor de ondersteuning van metrische gegevens bieden klassieke metrische gegevens alleen metrische gegevens over de **capaciteit** voor Azure Blob Storage. Metrische gegevens in Azure Monitor bieden capaciteits metrieken voor blob-, tabel-, bestands-, wachtrij-en Premium-opslag. Klassieke metrische gegevens geven metrische gegevens over **trans acties** op blob-, tabel-, Azure-en wachtrij opslag. Metrische gegevens in Azure Monitor Premium-opslag toevoegen aan deze lijst.

Als voor de activiteit in uw account geen metrische gegevens worden geactiveerd, wordt voor de metrische gegevens een waarde van nul (0) weer gegeven in de klassieke metrische gegevens. De metrische gegevens in Azure Monitor worden volledig wegge laten, wat leidt tot schone rapporten. Als er bijvoorbeeld een time-out voor de server wordt gerapporteerd, `ServerTimeoutError` wordt de waarde in de tabel metrische gegevens ingesteld op 0. Azure Monitor retourneert geen gegevens wanneer u een query uitvoert op de waarde metric `Transactions` met dimensie `ResponseType` gelijk aan `ServerTimeoutError` . 

Zie [metrische gegevens in azure monitor](../../azure-monitor/essentials/data-platform-metrics.md)voor meer informatie over metrische gegevens in azure monitor.

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Klassieke metrische gegevens toewijzen aan metrische gegevens in Azure Monitor

 Gebruik deze tabellen om te bepalen welke meet waarden in Azure Monitor dezelfde gegevens leveren als de metrieken die u momenteel gebruikt. 

**Metrische gegevens over capaciteit**

| Klassieke metriek | Metrische waarde in Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` met de dimensie `BlobType` gelijk aan `BlockBlob` of `PageBlob` |
| `ObjectCount`        | `BlobCount` met de dimensie `BlobType` gelijk aan `BlockBlob` of `PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Er zijn ook verschillende nieuwe capaciteits metrieken die niet beschikbaar waren als klassieke metrische gegevens. Zie [metrische gegevens](../blobs/monitor-blob-storage-reference.md#metrics)om de volledige lijst weer te geven.

**Metrische gegevens voor transacties**

| Klassieke metriek | Metrische waarde in Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `AuthorizationError` en dimensie gelijk `Authentication` aan is `Anonymous` |
| `AnonymousClientOtherError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ClientOtherError` en dimensie gelijk `Authentication` aan is `Anonymous` |
| `AnonymousClientTimeoutError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ClientTimeoutError` en dimensie gelijk `Authentication` aan is `Anonymous` |
| `AnonymousNetworkError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `NetworkError` en dimensie gelijk `Authentication` aan is `Anonymous` |
| `AnonymousServerOtherError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ServerOtherError` en dimensie gelijk `Authentication` aan is `Anonymous` |
| `AnonymousServerTimeoutError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ServerTimeoutError` en dimensie gelijk `Authentication` aan is `Anonymous` |
| `AnonymousSuccess` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `Success` en dimensie gelijk `Authentication` aan is `Anonymous` |
| `AnonymousThrottlingError` | Trans acties met de dimensie `ResponseType` gelijk aan `ClientThrottlingError` of `ServerBusyError` en dimensie `Authentication` gelijk aan `Anonymous` |
| `AuthorizationError` | Trans acties met de dimensie `ResponseType` gelijk aan `AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Trans acties met de dimensie `ResponseType` gelijk aan `ClientOtherError` |
| `ClientTimeoutError` | Trans acties met de dimensie `ResponseType` gelijk aan `ClientTimeoutError` |
| `NetworkError` | Trans acties met de dimensie `ResponseType` gelijk aan `NetworkError` |
| `PercentAuthorizationError` | Trans acties met de dimensie `ResponseType` gelijk aan `AuthorizationError` |
| `PercentClientOtherError` | Trans acties met de dimensie `ResponseType` gelijk aan `ClientOtherError` |
| `PercentNetworkError` | Trans acties met de dimensie `ResponseType` gelijk aan `NetworkError` |
| `PercentServerOtherError` | Trans acties met de dimensie `ResponseType` gelijk aan `ServerOtherError` |
| `PercentSuccess` | Trans acties met de dimensie `ResponseType` gelijk aan `Success` |
| `PercentThrottlingError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ClientThrottlingError` of is `ServerBusyError` |
| `PercentTimeoutError` | Trans acties met de dimensie `ResponseType` gelijk aan `ServerTimeoutError` of `ResponseType` gelijk aan `ClientTimeoutError` |
| `SASAuthorizationError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `AuthorizationError` en dimensie gelijk `Authentication` aan is `SAS` |
| `SASClientOtherError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ClientOtherError` en dimensie gelijk `Authentication` aan is `SAS` |
| `SASClientTimeoutError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ClientTimeoutError` en dimensie gelijk `Authentication` aan is `SAS` |
| `SASNetworkError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `NetworkError` en dimensie gelijk `Authentication` aan is `SAS` |
| `SASServerOtherError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ServerOtherError` en dimensie gelijk `Authentication` aan is `SAS` |
| `SASServerTimeoutError` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `ServerTimeoutError` en dimensie gelijk `Authentication` aan is `SAS` |
| `SASSuccess` | Trans acties waarbij de dimensie `ResponseType` gelijk aan `Success` en dimensie gelijk `Authentication` aan is `SAS` |
| `SASThrottlingError` | Trans acties met de dimensie `ResponseType` gelijk aan `ClientThrottlingError` of `ServerBusyError` en dimensie `Authentication` gelijk aan `SAS` |
| `ServerOtherError` | Trans acties met de dimensie `ResponseType` gelijk aan `ServerOtherError` |
| `ServerTimeoutError` | Trans acties met de dimensie `ResponseType` gelijk aan `ServerTimeoutError` |
| `Success` | Trans acties met de dimensie `ResponseType` gelijk aan `Success` |
| `ThrottlingError` | `Transactions` met de dimensie `ResponseType` gelijk aan `ClientThrottlingError` of `ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor](../../azure-monitor/overview.md)