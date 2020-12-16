---
title: Naslag informatie voor Azure Queue Storage monitoring-gegevens
description: Naslag informatie over Logboeken en metrieken voor het bewaken van gegevens van Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: ba8a82ed1113bfb3e71560ca9a6c713602df21f2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590644"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Naslag informatie voor Azure Queue Storage monitoring-gegevens

Zie [bewaking Azure Storage](monitor-queue-storage.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure Storage.

## <a name="metrics"></a>Metrische gegevens

De volgende tabellen geven een lijst van de platform gegevens die zijn verzameld voor Azure Storage.

### <a name="capacity-metrics"></a>Metrische gegevens over capaciteit

Metrische waarden voor capaciteit worden dagelijks vernieuwd (Maxi maal 24 uur). De tijdgranulariteit definieert het tijds interval waarvoor metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens over capaciteit zijn één uur (PT1H).

Azure Storage biedt de volgende metrische gegevens over capaciteit in Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Metrische capaciteit op account niveau

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage metrische gegevens

In deze tabel worden [Queue Storage metrische gegevens](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| **QueueCapacity** | De hoeveelheid Queue Storage die wordt gebruikt door het opslag account. <br><br> Teleenheid `Bytes` <br> Aggregatie type: `Average` <br> Waarde-voor beeld: `1024` |
| **QueueCount** | Het aantal wacht rijen in het opslag account. <br><br> Teleenheid `Count` <br> Aggregatie type: `Average` <br> Waarde-voor beeld: `1024` |
| **QueueMessageCount** | Het geschatte aantal wachtrij berichten in het opslag account. <br><br> Teleenheid `Count` <br> Aggregatie type: `Average` <br> Waarde-voor beeld: `1024` |

### <a name="transaction-metrics"></a>Metrische gegevens voor transacties

Metrische gegevens over trans acties worden verzonden voor elke aanvraag naar een opslag account van Azure Storage naar Azure Monitor. In het geval van geen activiteit in uw opslag account, worden er geen gegevens in de periode weer gegeven. Alle metrische gegevens over trans acties zijn beschikbaar op zowel account-als Queue Storage service niveau. De tijd korrel definieert het tijds interval dat metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens van de trans actie zijn PT1H en PT1M.

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

- Zie [azure Queue Storage bewaken](monitor-queue-storage.md) voor een beschrijving van het controleren van Azure Queue Storage.
- Zie [Azure-resources bewaken met Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
