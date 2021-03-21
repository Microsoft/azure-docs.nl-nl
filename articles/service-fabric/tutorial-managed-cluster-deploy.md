---
title: Een beheerd Service Fabric-cluster (preview) implementeren
description: In deze zelfstudie implementeert u een beheerd Service Fabric-cluster om te testen.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: fd54e6f8c5272c3a8fe0e0162ca2e57968b24642
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791708"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Zelfstudie: Een beheerd Service Fabric-cluster (preview) implementeren

In deze serie zelfstudies wordt het volgende besproken:

> [!div class="checklist"]
> * Het implementeren van een beheerd Service Fabric-cluster 
> * [Het uitbreiden van een beheerd Service Fabric-cluster](tutorial-managed-cluster-scale.md)
> * [Het toevoegen en verwijderen van knooppunten in een beheerd Service Fabric-cluster](tutorial-managed-cluster-add-remove-node-type.md)
> * [Het implementeren van toepassing op een beheerd Service Fabric-cluster](tutorial-managed-cluster-deploy-app.md)

In dit deel van de serie wordt het volgende uitgelegd:

> [!div class="checklist"]
> * Verbinding maken met uw Azure-account
> * Een nieuwe resourcegroep maken
> * Een beheerd Service Fabric-cluster implementeren
> * Een primair knooppunttype aan het cluster toevoegen

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Maak een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan als u nog geen abonnement op Azure hebt

* Installeer de [Service Fabric SDK en PowerShell-module](service-fabric-get-started.md).

* Installeer [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (of hoger).

## <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Vervang `<your-subscription>` door de tekenreeks van het abonnement voor uw Azure-account en maak verbinding:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Een nieuwe resourcegroep maken

Maak vervolgens de resourcegroep voor het beheerde Service Fabric-cluster, waarbij u `<your-rg>` en `<location>` vervangt door de gewenste groepsnaam en -locatie.

> [!NOTE]
> De ondersteunde regio's voor de openbare preview zijn onder andere `centraluseuap`, `eastus2euap`, `eastasia`, `northeurope`, `westcentralus` en `eastus2`.

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Een beheerd Service Fabric-cluster implementeren

### <a name="create-a-service-fabric-managed-cluster"></a>Een beheerd Service Fabric-cluster maken

In deze stap maakt u een beheerd-Service Fabric-cluster met behulp van de PowerShell-opdracht New-AzServiceFabricManagedCluster. In het volgende voorbeeld wordt een cluster met de naam myCluster gemaakt in de resourcegroep met de naam myResourceGroup. Deze resourcegroep is gemaakt in de vorige stap in de regio eastus2.

Geef voor deze stap uw eigen waarden op voor de volgende parameters:

* **Clusternaam**: Voer een unieke naam in voor uw cluster, zoals *mysfcluster*.
* **Wachtwoord voor beheerder**: Voer een wachtwoord in voor de beheerder dat moet worden gebruikt voor RDP op de onderliggende virtuele machines in het cluster.
* **Vingerafdruk van het clientcertificaat**: Geef de vingerafdruk van het clientcertificaat op die u wilt gebruiken om toegang te krijgen tot uw cluster. Als u geen certificaat hebt, volgt u [een certificaat instellen en ophalen](../key-vault/certificates/quick-create-portal.md) om een zelfondertekend certificaat te maken.
* **Cluster-SKU**: Geef het [type beheerd Service Fabric-cluster](overview-managed-cluster.md#service-fabric-managed-cluster-skus) op dat moet worden geïmplementeerd. *Basis* SKU-clusters zijn alleen bedoeld voor testimplementaties en zijn niet toegestaan voor het toevoegen of verwijderen van knooppunttypen.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Een primair knooppunttype toevoegen aan het beheerde Service Fabric-cluster

In deze stap voegt u een primair knooppunttype toe aan het cluster dat u zojuist hebt gemaakt. Elk Service Fabric-cluster moet ten minste één primair knooppunttype hebben.

Geef voor deze stap uw eigen waarden op voor de volgende parameters:

* **Naam van knooppunttype**: Voer een unieke naam in voor het knooppunttype dat aan uw cluster moet worden toegevoegd, zoals 'NT1'.

> [!NOTE]
> Als het knooppunttype dat wordt toegevoegd, het eerste of enige knooppunttype in het cluster is, moet de primaire eigenschap worden gebruikt.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Het volledig uitvoeren van de opdracht kan even duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

### <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Zodra de implementatie is voltooid, gaat u naar de Service Fabric Explorer-waarde op de pagina met een overzicht van de resources van het beheerde Service Fabric-cluster in Portal. Wanneer u wordt gevraagd om een certificaat, gebruikt u het certificaat waarvoor de clientvingerafdruk is opgenomen in de PowerShell-opdracht.

## <a name="next-steps"></a>Volgende stappen

In deze stap hebben we onze eerste beheerde Service Fabric-cluster gemaakt en geïmplementeerd. Zie voor meer informatie over het schalen van een cluster:

> [!div class="nextstepaction"]
> [Uitbreiden van een beheerd Service Fabric-cluster](tutorial-managed-cluster-scale.md)
