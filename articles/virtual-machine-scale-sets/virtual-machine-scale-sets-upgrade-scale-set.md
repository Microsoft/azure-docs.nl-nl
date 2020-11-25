---
title: Een schaalset voor virtuele Azure-machines wijzigen
description: Meer informatie over hoe u een schaalset voor virtuele Azure-machines wijzigt en bijwerkt met de REST Api's, Azure PowerShell en Azure CLI
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 7577c8510746d1140c1f8b70081f600d992ae512
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016672"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Een virtuele-machineschaalset wijzigen

Gedurende de levens cyclus van uw toepassingen moet u mogelijk de schaalset voor virtuele machines wijzigen of bijwerken. Deze updates kunnen ook betrekking hebben op het bijwerken van de configuratie van de schaalset of het wijzigen van de toepassings configuratie. In dit artikel wordt beschreven hoe u een bestaande schaalset wijzigt met de REST Api's, Azure PowerShell of Azure CLI.

## <a name="fundamental-concepts"></a>Fundamentele concepten

### <a name="the-scale-set-model"></a>Het model voor schaal sets
Een schaalset heeft een model met schaal sets waarmee de *gewenste* status van de schaalset als geheel wordt vastgelegd. Als u een query wilt uitvoeren voor het model voor een schaalset, kunt u de gebruiken 

