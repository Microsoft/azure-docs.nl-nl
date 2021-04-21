---
title: Extensiesequencing gebruiken met virtuele-machineschaalsets van Azure
description: Meer informatie over het sequentie-inrichten van extensies bij het implementeren van meerdere extensies op virtuele-machineschaalsets.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1b5aea1f0f0101231408dc9ad7b57a30f2c86256
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788153"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Sequentie-extensies inrichten in virtuele-machineschaalsets
Extensies van virtuele Azure-machines bieden mogelijkheden zoals configuratie en beheer na de implementatie, bewaking, beveiliging en meer. Productie-implementaties maken doorgaans gebruik van een combinatie van meerdere extensies die zijn geconfigureerd voor de VM-exemplaren om de gewenste resultaten te bereiken.

Wanneer u meerdere extensies op een virtuele machine gebruikt, is het belangrijk om ervoor te zorgen dat extensies waarvoor dezelfde besturingssysteembronnen zijn vereist, niet tegelijkertijd proberen om deze resources te verkrijgen. Sommige extensies zijn ook afhankelijk van andere extensies om vereiste configuraties te bieden, zoals omgevingsinstellingen en geheimen. Zonder de juiste volgorde en volgorde kunnen afhankelijke extensie-implementaties mislukken.

In dit artikel wordt beschreven hoe u extensies kunt sequentieeren die moeten worden geconfigureerd voor de VM-exemplaren in virtuele-machineschaalsets.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgenomen dat u bekend bent met:
-   Extensies voor virtuele [Azure-machines](../virtual-machines/extensions/overview.md)
-   [Virtuele-machineschaalsets](virtual-machine-scale-sets-upgrade-scale-set.md) wijzigen

## <a name="when-to-use-extension-sequencing"></a>Wanneer u extensiesequencing gebruikt
Extensies in sequencing zijn niet verplicht voor schaalsets en, tenzij opgegeven, kunnen extensies in een bepaalde volgorde worden ingericht op een schaalsetexequentie.

Als uw schaalsetmodel bijvoorbeeld twee extensies ( ExtensionA en ExtensionB) in het model heeft, kan een van de volgende inrichtingsreeksen optreden:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Als uw toepassing vereist dat extensie A altijd vóór extensie B wordt ingericht, moet u extensiesequenctie gebruiken zoals beschreven in dit artikel. Met extensievolgorde wordt nu slechts één reeks uitgevoerd:
-   ExtensionA - > ExtensionB

Extensies die niet zijn opgegeven in een gedefinieerde inrichtingsreeks, kunnen op elk moment worden ingericht, inclusief vóór, na of tijdens een gedefinieerde reeks. Extensie sequencing geeft alleen aan dat een specifieke extensie wordt ingericht na een andere specifieke extensie. Dit heeft geen invloed op het inrichten van andere extensies die in het model zijn gedefinieerd.

Als uw schaalsetmodel bijvoorbeeld drie extensies heeft: Extensie A, Extensie B en Extensie C, die zijn opgegeven in het model en Extensie C is ingesteld om te worden ingericht na extensie A, kan een van de volgende inrichtingsreeksen optreden:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Als u ervoor wilt zorgen dat er geen andere extensie wordt ingericht terwijl de gedefinieerde extensiereeks wordt uitgevoerd, raden we u aan alle extensies in uw schaalsetmodel te sequereren. In het bovenstaande voorbeeld kan extensie B worden ingesteld om te worden ingericht na extensie C, zodat er slechts één reeks kan plaatsvinden:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Extensiesequencing gebruiken
Als u de inrichting van extensies wilt sequentieën, moet u de extensiedefinitie in het schaalsetmodel bijwerken met de eigenschap provisionAfterExtensions, die een matrix met extensienamen accepteert. De extensies die worden vermeld in de eigenschap matrixwaarde moeten volledig worden gedefinieerd in het schaalsetmodel.

### <a name="template-deployment"></a>Sjabloonimplementatie
Het volgende voorbeeld definieert een sjabloon waarbij de schaalset drie extensies heeft: ExtensionA, ExtensionB en ExtensionC, zodat extensies in de volgorde worden ingericht:
-   ExtensionA -> ExtensionB -> ExtensionC

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

Omdat de eigenschap provisionAfterExtensions een matrix met extensienamen accepteert, kan het bovenstaande voorbeeld zodanig worden gewijzigd dat ExtensionC wordt ingericht na ExtensionA en ExtensionB, maar er geen volgorde is vereist tussen ExtensionA en ExtensionB. De volgende sjabloon kan worden gebruikt om dit scenario te bereiken:

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
In het volgende voorbeeld wordt een nieuwe extensie met de naam ExtensionC toegevoegd aan een schaalsetmodel. ExtensionC heeft afhankelijkheden van ExtensionA en ExtensionB, die al zijn gedefinieerd in het schaalsetmodel.

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

Als ExtensionC eerder in het schaalsetmodel is gedefinieerd en u nu de afhankelijkheden ervan wilt toevoegen, kunt u een uitvoeren om de eigenschappen van de reeds geïmplementeerde extensie `PATCH` te bewerken.

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
Wijzigingen in bestaande exemplaren van schaalsets worden toegepast bij de volgende [upgrade.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-powershell"></a>Azure PowerShell
Gebruik de cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de application health-extensie toe te voegen aan de definitie van het schaalsetmodel. Voor het sequeneren van extensies is het gebruik van Az PowerShell 1.2.0 of hoger vereist.

In het volgende voorbeeld wordt de [extensie Application Health toegevoegd](virtual-machine-scale-sets-health-extension.md) aan de in een `extensionProfile` schaalsetmodel van een Windows-schaalset. De toepassings health-extensie wordt ingericht na het inrichten van de [aangepaste scriptextensie](../virtual-machines/extensions/custom-script-windows.md), die al is gedefinieerd in de schaalset.

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
Gebruik [az vmss extension set om](/cli/azure/vmss/extension#az_vmss_extension_set) de extensie Application Health toe te voegen aan de definitie van het schaalsetmodel. Voor het sequeneren van extensies is het gebruik van Azure CLI 2.0.55 of hoger vereist.

In het volgende voorbeeld wordt de [extensie Application Health toegevoegd](virtual-machine-scale-sets-health-extension.md) aan het schaalsetmodel van een Windows-schaalset. De toepassings health-extensie wordt ingericht na het inrichten van de [aangepaste scriptextensie](../virtual-machines/extensions/custom-script-windows.md), die al is gedefinieerd in de schaalset.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>Kunt u geen extensie met afhankelijkheden toevoegen?
1. Zorg ervoor dat de extensies die zijn opgegeven in provisionAfterExtensions zijn gedefinieerd in het schaalsetmodel.
2. Zorg ervoor dat er geen circulaire afhankelijkheden worden geïntroduceerd. De volgende reeks is bijvoorbeeld niet toegestaan: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Zorg ervoor dat alle extensies waar u afhankelijkheden van neemt, een eigenschap 'settings' hebben onder extensie 'properties'. Als ExtentionB bijvoorbeeld na ExtensionA moet worden ingericht, moet ExtensionA het veld 'settings' onder ExtensionA 'properties' hebben. U kunt een lege eigenschap 'settings' opgeven als de extensie geen vereiste instellingen vereist.

### <a name="not-able-to-remove-extensions"></a>Kunt u extensies niet verwijderen?
Zorg ervoor dat de extensies die worden verwijderd, niet worden vermeld onder provisionAfterExtensions voor andere extensies.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machineschaalsets.
