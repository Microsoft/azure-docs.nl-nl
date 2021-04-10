---
title: Knooppunten toevoegen en verwijderen in een beheerde Service Fabric-cluster (preview)
description: In deze zelfstudie leert u hoe u een knooppunttype van een beheerde Service Fabric-cluster toevoegt en verwijdert.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: bb33512652677fc4e46d8ba3668dca985bbcfe01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791237"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Zelfstudie: Knooppunten toevoegen en verwijderen vanuit een beheerde Service Fabric-cluster (preview)

In deze reeks zelfstudies wordt het volgende besproken:

> [!div class="checklist"]
> * [Een beheerde Service Fabric-cluster implementeren](tutorial-managed-cluster-deploy.md)
> * [Het uitbreiden van een beheerde Service Fabric-cluster](tutorial-managed-cluster-scale.md)
> * Het toevoegen en verwijderen van knooppunten in een beheerde Service Fabric-cluster
> * [Een toepassing implementeren op een beheerde Service Fabric-cluster](tutorial-managed-cluster-deploy-app.md)

In dit deel van de reeks wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Een knooppunttype toevoegen aan een beheerde Service Fabric-cluster
> * Een knooppunttype verwijderen uit een beheerde Service Fabric-cluster

## <a name="prerequisites"></a>Vereisten

* Een beheerd Service Fabric-cluster (zie [*Een beheerd cluster implementeren*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) of hoger (zie [*Azure PowerShell installeren*](/powershell/azure/install-az-ps)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Een knooppunttype toevoegen aan een beheerde Service Fabric-cluster

U kunt een knooppunttype toevoegen aan een beheerde Service Fabric-cluster via een Azure Resource Manager-sjabloon, Power shell of CLI. In deze zelfstudie wordt een knooppunttype toegevoegd met behulp van de Azure PowerShell.

Als u een nieuw knooppunttype wilt maken, moet u drie eigenschappen definiëren:

* **Naam van knooppunttype**: Naam die uniek is voor bestaande knooppunttypen in de cluster.
* **Aantal exemplaren**: Het aanvankelijke aantal knooppunten van het nieuwe knooppunttype.
* **VM-grootte**: VM-SKU voor de knooppunten. Als dit niet is opgegeven, wordt de standaardwaarde *Standard_D2* gebruikt.

> [!NOTE]
> Als het knooppunttype dat wordt toegevoegd, het eerste of enige knooppunttype in het cluster is, moet de primaire eigenschap worden gebruikt.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Een knooppunttype verwijderen uit een beheerde Service Fabric-cluster

Om een knooppunttype te verwijderen uit een beheerde Service Fabric-cluster, moet u PowerShell of CLI gebruiken. In deze zelfstudie wordt een knooppunttype verwijderd met behulp van Azure PowerShell.

> [!NOTE]
> Het is niet mogelijk om een primair knooppunttype te verwijderen als het het enige knooppunttype in de cluster is.  

Een knooppunttype verwijderen:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>Volgende stappen

 In deze sectie hebben we knooppunttypen toegevoegd en verwijderd. Om een toepassing te leren implementeren op een beheerde Service Fabric-cluster, zie:

> [!div class="nextstepaction"]
> [Een app implementeren naar een beheerde Service Fabric-cluster](tutorial-managed-cluster-deploy-app.md)
