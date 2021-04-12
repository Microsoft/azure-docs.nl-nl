---
title: Connectiviteits instellingen voor Azure SQL Database en Azure Synapse Analytics
description: In dit artikel worden de keuze van de versie Transport Layer Security (TLS) en de proxy versus omleidings instellingen voor Azure SQL Database en Azure Synapse Analytics uitgelegd.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 0c45a48e6cafa722945400554f2f81916da13775
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627605"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL-Verbindings instellingen
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In dit artikel worden de instellingen beschreven waarmee de verbinding met de server wordt beheerd voor Azure SQL Database en [toegewezen SQL-groep (voorheen SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) in azure Synapse Analytics. Deze instellingen zijn van toepassing op alle SQL Database en toegewezen SQL-groep (voorheen SQL DW)-data bases die zijn gekoppeld aan de server.

> [!IMPORTANT]
> Dit artikel is niet van toepassing op Azure SQL Managed instance.

De connectiviteits instellingen zijn toegankelijk via het scherm **firewalls en virtuele netwerken** , zoals wordt weer gegeven in de volgende scherm afbeelding:

 ![Scherm opname van het venster connectiviteits instellingen.][1]

> [!NOTE]
> Deze instellingen worden direct van kracht nadat ze zijn toegepast. Uw klanten kunnen verbindings verlies ondervinden als ze niet voldoen aan de vereisten voor elke instelling.

## <a name="deny-public-network-access"></a>Openbare netwerktoegang weigeren

Wanneer het **weigeren van open bare netwerk toegang** is ingesteld op **Ja**, zijn alleen verbindingen via persoonlijke eind punten toegestaan. Als deze instelling **niet** is ingesteld (standaard), kunnen klanten verbinding maken met behulp van open bare eind punten (met firewall regels op basis van IP of met firewall regels op basis van een virtueel netwerk) of privé-eind punten (met behulp van een persoonlijke Azure-koppeling), zoals beschreven in het [overzicht van netwerk toegang](network-access-controls-overview.md).

 ![Diagram waarin de connectiviteit wordt weer gegeven wanneer open bare netwerk toegang weigeren is ingesteld op Ja en wanneer het weigeren van open bare netwerk toegang is ingesteld op Nee.][2]

Elke poging om **open bare netwerk toegang** te geven tot **Ja** zonder bestaande persoonlijke eind punten op de logische server, mislukt met een fout bericht dat lijkt op het volgende:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Als u firewall regels voor virtuele netwerken wilt definiëren op een logische server die al is geconfigureerd met persoonlijke eind punten, stelt u **toegang tot open bare netwerk weigeren** in op **Nee**.

Wanneer het **weigeren van open bare netwerk toegang** is ingesteld op **Ja**, zijn alleen verbindingen via persoonlijke eind punten toegestaan. Alle verbindingen via open bare eind punten worden geweigerd met een fout bericht van de volgende strekking:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Wanneer het **weigeren van open bare netwerk toegang** is ingesteld op **Ja**, worden pogingen om firewall regels toe te voegen of bij te werken, geweigerd met een fout bericht dat vergelijkbaar is met het volgende:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Open bare netwerk toegang wijzigen via Power shell

> [!IMPORTANT]
> Azure SQL Database ondersteunt nog steeds de Power shell-Azure Resource Manager module, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

In het volgende Power shell-script ziet u hoe `Get` en `Set` de eigenschap **open bare netwerk toegang** op server niveau:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Open bare netwerk toegang via CLI wijzigen

> [!IMPORTANT]
> Voor alle scripts in deze sectie is de [Azure cli](/cli/azure/install-azure-cli)vereist.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u de instelling **open bare netwerk toegang** in een bash-shell wijzigt:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Minimale TLS-versie 

Met de instelling minimale versie van [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) kunnen klanten kiezen welke versie van TLS door hun SQL database wordt gebruikt.

Momenteel bieden we ondersteuning voor TLS 1.0, 1.1 en TLS 1.2. Door een minimale TLS-versie in te stellen zorgt u ervoor dat nieuwere versies van TLS worden ondersteund. Als u bijvoorbeeld een TLS-versie 1,1 selecteert, worden alleen verbindingen met TLS 1,1 en 1,2 geaccepteerd en worden verbindingen met TLS-1,0 geweigerd. Als u hebt getest of uw toepassingen inderdaad ondersteuning bieden voor TLS 1.2, raden we u aan de minimale TLS-versie hierop in te stellen. Deze versie omvat oplossingen voor beveiligingsproblemen in eerdere versies, en is de hoogste versie van TLS die wordt ondersteund in Azure SQL Database.

> [!IMPORTANT]
> De standaard waarde voor de minimale TLS-versie is alle versies toestaan. Nadat u een versie van TLS afdwingt, is het niet mogelijk om terug te keren naar de standaard waarde.

Voor klanten met toepassingen die afhankelijk zijn van oudere versies van TLS, wordt u aangeraden de minimale TLS-versie in te stellen op basis van de vereisten van uw toepassingen. Voor klanten die afhankelijk zijn van toepassingen om verbinding te maken met behulp van een niet-versleutelde verbinding, wordt u aangeraden geen minimale TLS-versie in te stellen.

Zie [TLS-overwegingen voor SQL database connectiviteit](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)voor meer informatie.

Nadat u de minimale versie van TLS hebt ingesteld, mislukken aanmeldings pogingen van klanten die een TLS-versie gebruiken die lager is dan de minimale TLS-versie van de server, met de volgende fout:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>De minimale TLS-versie instellen via Power shell

> [!IMPORTANT]
> Azure SQL Database ondersteunt nog steeds de Power shell-Azure Resource Manager module, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

Het volgende Power shell-script laat zien hoe `Get` en hoe `Set` de eigenschap van de **minimale TLS-versie** op het niveau van de logische server:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>De minimale TLS-versie instellen via de Azure CLI

> [!IMPORTANT]
> Voor alle scripts in deze sectie is de [Azure cli](/cli/azure/install-azure-cli)vereist.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u de instelling **minimale TLS-versie** in een bash-shell wijzigt:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Het verbindings beleid wijzigen

Het [verbindings beleid](connectivity-architecture.md#connection-policy) bepaalt hoe klanten verbinding maken met Azure SQL database.

## <a name="change-the-connection-policy-via-powershell"></a>Het verbindings beleid wijzigen via Power shell

> [!IMPORTANT]
> Azure SQL Database ondersteunt nog steeds de Power shell-Azure Resource Manager module, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

Het volgende Power shell-script laat zien hoe u het verbindings beleid kunt wijzigen met behulp van Power shell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Het verbindings beleid wijzigen via de Azure CLI

> [!IMPORTANT]
> Voor alle scripts in deze sectie is de [Azure cli](/cli/azure/install-azure-cli)vereist.

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u het verbindings beleid wijzigt in een bash-shell:

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI vanaf een Windows-opdracht prompt

Het volgende CLI-script laat zien hoe u het verbindings beleid wijzigt vanuit een Windows-opdracht prompt (waarbij de Azure CLI is geïnstalleerd):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [connectiviteits architectuur](connectivity-architecture.md)voor een overzicht van de werking van connectiviteit in Azure SQL database.
- Zie voor meer informatie over het wijzigen van het verbindings beleid voor een-server verbinding [-beleid](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