- REST API met [Compute/virtualmachinescalesets/Get](/rest/api/compute/virtualmachinescalesets/get) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI met [AZ vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- U kunt ook [resources.Azure.com](https://resources.azure.com) of de taalspecifieke [Azure-sdk's](https://azure.microsoft.com/downloads/)gebruiken.

De exacte presentatie van de uitvoer is afhankelijk van de opties die u voor de opdracht opgeeft. In het volgende voor beeld wordt de gecomprimeerde voorbeeld uitvoer van de Azure CLI weer gegeven:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Deze eigenschappen zijn van toepassing op de schaalset als geheel.


### <a name="the-scale-set-instance-view"></a>De instantie weergave van de schaalset
Een schaalset heeft ook de weer gave ' Scale set instance ', waarmee de huidige *runtime* status van de schaalset als geheel wordt vastgelegd. Als u een query wilt uitvoeren naar de instantie weergave voor een schaalset, kunt u het volgende gebruiken:

- REST API met [Compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI met [AZ vmss Get-instance-View](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- U kunt ook [resources.Azure.com](https://resources.azure.com) of de taalspecifieke [Azure sdk's](https://azure.microsoft.com/downloads/) gebruiken

De exacte presentatie van de uitvoer is afhankelijk van de opties die u voor de opdracht opgeeft. In het volgende voor beeld wordt de gecomprimeerde voorbeeld uitvoer van de Azure CLI weer gegeven:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Deze eigenschappen bieden een samen vatting van de huidige runtime status van de Vm's in de schaalset, zoals de status van uitbrei dingen die zijn toegepast op de schaalset.


### <a name="the-scale-set-vm-model-view"></a>De VM-model weergave van de schaalset
Net als bij de manier waarop een schaalset een model weergave heeft, heeft elk VM-exemplaar in de schaalset een eigen model weergave. Als u een query wilt uitvoeren naar de model weergave voor een bepaald VM-exemplaar in een schaalset, kunt u het volgende gebruiken:

- REST API met [Compute/virtualmachinescalesetvms/Get](/rest/api/compute/virtualmachinescalesetvms/get) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI met [AZ vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- U kunt ook [resources.Azure.com](https://resources.azure.com) of de [Azure sdk's](https://azure.microsoft.com/downloads/)gebruiken.

De exacte presentatie van de uitvoer is afhankelijk van de opties die u voor de opdracht opgeeft. In het volgende voor beeld wordt de gecomprimeerde voorbeeld uitvoer van de Azure CLI weer gegeven:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Deze eigenschappen beschrijven de configuratie van een VM-exemplaar binnen een schaalset, niet de configuratie van de schaalset als geheel. Het model Scale set heeft bijvoorbeeld `overprovision` een eigenschap, terwijl het model voor een VM-exemplaar binnen een schaalset niet. Dit verschil is omdat het overinrichten een eigenschap is van de volledige schaalset, niet de afzonderlijke VM-exemplaren in de schaalset (Zie [ontwerp overwegingen voor schaal sets](virtual-machine-scale-sets-design-overview.md#overprovisioning)) voor meer informatie over overinrichting.


### <a name="the-scale-set-vm-instance-view"></a>De weer gave van de VM-instantie voor schaal sets
Net als bij het weer geven van een exemplaar van een schaalset heeft elk VM-exemplaar in de schaalset een eigen exemplaar weergave. Als u de instantie weergave wilt doorzoeken voor een bepaald VM-exemplaar binnen een schaalset, kunt u het volgende gebruiken:

- REST API met [Compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) als volgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell met [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI met [AZ vmss Get-instance-View](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- U kunt ook [resources.Azure.com](https://resources.azure.com) of de [Azure sdk's](https://azure.microsoft.com/downloads/) gebruiken

De exacte presentatie van de uitvoer is afhankelijk van de opties die u voor de opdracht opgeeft. In het volgende voor beeld wordt de gecomprimeerde voorbeeld uitvoer van de Azure CLI weer gegeven:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Met deze eigenschappen wordt de huidige runtime status van een VM-exemplaar in een schaalset beschreven, inclusief eventuele uitbrei dingen die worden toegepast op de schaalset.


## <a name="how-to-update-global-scale-set-properties"></a>Eigenschappen van globale schaal sets bijwerken
Als u een eigenschap van een globale schaalset wilt bijwerken, moet u de eigenschap in het model voor schaal sets bijwerken. U kunt deze update uitvoeren via:

- REST API met [Compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) als volgt:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- U kunt een resource manager-sjabloon met de eigenschappen van de REST API implementeren om eigenschappen van globale schaal sets bij te werken.

- Azure PowerShell met [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI met [AZ vmss update](/cli/azure/vmss):
    - Een eigenschap wijzigen:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Een object toevoegen aan een lijst eigenschap in een schaalset: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Een object verwijderen uit een lijst eigenschap in een schaalset: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Als u de schaalset eerder met de opdracht hebt geïmplementeerd `az vmss create` , kunt u de `az vmss create` opdracht opnieuw uitvoeren om de schaalset bij te werken. Zorg ervoor dat alle eigenschappen in de `az vmss create` opdracht hetzelfde zijn als voorheen, met uitzonde ring van de eigenschappen die u wilt wijzigen.

- U kunt ook [resources.Azure.com](https://resources.azure.com) of de [Azure sdk's](https://azure.microsoft.com/downloads/)gebruiken.

Zodra het model voor de schaalset is bijgewerkt, is de nieuwe configuratie van toepassing op alle nieuwe Vm's die in de schaalset worden gemaakt. De modellen voor de bestaande virtuele machines in de schaalset moeten echter wel up-to-date zijn met het meest recente model van de schaalset. In het model voor elke VM is een Booleaanse eigenschap met `latestModelApplied` de naam die aangeeft of de virtuele machine up-to-date is met het laatste model van de schaalset ( `true` Dit betekent dat de virtuele machine up-to-date is met het meest recente model).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Vm's up-to-date zetten met het nieuwste model voor schaal sets
Schaal sets hebben een ' upgrade beleid ' dat bepaalt hoe Vm's up-to-date worden gebracht met het nieuwste model voor schaal sets. De drie modi voor het upgrade beleid zijn:

- **Automatische** -in deze modus biedt de schaalset geen garanties over de volg orde van de vm's die worden uitgevoerd. De schaalset kan alle Vm's op hetzelfde moment uitvoeren. 
- **Rolling** In deze modus wordt de update in batches met een optionele onderbrekings tijd tussen batches getotaliseerd.
- **Hand matig** : in deze modus, wanneer u het model voor de schaalset bijwerkt, gebeurt er niets met bestaande vm's.
 
Als u bestaande Vm's wilt bijwerken, moet u een hand matige upgrade uitvoeren van elke bestaande VM. U kunt deze hand matige upgrade uitvoeren met:

- REST API met [Compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) als volgt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell met [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI met [AZ vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- U kunt ook de taalspecifieke [Azure sdk's](https://azure.microsoft.com/downloads/)gebruiken.

>[!NOTE]
> Service Fabric-clusters kunnen alleen gebruikmaken van de *automatische* modus, maar de update wordt anders afgehandeld. Zie [service Fabric Application upgrades](../service-fabric/service-fabric-application-upgrade.md)(Engelstalig) voor meer informatie.

Er is één type wijziging in eigenschappen van globale schaal sets die het upgrade beleid niet volgen. Wijzigingen in het besturings systeem voor de schaalset en het profiel van de gegevens schijf (zoals beheerders naam en-wacht woord) kunnen alleen worden gewijzigd in API-versie *2017-12-01* of hoger. Deze wijzigingen zijn alleen van toepassing op virtuele machines die zijn gemaakt na de wijziging in het model voor schaal sets. Als u bestaande Vm's up-to-date wilt maken, moet u een installatie kopie van elke bestaande VM uitvoeren. U kunt deze installatie kopie uitvoeren via:

- REST API met [behulp van Compute/virtualmachinescalesets/Reimage](/rest/api/compute/virtualmachinescalesets/reimage) als volgt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell met [set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI met [AZ vmss Reimage](/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- U kunt ook de taalspecifieke [Azure sdk's](https://azure.microsoft.com/downloads/)gebruiken.


## <a name="properties-with-restrictions-on-modification"></a>Eigenschappen met beperkingen voor wijziging

### <a name="create-time-properties"></a>Create-Time-eigenschappen
Sommige eigenschappen kunnen alleen worden ingesteld wanneer u de schaalset maakt. Deze eigenschappen zijn onder andere:

- Beschikbaarheidszones
- Installatie kopie verwijzing Publisher
- Aanbieding voor afbeeldings referenties
- Type opslag account voor beheerde besturingssysteem schijf

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Eigenschappen die alleen kunnen worden gewijzigd op basis van de huidige waarde
Sommige eigenschappen kunnen worden gewijzigd, met uitzonde ring van de huidige waarde. Deze eigenschappen zijn onder andere:

- **singlePlacementGroup** -als singlePlacementGroup is ingesteld op True, kan dit worden gewijzigd in false. Als singlePlacementGroup echter False is, kan het **niet** worden gewijzigd in True.
- **subnet** -het subnet van een schaalset kan worden gewijzigd zolang het oorspronkelijke subnet en het nieuwe subnet zich in hetzelfde virtuele netwerk bevinden.

### <a name="properties-that-require-deallocation-to-change"></a>Eigenschappen waarvan de toewijzing moet worden gewijzigd
Sommige eigenschappen kunnen alleen worden gewijzigd in bepaalde waarden als de toewijzing van de virtuele machines in de schaalset ongedaan wordt gemaakt. Deze eigenschappen zijn onder andere:

- **SKU-naam**: als de nieuwe VM-SKU niet wordt ondersteund op de hardware waarop de schaalset momenteel is ingeschakeld, moet u de toewijzing van de vm's in de schaalset ongedaan maken voordat u de naam van de SKU wijzigt. Zie [het formaat van een Azure VM wijzigen](../virtual-machines/windows/resize-vm.md)voor meer informatie.


## <a name="vm-specific-updates"></a>VM-specifieke updates
Bepaalde wijzigingen kunnen worden toegepast op specifieke Vm's in plaats van de eigenschappen van de globale schaalset. Op dit moment is de enige specifieke VM-update die wordt ondersteund, om gegevens schijven te koppelen/loskoppelen van Vm's in de schaalset. Deze functie is beschikbaar als preview-versie. Zie de [Preview-documentatie](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk)voor meer informatie.


## <a name="scenarios"></a>Scenario's

### <a name="application-updates"></a>Toepassings updates
Als een toepassing wordt geïmplementeerd naar een schaalset via uitbrei dingen, zorgt een update voor de configuratie van de extensie ervoor dat de toepassing wordt bijgewerkt volgens het upgrade beleid. Als u bijvoorbeeld een nieuwe versie van een script hebt dat in een aangepaste script extensie moet worden uitgevoerd, kunt u de eigenschap *fileUris* bijwerken om naar het nieuwe script te verwijzen. In sommige gevallen wilt u mogelijk een update forceren, ook al is de configuratie van de extensie ongewijzigd (bijvoorbeeld omdat u het script hebt bijgewerkt zonder een wijziging in de URI van het script). In dergelijke gevallen kunt u de *updatetag* wijzigen om een update af te dwingen. Het Azure-platform interpreteert deze eigenschap niet. Als u de waarde wijzigt, heeft dit geen effect op de uitvoering van de uitbrei ding. Door een wijziging wordt de uitbrei ding gewoon geforceerd opnieuw uitgevoerd. Zie de [rest API-documentatie voor uitbrei dingen](/rest/api/compute/virtualmachineextensions/createorupdate)voor meer informatie over de *updatetag*. Houd er rekening mee dat de *updatetag* kan worden gebruikt met alle uitbrei dingen, niet alleen de aangepaste script extensie.

Het is ook gebruikelijk dat toepassingen worden geïmplementeerd via een aangepaste installatie kopie. Dit scenario wordt behandeld in de volgende sectie.

### <a name="os-updates"></a>Updates van het besturings systeem
Als u installatie kopieën van het Azure-platform gebruikt, kunt u de installatie kopie bijwerken door de *imageReference* te wijzigen (zie de [rest API-documentatie](/rest/api/compute/virtualmachinescalesets/createorupdate)) voor meer informatie.

>[!NOTE]
> Bij platform installatie kopieën is het gebruikelijk om "meest recente" op te geven voor de versie van de installatie kopie. Wanneer u een installatie kopie maakt, uitschaalt en opnieuw bewaart, worden virtuele machines gemaakt met de meest recente beschik bare versie. Het betekent echter **niet** dat de installatie kopie van het besturings systeem na verloop van tijd automatisch wordt bijgewerkt wanneer er nieuwe versies van de installatie kopieën worden uitgebracht. Een afzonderlijke functie is momenteel beschikbaar als preview-versie die automatische upgrades van het besturings systeem biedt. Zie de [documentatie voor automatische besturingssysteem upgrades](virtual-machine-scale-sets-automatic-upgrade.md)voor meer informatie.

Als u aangepaste installatie kopieën gebruikt, kunt u de installatie kopie bijwerken door de *imageReference* -id bij te werken (zie de [rest API-documentatie](/rest/api/compute/virtualmachinescalesets/createorupdate)) voor meer informatie.

## <a name="examples"></a>Voorbeelden

### <a name="update-the-os-image-for-your-scale-set"></a>De installatie kopie van het besturings systeem voor uw schaalset bijwerken
Mogelijk hebt u een schaalset waarop een oude versie van Ubuntu LTS 16,04 wordt uitgevoerd. U wilt bijwerken naar een nieuwere versie van Ubuntu LTS 16,04, zoals versie *16.04.201801090*. De eigenschap versie van de afbeeldings verwijzing maakt geen deel uit van een lijst, dus u kunt deze eigenschappen rechtstreeks wijzigen met een van de volgende opdrachten:

- Azure PowerShell met [Update-AzVmss](/powershell/module/az.compute/update-azvmss) als volgt:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI met [AZ vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

U kunt ook de installatie kopie wijzigen die door uw schaalset wordt gebruikt. Het is bijvoorbeeld mogelijk dat u een aangepaste installatie kopie wilt bijwerken of wijzigen die wordt gebruikt door uw schaalset. U kunt de installatie kopie die uw schaalset gebruikt wijzigen door de eigenschap voor de verwijzings-ID van de afbeelding bij te werken. De eigenschap voor de verwijzings-ID van de afbeelding maakt geen deel uit van een lijst. u kunt deze eigenschap direct wijzigen met een van de volgende opdrachten:

- Azure PowerShell met [Update-AzVmss](/powershell/module/az.compute/update-azvmss) als volgt:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI met [AZ vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>De load balancer voor uw schaalset bijwerken
Stel dat u een schaalset hebt met een Azure Load Balancer en u de Azure Load Balancer wilt vervangen door een Azure-toepassing gateway. De eigenschappen load balancer en Application Gateway voor een schaalset maken deel uit van een lijst, zodat u de opdrachten kunt gebruiken om lijst elementen te verwijderen of toe te voegen in plaats van de eigenschappen rechtstreeks te wijzigen:

- Azure Power shell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Bij deze opdrachten wordt ervan uitgegaan dat er slechts één IP-configuratie is en load balancer op de schaalset. Als er meerdere zijn, moet u mogelijk een andere lijst index dan *0* gebruiken.


## <a name="next-steps"></a>Volgende stappen
U kunt ook algemene beheer taken uitvoeren op schaal sets met de [Azure cli](virtual-machine-scale-sets-manage-cli.md) of [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
