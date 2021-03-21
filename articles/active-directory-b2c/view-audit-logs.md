---
title: Audit Logboeken openen en bekijken
titleSuffix: Azure AD B2C
description: Toegang tot Azure AD B2C controle logboeken programmatisch en in de Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 04d5a989fcaf3cef3eea3b782842fb3255d4b317
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98661099"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Toegang tot Azure AD B2C controle logboeken

Azure Active Directory B2C (Azure AD B2C) verzendt audit logboeken met activiteit gegevens over B2C-resources, tokens die zijn uitgegeven en beheerders toegang. Dit artikel bevat een kort overzicht van de informatie die beschikbaar is in audit logs en instructies over het openen van deze gegevens voor uw Azure AD B2C-Tenant.

Audit logboek gebeurtenissen worden alleen **zeven dagen** bewaard. Plan uw logboeken te downloaden en op te slaan met een van de hieronder vermelde methoden als u een langere Bewaar periode nodig hebt.

> [!NOTE]
> U kunt geen gebruikers aanmeldingen zien voor afzonderlijke Azure AD B2C-toepassingen in het gedeelte **gebruikers** van de **Azure Active Directory** -of **Azure AD B2C** pagina's in de Azure Portal. De aanmeldings gebeurtenissen bevatten de gebruikers activiteit, maar kunnen niet worden gekoppeld aan de B2C-toepassing waarbij de gebruiker zich heeft aangemeld. U moet de audit logboeken voor gebruiken, zoals verderop in dit artikel wordt uitgelegd.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Overzicht van de activiteiten die beschikbaar zijn in de categorie B2C van audit logboeken

De categorie **B2C** in audit Logboeken bevat de volgende soorten activiteiten:

|Type activiteit |Beschrijving  |
|---------|---------|
|Autorisatie |Activiteiten met betrekking tot de autorisatie van een gebruiker om toegang te krijgen tot B2C-bronnen (bijvoorbeeld een beheerder die toegang heeft tot een lijst met B2C-beleid).         |
|Directory |Activiteiten gerelateerd aan adreslijst kenmerken die zijn opgehaald wanneer een beheerder zich aanmeldt met behulp van de Azure Portal. |
|Toepassing | Maak, lees-, update-en delete-bewerkingen (ruw) in B2C-toepassingen. |
|Sleutel |RUWE bewerkingen op sleutels die zijn opgeslagen in een B2C-sleutel container. |
|Resource |RUWE bewerkingen op B2C-resources. Bijvoorbeeld beleid en id-providers.
|Verificatie |Validatie van gebruikers referenties en het uitgeven van tokens.|

Raadpleeg de categorie **basis directory** voor een ruwe activiteiten voor gebruikers objecten.

## <a name="example-activity"></a>Voorbeeld activiteit

In dit voor beeld van een afbeelding van de Azure Portal worden de gegevens weer gegeven die zijn vastgelegd wanneer een gebruiker zich aanmeldt met een externe ID-provider, in dit geval Facebook:

![Voor beeld van de pagina met details van de controle logboek activiteit in Azure Portal](./media/view-audit-logs/audit-logs-example.png)

Het deel venster activity Details bevat de volgende relevante informatie:

|Sectie|Veld|Beschrijving|
|-------|-----|-----------|
| Activiteit | Name | Welke activiteit heeft plaatsgevonden. *Geef bijvoorbeeld een id_token voor de toepassing op*, waarmee de werkelijke gebruikers aanmelding wordt beëindigd. |
| Gestart door (actor) | ObjectId | De **object-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. Deze id is niet zichtbaar in de Azure Portal, maar is toegankelijk via de Microsoft Graph-API. |
| Gestart door (actor) | SPN | De **toepassings-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. |
| Doel(en) | ObjectId | De **object-id** van de gebruiker die zich aanmeldt. |
| Aanvullende details | TenantId | De **Tenant-id** van de Azure AD B2C Tenant. |
| Aanvullende details | PolicyId | De **beleids-id** van de gebruikers stroom (het beleid) dat wordt gebruikt voor het ondertekenen van de gebruiker in. |
| Aanvullende details | ApplicationID | De **toepassings-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Audit logboeken weer geven in de Azure Portal

