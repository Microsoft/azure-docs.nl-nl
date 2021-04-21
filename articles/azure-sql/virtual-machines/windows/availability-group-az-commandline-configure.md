---
title: Een beschikbaarheidsgroep configureren (PowerShell & Az CLI)
description: Gebruik PowerShell of de Azure CLI om het Windows-failovercluster, de listener voor de beschikbaarheidsgroep en de interne load balancer te maken op een virtuele SQL Server in Azure.
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
ms.openlocfilehash: ffd4ec6eff94589abbc8af70ecf9c0f7dc168962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766929"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>PowerShell of Az CLI gebruiken om een beschikbaarheidsgroep te configureren voor SQL Server azure-VM 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u [PowerShell](/powershell/scripting/install/installing-powershell) of [de Azure CLI](/cli/azure/sql/vm) gebruikt om een Windows-failovercluster te implementeren, SQL Server-VM's aan het cluster toe te voegen en de interne load balancer en listener voor een Always On-beschikbaarheidsgroep te maken. 

De implementatie van de beschikbaarheidsgroep wordt nog steeds handmatig uitgevoerd via SQL Server Management Studio (SSMS) of Transact-SQL (T-SQL). 

Hoewel in dit artikel PowerShell en de Az CLI worden gebruikt om de omgeving van de beschikbaarheidsgroep te [](availability-group-manually-configure-tutorial.md) configureren, is het ook mogelijk om dit te doen vanuit [de Azure Portal,](availability-group-azure-portal-configure.md)met behulp van [Azure-quickstartsjablonen](availability-group-quickstart-template-configure.md)of handmatig. 

## <a name="prerequisites"></a>Vereisten

Als u een Always On-beschikbaarheidsgroep wilt configureren, moet u aan de volgende vereisten hebben: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een resourcegroep met een domeincontroller. 
- Een of meer domein-VM's in Azure met [SQL Server 2016 (of hoger) Enterprise Edition](./create-sql-vm-portal.md) in dezelfde beschikbaarheidsset of in verschillende beschikbaarheidszones die zijn geregistreerd bij de SQL [IaaS Agent-extensie](sql-agent-extension-manually-register-single-vm.md).    
- De nieuwste versie van [PowerShell](/powershell/scripting/install/installing-powershell) of de [Azure CLI.](/cli/azure/install-azure-cli) 
- Twee beschikbare (niet gebruikt door een entiteit) IP-adressen. Een is voor de interne load balancer. De andere is voor de beschikbaarheidsgroeplistener binnen hetzelfde subnet als de beschikbaarheidsgroep. Als u een bestaande load balancer, hebt u slechts één beschikbaar IP-adres nodig voor de listener voor de beschikbaarheidsgroep. 

## <a name="permissions"></a>Machtigingen

U hebt de volgende accountmachtigingen nodig om de Always On-beschikbaarheidsgroep te configureren met behulp van de Azure CLI: 

- Een bestaand domeingebruikersaccount met de machtiging **Computerobject** maken in het domein. Zo heeft een domeinbeheerdersaccount doorgaans voldoende machtigingen (bijvoorbeeld: account@domain.com ). _Dit account moet ook deel uitmaken van de lokale beheerdersgroep op elke VM om het cluster te maken._
- Het domeingebruikersaccount dat de SQL Server. 
 
## <a name="create-a-storage-account"></a>Een opslagaccount maken 

Het cluster heeft een opslagaccount nodig om te fungeren als de cloud-witness. U kunt elk bestaand opslagaccount gebruiken of u kunt een nieuw opslagaccount maken. Als u een bestaand opslagaccount wilt gebruiken, gaat u verder met de volgende sectie. 

Met het volgende codefragment wordt het opslagaccount gemaakt: 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Mogelijk ziet u de fout als u een verouderde versie van `az sql: 'vm' is not in the 'az sql' command group` de Azure CLI gebruikt. Download de [nieuwste versie van Azure CLI om](/cli/azure/install-azure-cli-windows) deze fout te voorkomen.


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

## <a name="define-cluster-metadata"></a>Clustermetagegevens definiëren

