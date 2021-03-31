---
title: Uw Azure SQL-gegevens classificeren met Azure controle sfeer liggen-labels
description: Importeer uw classificatie uit Azure controle sfeer liggen in uw Azure SQL Database en Azure Synpase Analytics
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714898"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Uw Azure SQL-gegevens classificeren met Azure controle sfeer liggen-labels
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

In dit document wordt beschreven hoe u Azure controle sfeer liggen-labels toevoegt aan uw Azure SQL Database en Azure Synapse Analytics (voorheen SQL DW).

## <a name="create-an-application"></a>Een app maken

1. Open uw **Azure Active Directory** vanuit het Azure Portal.
2. Selecteer onder **beheren** de optie **app-registratie**.
3. Maak een nieuwe Azure Active Directory-app door een **nieuwe toepassing** te selecteren.
4. Voer een naam in voor uw toepassing en selecteer **registreren**.
5. Nadat de toepassing is gemaakt, opent u **certificaten & geheimen** onder **Manager**.
6. Maak een nieuw client geheim door onder **client geheimen** op **Nieuw client geheim** te selecteren.
7. Voeg een beschrijving toe aan het client geheim, selecteer een verval periode en selecteer **toevoegen**.
8. Behoud de **waarde** voor toekomstig gebruik.

   > [!NOTE]
   > Zodra u de pagina sluit, wordt de waarde gemaskeerd. Als u terugkeert naar de pagina, kunt u de waarde van het client geheim niet ophalen. U moet een nieuw client geheim genereren.

9. Ga terug naar de pagina overzicht van de zojuist gemaakte toepassing en kopieer de volgende waarden voor toekomstig gebruik:
    1. (Client-)id van de app
    1. (Tenant-)id van de map

## <a name="provide-permissions-to-the-application"></a>Machtigingen voor de toepassing opgeven

1. Zoek in uw Azure Portal naar **controle sfeer liggen-accounts**.
2. Selecteer het controle sfeer liggen-account waar uw SQL-data bases en Synapse worden geclassificeerd.
3. Open **toegangs beheer (IAM)**, selecteer **toevoegen**.

4. Selecteer **Roltoewijzing toevoegen**.
5. Zoek in de sectie **rol** naar **controle sfeer liggen-gegevens lezer** en selecteer deze.
6. Zoek in de sectie **selecteren** naar de toepassing die u eerder hebt gemaakt, Selecteer deze en druk op **Opslaan**.

## <a name="extract-the-classification-from-azure-purview"></a>De classificatie extra heren uit Azure controle sfeer liggen

1. Open uw controle sfeer liggen-account en zoek op de start pagina naar uw Azure SQL Database of Azure Synapse Analytics waarnaar u de labels wilt kopiëren.
2. Kopieer de gekwalificeerde naam onder **Eigenschappen** en bewaar deze voor toekomstig gebruik.
3. Open de Power shell-shell.

4. Kopieer een van de onderstaande scripts op basis van uw SQL-Asset type (Azure SQL Database of Azure Synapse).
5. Vul de para meters in met de waarden die u hierboven hebt gekopieerd:

   a. $TenantID: sectie 1, stap 9
   
   b. $ClientID: sectie 1, stap 9
   
   c. $SecretID: sectie 1, stap 8
   
   d. $purviewAccountName: sectie 2, stap 2
   
   e. $sqlDatabaseName: sectie 3, stap 2

6. Kopieer de uitvoer van het script voor toekomstig gebruik.

### <a name="for-azure-sql-database"></a>Voor Azure SQL Database

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Voor Azure Synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Voer de T-SQL-opdracht uit voor uw SQL-Asset

1. Maak verbinding met uw Azure SQL Database of uw Azure-Synapse met het hulp programma van uw keuze.
2. Voer de T-SQL-opdracht uit die u hebt gekopieerd uit de vorige sectie.

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Zie [Azure controle sfeer liggen-documentatie](../../purview/index.yml)voor meer informatie over Azure controle sfeer liggen.