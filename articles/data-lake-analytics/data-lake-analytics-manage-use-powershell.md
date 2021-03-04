---
title: Azure Data Lake Analytics beheren met Azure PowerShell
description: In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om Data Lake Analytics accounts, gegevens bronnen, gebruikers & taken te beheren.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/29/2018
ms.openlocfilehash: f1d5d732433a78b8e3e7ff94bd6aaa2eea8f4a87
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051783"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure Data Lake Analytics beheren met Azure PowerShell

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics accounts, gegevens bronnen, gebruikers en taken beheert met behulp van Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u Power shell met Data Lake Analytics wilt gebruiken, verzamelt u de volgende informatie:

* **Abonnements-id**: de id van het Azure-abonnement met uw data Lake Analytics-account.
* **Resource groep**: de naam van de Azure-resource groep die uw data Lake Analytics-account bevat.
* **Data Lake Analytics account naam**: de naam van uw data Lake Analytics-account.
* **Standaard data Lake Store accountnaam**: elk data Lake Analytics account heeft een standaard data Lake Store-account.
* **Locatie**: de locatie van uw data Lake Analytics-account, zoals "VS-Oost 2" of andere ondersteunde locaties.

De PowerShell-fragmenten in deze zelfstudie gebruiken deze variabelen om deze informatie op te slaan

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

### <a name="log-in-using-interactive-user-authentication"></a>Meld u aan met interactieve gebruikers verificatie

Meld u aan met een abonnements-ID of abonnements naam

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname
```

## <a name="saving-authentication-context"></a>Verificatie context opslaan

De `Connect-AzAccount` cmdlet vraagt altijd om referenties. U kunt voor komen dat u wordt gevraagd door gebruik te maken van de volgende cmdlets:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Aanmelden met behulp van een Service-Principal-identiteit (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname"
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Accounts beheren

### <a name="list-accounts"></a>Lijst van accounts

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Een account maken

Elke Data Lake Analytics-account vereist een Data Lake Store-standaardaccount dat wordt gebruikt voor het opslaan van logboeken. U kunt een bestaand account opnieuw gebruiken of een account maken.

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Account gegevens ophalen

Details over een account ophalen.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Controleren of er een account bestaat

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Gegevensbronnen beheren

Azure Data Lake Analytics ondersteunt momenteel de volgende gegevens bronnen:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Elk Data Lake Analytics-account heeft een standaard Data Lake Store-account. De standaard Data Lake Store-account wordt gebruikt voor het opslaan van meta gegevens van taken en taak controle Logboeken.

### <a name="find-the-default-data-lake-store-account"></a>De standaard Data Lake Store account zoeken

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

U kunt het standaard Data Lake Store-account vinden door de lijst met gegevens bronnen te filteren op de `IsDefault` eigenschap:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault }
```

### <a name="add-a-data-source"></a>Een gegevensbron toevoegen

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName
```

### <a name="list-data-sources"></a>Gegevens bronnen weer geven

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL-taken verzenden

### <a name="submit-a-string-as-a-u-sql-job"></a>Een teken reeks als U-SQL-taak verzenden

```powershell
$script = @"
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Een bestand verzenden als U-SQL-taak

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Taken weer geven

De uitvoer bevat de taken die momenteel worden uitgevoerd en de taken die onlangs zijn voltooid.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>De eerste N taken weer geven

Standaard wordt de lijst met taken gesorteerd op tijdstip van verzenden. De meest recent verzonden taken worden dus eerst weer gegeven. Het ADLA-account houdt standaard taken voor 180 dagen, maar de Get-AdlJob cmdlet retourneert standaard alleen de eerste 500. De para meter-top gebruiken om een specifiek aantal taken weer te geven.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Taken weer geven op taak status

Met behulp van de `-State` para meter. U kunt elk van deze waarden combi neren:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Taken weer geven op taak resultaat

Gebruik de `-Result` para meter om te detecteren of beëindigde taken zijn voltooid. Deze bevat deze waarden:

* Geannuleerd
* Mislukt
* Geen
* Geslaagd

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Taken weer geven per taak indiener

