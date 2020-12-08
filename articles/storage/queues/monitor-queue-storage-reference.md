---
title: Naslag informatie voor Azure Queue Storage-bewakings gegevens | Microsoft Docs
description: Naslag informatie over Logboeken en metrieken voor het bewaken van gegevens van Azure Queue-opslag.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: a3a07f828bb8ef20da630e909e46f03e8556eb98
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780362"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Naslag informatie over bewakings gegevens van Azure Queue Storage

Zie [bewaking Azure Storage](monitor-queue-storage.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure Storage.

## <a name="metrics"></a>Metrische gegevens

De volgende tabellen geven een lijst van de platform gegevens die zijn verzameld voor Azure Storage.

### <a name="capacity-metrics"></a>Metrische gegevens over capaciteit

Metrische waarden voor capaciteit worden dagelijks vernieuwd (Maxi maal 24 uur). De tijdgranulariteit definieert het tijds interval waarvoor metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens over capaciteit zijn één uur (PT1H).

Azure Storage biedt de volgende metrische gegevens over capaciteit in Azure Monitor.

#### <a name="account-level"></a>Account niveau

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage"></a>Queue Storage

In deze tabel worden [metrische opslag gegevens](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)voor de wachtrij weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| QueueCapacity | De hoeveelheid wachtrij opslag die door het opslag account wordt gebruikt. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| QueueCount   | Het aantal wacht rijen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| QueueMessageCount | Het geschatte aantal wachtrij berichten in de Queue-service van het opslag account. <br/><br/>Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

### <a name="transaction-metrics"></a>Metrische gegevens voor transacties

Metrische gegevens over trans acties worden verzonden voor elke aanvraag naar een opslag account van Azure Storage naar Azure Monitor. In het geval van geen activiteit in uw opslag account, worden er geen gegevens in de periode weer gegeven. Alle metrische gegevens over trans acties zijn beschikbaar op account-en wachtrij opslag service niveau. De tijd korrel definieert het tijds interval dat metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens van de trans actie zijn PT1H en PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrische dimensies

Azure Storage ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Resource Logboeken (preview-versie)

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview-versie en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Met deze preview-versie kunt u logboeken maken voor blobs (inclusief Azure Data Lake Storage Gen2), bestanden, wacht rijen, tabellen, Premium-opslag accounts in versie 1 voor algemeen gebruik en voor algemeen gebruik v2-opslag accounts. Klassieke opslag accounts worden niet ondersteund.

De volgende tabel geeft een lijst van de eigenschappen voor Azure Storage bron Logboeken wanneer ze worden verzameld in Azure Monitor Logboeken of Azure Storage. De eigenschappen beschrijven de bewerking, de service en het type autorisatie dat is gebruikt om de bewerking uit te voeren.

### <a name="fields-that-describe-the-operation"></a>Velden waarin de bewerking wordt beschreven

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Velden die beschrijven hoe de bewerking is geverifieerd

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Velden waarin de service wordt beschreven

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Zie ook

- Zie [Azure Queue-opslag bewaken](monitor-queue-storage.md) voor een beschrijving van de bewakings Azure Storage.
- Zie [Azure-resources bewaken met Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
