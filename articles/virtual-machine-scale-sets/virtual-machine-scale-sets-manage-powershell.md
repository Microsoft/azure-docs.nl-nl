---
title: Virtual Machine Scale Sets beheren met Azure PowerShell
description: Algemene Azure PowerShell-cmdlets voor het beheren van Virtual Machine Scale Sets, zoals het starten en stoppen van een exemplaar of het wijzigen van de capaciteit van de schaalset.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: eee4dd7fae872f6b3ddd01f60aba732edc170766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570587"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Een schaalset voor virtuele machines beheren met Azure PowerShell

Gedurende de levenscyclus van een schaalset voor virtuele machines moet u mogelijk een of meer beheertaken uitvoeren. Bovendien wilt u misschien scripts maken die verschillende levenscyclustaken automatiseren. In dit artikel vindt u meer informatie over de algemene Azure PowerShell-cmdlets waarmee u deze taken kunt uitvoeren.

Als u een schaalset voor virtuele machines wilt maken, kunt u [een schaalset maken met Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Informatie over een schaalset weer geven
Als u de algemene informatie over een schaalset wilt weer geven, gebruikt u [Get-AzVmss](/powershell/module/az.compute/get-azvmss). In het volgende voor beeld wordt informatie opgehaald over de schaalset met de naam *myScaleSet* in de resource groep *myResourceGroup* . Voer uw eigen namen als volgt in:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Virtuele machines weergeven in een schaalset
Gebruik [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)om een lijst met VM-exemplaren in een schaalset weer te geven. In het volgende voor beeld worden alle VM-exemplaren in de schaalset met de naam *myScaleSet* en in de resource groep *myResourceGroup* weer gegeven. Geef uw eigen waarden op voor deze namen:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Als u meer informatie wilt weer geven over een specifiek VM-exemplaar, voegt `-InstanceId` u de para meter toe aan [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) en geeft u een instantie op die u wilt weer geven. In het volgende voor beeld wordt informatie weer gegeven over het VM-exemplaar *0* in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . Voer uw eigen namen als volgt in:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

U kunt ook gedetailleerde informatie over *instanceView* ophalen voor alle exemplaren in één API-aanroep, waarmee u de API-beperking voor grote installaties kunt voor komen.

```powershell
Get-AzVmssVM -InstanceView -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="change-the-capacity-of-a-scale-set"></a>De capaciteit van een schaalset wijzigen
De voor gaande opdrachten hebben informatie over de schaalset en de VM-exemplaren. Als u het aantal instanties in de schaalset wilt verg Roten of verkleinen, kunt u de capaciteit wijzigen. De schaalset maakt of verwijdert automatisch het vereiste aantal Vm's en configureert vervolgens de virtuele machines om het toepassings verkeer te ontvangen.

Maak eerst een schaalsetobject met [Get-AzVmss](/powershell/module/az.compute/get-azvmss) en geef vervolgens een nieuwe waarde op voor `sku.capacity`. Gebruik [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om de capaciteitswijziging toe te passen. In het volgende voor beeld worden *myScaleSet* in de resource groep *myResourceGroup* bijgewerkt naar een capaciteit van *vijf* exemplaren. U kunt als volgt uw eigen waarden opgeven:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Het duurt een paar minuten om de capaciteit van de schaalset bij te werken. Als u de capaciteit van een schaalset verlaagt, worden de Vm's met de hoogste exemplaar-Id's eerst verwijderd.


## <a name="stop-and-start-vms-in-a-scale-set"></a>VM’s in een schaalset stoppen en opstarten
Als u een of meer VM's in een schaalset wilt stoppen, gebruikt u [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Met de parameter `-InstanceId` kunt u een of meer VM's opgeven om te stoppen. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestopt. Als u meerdere Vm's wilt stoppen, moet u elke exemplaar-ID scheiden met een komma.

In het volgende voor beeld wordt instance *0* in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* gestopt. U kunt als volgt uw eigen waarden opgeven:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

De standaardinstelling is dat de toewijzing van gestopte VM's ongedaan wordt gemaakt en er dus geen compute-kosten meer in rekening worden gebracht. Als u wilt dat de VM de ingerichte status blijft houden nadat deze is gestopt, voeg u de parameter `-StayProvisioned` toe aan de voorgaande opdracht. Voor gestopte VM's die ingericht blijven, worden periodieke compute-kosten in rekening gebracht.


### <a name="start-vms-in-a-scale-set"></a>Vm's in een schaalset starten
Als u een of meer VM's in een schaalset wilt starten, gebruikt u [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Met de parameter `-InstanceId` kunt u een of meer VM's opgeven om te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestart. Als u meerdere Vm's wilt starten, moet u elke exemplaar-ID scheiden met een komma.

In het volgende voor beeld wordt instantie *0* gestart in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . U kunt als volgt uw eigen waarden opgeven:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>VM’s in een schaalset opnieuw opstarten
Als u een of meer Vm's in een schaalset opnieuw wilt opstarten, gebruikt u [restart-AzVmss](/powershell/module/az.compute/restart-azvmss). Met de parameter `-InstanceId` kunt u een of meer VM's opgeven om opnieuw op te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset opnieuw opgestart. Als u meerdere Vm's opnieuw wilt opstarten, moet u elke exemplaar-ID scheiden met een komma.

In het volgende voor beeld wordt instantie *0* opnieuw gestart in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . U kunt als volgt uw eigen waarden opgeven:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Vm's uit een schaalset verwijderen
Als u een of meer virtuele machines in een schaalset wilt verwijderen, gebruikt u [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). Met de `-InstanceId` para meter kunt u een of meer vm's opgeven die moeten worden verwijderd. Als u geen exemplaar-ID opgeeft, worden alle Vm's in de schaalset verwijderd. Als u meerdere Vm's wilt verwijderen, moet u elke exemplaar-ID scheiden met een komma.

In het volgende voor beeld wordt instantie *0* verwijderd uit de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . U kunt als volgt uw eigen waarden opgeven:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Volgende stappen
Andere algemene taken voor schaal sets zijn onder andere het [implementeren van een toepassing](virtual-machine-scale-sets-deploy-app.md)en het [bijwerken van VM-exemplaren](virtual-machine-scale-sets-upgrade-scale-set.md). U kunt Azure PowerShell ook gebruiken voor het [configureren van regels voor automatisch schalen](virtual-machine-scale-sets-autoscale-overview.md).
