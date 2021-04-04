---
title: DNS-alias (Power shell & Azure CLI)
description: Met de cmdlets voor Power shell en Azure CLI kunt u nieuwe client verbindingen omleiden naar een andere SQL-Server in azure, zonder dat u een client configuratie moet aanraken.
keywords: DNS SQL-data base
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 02cfd839ed1b75fd85553f2e5a5150cadc29ff8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790454"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Power shell voor het Azure SQL Database van DNS-alias
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Dit artikel bevat een Power shell-script dat laat zien hoe u een DNS-alias kunt beheren voor de [SQL Server](logical-servers.md) die als host fungeert voor uw Azure SQL database.

> [!NOTE]
> Dit artikel is bijgewerkt voor het gebruik van de Azure PowerShell AZ-module of Azure CLI. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020.
>
> Zie [Inleiding tot de Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit van AZ-modules en AzureRM. Zie [install Azure PowerShell](/powershell/azure/install-az-ps) of [install Azure cli](/cli/azure/install-azure-cli)(Engelstalig) voor installatie-instructies.

## <a name="dns-alias-in-connection-string"></a>DNS-alias in connection string

Een client zoals SQL Server Management Studio (SSMS) kan de DNS-alias naam opgeven in plaats van de werkelijke server naam om verbinding te maken met een [logische SQL-Server](logical-servers.md). In het volgende voor beeld van een server teken reeks vervangt de alias *elke-unieke alias naam* het knoop punt met de eerste punt als scheidings teken in de reeks van de vier knooppunt server:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Vereisten

Als u het Power shell-script wilt uitvoeren dat in dit artikel wordt vermeld, gelden de volgende vereisten:

- Een Azure-abonnement en-account, voor een gratis proef versie, Zie [Azure-experimenten](https://azure.microsoft.com/free/)
- Twee servers

## <a name="example"></a>Voorbeeld

Het volgende code voorbeeld wordt gestart door letterlijke waarden toe te wijzen aan verschillende variabelen.

Als u de code wilt uitvoeren, bewerkt u de waarden voor de tijdelijke aanduiding zodat deze overeenkomen met echte waarden in uw systeem.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De volgende cmdlets worden gebruikt:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): maakt een DNS-alias in het Azure SQL database-service systeem. De alias verwijst naar server 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): alle aliassen ophalen en weer geven die zijn toegewezen aan server 1.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): wijzigt de server naam waarmee de alias is geconfigureerd, van Server 1 naar Server 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Verwijder de alias van Server 2 met behulp van de naam van de alias.

Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) om de module te installeren of te upgraden.

Gebruik `Get-Module -ListAvailable Az` in *power shell \_ise.exe* om de versie te vinden.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De volgende opdrachten worden gebruikt:

- [AZ SQL Server DNS-alias Create](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Hiermee wordt een DNS-alias voor een server gemaakt. De alias verwijst naar server 1.
- [AZ SQL Server DNS-alias show](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): alle aliassen ophalen en weer geven die zijn toegewezen aan server 1.
- [AZ SQL Server DNS-alias set](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): wijzigt de server naam waarmee de alias is geconfigureerd, van Server 1 naar Server 2.
- [AZ SQL Server DNS-alias delete](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Verwijder de alias van Server 2 met behulp van de naam van de alias.

Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Volgende stappen

Zie [DNS-alias voor Azure SQL database](./dns-alias-overview.md)voor een volledige uitleg van de functie voor de DNS-alias voor SQL database.