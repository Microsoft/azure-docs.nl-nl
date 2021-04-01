---
title: De toepassings status extensie gebruiken met schaal sets voor virtuele Azure-machines
description: Meer informatie over het gebruik van de toepassings status extensie voor het controleren van de status van uw toepassingen die zijn geïmplementeerd op schaal sets voor virtuele machines.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: a38a715b45ab4d0810862ef4d016e4187ea507ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84783041"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>De toepassingsstatusextensie gebruiken met virtuele-machineschaalsets
Het controleren van de status van uw toepassing is een belang rijk signaal voor het beheren en upgraden van uw implementatie. Virtuele-machine schaal sets van Azure bieden ondersteuning voor [rolling upgrades](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , waaronder [automatische upgrades van besturings systemen](virtual-machine-scale-sets-automatic-upgrade.md), die afhankelijk zijn van de status controle van de afzonderlijke instanties om uw implementatie bij te werken. U kunt ook de status extensie gebruiken om de toepassings status van elk exemplaar in uw schaalset te bewaken en de reparatie van instanties uit te voeren met [automatische herstel bewerkingen](virtual-machine-scale-sets-automatic-instance-repairs.md).

In dit artikel wordt beschreven hoe u de toepassings status uitbreiding kunt gebruiken om de status van uw toepassingen te controleren die zijn geïmplementeerd op schaal sets voor virtuele machines.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u bekend bent met:
-   [Extensies](../virtual-machines/extensions/overview.md) voor virtuele Azure-machines
-   Schaal sets voor virtuele machines [wijzigen](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-the-application-health-extension"></a>Wanneer de toepassings status uitbreiding moet worden gebruikt
De uitbrei ding voor de toepassings status wordt geïmplementeerd binnen een exemplaar van een virtuele-machine schaalset en rapporten over de VM-status in het exemplaar van de schaalset. U kunt de uitbrei ding voor het testen van het eind punt van een toepassing configureren en de status van de toepassing op dat exemplaar bijwerken. Deze status van het exemplaar wordt gecontroleerd door Azure om te bepalen of een exemplaar in aanmerking komt voor upgrade bewerkingen.

Als de uitbrei ding rapporteert over de status van binnen een virtuele machine, kan de uitbrei ding worden gebruikt in situaties waarin externe tests, zoals het controleren van de status van toepassingen (die gebruikmaken van aangepaste Azure Load Balancer [tests](../load-balancer/load-balancer-custom-probe-overview.md)), niet kunnen worden gebruikt.

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de uitbrei ding van de toepassings status. Voor de uitbrei ding is mini maal een TCP-, http-of HTTPS-aanvraag met een bijbehorend poort-of aanvraag pad vereist.

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

### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld | Gegevenstype
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | tekenreeks |
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | tekenreeks |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Instellingen

| Name | Waarde/voor beeld | Gegevenstype
| ---- | ---- | ----
| protocol | `http` of `https` of `tcp` | tekenreeks |
| poort | Optioneel wanneer protocol is `http` of `https` , verplicht wanneer protocol wordt `tcp` | int |
| requestPath | Verplicht wanneer het protocol is `http` of `https` niet is toegestaan wanneer het protocol is `tcp` | tekenreeks |

## <a name="deploy-the-application-health-extension"></a>De toepassings status uitbreiding implementeren
Er zijn meerdere manieren om de toepassings status extensie te implementeren op uw schaal sets, zoals beschreven in de onderstaande voor beelden.

### <a name="rest-api"></a>REST-API

In het volgende voor beeld wordt de toepassings status extensie (met de naam myHealthExtension) toegevoegd aan de extensionProfile in het model met de schaalset van een op Windows gebaseerde schaalset.

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

Gebruik de cmdlet [add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) om de toepassings status extensie toe te voegen aan de model definitie van de schaalset.

In het volgende voor beeld wordt de toepassings status extensie toegevoegd aan de `extensionProfile` in het model Scale set van een op Windows gebaseerde schaalset. In het voor beeld wordt de nieuwe AZ Power shell-module gebruikt.

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

Gebruik [AZ vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set) om de toepassings status extensie toe te voegen aan de model definitie van de schaalset.

In het volgende voor beeld wordt de toepassings status extensie toegevoegd aan het model met schaal sets van een op Linux gebaseerde schaalset.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
De extension.jsvoor bestands inhoud.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Problemen oplossen
Uitvoer voor uitvoering van extensie wordt vastgelegd in bestanden die in de volgende directory's zijn gevonden:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

In de logboeken wordt ook regel matig de status van de toepassings status vastgelegd.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machine schaal sets.