De Azure [CLI-opdrachtgroep az sql vm group](/cli/azure/sql/vm/group) beheert de metagegevens van de WSFC-service (Windows Server Failover Cluster) die als host voor de beschikbaarheidsgroep wordt gebruikt. Clustermetagegevens omvatten het Active Directory-domein, clusteraccounts, opslagaccounts die moeten worden gebruikt als cloud-witness en SQL Server versie. Gebruik [az sql vm group create](/cli/azure/sql/vm/group#az_sql_vm_group_create) om de metagegevens voor WSFC te definiëren, zodat wanneer de eerste SQL Server VM wordt toegevoegd, het cluster wordt gemaakt zoals gedefinieerd. 

Het volgende codefragment definieert de metagegevens voor het cluster:

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

## <a name="add-vms-to-the-cluster"></a>VM's toevoegen aan het cluster

Als u de eerste SQL Server-VM aan het cluster toevoegt, wordt het cluster gemaakt. Met [de opdracht az sql vm add-to-group](/cli/azure/sql/vm#az_sql-vm_add_to_group) maakt u het cluster met de eerder opgegeven naam, installeert u de clusterrol op de SQL Server-VM's en voegt u deze toe aan het cluster. Navolgend gebruik van `az sql vm add-to-group` de opdracht voegt meer SQL Server VM's toe aan het zojuist gemaakte cluster. 

Met het volgende codefragment wordt het cluster gemaakt en wordt het eerste SQL Server VM toegevoegd: 

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
Gebruik deze opdracht om andere virtuele SQL Server toe te voegen aan het cluster. Wijzig alleen de `-n` parameter voor de SQL Server VM-naam. 

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

Een failovercluster wordt alleen ondersteund door Microsoft als het cluster wordt gevalideerd. Maak verbinding met de VM met behulp van de gewenste methode, zoals Remote Desktop Protocol (RDP) en controleer of uw cluster wordt gevalideerd voordat u verdergaat. Als u dit niet doet, wordt uw cluster niet ondersteund. 

U kunt het cluster valideren met Failoverclusterbeheer (FCM) of de volgende PowerShell-opdracht:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Beschikbaarheidsgroep maken

Maak de beschikbaarheidsgroep handmatig zoals u gewend bent, met behulp [van SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)of [Transact-SQL.](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) 

>[!IMPORTANT]
> Maak *op* dit moment geen listener, omdat dit wordt gedaan via de Azure CLI in de volgende secties.  

## <a name="create-internal-load-balancer"></a>Interne load balancer

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

De Always On-beschikbaarheidsgroeplistener vereist een intern exemplaar van Azure Load Balancer. De interne load balancer biedt een zwevend IP-adres voor de beschikbaarheidsgroeplistener die snellere failover en opnieuw verbinden mogelijk maakt. Als de SQL Server's in een beschikbaarheidsgroep deel uitmaken van dezelfde beschikbaarheidsset, kunt u een Basic-load balancer. Anders moet u een Standard-load balancer.  

> [!NOTE]
> De interne load balancer moeten zich in hetzelfde virtuele netwerk als de SQL Server VM-exemplaren. 

Met het volgende codefragment wordt de interne load balancer:

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
> De openbare IP-resource voor elke SQL Server VM moet een Standard-SKU hebben die compatibel is met de Standard-load balancer. Als u de SKU van de openbare IP-resource van uw VM wilt bepalen, gaat u naar **Resourcegroep,** selecteert u de resource openbaar **IP-adres** voor de gewenste SQL Server-VM en zoekt u de waarde onder **SKU** in het deelvenster **Overzicht.**  

## <a name="create-listener"></a>Listener maken

Nadat u de beschikbaarheidsgroep handmatig hebt gemaakt, kunt u de listener maken met [az sql vm ag-listener.](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create) 

De *resource-id van het subnet* is de waarde van `/subnets/<subnetname>` toegevoegd aan de resource-id van de virtuele netwerkresource. De resource-id van het subnet identificeren:
   1. Ga naar de resourcegroep in de [Azure Portal.](https://portal.azure.com) 
   1. Selecteer de resource van het virtuele netwerk. 
   1. Selecteer **Eigenschappen** in het **deelvenster** Instellingen. 
   1. Identificeer de resource-id voor het virtuele netwerk en plaats deze aan het einde om de resource-id van het `/subnets/<subnetname>` subnet te maken. Bijvoorbeeld:
      - De resource-id van uw virtuele netwerk is: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Uw subnetnaam is: `default`
      - Daarom is uw subnetresource-id: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Met het volgende codefragment wordt de listener voor de beschikbaarheidsgroep gemaakt:

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
Er is een extra complexiteitslaag wanneer u een beschikbaarheidsgroep implementeert op SQL Server virtuele azure-VM's. De resourceprovider en de virtuele-machinegroep beheren nu de resources. Als u replica's toevoegt aan of verwijdert uit de beschikbaarheidsgroep, is er dus een extra stap voor het bijwerken van de listenermetagegevens met informatie over de SQL Server virtuele SQL Server's. Wanneer u het aantal replica's in de beschikbaarheidsgroep wijzigt, moet u ook de opdracht [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) gebruiken om de listener bij te werken met de metagegevens van de SQL Server-VM's. 


### <a name="add-a-replica"></a>Een replica toevoegen

Een nieuwe replica toevoegen aan de beschikbaarheidsgroep:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voeg de SQL Server-VM toe aan de clustergroep:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Gebruik SQL Server Management Studio om het SQL Server toe te voegen als een replica binnen de beschikbaarheidsgroep.
1. Voeg de metagegevens SQL Server VM toe aan de listener:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Voeg de SQL Server-VM toe aan de clustergroep:

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

1. Gebruik SQL Server Management Studio om het SQL Server toe te voegen als een replica binnen de beschikbaarheidsgroep.
1. Voeg de metagegevens SQL Server virtuele VM toe aan de listener:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Een replica verwijderen

Een replica uit de beschikbaarheidsgroep verwijderen:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Verwijder de replica uit de beschikbaarheidsgroep met behulp van SQL Server Management Studio. 
1. Verwijder de metagegevens SQL Server virtuele VM uit de listener:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Verwijder de SQL Server VM uit het cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verwijder de replica uit de beschikbaarheidsgroep met behulp van SQL Server Management Studio. 
1. Verwijder de metagegevens SQL Server virtuele VM uit de listener:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Verwijder de SQL Server VM uit het cluster:

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
Als u de listener voor beschikbaarheidsgroep die is geconfigureerd met de Azure CLI later moet verwijderen, moet u de SQL IaaS Agent-extensie uitvoeren. Omdat de listener is geregistreerd via de SQL IaaS Agent-extensie, is het niet voldoende om deze via SQL Server Management Studio verwijderen. 

De beste methode is om deze te verwijderen via de SQL IaaS Agent-extensie met behulp van het volgende codefragment in de Azure CLI. Hiermee verwijdert u de metagegevens van de listener van de beschikbaarheidsgroep uit de SQL IaaS Agent-extensie. Ook wordt de listener fysiek verwijderd uit de beschikbaarheidsgroep. 

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

Verwijder alle knooppunten uit het cluster om deze te vernietigen en verwijder vervolgens de metagegevens van het cluster uit de SQL IaaS Agent-extensie. U kunt dit doen met behulp van de Azure CLI of PowerShell. 


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Verwijder eerst alle virtuele SQL Server uit het cluster: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Als dit de enige VM's in het cluster zijn, wordt het cluster vernietigd. Als er naast de SQL Server-VM's andere VM's in het cluster zijn verwijderd, worden de andere VM's niet verwijderd en wordt het cluster niet vernietigd. 

Verwijder vervolgens de metagegevens van het cluster uit de SQL IaaS Agent-extensie: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwijder eerst alle virtuele SQL Server uit het cluster. Hierdoor worden de knooppunten fysiek uit het cluster verwijderd en wordt het cluster vernietigd: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Als dit de enige VM's in het cluster zijn, wordt het cluster vernietigd. Als er naast de SQL Server-VM's andere VM's in het cluster zijn verwijderd, worden de andere VM's niet verwijderd en wordt het cluster niet vernietigd. 

Verwijder vervolgens de metagegevens van het cluster uit de SQL IaaS Agent-extensie: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server VM's](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen SQL Server VM's](frequently-asked-questions-faq.md)
* [Release-opmerkingen voor SQL Server VM's](../../database/doc-changes-updates-release-notes.md)
* [Schakelen tussen licentiemodellen voor een SQL Server-VM](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Overzicht van Always On-beschikbaarheidsgroepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuratie van een server-exemplaar voor Always On-beschikbaarheidsgroepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Beheer van een beschikbaarheidsgroep &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Bewaking van beschikbaarheidsgroepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Overzicht van Transact-SQL-instructies voor Always On-beschikbaarheidsgroepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Overzicht van PowerShell-cmdlets voor Always On-beschikbaarheidsgroepen &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)