---
title: Exemplaarbeveiliging voor exemplaren van virtuele-machineschaalsets van Azure
description: Meer informatie over het beveiligen van exemplaren van virtuele-machineschaalsets in Azure tegen inschalen en schaalsetbewerkingen.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 292abce3361c000eeeef2c399d5ffa2d2c4852e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762853"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Exemplaarbeveiliging voor exemplaren van virtuele-machineschaalsets van Azure

Virtuele-machineschaalsets van Azure bieden een [](virtual-machine-scale-sets-autoscale-overview.md)betere elasticiteit voor uw workloads via automatisch schalen, zodat u kunt configureren wanneer uw infrastructuur wordt uitschalen en wanneer deze wordt inschalen. Met schaalsets kunt u een groot aantal VM's ook centraal beheren, configureren en bijwerken via verschillende [upgradebeleidsinstellingen.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) U kunt een update configureren voor het schaalsetmodel en de nieuwe configuratie wordt automatisch toegepast op elk exemplaar van de schaalset als u het upgradebeleid hebt ingesteld op Automatisch of Rolling.

Terwijl uw toepassing verkeer verwerkt, kunnen er situaties zijn waarin u wilt dat specifieke exemplaren anders worden behandeld dan de rest van het schaalset-exemplaar. Bepaalde exemplaren in de schaalset kunnen bijvoorbeeld langlopende bewerkingen uitvoeren en u wilt niet dat deze exemplaren worden inschalen totdat de bewerkingen zijn voltooid. Mogelijk hebt u ook een aantal instanties in de schaalset gespecialiseerd om extra of andere taken uit te voeren dan de andere leden van de schaalset. U wilt dat deze 'speciale' VM's niet worden gewijzigd met de andere exemplaren in de schaalset. Exemplaarbeveiliging biedt de aanvullende besturingselementen om deze en andere scenario's voor uw toepassing mogelijk te maken.

In dit artikel wordt beschreven hoe u de verschillende mogelijkheden voor exemplaarbeveiliging kunt toepassen en gebruiken met instanties van schaalsets.

## <a name="types-of-instance-protection"></a>Typen exemplaarbeveiliging
Schaalsets bieden twee typen instantiebeveiligingsmogelijkheden:

-   **Beveiligen tegen inschalen**
    - Ingeschakeld via de **eigenschap protectFromScaleIn op** het exemplaar van de schaalset
    - Beschermt exemplaar tegen automatisch schalen geïnitieerde inschalen
    - Door de gebruiker geïnitieerde exemplaarbewerkingen (inclusief het verwijderen van **exemplaren) worden niet geblokkeerd**
    - Bewerkingen die zijn gestart op de schaalset (upgraden, opnieuw maken, de toewijzing van de toewijzing verwijderen, enzovoort) **worden niet geblokkeerd**

-   **Beveiligen tegen schaalsetacties**
    - Ingeschakeld via de **eigenschap protectFromScaleSetActions** op het exemplaar van de schaalset
    - Beschermt exemplaar tegen automatisch schalen geïnitieerde inschalen
    - Beschermt exemplaren tegen bewerkingen die zijn geïnitieerd op de schaalset (zoals upgraden, opnieuw instellen, toewijzing van de toewijzing verwijderen, enzovoort)
    - Door de gebruiker geïnitieerde exemplaarbewerkingen (inclusief het verwijderen van **exemplaren) worden niet geblokkeerd**
    - Verwijderen van de volledige schaalset wordt **niet geblokkeerd**

