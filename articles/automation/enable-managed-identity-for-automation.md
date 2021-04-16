---
title: Een beheerde identiteit inschakelen voor uw Azure Automation account (preview)
description: In dit artikel wordt beschreven hoe u een beheerde identiteit kunt instellen voor Azure Automation accounts.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514798"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Een beheerde identiteit inschakelen voor uw Azure Automation account (preview)

In dit onderwerp ziet u hoe u een beheerde identiteit maakt voor een Azure Automation account en hoe u dit kunt gebruiken voor toegang tot andere resources. Zie Beheerde identiteiten voor meer informatie over hoe beheerde identiteiten [Azure Automation.](automation-security-overview.md#managed-identities-preview)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zowel de beheerde identiteit als de Doel-Azure-resources die door uw runbook worden beheerd met behulp van die identiteit, moeten zich in hetzelfde Azure-abonnement hebben.

- De nieuwste versie van Azure Automation-accountmodules. Dit is momenteel 1.6.0. (Zie [Az.Automation 1.6.0](https://www.powershellgallery.com/packages/Az.Automation/1.6.0) voor meer informatie over deze versie.)

- Een Azure-resource die u wilt openen vanuit uw Automation-runbook. Voor deze resource moet een rol zijn gedefinieerd voor de beheerde identiteit, waarmee het Automation-runbook de toegang tot de resource kan verifiëren. Als u rollen wilt toevoegen, moet u een eigenaar zijn voor de resource in de bijbehorende Azure AD-tenant.

- Als u hybride taken wilt uitvoeren met behulp van een beheerde identiteit, moet Hybrid Runbook Worker bijwerken naar de nieuwste versie. De minimaal vereiste versies zijn:

   - Windows Hybrid Runbook Worker: versie 7.3.1125.0
   - Linux Hybrid Runbook Worker: versie 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Door het systeem toegewezen identiteit inschakelen

>[!IMPORTANT]
>De nieuwe identiteit op accountniveau van Automation overschrijft alle eerder door het systeem toegewezen identiteiten op VM-niveau (die worden beschreven in Runbookverificatie gebruiken met beheerde [identiteiten).](/automation-hrw-run-runbooks#runbook-auth-managed-identities) Als u hybride taken op Azure-VM's die gebruikmaken van de door het systeem toegewezen identiteit van een VM voor toegang tot runbook-resources, gebruikt u de Automation-accountidentiteit voor de hybride taken. Dit betekent dat de uitvoering van uw bestaande taak mogelijk wordt beïnvloed als u de functie Door de klant beheerde sleutels (CMK) van uw Automation-account gebruikt.<br/><br/>Als u de beheerde identiteit van de VM wilt blijven gebruiken, moet u de Automation-identiteit op accountniveau niet inschakelen. Als u deze al hebt ingeschakeld, kunt u de door het Automation-account beheerde identiteit uitschakelen. Zie [De door Azure Automation account beheerde identiteit uitschakelen.](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation)

Het instellen van door het systeem toegewezen identiteiten voor Azure Automation kan op twee manieren worden uitgevoerd. U kunt de Azure Portal of de Azure-REST API.

>[!NOTE]
>Door de gebruiker toegewezen identiteiten worden nog niet ondersteund.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Door het systeem toegewezen identiteit inschakelen in Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Navigeer naar uw Automation-account en selecteer **Identiteit** onder **Accountinstellingen.**

1. Stel de **optie Door het systeem toegewezen** in op **Aan** en druk op **Opslaan.** Wanneer u wordt gevraagd om te bevestigen, selecteert u **Ja.**

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Door het systeem toegewezen identiteit inschakelen in Azure Portal.":::

Uw Automation-account kan nu gebruikmaken van de door het systeem toegewezen identiteit, die is geregistreerd bij Azure Active Directory (Azure AD) en wordt vertegenwoordigd door een object-id.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Object-id van beheerde identiteit.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Door het systeem toegewezen identiteit inschakelen via de REST API

U kunt een door het systeem toegewezen beheerde identiteit configureren voor het Automation-account met behulp van de volgende REST API aanroepen.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Aanvraagbody
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Eigenschap (JSON) | Waarde | Beschrijving|
|----------|-----------|------------|
| principalid | \<principal-ID\> | De Globally Unique Identifier (GUID) van het service-principal-object voor de beheerde identiteit die uw Automation-account in de Azure AD-tenant vertegenwoordigt. Deze GUID wordt soms weergegeven als een 'object-id' of object-id. |
| tenantid | \<Azure-AD-tenant-ID\> | De Globally Unique Identifier (GUID) die staat voor de Azure AD-tenant waar het Automation-account nu lid is. Binnen de Azure AD-tenant heeft de service-principal dezelfde naam als het Automation-account. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Identiteit toegang geven tot Azure-resources door een token te verkrijgen

Een Automation-account kan de beheerde identiteit gebruiken om tokens op te halen voor toegang tot andere resources die worden beveiligd door Azure AD, zoals Azure Key Vault. Deze tokens vertegenwoordigen geen specifieke gebruiker van de toepassing. In plaats daarvan vertegenwoordigen ze de toepassing die toegang heeft tot de resource. In dit geval vertegenwoordigt het token bijvoorbeeld een Automation-account.

Voordat u uw door het systeem toegewezen beheerde identiteit kunt gebruiken voor verificatie, moet u toegang instellen voor die identiteit in de Azure-resource waar u de identiteit wilt gebruiken. Als u deze taak wilt voltooien, wijst u de juiste rol toe aan die identiteit op de Azure-doelresource.

In dit voorbeeld wordt Azure PowerShell om te laten zien hoe u de rol Inzender in het abonnement kunt toewijzen aan de Azure-doelresource. De rol Inzender wordt als voorbeeld gebruikt en is in uw geval al dan niet vereist.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Toegang verifiëren met beheerde identiteit

Nadat u de beheerde identiteit voor uw Automation-account hebt ingeschakeld en een identiteit toegang hebt tot de doelresource, kunt u die identiteit opgeven in runbooks voor resources die beheerde identiteit ondersteunen. Gebruik voor identiteitsondersteuning de `Connect-AzAccount` cmdlet Az. Zie [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) in de PowerShell-referentie.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Als uw organisatie nog steeds gebruik maakt van de afgeschafte AzureRM-cmdlets, kunt u `Connect-AzureRMAccount -Identity` gebruiken.

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Een toegangs token genereren zonder Azure-cmdlets

Zorg voor HTTP-eindpunten voor het volgende.
- De metagegevensheader moet aanwezig zijn en moet worden ingesteld op 'true'.
- Een resource moet samen met de aanvraag worden doorgegeven als een queryparameter voor een GET-aanvraag en als formuliergegevens voor een POST-aanvraag.
- De X-IDENTITY-HEADER moet worden ingesteld op de waarde van de omgevingsvariabele IDENTITY_HEADER voor Hybrid Runbook Workers. 
- Het inhoudstype voor de Post-aanvraag moet 'application/x-www-form-urlencoded' zijn. 

### <a name="sample-get-request"></a>Voorbeeld van GET-aanvraag

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Post-voorbeeldaanvraag
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Voorbeeldrunbooks met beheerde identiteit

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Voorbeeldrunbook voor toegang tot een SQL database zonder Azure-cmdlets

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Voorbeeldrunbook voor toegang tot een sleutelkluis met behulp van Azure-cmdlets

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Python-voorbeeldrunbook om een token op te halen
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Volgende stappen

- Zie Disable [your Azure Automation account managed identity (preview)](disable-managed-identity-for-automation.md)(Beheerde identiteit voor uw account uitschakelen (preview) als u een beheerde identiteit wilt uitschakelen.

- Zie Overzicht van Automation Azure Automation accountverificatie voor een overzicht van de beveiliging [van uw account.](automation-security-overview.md)