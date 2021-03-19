---
title: Uitbrei ding van extensie gebruiken met virtuele-machine schaal sets van Azure
description: Meer informatie over het inrichten van Sequence-extensies bij het implementeren van meerdere uitbrei dingen in virtuele-machine schaal sets.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83121028"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Inrichten van Sequence-extensie in virtuele-machine schaal sets
Extensies voor virtuele Azure-machines bieden mogelijkheden zoals configuratie en beheer na de implementatie, bewaking, beveiliging en meer. Productie-implementaties gebruiken meestal een combi natie van meerdere uitbrei dingen die zijn geconfigureerd voor de VM-exemplaren om de gewenste resultaten te krijgen.

Wanneer u meerdere uitbrei dingen op een virtuele machine gebruikt, is het belang rijk om ervoor te zorgen dat uitbrei dingen waarvoor dezelfde resources van het besturings systeem zijn vereist, deze bronnen niet tegelijkertijd proberen op te halen. Sommige uitbrei dingen zijn ook afhankelijk van andere uitbrei dingen om vereiste configuraties te bieden, zoals omgevings instellingen en geheimen. Zonder dat de juiste volg orde en sequentiëren zijn geïmplementeerd, kunnen de implementaties van afhankelijke extensies mislukken.

In dit artikel wordt beschreven hoe u de volg orde van extensies kunt configureren voor de VM-exemplaren in virtuele-machine schaal sets.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u bekend bent met:
-   [Extensies](../virtual-machines/extensions/overview.md) voor virtuele Azure-machines
-   Schaal sets voor virtuele machines [wijzigen](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-extension-sequencing"></a>Wanneer moet u de extensie volgorde gebruiken?
Uitbrei dingen voor sequentiëren in niet verplicht voor schaal sets, en tenzij opgegeven, kunnen uitbrei dingen worden ingericht op een exemplaar van een schaalset in een wille keurige volg orde.

Als uw Scale set-model bijvoorbeeld twee extensies heeft: Extensiona en ExtensionB, die in het model zijn opgegeven, kan een van de volgende inrichtings reeksen optreden:
-   Extensiea-> ExtensionB
-   ExtensionB-> Extensiona

Als voor uw toepassing extensie A vereist is om altijd te worden ingericht vóór de extensie B, moet u de uitbrei ding van de extensie gebruiken zoals beschreven in dit artikel. Met uitbrei ding van extensie wordt er slechts één reeks weer gegeven:
-   Extensiea-> ExtensionB

Extensies die niet in een gedefinieerde inrichtings volgorde zijn opgegeven, kunnen op elk gewenst moment worden ingericht, met inbegrip van vóór, na, of tijdens een gedefinieerde reeks. Uitbrei ding van extensie geeft alleen aan dat een specifieke extensie wordt ingericht na een andere specifieke uitbrei ding. Dit heeft geen invloed op het inrichten van een andere uitbrei ding die is gedefinieerd in het model.

Als uw schaalset bijvoorbeeld drie extensies heeft: extensie A, extensie B en extensie C die is opgegeven in het model, en extension C is ingesteld om te worden ingericht na extensie A, dan kan een van de volgende inrichtings reeksen optreden:
-   Extensiona-> ExtensionC-> ExtensionB
-   ExtensionB-> Extensiona-> ExtensionC
-   Extensiona-> ExtensionB-> ExtensionC

Als u er zeker van wilt zijn dat er geen andere uitbrei ding is ingericht tijdens de uitvoering van de gedefinieerde extensie reeks, kunt u het beste alle uitbrei dingen in het model voor de schaalset opgeven. In het bovenstaande voor beeld kan extension B worden ingesteld om te worden ingericht na extensie C, zodat er slechts één reeks kan optreden:
-   Extensiona-> ExtensionC-> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Instructies voor het gebruik van uitbrei dingen gebruiken
Voor het inrichten van Sequence-extensies moet u de extensie definitie in het model voor schaal sets bijwerken met de eigenschap "provisionAfterExtensions", waarmee een matrix met extensie namen wordt geaccepteerd. De uitbrei dingen die worden vermeld in de waarde van de eigenschaps matrix, moeten volledig zijn gedefinieerd in het model voor schaal sets.

### <a name="template-deployment"></a>Sjabloon implementatie
In het volgende voor beeld wordt een sjabloon gedefinieerd waarbij de schaalset drie extensies bevat: Extensiona, ExtensionB en ExtensionC, zodat uitbrei dingen in de volg orde worden ingericht:
-   Extensiona-> ExtensionB-> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Omdat de eigenschap ' provisionAfterExtensions ' een matrix met extensie namen accepteert, kan het bovenstaande voor beeld zodanig worden gewijzigd dat ExtensionC is ingericht na Extensiona en ExtensionB, maar er is geen volg orde vereist tussen Extensiona en ExtensionB. De volgende sjabloon kan worden gebruikt om dit scenario te verzorgen:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST-API
In het volgende voor beeld wordt een nieuwe uitbrei ding met de naam ExtensionC toegevoegd aan een model met een schaalset. ExtensionC heeft afhankelijkheden voor Extensiona en ExtensionB, die al zijn gedefinieerd in het model voor schaal sets.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Als ExtensionC eerder is gedefinieerd in het model voor de schaalset en u nu de afhankelijkheden ervan wilt toevoegen, kunt u een uitvoeren `PATCH` om de al geïmplementeerde extensie-eigenschappen te bewerken.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Wijzigingen in bestaande exemplaren van een schaalset worden toegepast bij de volgende [upgrade](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de toepassings status extensie toe te voegen aan de model definitie van de schaalset. Voor extensie sequentiëren is het gebruik van AZ Power shell 1.2.0 of hoger vereist.

In het volgende voor beeld wordt de [toepassings status extensie](virtual-machine-scale-sets-health-extension.md) toegevoegd aan de `extensionProfile` in een schaalset model van een op Windows gebaseerde schaalset. De uitbrei ding voor de toepassings status wordt ingericht na het inrichten van de [aangepaste script extensie](../virtual-machines/extensions/custom-script-windows.md), al gedefinieerd in de schaalset.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Gebruik [AZ vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) om de toepassings status extensie toe te voegen aan de model definitie van de schaalset. Voor extensie sequentiëren is het gebruik van Azure CLI 2.0.55 of hoger vereist.

In het volgende voor beeld wordt de [toepassings status extensie](virtual-machine-scale-sets-health-extension.md) toegevoegd aan het model met schaal sets van een op Windows gebaseerde schaalset. De uitbrei ding voor de toepassings status wordt ingericht na het inrichten van de [aangepaste script extensie](../virtual-machines/extensions/custom-script-windows.md), al gedefinieerd in de schaalset.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Problemen oplossen

### <a name="not-able-to-add-extension-with-dependencies"></a>Kan uitbrei ding met afhankelijkheden niet toevoegen?
1. Zorg ervoor dat de uitbrei dingen die zijn opgegeven in provisionAfterExtensions zijn gedefinieerd in het model voor schaal sets.
2. Zorg ervoor dat er geen circulaire afhankelijkheden worden geïntroduceerd. De volgende reeks is bijvoorbeeld niet toegestaan: Extensiona-> ExtensionB-> ExtensionC-> Extensiona
3. Zorg ervoor dat alle uitbrei dingen waarvoor u afhankelijkheden maakt, een eigenschap "Settings" hebben onder extensie "Eigenschappen". Als ExtentionB bijvoorbeeld moeten worden ingericht na Extensiea, moet Extensiona het veld ' instellingen ' hebben onder Extensiona ' Eigenschappen '. U kunt een lege eigenschap "Settings" opgeven als de extensie de vereiste instellingen niet verplicht stelt.

### <a name="not-able-to-remove-extensions"></a>Kunt u geen uitbrei dingen verwijderen?
Zorg ervoor dat de uitbrei dingen die worden verwijderd, niet worden vermeld onder provisionAfterExtensions voor andere uitbrei dingen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machine schaal sets.
