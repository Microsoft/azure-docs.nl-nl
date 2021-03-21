---
title: Instantie beveiliging voor instanties van de schaalset voor virtuele machines van Azure
description: Meer informatie over het beveiligen van de schaalset-instanties van Azure virtual machine van scale-in en scale-set-bewerkingen.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8c4944da8ffcaa75e6448483918a29809c32830b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746801"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Instantie beveiliging voor instanties van de schaalset voor virtuele machines van Azure

Met virtuele-machine schaal sets van Azure kunt u uw workloads beter belasten door automatisch te [schalen](virtual-machine-scale-sets-autoscale-overview.md), zodat u configureert wanneer uw infra structuur wordt geschaald en wanneer deze wordt geschaald. Met schaal sets kunt u ook een groot aantal virtuele machines centraal beheren, configureren en bijwerken met behulp van verschillende instellingen voor het [upgrade beleid](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) . U kunt een update configureren voor het model met de schaalset en de nieuwe configuratie wordt automatisch toegepast op elk exemplaar van de schaalset als u het upgrade beleid hebt ingesteld op automatisch of rolling.

Als uw toepassing verkeer verwerkt, zijn er situaties waarin u wilt dat bepaalde instanties anders worden behandeld dan de rest van het exemplaar van de schaalset. Bepaalde instanties in de schaalset kunnen bijvoorbeeld langlopende bewerkingen uitvoeren en u wilt niet dat deze instanties worden geschaald, totdat de bewerkingen zijn voltooid. Mogelijk hebt u ook enkele instanties in de schaalset nodig om extra of andere taken uit te voeren dan de andere leden van de schaalset. U wilt dat deze ' speciale ' Vm's niet worden gewijzigd met de andere exemplaren in de schaalset. Instantie beveiliging biedt de extra besturings elementen om deze en andere scenario's voor uw toepassing in te scha kelen.

In dit artikel wordt beschreven hoe u de verschillende functies voor instantie beveiliging kunt Toep assen en gebruiken met instanties van schaal sets.

## <a name="types-of-instance-protection"></a>Typen instantie beveiliging
Schaal sets bieden twee typen mogelijkheden voor het beveiligen van exemplaren:

-   **Beveiligen tegen schalen**
    - Ingeschakeld via de eigenschap **protectFromScaleIn** voor het exemplaar van de schaalset
    - Beschermt het exemplaar van automatisch schalen gestarte inschalen
    - Door de gebruiker geïnitieerde instantie bewerkingen (inclusief het verwijderen van instanties) worden **niet geblokkeerd**
    - Bewerkingen die zijn gestart op de schaalset (upgrade, installatie kopie, toewijzing ongedaan maken, enz.) worden **niet geblokkeerd**

-   **Beveiligen tegen acties van schaal sets**
    - Ingeschakeld via de eigenschap **protectFromScaleSetActions** voor het exemplaar van de schaalset
    - Beschermt het exemplaar van automatisch schalen gestarte inschalen
    - Beveiligt instantie van bewerkingen die zijn gestart op de schaalset (zoals upgrade, installatie kopie, toewijzing opheffen, enzovoort)
    - Door de gebruiker geïnitieerde instantie bewerkingen (inclusief het verwijderen van instanties) worden **niet geblokkeerd**
    - Verwijderen van de set met volledige schaal wordt **niet geblokkeerd**

## <a name="protect-from-scale-in"></a>Beveiligen tegen schalen
Instantie beveiliging kan worden toegepast op instanties van schaal sets nadat de exemplaren zijn gemaakt. De beveiliging wordt alleen toegepast en gewijzigd op het [exemplaar model](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) en niet in het model met de [schaalset](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Er zijn meerdere manieren voor het Toep assen van schaal beveiliging op de instanties van uw schaalset, zoals wordt beschreven in de onderstaande voor beelden.

### <a name="azure-portal"></a>Azure Portal

U kunt beveiliging tegen schalen Toep assen via de Azure Portal naar een exemplaar in de schaalset. U kunt niet meer dan één exemplaar tegelijk aanpassen. Herhaal de stappen voor elk exemplaar dat u wilt beveiligen.
 
1. Ga naar een bestaande schaalset voor virtuele machines.
1. Selecteer **instanties** in het menu aan de linkerkant onder **instellingen**.
1. Selecteer de naam van het exemplaar dat u wilt beveiligen.
1. Selecteer het tabblad **beveiligings beleid** .
1. Selecteer in de Blade **beveiligings beleid** de optie **beveiligen tegen schalen** .
1. Selecteer **Opslaan**. 

### <a name="rest-api"></a>REST-API

In het volgende voor beeld wordt scale-in-beveiliging toegepast op een exemplaar in de schaalset.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Instantie beveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) om beveiliging tegen schalen toe te passen op uw Scale set-exemplaar.

