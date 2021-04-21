---
title: Application Health-extensie gebruiken met virtuele-machineschaalsets van Azure
description: Meer informatie over het gebruik van de extensie Application Health voor het bewaken van de status van uw toepassingen die zijn geïmplementeerd op virtuele-machineschaalsets.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 381573ae40f6c31a1c7dbf18bc60be5944fff39e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762889"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>De toepassingsstatusextensie gebruiken met virtuele-machineschaalsets
Het bewaken van de status van uw toepassing is een belangrijk signaal voor het beheren en upgraden van uw implementatie. Virtuele-machineschaalsets van Azure bieden ondersteuning voor rolling [upgrades,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) waaronder automatische [upgrades](virtual-machine-scale-sets-automatic-upgrade.md)van de besturingssysteemafbeelding, die afhankelijk zijn van statuscontrole van de afzonderlijke exemplaren om uw implementatie bij te upgraden. U kunt ook de statusextensie gebruiken om de toepassingstoestand van elk exemplaar in uw schaalset te bewaken en exemplaarreparaties uit te voeren met [behulp van automatische exemplaarreparaties.](virtual-machine-scale-sets-automatic-instance-repairs.md)

In dit artikel wordt beschreven hoe u de toepassingstoestandextensie kunt gebruiken om de status te bewaken van uw toepassingen die zijn geïmplementeerd op virtuele-machineschaalsets.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgenomen dat u bekend bent met:
-   Extensies voor virtuele [Azure-machines](../virtual-machines/extensions/overview.md)
-   [Virtuele-machineschaalsets](virtual-machine-scale-sets-upgrade-scale-set.md) wijzigen

## <a name="when-to-use-the-application-health-extension"></a>Wanneer gebruikt u de toepassings health-extensie?
De toepassings health-extensie wordt geïmplementeerd in een exemplaar van een virtuele-machineschaalset en rapporteert over de VM-status vanuit het exemplaar van de schaalset. U kunt de extensie configureren om te controleren op een toepassings-eindpunt en de status van de toepassing op dat exemplaar bij te werken. Deze exemplaarstatus wordt door Azure gecontroleerd om te bepalen of een exemplaar in aanmerking komt voor upgradebewerkingen.

Als de extensie status rapporteert vanuit een VM, kan de extensie worden gebruikt in situaties waarin externe [](../load-balancer/load-balancer-custom-probe-overview.md)tests zoals statustests van toepassingen (die gebruikmaken van aangepaste Azure Load Balancer-tests) niet kunnen worden gebruikt.

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON ziet u het schema voor de toepassings health-extensie. De extensie vereist ten minste een aanvraag 'tcp', 'http' of 'https' met een gekoppelde poort of aanvraagpad.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde/voorbeeld | Gegevenstype
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | tekenreeks |
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | tekenreeks |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Instellingen

| Name | Waarde/voorbeeld | Gegevenstype
| ---- | ---- | ----
| protocol | `http` of `https` of `tcp` | tekenreeks |
| poort | Optioneel wanneer protocol of `http` `https` is, verplicht wanneer protocol is `tcp` | int |
| requestPath | Verplicht wanneer protocol is `http` of `https` , niet toegestaan wanneer protocol is `tcp` | tekenreeks |

## <a name="deploy-the-application-health-extension"></a>De toepassings health-extensie implementeren
Er zijn meerdere manieren om de Toepassings health-extensie te implementeren in uw schaalsets, zoals beschreven in de onderstaande voorbeelden.

### <a name="rest-api"></a>REST-API

In het volgende voorbeeld wordt de extensie Application Health (met de naam myHealthExtension) toegevoegd aan het extensionProfile in het schaalsetmodel van een Windows-schaalset.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Gebruik `PATCH` om een reeds geïmplementeerde extensie te bewerken.

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de extensie Application Health toe te voegen aan de modeldefinitie van de schaalset.

In het volgende voorbeeld wordt de extensie Application Health toegevoegd aan `extensionProfile` de in het schaalsetmodel van een Windows-schaalset. In het voorbeeld wordt de nieuwe Az PowerShell-module gebruikt.

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
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Gebruik [az vmss extension set om](/cli/azure/vmss/extension#az_vmss_extension_set) de Application Health-extensie toe te voegen aan de definitie van het schaalsetmodel.

In het volgende voorbeeld wordt de extensie Application Health toegevoegd aan het schaalsetmodel van een Linux-schaalset.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
De extension.jsbestandsinhoud.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Problemen oplossen
De uitvoer van de extensie wordt vastgelegd in bestanden in de volgende mappen:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

De logboeken leggen ook periodiek de status van de toepassing vast.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machineschaalsets.
