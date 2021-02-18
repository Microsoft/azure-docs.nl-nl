---
title: Azure Batch bewaken
description: Meer informatie over Azure monitoring services, metrische gegevens, Diagnostische logboeken en andere bewakings functies voor Azure Batch.
ms.topic: how-to
ms.date: 04/05/2018
ms.openlocfilehash: b926f9c6d173cd0b8d886047eae490e4a151988f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595429"
---
# <a name="monitor-batch-solutions"></a>Batch-oplossingen controleren

Azure en de batch-service bieden een scala aan services, hulpprogram ma's en Api's voor het bewaken van uw batch-oplossingen. Dit overzichts artikel helpt u bij het kiezen van een bewakings benadering die aan uw behoeften voldoet.

Zie [Azure-toepassingen en-resources](../azure-monitor/overview.md)bewaken voor een overzicht van de Azure-onderdelen en-services die beschikbaar zijn voor het bewaken van Azure-resources.

## <a name="subscription-level-monitoring"></a>Bewaking op abonnements niveau

Op abonnements niveau, dat batch-accounts bevat, verzamelt het [Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md) operationele gebeurtenis gegevens in [verschillende categorieën](../azure-monitor/essentials/activity-log.md#view-the-activity-log).

Voor batch-accounts verzamelt het activiteiten logboek gebeurtenissen met betrekking tot het maken en verwijderen van accounts en sleutel beheer.

Een manier om gebeurtenissen uit uw activiteiten logboek op te halen, is met behulp van de Azure Portal. Klik op **alle services**  >  **activiteiten logboek**. U kunt ook een query uitvoeren op gebeurtenissen met behulp van de Azure CLI, Power shell-cmdlets of de Azure Monitor REST API. U kunt ook het activiteiten logboek exporteren of waarschuwingen voor het [activiteiten logboek](../azure-monitor/alerts/alerts-activity-log.md)configureren.

## <a name="batch-account-level-monitoring"></a>Bewaking op batch-account niveau

Bewaak elk batch-account met behulp van de functies van [Azure monitor](../azure-monitor/overview.md). Azure Monitor verzamelt [metrische gegevens](../azure-monitor/essentials/data-platform-metrics.md) en eventueel [Diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md) voor resources die zijn afgestemd op het niveau van een batch-account, zoals Pools, Jobs en taken. Verzamel en gebruik deze gegevens hand matig of programmatisch om activiteiten in uw batch-account te bewaken en problemen te onderzoeken. Zie [metrische batches, waarschuwingen en logboeken voor diagnostische evaluatie en controle](batch-diagnostics.md)voor meer informatie.
 
> [!NOTE]
> Metrische gegevens zijn standaard beschikbaar in uw batch-account zonder extra configuratie en ze hebben een rolling geschiedenis van 30 dagen. U moet Diagnostische logboek registratie inschakelen voor een batch-account en u kunt extra kosten in rekening brengen om diagnostische logboek gegevens op te slaan of te verwerken. 

## <a name="batch-resource-monitoring"></a>Batch-bron bewaking

Gebruik in uw batch-toepassingen de batch-Api's om de status van uw resources te controleren of te doorzoeken, inclusief taken, taken, knoop punten en Pools. Bijvoorbeeld:

* [Taken en reken knooppunten tellen per status](batch-get-resource-counts.md)
* [Query's maken om batch resources efficiënt weer te geven](batch-efficient-list-queries.md)
* [Taak afhankelijkheden maken](batch-task-dependencies.md)
* Een taak [beheer taak](/rest/api/batchservice/job/add#jobmanagertask) gebruiken
* De status van de [taak](/rest/api/batchservice/task/list#taskstate) controleren
* De [knooppunt status](/rest/api/batchservice/computenode/list#computenodestate) bewaken
* De [pool status](/rest/api/batchservice/pool/get#poolstate) controleren
* [Groeps gebruik in het account](/rest/api/batchservice/pool/listusagemetrics) bewaken
* [Groeps knooppunten tellen per status](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM-prestatie meter items en toepassings bewaking

* [Application Insights](../azure-monitor/app/app-insights-overview.md) is een Azure-service die u kunt gebruiken om de beschik baarheid, prestaties en het gebruik van uw batch-taken en taken programmatisch te bewaken. U kunt eenvoudig prestatie meter items ophalen van reken knooppunten (Vm's) en aangepaste informatie voor taken van de Vm's. 

  Zie [een batch .NET-toepassing bewaken en fouten opsporen met Application Insights](monitor-application-insights.md) en het bijbehorende [code voorbeeld](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)voor een voor beeld.

  > [!NOTE]
  > Mogelijk worden er extra kosten in rekening gebracht voor het gebruik van Application Insights. Bekijk de [prijs opties](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) is een gratis, uitgebreid, zelfstandig client hulpprogramma waarmee Azure batch toepassingen kunnen worden gemaakt, opgespoord en gecontroleerd. Download een [installatiepakket](https://azure.github.io/BatchExplorer/) voor Mac, Linux of Windows. Configureer eventueel uw batch-oplossing om [Application Insights gegevens weer te geven](https://github.com/Azure/batch-insights) , zoals prestatie meter items voor virtuele machines in batch Explorer.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
* Meer informatie over [Diagnostische logboek registratie](batch-diagnostics.md) met batch.
