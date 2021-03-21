---
title: Power shell voor VNet-eind punten en-regels voor één en gepoolde data bases
description: Voorziet in Power shell-scripts voor het maken en beheren van virtuele service-eind punten voor uw Azure SQL Database en Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 04/17/2019
ms.custom: sqldbrb=1
tags: azure-synapse
ms.openlocfilehash: 76a1d3aaadcbd1b15966a84f5dd2fe876f82c43a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177620"
---
# <a name="powershell-create-a-virtual-service-endpoint-and-vnet-rule-for-azure-sql-database"></a>Power shell: een virtueel service-eind punt en een VNet-regel voor Azure SQL Database maken
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

*Regels voor virtuele netwerken* zijn één firewall beveiligings functie waarmee wordt bepaald of de [logische SQL-server](../logical-servers.md) voor uw [Azure SQL database](../sql-database-paas-overview.md) -data bases, elastische Pools of data bases in [Azure Synapse](../../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken.

> [!IMPORTANT]
> Dit artikel is van toepassing op Azure SQL Database, waaronder Azure Synapse (voorheen SQL DW). Voor de eenvoud geldt de term Azure SQL Database in dit artikel voor data bases die deel uitmaken van Azure SQL Database of Azure Synapse. Dit artikel is *niet* van toepassing op Azure SQL Managed instance omdat hieraan geen service-eind punt is gekoppeld.

In dit artikel wordt een Power shell-script gedemonstreerd dat de volgende acties onderneemt:

1. Hiermee maakt u een Microsoft Azure *virtueel service-eind punt* in uw subnet.
2. Hiermee voegt u het eind punt toe aan de firewall van uw server om een regel voor het *virtuele netwerk* te maken.

Zie [Virtual service-eind punten voor Azure SQL database][sql-db-vnet-service-endpoint-rule-overview-735r]voor meer achtergrond informatie.

> [!TIP]
> Als alles u nodig hebt om de naam van het virtuele-service-eindpunt *type* voor Azure SQL database aan uw subnet te evalueren of toe te voegen, kunt u verder gaan naar ons meer [Direct Power shell-script](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de [ `Az.Sql` cmdlets](/powershell/module/az.sql). Zie [AzureRM. SQL](/powershell/module/AzureRM.Sql/)voor de oudere module. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

## <a name="major-cmdlets"></a>Primaire cmdlets

Dit artikel benadrukt de cmdlet [ **New-AzSqlServerVirtualNetworkRule**](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule) waarmee het subnet-eind punt wordt toegevoegd aan de toegangs beheer lijst (ACL) van uw server, waardoor een regel wordt gemaakt.

In de volgende lijst ziet u de volg orde van andere *belang rijke* cmdlets die u moet uitvoeren om u voor te bereiden op het aanroepen van **New-AzSqlServerVirtualNetworkRule**. In dit artikel vinden deze aanroepen plaats in [script 3 "virtuele netwerk regel"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Hiermee wordt een subnetobject gemaakt.
2. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork): maakt uw virtuele netwerk, waardoor het het subnet.
3. [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): wijst een virtueel service-eind punt toe aan uw subnet.
4. [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork): persistente updates die zijn aangebracht in uw virtuele netwerk.
5. [New-AzSqlServerVirtualNetworkRule](/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): nadat het subnet een eind punt is, voegt u het subnet als een regel voor het virtuele netwerk toe aan de ACL van uw server.
   - Deze cmdlet biedt de para meter **-IgnoreMissingVNetServiceEndpoint**, te beginnen met de module versie 5.1.1 van Azure RM Power shell.

## <a name="prerequisites-for-running-powershell"></a>Vereisten voor het uitvoeren van Power shell

- U kunt zich al aanmelden bij Azure, zoals via de [Azure Portal][http-azure-portal-link-ref-477t].
- U kunt al Power shell-scripts uitvoeren.

> [!NOTE]
> Zorg ervoor dat service-eind punten zijn ingeschakeld voor het VNet/subnet dat u wilt toevoegen aan uw server, anders mislukt het maken van de VNet-firewall regel.

## <a name="one-script-divided-into-four-chunks"></a>Eén script onderverdeeld in vier segmenten

Het Power shell-script voor demonstraties is onderverdeeld in een reeks kleinere scripts. De afdeling vereenvoudigt het leren en biedt flexibiliteit. De scripts moeten in hun aangegeven volg orde worden uitgevoerd. Als u nu geen tijd hebt om de scripts uit te voeren, wordt de daad werkelijke test uitvoer weer gegeven na het script 4.

<a name="a-script-10"></a>

### <a name="script-1-variables"></a>Script 1: variabelen

Met dit eerste Power shell-script worden waarden toegewezen aan variabelen. De volgende scripts zijn afhankelijk van deze variabelen.

> [!IMPORTANT]
> Voordat u dit script uitvoert, kunt u de waarden als u wilt bewerken. Als u al een resource groep hebt, kunt u bijvoorbeeld de naam van de resource groep bewerken als de toegewezen waarde.
>
> De naam van uw abonnement moet worden bewerkt in het script.

### <a name="powershell-script-1-source-code"></a>Power shell-script 1-bron code

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName $SubscriptionName

$ResourceGroupName = 'RG-YourNameHere'
$Region = 'westcentralus'

$VNetName = 'myVNet'
$SubnetName = 'mySubnet'
$VNetAddressPrefix = '10.1.0.0/16'
$SubnetAddressPrefix = '10.1.1.0/24'
$VNetRuleName = 'myFirstVNetRule-ForAcl'

$SqlDbServerName = 'mysqldbserver-forvnet'
$SqlDbAdminLoginName = 'ServerAdmin'
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Official type name.

Write-Host 'Completed script 1, the "Variables".'
```

<a name="a-script-20"></a>

### <a name="script-2-prerequisites"></a>Script 2: vereisten

Met dit script wordt het volgende script voor bereid, waarbij de eindpunt actie is. Met dit script maakt u de volgende items in de lijst, maar alleen als ze nog niet bestaan. U kunt script 2 overs Laan als u zeker weet dat deze items al bestaan:

- Azure-resourcegroep
- Logische SQL-server

### <a name="powershell-script-2-source-code"></a>Bron code voor Power shell-script 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists."

$gottenResourceGroup = $null
$gottenResourceGroup = Get-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue

if ($null -eq $gottenResourceGroup) {
    Write-Host "Creating your missing Resource Group - $ResourceGroupName."
    New-AzResourceGroup -Name $ResourceGroupName -Location $Region
} else {
    Write-Host "Good, your Resource Group already exists - $ResourceGroupName."
}

$gottenResourceGroup = $null

###########################################################
## Ensure your server already exists. ##
###########################################################

Write-Host "Check whether your server already exists."

$sqlDbServer = $null
$azSqlParams = @{
    ResourceGroupName = $ResourceGroupName
    ServerName        = $SqlDbServerName
    ErrorAction       = 'SilentlyContinue'
}
$sqlDbServer = Get-AzSqlServer @azSqlParams

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing server - $SqlDbServerName."
    Write-Host "Gather the credentials necessary to next create a server."

    $sqlAdministratorCredentials = [pscredential]::new($SqlDbAdminLoginName,(ConvertTo-SecureString -String $SqlDbAdminLoginPassword -AsPlainText -Force))

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending."
        return
    }

    Write-Host "Create your server."

    $sqlSrvParams = @{
        ResourceGroupName           = $ResourceGroupName
        ServerName                  = $SqlDbServerName
        Location                    = $Region
        SqlAdministratorCredentials = $sqlAdministratorCredentials
    }
    New-AzSqlServer @sqlSrvParams
} else {
    Write-Host "Good, your server already exists - $SqlDbServerName."
}

$sqlAdministratorCredentials = $null
$sqlDbServer = $null

Write-Host 'Completed script 2, the "Prerequisites".'
```

<a name="a-script-30"></a>

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3: een eind punt en een regel maken

Met dit script maakt u een virtueel netwerk met een subnet. Vervolgens wijst het script het type **micro soft. SQL** -eind punt toe aan uw subnet. Ten slotte voegt het script uw subnet toe aan de toegangs beheer lijst (ACL), waardoor er een regel wordt gemaakt.

### <a name="powershell-script-3-source-code"></a>Power shell-script 3-bron code

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network."

$subnetParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$subnet = New-AzVirtualNetworkSubnetConfig @subnetParams

Write-Host "Create a virtual network '$VNetName'.`nGive the subnet to the virtual network that we created."

$vnetParams = @{
    Name              = $VNetName
    AddressPrefix     = $VNetAddressPrefix
    Subnet            = $subnet
    ResourceGroupName = $ResourceGroupName
    Location          = $Region
}
$vnet = New-AzVirtualNetwork @vnetParams

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet."

$vnetSubParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @vnetSubParams

Write-Host "Persist the updates made to the virtual network > subnet."

$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

$vnet.Subnets[0].ServiceEndpoints  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Add the subnet .Id as a rule, into the ACLs for your server."

$ruleParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    VirtualNetworkSubnetId = $subnet.Id
}
New-AzSqlServerVirtualNetworkRule @ruleParams 

Write-Host "Verify that the rule is in the SQL Database ACL."

$rule2Params = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
}
Get-AzSqlServerVirtualNetworkRule @rule2Params

