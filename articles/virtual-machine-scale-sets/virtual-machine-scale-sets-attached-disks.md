---
title: Gegevens schijven die zijn gekoppeld aan Azure Virtual Machine Scale Sets
description: Meer informatie over het gebruik van gekoppelde gegevens schijven met schaal sets voor virtuele machines via overzichten van specifieke use cases.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9e4bdf868d3f8ddf3a049509ead30a4b1ba341b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86527435"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Virtuele-machineschaalsets in Azure en gekoppelde gegevensschijven
Voor het uitbreiden van uw beschikbare opslag bieden [virtuele-machineschaalsets](./index.yml) in Azure ondersteuning voor VM-instanties met gekoppelde gegevensschijven. U kunt gegevensschijven koppelen wanneer de schaalset wordt gemaakt of aan een bestaande schaalset.

> [!NOTE]
> Wanneer u een schaalset maakt met gekoppelde gegevensschijven, dient u de schijven nog steeds te koppelen en formatteren van binnen een VM om ze te gebruiken (net als bij zelfstandige virtuele Azure-machines). Een handige manier om dit proces te voltooien, is om een aangepaste scriptextensie te gebruiken die een script oproept om alle gegevensschijven op de VM te partitioneren en formatteren. Zie [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks) voor voorbeelden hiervan.


## <a name="create-and-manage-disks-in-a-scale-set"></a>Schijven in een schaalset maken en beheren
Zie een van de volgende zelfstudies voor gedetailleerde informatie over het maken van een schaalset met gekoppelde gegevensschijven, het voorbereiden en formatteren of toevoegen en verwijderen van gegevensschijven:

- [Azure-CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

De rest van dit artikel bevat een overzicht van de specifieke gebruiksvoorbeelden zoals Service Fabric-clusters die gegevensschijven vereisen of het koppelen van bestaande gegevensschijven met inhoud aan een schaalset.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Een Service Fabric-cluster maken met gekoppelde gegevensschijven
Elk [knooppunttype](../service-fabric/service-fabric-cluster-nodetypes.md) in een [Service Fabric](../service-fabric/index.yml)-cluster dat wordt uitgevoerd in Azure, wordt ondersteund door een virtuele-machineschaalset. U kunt met behulp van een Azure Resource Manager-sjabloon gegevensschijven koppelen aan de schaalsets die gezamenlijk het Service Fabric-cluster vormen. U kunt een [bestaande sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates) als uitgangspunt nemen. Neem in de sjabloon een sectie _dataDisks_ op in het _storageProfile_ van de _Microsoft.Compute/virtualMachineScaleSets_-resource(s) en implementeer de sjabloon. In het volgende voorbeeld wordt een gegevensschijf van 128 GB gekoppeld:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

U kunt automatisch partitioneren, formatteren en de gegevensschijven koppelen wanneer het cluster is geïmplementeerd. Voeg een extensie voor aangepaste scripts toe aan het _extensionProfile_ van de _virtualMachineProfile_ van de schaalset(s).

Als u de gegevensschijven automatisch wilt voorbereiden in een Windows-cluster, voegt u het volgende toe:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Als u de gegevensschijven automatisch wilt voorbereiden in een Linux-cluster, voegt u het volgende toe:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Vooraf gevulde gegevensschijven toevoegen aan een bestaande schaalset
Gegevensschijven die zijn opgegeven in het schaalsetmodel, zijn altijd leeg. U kunt echter een bestaande gegevensschijf koppelen aan een specifieke virtuele machine in een schaalset. Als u gegevens wilt door geven over alle virtuele machines in de schaalset, kunt u de gegevens schijf dupliceren en koppelen aan elke virtuele machine in de schaalset, of een aangepaste installatie kopie maken die de gegevens bevat en de schaalset inricht vanuit deze aangepaste installatie kopie, of u kunt Azure Files of een soort gelijke gegevens opslag gebruiken.


## <a name="additional-notes"></a>Aanvullende opmerkingen
Er is ondersteuning voor Azure Managed Disks en aan schaalsets gekoppelde gegevensschijven beschikbaar in API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) of hoger van de Microsoft.Compute API.

Azure Portal ondersteuning voor gekoppelde gegevens schijven in schaal sets is beperkt. Afhankelijk van uw vereisten kunt u Azure-sjablonen, CLI, PowerShell, SDK's en REST API gebruiken voor het beheren van gekoppelde schijven.
