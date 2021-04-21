---
title: Azure Queue Storage naslaginformatie over bewakingsgegevens
description: Naslaginformatie over logboeken en metrische gegevens voor het bewaken van Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 04/20/2021
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 506f5a46688f597b8ac5db341c5bbe5eb5fb67c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763141"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Azure Queue Storage naslaginformatie over bewakingsgegevens

Zie [Bewakingsgegevens Azure Storage](monitor-queue-storage.md) voor meer informatie over het verzamelen en analyseren van bewakingsgegevens voor Azure Storage.

## <a name="metrics"></a>Metrische gegevens

In de volgende tabellen worden de metrische platformgegevens vermeld die zijn verzameld voor Azure Storage.

### <a name="capacity-metrics"></a>Metrische gegevens over capaciteit

Waarden voor metrische capaciteitsgegevens worden dagelijks vernieuwd (maximaal 24 uur). Het tijdsinterval definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. Het ondersteunde tijds grain voor alle metrische capaciteitsgegevens is één uur (PT1H).

Azure Storage biedt de volgende metrische capaciteitsgegevens in Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Metrische gegevens voor capaciteit op accountniveau

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage metrische gegevens

In deze tabel ziet [u Queue Storage metrische gegevens.](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)

| Metrisch | Beschrijving |
| ------------------- | ----------------- |
| **QueueCapacity** | De hoeveelheid Queue Storage die door het opslagaccount wordt gebruikt. <br><br> Eenheid: `Bytes` <br> Aggregatietype: `Average` <br> Voorbeeld van waarde: `1024` |
| **QueueCount** | Het aantal wachtrijen in het opslagaccount. <br><br> Eenheid: `Count` <br> Aggregatietype: `Average` <br> Voorbeeld van waarde: `1024` |
| **QueueMessageCount** | Het aantal niet-verkende wachtrijberichten in het opslagaccount. <br><br> Eenheid: `Count` <br> Aggregatietype: `Average` <br> Voorbeeld van waarde: `1024` |

### <a name="transaction-metrics"></a>Metrische gegevens voor transacties

Metrische gegevens voor transacties worden bij elke aanvraag naar een opslagaccount van Azure Storage naar Azure Monitor. In het geval van geen activiteit op uw opslagaccount, zijn er geen gegevens over metrische transactiegegevens in de periode. Alle metrische gegevens voor transacties zijn beschikbaar op zowel account- als Queue Storage serviceniveau. Het tijdsinterval definieert het tijdsinterval dat metrische waarden worden weergegeven. De ondersteunde tijdsgranen voor alle metrische gegevens voor transacties zijn PT1H en PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensies voor metrische gegevens

Azure Storage ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Resourcelogboeken (preview)

> [!NOTE]
> Azure Storage logboeken in Azure Monitor is in openbare preview en is beschikbaar voor preview-tests in alle openbare cloudregio's. Deze preview maakt logboeken mogelijk voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wachtrijen, tabellen, Premium Storage-accounts in opslagaccounts voor algemeen gebruik v1 en v2 voor algemeen gebruik. Klassieke opslagaccounts worden niet ondersteund.

De volgende tabel bevat de eigenschappen voor Azure Storage resourcelogboeken wanneer ze worden verzameld in Azure Monitor logboeken of Azure Storage. De eigenschappen beschrijven de bewerking, de service en het type autorisatie dat is gebruikt om de bewerking uit te voeren.

### <a name="fields-that-describe-the-operation"></a>Velden die de bewerking beschrijven

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Velden die beschrijven hoe de bewerking is geverifieerd

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Velden die de service beschrijven

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Zie ook

- Zie [Bewakingsde Azure Queue Storage](monitor-queue-storage.md) voor een beschrijving van Azure Queue Storage.
- Zie [Azure-resources bewaken met Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