## <a name="protect-from-scale-in"></a>Beveiligen tegen inschalen
Exemplaarbeveiliging kan worden toegepast op instanties van schaalsets nadat de exemplaren zijn gemaakt. Beveiliging wordt alleen toegepast en gewijzigd op het [exemplaarmodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) en niet op het [schaalsetmodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Er zijn meerdere manieren om schaalbeveiliging toe te passen op uw schaalset-exemplaren, zoals beschreven in de onderstaande voorbeelden.

### <a name="azure-portal"></a>Azure Portal

U kunt inschaalbeveiliging via de Azure Portal op een exemplaar in de schaalset. U kunt niet meer dan één exemplaar tegelijk aanpassen. Herhaal de stappen voor elk exemplaar dat u wilt beveiligen.
 
1. Ga naar een bestaande virtuele-machineschaalset.
1. Selecteer **Exemplaren** in het menu aan de linkerkant, onder **Instellingen.**
1. Selecteer de naam van het exemplaar dat u wilt beveiligen.
1. Selecteer het **tabblad Beveiligingsbeleid.**
1. Selecteer op **de** blade Beveiligingsbeleid de optie **Beveiligen tegen inschalen.**
1. Selecteer **Opslaan**. 

### <a name="rest-api"></a>REST-API

In het volgende voorbeeld wordt inschaalbeveiliging toegepast op een exemplaar in de schaalset.

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
>Exemplaarbeveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de [cmdlet Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) om schaalbeveiliging toe te passen op uw exemplaar van de schaalset.

In het volgende voorbeeld wordt inschaalbeveiliging toegepast op een exemplaar in de schaalset met exemplaar-id 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [az vmss update om](/cli/azure/vmss#az_vmss_update) schaalbeveiliging toe te passen op uw schaalset-exemplaar.

In het volgende voorbeeld wordt inschaalbeveiliging toegepast op een exemplaar in de schaalset met exemplaar-id 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Beveiligen tegen acties in schaalsets
Exemplaarbeveiliging kan worden toegepast op instanties van schaalsets nadat de exemplaren zijn gemaakt. Beveiliging wordt alleen toegepast en gewijzigd op het [exemplaarmodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) en niet op het [schaalsetmodel](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Door een exemplaar te beveiligen tegen schaalsetacties wordt het exemplaar ook beschermd tegen automatisch schalen geïnitieerde inschalen.

Er zijn meerdere manieren om beveiliging voor schaalsetacties toe te passen op uw schaalset-exemplaren, zoals beschreven in de onderstaande voorbeelden.

### <a name="azure-portal"></a>Azure Portal

U kunt beveiliging van schaalsetacties toepassen via de Azure Portal op een exemplaar in de schaalset. U kunt niet meer dan één exemplaar tegelijk aanpassen. Herhaal de stappen voor elk exemplaar dat u wilt beveiligen.
 
1. Ga naar een bestaande virtuele-machineschaalset.
1. Selecteer **Exemplaren** in het menu aan de linkerkant, onder **Instellingen.**
1. Selecteer de naam van het exemplaar dat u wilt beveiligen.
1. Selecteer het **tabblad Beveiligingsbeleid.**
1. Selecteer op de blade **Beveiligingsbeleid** de optie **Beveiligen tegen schaalsetacties.**
1. Selecteer **Opslaan**. 

### <a name="rest-api"></a>REST-API

In het volgende voorbeeld wordt beveiliging van schaalsetacties toegepast op een exemplaar in de schaalset.

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
>Exemplaarbeveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger.</br>
Door een exemplaar te beveiligen tegen schaalsetacties wordt het exemplaar ook beschermd tegen automatisch schalen geïnitieerde inschalen. U kunt 'protectFromScaleIn': false niet opgeven bij het instellen van 'protectFromScaleSetActions': true

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) om beveiliging van schaalsetacties toe te passen op uw exemplaar van de schaalset.

In het volgende voorbeeld wordt beveiliging van schaalsetacties toegepast op een exemplaar in de schaalset met exemplaar-id 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [az vmss update om](/cli/azure/vmss#az_vmss_update) beveiliging van schaalsetacties toe te passen op uw schaalset-exemplaar.

In het volgende voorbeeld wordt beveiliging van schaalsetacties toegepast op een exemplaar in de schaalset met exemplaar-id 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Problemen oplossen
### <a name="no-protectionpolicy-on-scale-set-model"></a>Geen beveiligingBeleid voor schaalsetmodel
Exemplaarbeveiliging is alleen van toepassing op exemplaren van schaalsets en niet op het schaalsetmodel.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Geen beveiligingBeleid voor het exemplaarmodel van de schaalset
Standaard wordt het beveiligingsbeleid niet toegepast op een exemplaar wanneer het wordt gemaakt.

Nadat de exemplaren zijn gemaakt, kunt u exemplaarbeveiliging toepassen op instanties van schaalsets.

### <a name="not-able-to-apply-instance-protection"></a>Kan geen exemplaarbeveiliging toepassen
Exemplaarbeveiliging wordt alleen ondersteund met API-versie 2019-03-01 en hoger. Controleer de API-versie die wordt gebruikt en werk deze indien nodig bij. Mogelijk moet u ook uw PowerShell of CLI bijwerken naar de nieuwste versie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machineschaalsets.
