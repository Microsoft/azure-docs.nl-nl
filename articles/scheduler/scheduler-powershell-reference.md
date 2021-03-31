---
title: Naslag informatie over Power shell-cmdlets
description: Meer informatie over Power shell-cmdlets voor Azure scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: ccc9f709348d961e49bced00946658a6997837c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368108"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Naslag informatie over Power shell-cmdlets voor Azure scheduler

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Scheduler is niet meer beschikbaar in de Azure Portal, maar de [rest API](/rest/api/scheduler) en [Azure scheduler Power shell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taak verzamelingen kunt beheren.

U kunt Power shell-cmdlets gebruiken om scripts te schrijven voor het maken en beheren van scheduler-taken en-taak verzamelingen. In dit artikel vindt u een overzicht van de belangrijkste Power shell-cmdlets voor Azure scheduler met koppelingen naar de bijbehorende referentie artikelen. Zie [Azure PowerShell installeren en configureren voor meer informatie over](/powershell/azure/)het installeren van Azure PowerShell voor uw Azure-abonnement. Zie [Using Azure PowerShell with Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)(Engelstalig) voor meer informatie over [Azure Resource Manager-cmdlets](/powershell/azure/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | Beschrijving |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Hiermee wordt een taak verzameling uitgeschakeld. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Hiermee schakelt u een taak verzameling in. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Hiermee worden scheduler-taken opgehaald. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Hiermee worden taak verzamelingen opgehaald. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Hiermee wordt de taak geschiedenis opgehaald. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Hiermee maakt u een HTTP-taak. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Hiermee maakt u een taak verzameling. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Hiermee maakt u een Service Bus wachtrij taak. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Hiermee maakt u een taak voor een Service Bus onderwerp. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Hiermee maakt u een opslag wachtrij taak. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Hiermee wordt een planner taak verwijderd. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Hiermee verwijdert u een taak verzameling. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Hiermee wijzigt u de HTTP-taak van een scheduler. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Hiermee wijzigt u een taak verzameling. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Hiermee wijzigt u een Service Bus wachtrij taak. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Hiermee wijzigt u de taak voor een Service Bus onderwerp. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Hiermee wordt een wachtrij taak voor de opslag gewijzigd. |
||| 

Voor meer informatie kunt u een van de volgende cmdlets uitvoeren: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)