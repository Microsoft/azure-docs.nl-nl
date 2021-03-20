---
title: Een beschikbaarheids groep configureren (Power shell & AZ CLI)
description: Gebruik Power shell of de Azure CLI om het Windows-failovercluster, de beschikbaarheids groep-listener en de interne load balancer op een SQL Server virtuele machine in azure te maken.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 865ee3a5aeb8a2dd06d8759ba04d02259d2b4bee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359962"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Power shell of AZ CLI gebruiken voor het configureren van een beschikbaarheids groep voor SQL Server op Azure VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u [Power shell](/powershell/scripting/install/installing-powershell) of de [Azure cli](/cli/azure/sql/vm) gebruikt om een Windows-failovercluster te implementeren, SQL Server vm's toe te voegen aan het cluster en de interne Load Balancer en listener te maken voor een always on-beschikbaarheids groep. 

De implementatie van de beschikbaarheids groep wordt nog steeds hand matig uitgevoerd via SQL Server Management Studio (SSMS) of Transact-SQL (T-SQL). 

Hoewel in dit artikel Power shell en de AZ CLI worden gebruikt voor het configureren van de omgeving voor de beschikbaarheids groep, is het ook mogelijk om dit te doen vanuit de [Azure Portal](availability-group-azure-portal-configure.md), met behulp van [Azure Quick](availability-group-quickstart-template-configure.md)start-sjablonen of ook [hand matig](availability-group-manually-configure-tutorial.md) . 

## <a name="prerequisites"></a>Vereisten

Als u een always on-beschikbaarheids groep wilt configureren, moet u over de volgende vereisten beschikken: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een resource groep met een domein controller. 
- Een of meer virtuele machines die zijn gekoppeld aan een domein [in azure met SQL Server 2016 (of hoger) Enter prise Edition](./create-sql-vm-portal.md) in *dezelfde* beschikbaarheidsset of *verschillende* beschikbaarheids zones die zijn [geregistreerd bij de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md).  
- De nieuwste versie van [Power shell](/powershell/scripting/install/installing-powershell) of de [Azure cli](/cli/azure/install-azure-cli). 
- Twee beschik bare (niet gebruikt door een entiteit) IP-adressen. Een voor de interne load balancer. De andere is voor de beschikbaarheids groep-listener binnen hetzelfde subnet als de beschikbaarheids groep. Als u een bestaande load balancer gebruikt, hebt u slechts één beschikbaar IP-adres nodig voor de beschikbaarheids groep-listener. 

## <a name="permissions"></a>Machtigingen

U hebt de volgende account machtigingen nodig voor het configureren van de AlwaysOn-beschikbaarheids groep met behulp van de Azure CLI: 

- Een bestaand domein gebruikers account met een machtiging voor het **maken van computer objecten** in het domein. Een domein beheerders account heeft bijvoorbeeld doorgaans voldoende machtigingen (bijvoorbeeld: account@domain.com ). _Dit account moet ook lid zijn van de lokale groep Administrators op elke virtuele machine om het cluster te maken._
- Het domein gebruikers account dat SQL Server beheert. 
 
## <a name="create-a-storage-account"></a>Een opslagaccount maken 

Het cluster heeft een opslag account nodig om te fungeren als de cloudwitness. U kunt elk bestaand opslag account gebruiken of u kunt een nieuw opslag account maken. Als u een bestaand opslag account wilt gebruiken, gaat u verder met de volgende sectie. 

Met het volgende code fragment wordt het opslag account gemaakt: 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Mogelijk wordt de fout weer geven `az sql: 'vm' is not in the 'az sql' command group` Als u een verouderde versie van de Azure cli gebruikt. Down load de [nieuwste versie van Azure cli](/cli/azure/install-azure-cli-windows) om deze fout te verhelpen.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Meta gegevens van het cluster definiëren