Write-Host 'Completed script 3, the "Virtual-Network-Rule".'
```

<a name="a-script-40"></a>

## <a name="script-4-clean-up"></a>Script 4: opschonen

Met dit laatste script verwijdert u de resources die de vorige scripts hebben gemaakt voor de demonstratie. Het script vraagt echter om bevestiging voordat het volgende wordt verwijderd:

- Logische SQL-server
- Azure-resource groep

U kunt script 4 telkens uitvoeren nadat het script 1 is voltooid.

### <a name="powershell-script-4-source-code"></a>Power shell-script 4-bron code

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL Database ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL Database ACL."

$removeParams = @{
    ResourceGroupName      = $ResourceGroupName
    ServerName             = $SqlDbServerName
    VirtualNetworkRuleName = $VNetRuleName
    ErrorAction            = 'SilentlyContinue'
}
Remove-AzSqlServerVirtualNetworkRule @removeParams

Write-Host "Delete the endpoint from the subnet."

$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

Remove-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vnet

Write-Host "Delete the virtual network (thus also deletes the subnet)."

$removeParams = @{
    Name              = $VNetName
    ResourceGroupName = $ResourceGroupName
    ErrorAction       = 'SilentlyContinue'
}
Remove-AzVirtualNetwork @removeParams

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Logical SQL server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your server AND your resource group?  [yes/no]'
if ('yes' -eq $yesno) {
    Write-Host "Remove the server."

    $removeParams = @{
        ServerName        = $SqlDbServerName
        ResourceGroupName = $ResourceGroupName
        ErrorAction       = 'SilentlyContinue'
    }
    Remove-AzSqlServer @removeParams

    Write-Host "Remove the Azure Resource Group."
    
    Remove-AzResourceGroup -Name $ResourceGroupName -ErrorAction SilentlyContinue
} else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group."
}

Write-Host 'Completed script 4, the "Clean-Up".'
```

