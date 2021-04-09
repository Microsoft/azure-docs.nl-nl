---
title: Een Azure-SSIS Integration Runtime inrichten met PowerShell
description: Informatie over het inrichten van een Azure-SSIS Integration Runtime in Azure Data Factory met PowerShell, zodat u SSIS-pakketten kunt implementeren en uitvoeren in Azure.
ms.service: data-factory
ms.devlang: powershell
ms.topic: tutorial
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: a0a6e115d2b76ab3f060ee1bcb197da58f2c8fa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391283"
---
# <a name="set-up-an-azure-ssis-ir-in-azure-data-factory-by-using-powershell"></a>Een Azure-SSIS IR instellen in Azure Data Factory met behulp van PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Deze zelfstudie bevat de stappen voor het gebruik van PowerShell om een Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) in te richten in Azure Data Factory (ADF). Een Azure-SSIS IR ondersteunt:

- Pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door een server of beheerd exemplaar van Azure SQL Database (projectimplementatiemodel)
- Pakketten die zijn geïmplementeerd in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel)

Nadat een Azure-SSIS IR is ingericht, kunt u vertrouwde hulpprogramma's gebruiken om uw pakketten in Azure te implementeren en uit te voeren. Deze hulpprogramma's zijn al ingeschakeld voor Azure en bevatten SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) en opdrachtregelprogramma's zoals [dtutil](/sql/integration-services/dtutil-utility) en [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Zie [Overzicht van integratieruntime in Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime) voor algemene informatie over een Azure-SSIS IR.

> [!NOTE]
> In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om een Azure-SSIS IR in te stellen. Als de Azure-portal of een Azure Data Factory-app wilt gebruiken om de Azure-SSIS IR in te stellen, raadpleegt u [Zelfstudie: Een Azure-SSIS IR instellen](./tutorial-deploy-ssis-packages-azure.md). 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een data factory maken.
> * Een Azure-SSIS Integration Runtime maken.
> * De Azure SSIS Integration Runtime starten.
> * Het volledige script nalopen.
> * SSIS-pakketten implementeren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

- **Azure SQL Database Server of een beheerd exemplaar (optioneel)** . Als u nog geen databaseserver hebt, maakt u die in Azure Portal voordat u begint. Met Data Factory wordt vervolgens een SSISDB-exemplaar op deze databaseserver gemaakt. 

  Het wordt aangeraden om de databaseserver in dezelfde Azure-regio te maken als de Integration Runtime. Met deze configuratie kan de Integration Runtime uitvoeringslogboeken wegschrijven naar SSISDB zonder dat hierbij Azure-regio's worden overschreden.

  Houd de volgende zaken in gedachten:

  - Op basis van de geselecteerde databaseserver kan het SSISDB-exemplaar namens u worden gemaakt als een enkele database, als onderdeel van een elastische pool of in een beheerd exemplaar. Deze is toegankelijk in een openbaar netwerk of kan worden toegevoegd aan een virtueel netwerk. Zie [SQL Database en een beheerd exemplaar van SQL vergelijken](create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance) voor hulp bij het kiezen van het type databaseserver om SSISDB te hosten. 
  
    Als u een Azure SQL Database-server met IP-firewall regels/service-eindpunten voor virtuele netwerken of een beheerd exemplaar met een privé-eindpunt gebruikt om SSISDB te hosten, of als u toegang tot on-premises gegevens nodig hebt zonder een zelf-hostende IR te configureren, moet u uw Azure-SSIS IR toevoegen aan een virtueel netwerk. Zie [Een Azure-SSIS IR maken in een virtueel netwerk](./create-azure-ssis-integration-runtime.md) voor meer informatie.

  - Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor de databaseserver. Deze instelling is niet van toepassing wanneer u Azure SQL Database met IP-firewall regels/service-eindpunten voor virtuele netwerken of een beheerd exemplaar met een privé-eindpunt gebruikt om SSISDB te hosten. Zie [Secure Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules) (Azure SQL Database beveiligen) voor meer informatie. Zie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) om deze instelling in te schakelen met behulp van PowerShell.

  - Voeg het IP-adres van de clientcomputer (of een reeks IP-adressen dat het IP-adres van de clientcomputer bevat) toe aan de lijst met client-IP-adressen in de instellingen van de firewall voor de databaseserver. Zie [Overzicht van firewallregels op Azure SQL Database-serverniveau en -databaseniveau](../azure-sql/database/firewall-configure.md) voor meer informatie.

  - U kunt verbinding maken met de databaseserver via SQL-verificatie met de beheerdersreferenties van de server of Azure Active Directory-verificatie met de beheerde identiteit voor uw data factory. Voor het laatste moet u de beheerde identiteit voor uw data factory toevoegen aan een Azure Active Directory-groep met toegangsmachtigingen tot de databaseserver. Zie [Een Azure-SSIS IR met Azure Active Directory-verificatie maken](./create-azure-ssis-integration-runtime.md) voor meer informatie.

  - Controleer of de databaseserver al een SSISDB-exemplaar heeft. Het inrichten van een Azure-SSIS IR biedt geen ondersteuning voor het gebruik van een bestaand SSIS-exemplaar.

- **Azure PowerShell**. Volg de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps) om een PowerShell-script uit te voeren om uw Azure-SSIS IR in te stellen.

> [!NOTE]
> Zie [Beschikbaarheid van Azure Data Factory en Azure SSIS IR per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all) voor een lijst met Azure-regio's waarin Azure Data Factory en Azure-SSIS IR momenteel beschikbaar zijn. 

## <a name="open-the-windows-powershell-ise"></a>Open de Windows PowerShell ISE

