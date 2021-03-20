---
title: Een extensie voor virtuele-machine schaal sets toevoegen aan een Service Fabric beheerd cluster knooppunt type (preview)
description: U gaat als volgt te werk om een extensie van een virtuele-machine schaalset toe te voegen een Service Fabric beheerd cluster knooppunt type
ms.topic: article
ms.date: 09/28/2020
ms.openlocfilehash: a47908b511f79c18482e9d21e623f1cb4dc70ed1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737763"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type-preview"></a>Een extensie voor virtuele-machine schaal sets toevoegen aan een Service Fabric beheerd cluster knooppunt type (preview)

Elk knooppunt type in een door Service Fabric beheerd cluster wordt ondersteund door een schaalset voor virtuele machines. Hiermee kunt u [uitbrei dingen voor virtuele-machine schaal sets](../virtual-machines/extensions/overview.md) toevoegen aan uw service Fabric beheerde cluster knooppunt typen.

Met de Power shell [-opdracht add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) kunt u een extensie voor virtuele-machine schaal sets toevoegen aan een knooppunt type.

U kunt ook een virtuele-machine Scale set-extensie hebben op een Service Fabric beheerd cluster knooppunt type in uw Azure Resource Manager sjabloon, bijvoorbeeld:

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Zie voor meer informatie over het configureren van Service Fabric beheerde cluster knooppunt typen het [knoop punt type beheerd cluster](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over beheerde Service Fabric-clusters, zie:

> [!div class="nextstepaction"]
> [Veelgesteld vragen over beheerde Service Fabric-clusters](./faq-managed-cluster.md)
