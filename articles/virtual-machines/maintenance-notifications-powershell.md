---
title: Onderhouds meldingen voor Azure-Vm's ontvangen met Power shell
description: Bekijk onderhouds meldingen voor virtuele machines die in Azure worden uitgevoerd en start selfservice onderhoud met behulp van Power shell.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4278aa563d76b783e02e67ee964764864931fd68
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552368"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Verwerken van gepland onderhoud met behulp van Power shell

**Dit artikel is van toepassing op virtuele machines met Linux en Windows.**

U kunt Azure PowerShell gebruiken om te zien wanneer Vm's zijn gepland voor [onderhoud](maintenance-notifications.md). Informatie over gepland onderhoud is beschikbaar via de cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) wanneer u de `-status` para meter gebruikt.
  
Onderhouds informatie wordt alleen geretourneerd als er onderhoud wordt gepland. Als er geen onderhoud is gepland dat van invloed is op de virtuele machine, retourneert de cmdlet geen onderhouds informatie. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Uitvoer

```
MaintenanceRedeployStatus               : 
  IsCustomerInitiatedMaintenanceAllowed : True
  PreMaintenanceWindowStartTime         : 5/14/2018 12:30:00 PM
  PreMaintenanceWindowEndTime           : 5/19/2018 12:30:00 PM
  MaintenanceWindowStartTime            : 5/21/2018 4:30:00 PM
  MaintenanceWindowEndTime              : 6/4/2018 4:30
  LastOperationResultCode               : None 
```

De volgende eigenschappen worden geretourneerd onder MaintenanceRedeployStatus: 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Hiermee wordt aangegeven of u op dit moment onderhoud kunt starten op de virtuele machine |
| PreMaintenanceWindowStartTime         | Het begin van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt initiëren |
| PreMaintenanceWindowEndTime           | Het einde van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt initiëren |
| MaintenanceWindowStartTime            | Het begin van de geplande werkzaamheden waarbij Azure onderhoud initieert op uw virtuele machine |
| MaintenanceWindowEndTime              | Het einde van het geplande onderhouds venster waarin Azure onderhoud initieert op uw virtuele machine |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud op de VM te initiëren |



U kunt ook de onderhouds status voor alle virtuele machines in een resource groep ophalen met behulp van [Get-AzVM](/powershell/module/az.compute/get-azvm) en geen virtuele machine opgeven.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

In het volgende Power shell-voor beeld wordt uw abonnements-ID gebruikt en wordt een lijst geretourneerd met de Vm's die zijn gepland voor onderhoud.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Onderhoud starten op uw virtuele machine met behulp van Power shell

Met behulp van informatie van de functie in de vorige sectie wordt het onderhoud op een VM gestart als **IsCustomerInitiatedMaintenanceAllowed** is ingesteld op True.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klassieke implementaties

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Als u nog steeds verouderde Vm's hebt die zijn geïmplementeerd met behulp van het klassieke implementatie model, kunt u Power shell gebruiken om te zoeken naar Vm's en onderhoud te initiëren.

Als u de onderhouds status van een virtuele machine wilt ophalen, typt u:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Als u onderhoud wilt starten op uw klassieke VM, typt u:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Volgende stappen

U kunt gepland onderhoud ook afhandelen met behulp van de [Azure cli](maintenance-notifications-cli.md) of [Portal](maintenance-notifications-portal.md).