<a name="a-actual-output"></a>

<a name="a-verify-subnet-is-endpoint-ps-100"></a>

## <a name="verify-your-subnet-is-an-endpoint"></a>Controleren of uw subnet een eind punt is

Mogelijk hebt u een subnet dat al is toegewezen aan de naam van het **micro soft. SQL** -type, wat betekent dat het al een virtueel service-eind punt is. U kunt de [Azure Portal][http-azure-portal-link-ref-477t] gebruiken om een regel voor een virtueel netwerk te maken op basis van het eind punt.

U kunt er ook voor zorgen dat u niet zeker weet of uw subnet de naam van het **micro soft. SQL** -type heeft. U kunt het volgende Power shell-script uitvoeren om de volgende acties uit te voeren:

1. Controleer of het subnet de naam van het **micro soft. SQL** -type heeft.
2. U kunt desgewenst de naam van het type toewijzen als deze niet aanwezig is.
    - Het script vraagt u om te *bevestigen* voordat de naam van het ontbrekende type wordt toegepast.

### <a name="phases-of-the-script"></a>Fasen van het script

Dit zijn de fasen van het Power shell-script:

1. Meld u aan bij uw Azure-account en u hebt slechts één keer per PS-sessie nodig.  Variabelen toewijzen.
2. Zoek naar uw virtuele netwerk en vervolgens naar uw subnet.
3. Is het subnet gelabeld als **micro soft. SQL** -eindpunt server type?
4. Voeg in uw subnet een virtueel service-eind punt van de type naam **micro soft. SQL** toe.

> [!IMPORTANT]
> Voordat u dit script uitvoert, moet u de waarden bewerken die zijn toegewezen aan de $-variabelen, aan de bovenkant van het script.

### <a name="direct-powershell-source-code"></a>Directe Power shell-bron code

Met dit Power shell-script wordt niets bijgewerkt, tenzij u Ja antwoordt als u wordt gevraagd om een bevestiging. Met het script kan de type naam **micro soft. SQL** worden toegevoegd aan uw subnet. Maar het script probeert de add alleen toe als uw subnet de type naam niet heeft.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]'
if ('yes' -eq $yesno) { Connect-AzAccount }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName = 'yourSubscriptionName'
Select-AzSubscription -SubscriptionName "$SubscriptionName"

$ResourceGroupName = 'yourRGName'
$VNetName = 'yourVNetName'
$SubnetName = 'yourSubnetName'
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.' # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql'  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null
$vnet = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VNetName

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'."
    return
}

$subnet = $null
for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn]
    if ($subnet.Name -eq $SubnetName) { break }
    $subnet = $null
}

if ($null -eq $subnet) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'"
    Return
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null
for ($nn = 0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn]
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql
        break
    }
    $endpointMsSql = $null
}

if ($null -eq $endpointMsSql) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."
    return
} else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'."

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]'
    if ('no' -eq $yesno) { return }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$setParams = @{
    Name            = $SubnetName
    AddressPrefix   = $SubnetAddressPrefix
    VirtualNetwork  = $vnet
    ServiceEndpoint = $ServiceEndpointTypeName_SqlDb
}
$vnet = Set-AzVirtualNetworkSubnetConfig @setParams

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet

for ($nn = 0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints # Display.
}
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]:../vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
