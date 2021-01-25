---
title: De DNS-server instelling voor het virtuele netwerk synchroniseren op het virtuele cluster van het SQL-beheerde exemplaar
description: Meer informatie over het synchroniseren van de instelling voor DNS-servers voor virtuele netwerken op een virtueel cluster met SQL-beheerde exemplaren.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 0da38475c0e3c766cabbf765ea89dc5714a5b830
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747566"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>De DNS-server instelling voor het virtuele netwerk synchroniseren op het virtuele cluster van het SQL-beheerde exemplaar
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt uitgelegd wanneer en hoe u de instelling van een DNS-server voor een virtueel netwerk op een virtueel cluster met SQL beheerd exemplaar synchroniseert.

## <a name="when-to-synchronize-the-dns-setting"></a>Wanneer u de DNS-instelling synchroniseert

Er zijn enkele scenario's (bijvoorbeeld Database Mail, servers die zijn gekoppeld aan andere SQL Server-exemplaren in uw cloud of hybride omgeving) waarvoor de namen van particuliere hosts moeten worden omgezet vanuit een SQL Managed Instance. In dit geval moet u een aangepast DNS configureren in Azure. Zie [Configure a Custom DNS for Azure SQL Managed instance](custom-dns-configure.md) voor meer informatie.

Als deze wijziging wordt geïmplementeerd nadat het beheerde exemplaar voor het hosten van een [virtueel cluster](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) is gemaakt, moet u de instelling voor DNS-servers op het virtuele cluster synchroniseren met de configuratie van het virtuele netwerk.

> [!IMPORTANT]
> Het synchroniseren van de DNS-server instelling heeft gevolgen voor alle beheerde exemplaren die worden gehost in het virtuele cluster.

## <a name="how-to-synchronize-the-dns-setting"></a>De DNS-instelling synchroniseren

### <a name="azure-rbac-permissions-required"></a>Vereiste Azure RBAC-machtigingen

Het synchroniseren van de DNS-server configuratie voor gebruikers moet een van de volgende Azure-rollen hebben:

- De rol van abonnements eigenaar of
- Rol van beheerde instantie Inzender of
- Aangepaste rol met de volgende machtiging:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Het virtuele netwerk ophalen waarop de DNS-server instelling is bijgewerkt.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
Gebruik de Power shell-opdracht [invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) om de configuratie van de DNS-servers voor alle virtuele clusters in het subnet te synchroniseren.

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Azure CLI gebruiken

Het virtuele netwerk ophalen waarop de DNS-server instelling is bijgewerkt.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Gebruik de Azure CLI [-opdracht AZ resource invoke: Action](/cli/azure/resource?view=azure-cli-latest#az_resource_invoke_action) DNS servers-configuratie synchroniseren voor alle virtuele clusters in het subnet.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van een aangepaste DNS [configureren van een aangepaste DNS voor Azure SQL Managed instance](custom-dns-configure.md).
- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