De Azure Portal biedt toegang tot de audit logboek gebeurtenissen in uw Azure AD B2C-Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Ga naar de map die uw Azure AD B2C-Tenant bevat en blader vervolgens naar **Azure AD B2C**.
1. Selecteer onder **activiteiten** in het linkermenu **audit logboeken**.

Er wordt een lijst weer gegeven met activiteiten gebeurtenissen die zijn vastgelegd in de afgelopen zeven dagen.

![Voorbeeld filter met twee activiteiten gebeurtenissen in Azure Portal](./media/view-audit-logs/audit-logs-example-filter.png)

Er zijn verschillende filter opties beschikbaar, waaronder:

* **Resource type van activiteit** : filteren op de activiteitstypen die worden weer gegeven in de sectie [overzicht van activiteiten die beschikbaar zijn](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Datum** : filter het datum bereik van de weer gegeven activiteiten.

Als u een rij in de lijst selecteert, worden de gegevens van de activiteit voor de gebeurtenis weer gegeven.

Als u de lijst met activiteiten gebeurtenissen in een CSV-bestand (Comma-Separated Values) wilt downloaden, selecteert u **downloaden**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Audit logboeken ophalen met de rapportage-API van Azure AD

Audit logboeken worden gepubliceerd op dezelfde pijp lijn als andere activiteiten voor Azure Active Directory, zodat ze toegankelijk zijn via de [Azure Active Directory rapportage-API](/graph/api/directoryaudit-list). Zie [aan de slag met de Azure Active Directory-rapportage-API](../active-directory/reports-monitoring/concept-reporting-api.md)voor meer informatie.

### <a name="enable-reporting-api-access"></a>Rapportage-API-toegang inschakelen

Als u toegang tot de Azure AD-rapportage-API wilt toestaan op basis van scripts of toepassingen, hebt u een toepassing nodig die is geregistreerd in uw Azure AD B2C-Tenant met de volgende API-machtigingen. U kunt deze machtigingen inschakelen voor een bestaande registratie van de toepassing binnen uw B2C-Tenant of een nieuwe maken die specifiek is voor gebruik met audit logboek automatisering.

* Microsoft Graph > toepassings machtigingen > AuditLog > AuditLog. Read. all

Volg de stappen in het volgende artikel om een toepassing met de vereiste machtigingen te registreren:

[Azure AD B2C beheren met Microsoft Graph](microsoft-graph-get-started.md)

Nadat u een toepassing met de juiste machtigingen hebt geregistreerd, raadpleegt u de sectie Power shell-script verderop in dit artikel voor een voor beeld van hoe u activiteiten gebeurtenissen kunt ophalen met een script.

### <a name="access-the-api"></a>Toegang krijgen tot de API

Als u Azure AD B2C controle logboek gebeurtenissen via de API wilt downloaden, filtert u de logboeken op de `B2C` categorie. Als u wilt filteren op categorie, gebruikt `filter` u de query reeks parameter wanneer u het Azure AD Reporting API-eind punt aanroept.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-script

In het volgende Power shell-script ziet u een voor beeld van het uitvoeren van een query op de Azure AD-rapportage-API. Na het uitvoeren van een query op de API, worden de vastgelegde gebeurtenissen in de standaard uitvoer afgedrukt en wordt de JSON-uitvoer naar een bestand geschreven.

U kunt dit script proberen in de [Azure Cloud shell](overview.md). Zorg ervoor dat u deze update bijwerkt met de toepassings-ID, het client geheim en de naam van uw Azure AD B2C-Tenant.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Hier volgt een voor beeld van de JSON-weer gave van de gebeurtenis activiteit die eerder in dit artikel is weer gegeven:

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

U kunt andere beheer taken automatiseren, bijvoorbeeld [Azure AD B2C gebruikers accounts beheren met Microsoft Graph](microsoft-graph-operations.md).