De `-Submitter` para meter helpt u bij het identificeren van wie een taak heeft ingediend.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Taken weer geven op Verzend tijd

De `-SubmittedAfter` is handig voor het filteren op een tijds bereik.

```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Taak status ophalen

Haal de status van een bepaalde taak op.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="cancel-a-job"></a>Een taak annuleren

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Wachten tot een taak is voltooid

In plaats van herhalen `Get-AdlAnalyticsJob` totdat een taak is voltooid, kunt u de `Wait-AdlJob` cmdlet gebruiken om te wachten tot de taak is beëindigd.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```


## <a name="list-job-pipelines-and-recurrences"></a>Taak pijplijnen en-herhalingen weer geven

Gebruik de `Get-AdlJobPipeline` cmdlet om de pijplijn informatie te bekijken die eerder taken heeft ingediend.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Gebruik de `Get-AdlJobRecurrence` cmdlet om de terugkeer gegevens voor eerder ingediende taken weer te geven.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="manage-compute-policies"></a>Reken beleid beheren

### <a name="list-existing-compute-policies"></a>Bestaande Compute-beleids regels weer geven

De `Get-AdlAnalyticsComputePolicy` cmdlet haalt informatie op over reken beleid voor een Data Lake Analytics-account.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Een reken beleid maken

De `New-AdlAnalyticsComputePolicy` cmdlet maakt een nieuw reken beleid voor een Data Lake Analytics-account. In dit voor beeld wordt het maximum aantal van AUs dat beschikbaar is voor de opgegeven gebruiker ingesteld op 50 en de minimale taak prioriteit tot 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="manage-files"></a>Bestanden beheren

### <a name="check-for-the-existence-of-a-file"></a>Controleren op aanwezigheid van een bestand

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Uploaden en downloaden

Upload een bestand.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv"
```

Upload een hele map recursief.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Down load een bestand.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Down load een hele map recursief.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Als het uploaden of downloaden wordt onderbroken, kunt u proberen het proces te hervatten door de cmdlet opnieuw uit te voeren met de ``-Resume`` vlag.

## <a name="manage-the-u-sql-catalog"></a>De U-SQL-Catalogus beheren

De U-SQL-catalogus wordt gebruikt om gegevens en code te structureren zodat ze kunnen worden gedeeld door U-SQL-scripts. De catalogus maakt de hoogst mogelijke prestaties mogelijk met gegevens in Azure Data Lake. Zie [U-SQL-catalogus gebruiken](./data-lake-analytics-u-sql-get-started.md) voor meer informatie.

### <a name="list-items-in-the-u-sql-catalog"></a>Items in de U-SQL-catalogus weer geven

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Alle assembly's van de U-SQL-catalogus weer geven

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Details over een catalogus item ophalen

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Referenties opslaan in de catalogus

Maak binnen een U-SQL database een referentie object voor een Data Base die wordt gehost in Azure. Momenteel zijn U-SQL-referenties het enige type catalogus item dat u kunt maken via Power shell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Firewallregels beheren

### <a name="list-firewall-rules"></a>Firewall regels weer geven

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Een firewall regel toevoegen

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Een firewall regel wijzigen

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Een firewall regel verwijderen

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>IP-adressen van Azure toestaan

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="working-with-azure"></a>Werken met Azure

### <a name="get-error-details"></a>Fout Details ophalen

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Controleren of u als beheerder werkt op de Windows-computer

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Een TenantID zoeken

Van de naam van een abonnement:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Van een abonnements-ID:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Van een domein adres zoals ' contoso.com '

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Alle abonnementen en Tenant-Id's weer geven

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Een Data Lake Analytics-account maken met behulp van een sjabloon

U kunt ook een Azure Resource Group-sjabloon gebruiken met behulp van het volgende voor beeld: [een Data Lake Analytics-account maken met behulp van een sjabloon](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Aan de slag met data Lake Analytics met behulp van de [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [Azure cli](data-lake-analytics-get-started-cli.md)
* Azure data Lake Analytics beheren met [Azure Portal](data-lake-analytics-manage-use-portal.md)  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [cli](data-lake-analytics-manage-use-cli.md)