In het volgende voor beeld wordt scale-in-beveiliging toegepast op een exemplaar in de schaalset met exemplaar-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [AZ vmss update](/cli/azure/vmss#az-vmss-update) om beveiliging tegen schalen toe te passen op uw Scale set-exemplaar.

In het volgende voor beeld wordt scale-in-beveiliging toegepast op een exemplaar in de schaalset met exemplaar-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Beveiligen tegen acties van schaal sets
Instantie beveiliging kan worden toegepast op instanties van schaal sets nadat de exemplaren zijn gemaakt. De beveiliging wordt alleen toegepast en gewijzigd op het [exemplaar model](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) en niet in het model met de [schaalset](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Het beveiligen van een exemplaar van de schaalset acties beschermt ook het exemplaar van automatisch schalen gestarte inschalen.

Er zijn meerdere manieren voor het Toep assen van acties voor het instellen van schaal sets op de instanties van uw schaalset, zoals wordt beschreven in de onderstaande voor beelden.

### <a name="azure-portal"></a>Azure Portal

U kunt de beveiliging van schaal sets via de Azure Portal Toep assen op een exemplaar in de schaalset. U kunt niet meer dan één exemplaar tegelijk aanpassen. Herhaal de stappen voor elk exemplaar dat u wilt beveiligen.
 
1. Ga naar een bestaande schaalset voor virtuele machines.
1. Selecteer **instanties** in het menu aan de linkerkant onder **instellingen**.
1. Selecteer de naam van het exemplaar dat u wilt beveiligen.
1. Selecteer het tabblad **beveiligings beleid** .
1. Selecteer in de Blade **beveiligings beleid** de optie **beveiligen voor schaalset** .
1. Selecteer **Opslaan**. 

### <a name="rest-api"></a>REST-API

In het volgende voor beeld wordt de beveiliging van schaal sets-acties toegepast op een exemplaar in de schaalset.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Instantie beveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger.</br>
Het beveiligen van een exemplaar van de schaalset acties beschermt ook het exemplaar van automatisch schalen gestarte inschalen. U kunt ' protectFromScaleIn ' niet opgeven: False bij het instellen van ' protectFromScaleSetActions ': True

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) om beveiliging toe te passen op basis van de acties van de schaalset in het exemplaar van de schaalset.

In het volgende voor beeld wordt de beveiliging van schaal sets-acties toegepast op een exemplaar in de schaalset met exemplaar-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [AZ vmss update](/cli/azure/vmss#az-vmss-update) om beveiliging toe te passen op de schaalset-acties in het exemplaar van de schaalset.

In het volgende voor beeld wordt de beveiliging van schaal sets-acties toegepast op een exemplaar in de schaalset met exemplaar-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Problemen oplossen
### <a name="no-protectionpolicy-on-scale-set-model"></a>Geen protectionPolicy in model voor schaalset
Exemplaar beveiliging is alleen van toepassing op instanties van een schaalset en niet in het model voor de schaalset.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Geen protectionPolicy op het exemplaar model van de schaalset
Beveiligings beleid wordt standaard niet toegepast op een exemplaar wanneer het wordt gemaakt.

U kunt instantie beveiliging Toep assen op instanties van schaal sets nadat de instanties zijn gemaakt.

### <a name="not-able-to-apply-instance-protection"></a>Kan geen instantie beveiliging Toep assen
Instantie beveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger. Controleer of de gebruikte API-versie wordt gebruikt en werk de update indien nodig bij. Mogelijk moet u ook uw Power shell of CLI bijwerken naar de nieuwste versie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machine schaal sets.