De opdracht groep Azure CLI [AZ SQL VM Group](/cli/azure/sql/vm/group) beheert de meta gegevens van de WSFC-service (Windows Server failover cluster) die als host fungeert voor de beschikbaarheids groep. De meta gegevens van het cluster omvatten de Active Directory domein, cluster accounts, opslag accounts die moeten worden gebruikt als de cloudwitness en SQL Server versie. Gebruik [AZ SQL VM Group Create](/cli/azure/sql/vm/group#az-sql-vm-group-create) om de meta gegevens voor WSFC te definiëren, zodat wanneer de eerste SQL Server virtuele machine wordt toegevoegd, het cluster wordt gemaakt zoals gedefinieerd. 

Het volgende code fragment definieert de meta gegevens voor het cluster:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Vm's toevoegen aan het cluster

Als u de eerste SQL Server VM toevoegt aan het cluster, wordt het cluster gemaakt. Met de opdracht [AZ SQL VM add-to-Group](/cli/azure/sql/vm#az-sql-vm-add-to-group) maakt u het cluster met de naam die u eerder hebt opgegeven, installeert u de cluster functie op de SQL Server-vm's en voegt u deze toe aan het cluster. Met het volgende gebruik van de `az sql vm add-to-group` opdracht voegt u meer SQL Server vm's toe aan het zojuist gemaakte cluster. 

Met het volgende code fragment wordt het cluster gemaakt en wordt de eerste SQL Server VM hieraan toegevoegd: 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Gebruik deze opdracht om andere SQL Server Vm's toe te voegen aan het cluster. Wijzig alleen de `-n` para meter voor de naam van de SQL Server-VM. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Cluster valideren 

Voor een failovercluster dat door micro soft wordt ondersteund, moet de cluster validatie worden door gegeven. Maak verbinding met de virtuele machine met behulp van uw voorkeurs methode, zoals Remote Desktop Protocol (RDP) en valideer of uw cluster valideert voordat u verder gaat. Als u dit niet doet, wordt de status van het cluster niet ondersteund. 

U kunt het cluster valideren met behulp van Failoverclusterbeheer (FCM) of de volgende Power shell-opdracht:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Beschikbaarheids groep maken

Maak de beschikbaarheids groep hand matig zoals u dat gewoonlijk zou doen met behulp van [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [Power shell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)of [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Maak op dit moment *geen* listener, omdat dit wordt gedaan via de Azure cli in de volgende secties.  

## <a name="create-internal-load-balancer"></a>Interne load balancer maken

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Voor de always on-beschikbaarheids groep-listener is een intern exemplaar van Azure Load Balancer vereist. De interne load balancer biedt een ' zwevend ' IP-adres voor de beschikbaarheids groep-listener die snellere failover en opnieuw verbinden mogelijk maakt. Als de SQL Server Vm's in een beschikbaarheids groep deel uitmaken van dezelfde beschikbaarheidsset, kunt u een basis load balancer gebruiken. Anders moet u een standaard load balancer gebruiken.  

> [!NOTE]
> De interne load balancer moeten zich in hetzelfde virtuele netwerk bevindt als de VM-exemplaren van SQL Server. 

Met het volgende code fragment maakt u de interne load balancer:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> De open bare IP-resource voor elke SQL Server virtuele machine moet een standaard-SKU hebben om compatibel te zijn met de standaard load balancer. Als u de SKU van de open bare IP-resource van de virtuele machine wilt bepalen, gaat u naar de **resource groep**, selecteert u de resource voor het **open bare IP-adres** voor de gewenste SQL Server VM en zoekt u de waarde onder **SKU** in het deel venster **overzicht** .  

## <a name="create-listener"></a>Listener maken

Nadat u de beschikbaarheids groep hand matig hebt gemaakt, kunt u de listener maken met behulp van [AZ SQL VM AG-listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create). 

De *resource-id* van het subnet is de waarde van `/subnets/<subnetname>` toegevoegd aan de resource-id van de virtuele netwerk resource. De resource-ID van het subnet identificeren:
   1. Ga naar de resource groep in de [Azure Portal](https://portal.azure.com). 
   1. Selecteer de bron van het virtuele netwerk. 
   1. Selecteer **Eigenschappen** in het deel venster **instellingen** . 
   1. Identificeer de bron-ID voor het virtuele netwerk en voeg deze toe aan `/subnets/<subnetname>` het einde om de resource-id van het subnet te maken. Bijvoorbeeld:
      - De resource-ID van uw virtuele netwerk is: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - De naam van het subnet is: `default`
      - De ID van uw subnet-bron is daarom: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Met het volgende code fragment wordt de beschikbaarheids groep-listener gemaakt:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Aantal replica's wijzigen 
Er is een toegevoegde laag complexiteit wanneer u een beschikbaarheids groep implementeert voor SQL Server Vm's die worden gehost in Azure. De resource provider en de virtuele-machine groep beheren nu de resources. Als u replica's toevoegt aan of verwijdert uit de beschikbaarheids groep, is er een extra stap voor het bijwerken van de meta gegevens van de listener met informatie over de SQL Server Vm's. Wanneer u het aantal replica's in de beschikbaarheids groep wijzigt, moet u ook de opdracht [AZ SQL VM Group AG-listener update](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) gebruiken om de listener bij te werken met de meta gegevens van de SQL Server vm's. 


### <a name="add-a-replica"></a>Een replica toevoegen

Een nieuwe replica toevoegen aan de beschikbaarheids groep:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voeg de SQL Server virtuele machine toe aan de cluster groep:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Gebruik SQL Server Management Studio om het SQL Server exemplaar toe te voegen als een replica in de beschikbaarheids groep.
1. Voeg de SQL Server VM-meta gegevens toe aan de listener:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Voeg de SQL Server virtuele machine toe aan de cluster groep:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Gebruik SQL Server Management Studio om het SQL Server exemplaar toe te voegen als een replica in de beschikbaarheids groep.
1. Voeg de SQL Server VM-meta gegevens toe aan de listener:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Een replica verwijderen

Een replica verwijderen uit de beschikbaarheids groep:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Verwijder de replica uit de beschikbaarheids groep met behulp van SQL Server Management Studio. 
1. De meta gegevens van de SQL Server virtuele machine uit de listener verwijderen:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Verwijder de SQL Server-VM uit het cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verwijder de replica uit de beschikbaarheids groep met behulp van SQL Server Management Studio. 
1. De meta gegevens van de SQL Server virtuele machine uit de listener verwijderen:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Verwijder de SQL Server-VM uit het cluster:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Listener verwijderen
Als u de beschikbaarheids groep die u later hebt geconfigureerd met de Azure CLI wilt verwijderen, moet u de SQL IaaS agent-extensie door lopen. Omdat de listener is geregistreerd via de SQL IaaS agent-extensie, hoeft u deze niet te verwijderen via SQL Server Management Studio onvoldoende. 

U kunt de methode het beste verwijderen via de SQL IaaS agent-extensie met behulp van het volgende code fragment in de Azure CLI. Hiermee verwijdert u de meta gegevens van de beschikbaarheids groep van de SQL IaaS agent-extensie. Ook wordt de listener fysiek uit de beschikbaarheids groep verwijderd. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Cluster verwijderen

Verwijder alle knoop punten uit het cluster om deze te vernietigen en verwijder vervolgens de meta gegevens van het cluster uit de SQL IaaS agent-extensie. U kunt dit doen met behulp van de Azure CLI of Power shell. 


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Verwijder eerst alle SQL Server-Vm's uit het cluster: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Als dit de enige Vm's in het cluster zijn, wordt het cluster vernietigd. Als er andere virtuele machines in het cluster zijn dan de SQL Server Vm's die zijn verwijderd, worden de andere Vm's niet verwijderd en wordt het cluster niet vernietigd. 

Verwijder vervolgens de meta gegevens van het cluster uit de uitbrei ding SQL IaaS agent: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwijder eerst alle SQL Server-Vm's uit het cluster. Hiermee verwijdert u de knoop punten van het cluster fysiek en vernietigt u het cluster: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Als dit de enige Vm's in het cluster zijn, wordt het cluster vernietigd. Als er andere virtuele machines in het cluster zijn dan de SQL Server Vm's die zijn verwijderd, worden de andere Vm's niet verwijderd en wordt het cluster niet vernietigd. 

Verwijder vervolgens de meta gegevens van het cluster uit de uitbrei ding SQL IaaS agent: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server Vm's](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server Vm's](frequently-asked-questions-faq.md)
* [Release opmerkingen voor SQL Server Vm's](../../database/doc-changes-updates-release-notes.md)
* [Licentie modellen voor een SQL Server VM overschakelen](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Overzicht van AlwaysOn-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuratie van een Server exemplaar voor Always on-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Beheer van een beschikbaarheids groep &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Bewaking van beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Overzicht van Transact-SQL-instructies voor AlwaysOn-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Overzicht van Power shell-cmdlets voor AlwaysOn-beschikbaarheids groepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)