Open de Windows PowerShell Integrated Scripting Environment (ISE) met beheerdersbevoegdheden. 

## <a name="create-variables"></a>Variabelen maken

Kopieer het volgende script naar de ISE. Geef waarden op voor de variabelen. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character (for example, "$"), precede it with the escape character "`" (for example, "`$")
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info; this is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, although Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

## <a name="sign-in-and-select-your-subscription"></a>Aanmelden en abonnement selecteren

Voeg de volgende code toe aan het script om u aan te melden en uw Azure-abonnement te selecteren:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-your-database-server"></a>Verbinding met de databaseserver valideren

Als u de verbinding wilt valideren, voegt u het volgende script toe: 

```powershell
# Validate only if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Bekijk het volgende voorbeeld als u een instantie van Azure SQL Database wilt maken als onderdeel van het script. Stel waarden in voor de variabelen die nog niet zijn gedefinieerd (bijvoorbeeld SSISDBServerName, FirewallIPAddress). 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

Als uw resourcegroep al bestaat, hoeft u deze code niet naar het script te kopiëren. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

Voer de volgende opdracht uit om een data factory te maken:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure-SSIS Integration Runtime maken

Voer de volgende opdrachten uit om een Azure-SSIS Integration Runtime te maken die SSIS-pakketten uitvoert in Azure. Als u geen gebruikmaakt van SSISDB, kunt u de parameters CatalogServerEndpoint, CatalogPricingTier en CatalogAdminCredential weglaten.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
```

## <a name="start-the-azure-ssis-integration-runtime"></a>De Azure SSIS Integration Runtime starten

Voer de volgende opdrachten uit om de Azure-SSIS IR te starten:

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Dit proces duurt ongeveer 5 minuten, tenzij u een aangepaste installatietijd hebt ingesteld.
>
> Als u SSISDB gebruikt, maakt de Data Factory-service verbinding met uw databaseserver om SSISDB voor te bereiden. 
> 
> Wanneer u een Azure-SSIS IR inricht, worden ook het Azure Feature Pack voor SSIS en de Access Redistributable geïnstalleerd. Deze onderdelen bieden connectiviteit met Excel- en Access-bestanden en met verschillende Azure-gegevensbronnen, naast de gegevensbronnen die worden ondersteund door de ingebouwde onderdelen. Zie [Ingebouwde/vooraf geïnstalleerde onderdelen op Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md) voor meer informatie over ingebouwde en vooraf geïnstalleerde onderdelen. Zie [Aangepaste installatie voor Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie over aanvullende onderdelen die u kunt installeren.

## <a name="full-script"></a>Volledige script

Met het PowerShell-script in deze sectie wordt er een instantie van Azure-SSIS IR geconfigureerd die SSIS-pakketten uitvoert. Nadat u dit script hebt uitgevoerd, kunt u SSIS-pakketten implementeren en uitvoeren in Azure.

1. Open de ISE.
2. Voer bij de opdrachtprompt van de ISE de volgende opdracht uit:  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Kopieer het PowerShell-script in deze sectie naar de ISE.
4. Geef aan het begin van het script de juiste waarden voor alle parameters op.
5. Voer het script uit. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x the number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you want to use SSISDB, ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-your-azure-ssis-ir"></a>Azure-SSIS IR controleren en beheren

Zie de volgende artikelen voor informatie over het controleren en beheren van de Azure-SSIS IR: 

- [De Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [De Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>SSIS-pakketten implementeren

Als u SSISDB gebruikt, kunt u uw pakketten ernaar implementeren en deze uitvoeren op uw Azure-SSIS IR met behulp van de Azure SSDT- of SSMS-hulpprogramma's. Deze hulpprogramma's maken verbinding met uw databaseserver via het servereindpunt: 

- Voor een Azure SQL Database-server is de indeling van het servereindpunt `<server name>.database.windows.net`.
- Voor een beheerd exemplaar met een privé-eindpunt is de indeling van het servereindpunt `<server name>.<dns prefix>.database.windows.net`.
- Voor een beheerd exemplaar met een openbaar eindpunt is de indeling van het servereindpunt `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Als u geen gebruik maakt van SSISDB, kunt u uw pakketten implementeren in het bestandssysteem, Azure Files of MSDB gehost door uw Azure SQL Managed Instance en deze uitvoeren op uw Azure-SSIS IR met behulp van de opdrachtregelprogramma's [dtutil](/sql/integration-services/dtutil-utility) en [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md). 

Zie [SSIS-projecten/pakketten implementeren](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages) voor meer informatie.

In beide gevallen kunt u uw geïmplementeerde pakketten ook uitvoeren op Azure-SSIS IR met behulp van de activiteit voor het uitvoeren van SSIS-pakketten in Data Factory-pijplijnen. Zie [Het uitvoeren van SSIS-pakketten aanroepen als een Data Factory-activiteit van de eerste klasse](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie.

Hier kunt u nog meer lezen over SSIS: 

- [SSIS-pakketten implementeren, uitvoeren en bewaken in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Verbinding maken met SSISDB in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie) 
- [Pakketuitvoering plannen in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd: 

> [!div class="checklist"]
> * Een data factory maken.
> * Een Azure-SSIS Integration Runtime maken.
> * De Azure SSIS Integration Runtime starten.
> * Het volledige script nalopen.
> * SSIS-pakketten implementeren.

Als u informatie wilt over het aanpassen van uw Azure-SSIS Integration Runtime, leest u het volgende artikel:

> [!div class="nextstepaction"]
>[Uw Azure-SSIS IR aanpassen](./how-to-configure-azure-ssis-ir-custom-setup.md)