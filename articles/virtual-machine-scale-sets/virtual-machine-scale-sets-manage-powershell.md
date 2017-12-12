---
title: Virtuele-Machineschaalsets met Azure PowerShell beheren | Microsoft Docs
description: Algemene Azure PowerShell-cmdlets voor het beheren van virtuele-Machineschaalsets, zoals het starten en stoppen van een exemplaar of de schaal wijzigen instellen capaciteit.
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 39836b207a84911d4749da8a084779d93949846b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Een virtuele-machineschaalset ingesteld met Azure PowerShell beheren
Gedurende de levenscyclus van een virtuele-machineschaalset, moet u wellicht een of meer beheertaken uitvoeren. Bovendien wilt u scripts maken die verschillende lifecycle-taken automatiseren. In dit artikel vindt u details van de algemene Azure PowerShell-cmdlets waarmee u kunt deze taken uitvoeren.

U moet de meest recente Azure PowerShell-module voor het voltooien van deze beheertaken. Zie voor meer informatie over het installeren en gebruiken van de meest recente versie [aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps). Als u maken van een virtuele-machineschaalset wilt, kunt u [een schaal ingesteld in de Azure portal maken](virtual-machine-scale-sets-portal-create.md).


## <a name="view-information-about-a-scale-set"></a>Informatie over een schaalset weergeven
Gebruiken om de algemene informatie weergeven over een schaalset, [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). Het volgende voorbeeld wordt informatie over de set met de naam scale *myScaleSet* in de *myResourceGroup* resourcegroep. Voer uw eigen namen:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Weergave virtuele machines in een schaalset
Een overzicht van VM-instantie in een schaalset gebruiken [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Het volgende voorbeeld lijst met alle VM-exemplaren in de set met de naam scale *myScaleSet* en in de *myResourceGroup* resourcegroep. Uw eigen waarden opgeven voor deze namen:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Om weer te geven als u meer informatie over een specifiek exemplaar van de VM, voeg de `-InstanceId` -parameter voor [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) en geeft u een exemplaar om weer te geven. Informatie over de VM-instantie voor het volgende voorbeeld-weergaven *0* in de set met de naam scale *myScaleSet* en de *myResourceGroup* resourcegroep. Voer uw eigen namen:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>De capaciteit van een schaalset wijzigen
De voorgaande opdrachten hebt u geleerd informatie over uw schaalset en de VM-exemplaren. Als u wilt vergroten of verkleinen het aantal exemplaren in de schaalset, kunt u de capaciteit. De schaal automatisch ingesteld maakt of verwijdert u het vereiste aantal virtuele machines en configureert u de virtuele machines voor het ontvangen verkeer van de toepassing.

Maak eerst een object met een scale set [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), typt u een nieuwe waarde voor `sku.capacity`. De als capaciteitswijziging wilt toepassen, gebruikt u [Update AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Het volgende voorbeeld updates *myScaleSet* in de *myResourceGroup* resourcegroep naar een capaciteit van *5* exemplaren. Geef uw eigen waarden als volgt:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Als duurt een paar minuten bijwerken van de capaciteit van de schaal is ingesteld. Als u verlaagt de capaciteit van een schaal ingesteld, wordt de virtuele machines met de hoogste exemplaar id's eerst worden verwijderd.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppen en starten van virtuele machines in een schaalset
Voor het beëindigen van een of meer virtuele machines in een schaalset gebruikt [Stop AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). De `-InstanceId` parameter kunt u een of meer virtuele machines stoppen opgeven. Als u een exemplaar-ID niet opgeeft, worden alle virtuele machines in de schaalset gestopt. Als u wilt stoppen met meerdere virtuele machines, elk exemplaar-ID met een komma te scheiden.

Het volgende voorbeeld gestopt exemplaar *0* in de set met de naam scale *myScaleSet* en de *myResourceGroup* resourcegroep. Geef de waarden als volgt:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Standaard gestopte virtuele machines zijn de toewijzing ongedaan gemaakt en kunnen niet worden compute-kosten in rekening. Als u wilt dat de virtuele machine te blijven ingerichte status gestopt, het toevoegen van de `-StayProvisioned` -parameter voor de voorgaande opdracht. Gestopte virtuele machines die ingerichte blijven worden regelmatig compute-kosten in rekening.


### <a name="start-vms-in-a-scale-set"></a>Starten van virtuele machines in een schaalset
Gebruik voor het starten van een of meer virtuele machines in een schaalset, [Start AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). De `-InstanceId` parameter kunt u opgeven van een of meer virtuele machines worden gestart. Als u een exemplaar-ID niet opgeeft, worden alle virtuele machines in de schaalset gestart. U kunt meerdere virtuele machines starten door elk exemplaar-ID met een komma te scheiden.

Het volgende voorbeeld wordt een exemplaar gestart *0* in de set met de naam scale *myScaleSet* en de *myResourceGroup* resourcegroep. Geef de waarden als volgt:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Opnieuw opstarten van virtuele machines in een schaalset
Als een of meer virtuele machines in een schaalset opnieuw, wilt u [Retart AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). De `-InstanceId` parameter kunt u een of meer virtuele machines opnieuw opstarten opgeven. Als u een exemplaar-ID niet opgeeft, worden alle virtuele machines in de schaalset opnieuw gestart. Als u wilt meerdere virtuele machines opnieuw is opgestart, elke instantie-ID met een komma te scheiden.

Het volgende voorbeeld wordt opnieuw opgestart exemplaar *0* in de set met de naam scale *myScaleSet* en de *myResourceGroup* resourcegroep. Geef de waarden als volgt:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Virtuele machines uit een set scale verwijderen
Gebruik te verwijderen van een of meer virtuele machines in een set scale [verwijderen AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). De `-InstanceId` parameter kunt u opgeven van een of meer VM's te verwijderen. Als u een exemplaar-ID niet opgeeft, worden alle virtuele machines in de schaalset verwijderd. Als u wilt verwijderen van meerdere virtuele machines, elk exemplaar-ID met een komma te scheiden.

Het volgende voorbeeld verwijdert exemplaar *0* in de set met de naam scale *myScaleSet* en de *myResourceGroup* resourcegroep. Geef de waarden als volgt:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Volgende stappen
Andere algemene taken voor schaalsets omvatten het [implementeren van een toepassing](virtual-machine-scale-sets-deploy-app.md), en [upgrade VM-instanties](virtual-machine-scale-sets-upgrade-scale-set.md). U kunt ook Azure PowerShell te gebruiken [regels voor automatisch schalen configureren](virtual-machine-scale-sets-autoscale-overview.md).
