---
title: Een beheerd Service Fabric-cluster uitbreiden (preview)
description: In deze zelfstudie leert u hoe u een knooppunttype van een beheer Service Fabric-cluster uitbreidt.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 769bcb339b2cc1419c7a3d92d0f08130029a9f95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785428"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Zelfstudie: Een beheerd Service Fabric-cluster uitbreiden (preview)

In deze serie zelfstudies wordt het volgende besproken:

> [!div class="checklist"]
> * [Een beheerd Service Fabric-cluster implementeren.](tutorial-managed-cluster-deploy.md)
> * Een beheerd Service Fabric-cluster uitbreiden
> * [Knooppunten toevoegen en verwijderen in een beheerd Service Fabric-cluster](tutorial-managed-cluster-add-remove-node-type.md)
> * [Een toepassing implementeren op een beheerd Service Fabric-cluster](tutorial-managed-cluster-deploy-app.md)

In dit deel van de serie wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Een knooppunt van een beheerd Service Fabric-cluster schalen

## <a name="prerequisites"></a>Vereisten

* Een beheerd Service Fabric-cluster (zie [*Een beheerd cluster implementeren*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) of hoger (zie [*Azure PowerShell installeren*](/powershell/azure/install-az-ps)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Een beheerd Service Fabric-cluster schalen
Wijzig het aantal instanties om het aantal knooppunten op het knooppunttype dat u wilt schalen, te verhogen of te verlagen. De namen van knooppunttypen vindt u in de Azure Resource Manager-sjabloon (ARM-sjabloon) van de clusterimplementatie of in de Service Fabric Explorer.  

> [!NOTE]
> Als het knooppunttype primair is, kunt u niet minder dan drie knooppunten voor een SKU-basiscluster en vijf knooppunten voor een SKU-standaardcluster gebruiken.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

Er wordt automatisch een upgrade uitgevoerd op het cluster en na een paar minuten worden de extra knooppunten weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze stap hebben we een knooppunttype geschaald in een beheerd Service Fabric-cluster. Voor meer informatie over het toevoegen en verwijderen van knooppunttypen raadpleegt u:

> [!div class="nextstepaction"]
> [Knooppunten toevoegen en verwijderen in een beheerd Service Fabric-cluster](tutorial-managed-cluster-add-remove-node-type.md)
