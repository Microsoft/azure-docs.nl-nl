---
title: Back-ups maken van Azure-bestands shares met REST API
description: Meer informatie over het gebruik van REST API voor het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88824862"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Back-up maken van Azure-bestands share met Azure Backup via rest API

In dit artikel wordt beschreven hoe u een back-up van een Azure-bestands share maakt met behulp van Azure Backup via REST API.

In dit artikel wordt ervan uitgegaan dat u al een Recovery Services kluis en beleid hebt gemaakt voor het configureren van back-ups voor uw bestands share. Als dat niet het geval is, raadpleegt u de zelf studies voor het maken van de [kluis](./backup-azure-arm-userestapi-createorupdatevault.md) en het [maken van beleid](./backup-azure-arm-userestapi-createorupdatepolicy.md) rest API voor het maken van nieuwe kluizen en beleid.

Voor dit artikel gebruiken we de volgende bronnen:

- **RecoveryServicesVault**: *azurefilesvault*

- **Beleid:** *schedule1*

- **Resource groep**: *Azure files*

- **Opslag account**: *testvault2*

- **Bestands share**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Back-ups configureren voor een niet-beveiligde Azure-bestands share met REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Opslag accounts met niet-beveiligde Azure-bestands shares detecteren

De kluis moet alle Azure Storage-accounts in het abonnement detecteren met bestands shares waarvan een back-up kan worden gemaakt naar de Recovery Services kluis. Dit wordt geactiveerd met behulp van de [vernieuwings bewerking](/rest/api/backup/protectioncontainers/refresh). Het is een asynchrone *post* -bewerking die ervoor zorgt dat de kluis de meest recente lijst met alle niet-beveiligde Azure-bestands shares in het huidige abonnement en ' caches ' in de sjabloon krijgt. Zodra de bestands share in de cache is opgeslagen, hebben de Recovery Services toegang tot de bestands share en kunnen deze worden beveiligd.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

De post-URI heeft de `{subscriptionId}` `{vaultName}` `{vaultresourceGroupName}` `{fabricName}` para meters,, en. In ons voor beeld is de waarde voor de verschillende para meters als volgt:

- `{fabricName}` is *Azure*

- `{vaultName}` is *azurefilesvault*

- `{vaultresourceGroupName}` is *Azure files*

- $filter = backupManagementType EQ ' opslag '

Aangezien alle vereiste para meters in de URI zijn opgegeven, is er geen afzonderlijke aanvraag tekst nodig.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Reacties op de vernieuwings bewerking

De bewerking vernieuwen is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer de bewerking is voltooid.

##### <a name="example-responses-to-the-refresh-operation"></a>Voorbeeld reacties op de vernieuwings bewerking

Zodra de *post* -aanvraag is verzonden, wordt een antwoord van 202 (geaccepteerd) geretourneerd.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

De resulterende bewerking met behulp van de header ' Location ' met een eenvoudige *Get* -opdracht bijhouden

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Zodra alle Azure Storage accounts zijn gedetecteerd, retourneert de GET-opdracht een reactie van 200 (geen inhoud). De kluis kan nu elk opslag account detecteren met bestands shares waarvan een back-up kan worden gemaakt binnen het abonnement.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Lijst met opslag accounts ophalen met bestands shares waarvan een back-up kan worden gemaakt met Recovery Services kluis

Als u wilt controleren of ' caching ' is voltooid, vermeldt u alle opslag accounts in het abonnement met bestands shares waarvan een back-up kan worden gemaakt met de Recovery Services kluis. Ga vervolgens naar het gewenste opslag account in het antwoord. Dit wordt gedaan met behulp van de bewerking [ProtectableContainers ophalen](/rest/api/backup/protectablecontainers/list) .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

De *Get* -URI heeft alle vereiste para meters. Er is geen aanvullende aanvraag tekst nodig.

Voor beeld van antwoord tekst:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Omdat we het *testvault2* -opslag account in de antwoord tekst met de beschrijvende naam kunnen vinden, is de eerder uitgevoerde vernieuwings bewerking geslaagd. De Recovery Services kluis kan nu opslag accounts met niet-beveiligde bestands shares vinden in hetzelfde abonnement.

### <a name="register-storage-account-with-recovery-services-vault"></a>Opslag account registreren bij Recovery Services kluis

Deze stap is alleen nodig als u het opslag account niet eerder hebt geregistreerd bij de kluis. U kunt de kluis registreren via de [ProtectionContainers-register bewerking](/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Stel de variabelen voor de URI als volgt in:

- {resourceGroupName}- *Azure files*
- {fabricnaam}- *Azure*
- {kluisnaam}- *azurefilesvault*
- {containerName}-dit is het naam kenmerk in de antwoord tekst van de bewerking GET ProtectableContainers.
   In ons voor beeld is het *StorageContainer; Opslagpad Azure files; testvault2*

>[!NOTE]
> Neem altijd het naam kenmerk van het antwoord en vul dit op in deze aanvraag. U hoeft geen vaste code op te maken of de indeling van de container naam. Als u deze code maakt of inschakelt, mislukt de API-aanroep als de indeling van de container naam in de toekomst wordt gewijzigd.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

De aanvraag tekst voor het maken is als volgt:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Raadpleeg [ProtectionContainers-REGI ster](/rest/api/backup/protectioncontainers/register#azurestoragecontainer)voor de volledige lijst met definities van de hoofd tekst van de aanvraag en andere details.

Dit is een asynchrone bewerking en retourneert twee antwoorden: "202 geaccepteerd" wanneer de bewerking wordt geaccepteerd en "200 OK" wanneer de bewerking is voltooid.  Als u de bewerkings status wilt bijhouden, gebruikt u de locatie header om de meest recente status van de bewerking op te halen.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Voor beeld van een antwoord tekst wanneer de bewerking is voltooid:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

U kunt controleren of de registratie is geslaagd op basis van de waarde van de para meter *registrationstatus* in de hoofd tekst van het antwoord. In ons geval wordt de status weer gegeven als geregistreerd voor *testvault2*, zodat de registratie bewerking is geslaagd.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Een query uitvoeren op alle niet-beveiligde bestands shares onder een opslag account

U kunt informatie opvragen over Beveilig bare items in een opslag account met behulp van de [beveiligings containers-query](/rest/api/backup/protectioncontainers/inquire) bewerking. Het is een asynchrone bewerking en de resultaten moeten worden gevolgd met behulp van de locatie-header.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Stel de variabelen voor de bovenstaande URI als volgt in:

- {kluisnaam}- *azurefilesvault*
- {fabricnaam}- *Azure*
- {containerName}: Raadpleeg het naam kenmerk in de antwoord tekst van de bewerking GET ProtectableContainers. In ons voor beeld is het *StorageContainer; Opslagpad Azure files; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Zodra de aanvraag is voltooid, wordt de status code ' OK ' geretourneerd.

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Selecteer de bestands share waarvan u een back-up wilt maken

U kunt alle Beveilig bare items onder het abonnement weer geven en de gewenste bestands share vinden waarvan u een back-up wilt maken met behulp van de bewerking [BackupprotectableItems ophalen](/rest/api/backup/backupprotectableitems/list) .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Maak de URI als volgt:

- {kluisnaam}- *azurefilesvault*
- {$filter}- *backupManagementType EQ ' opslag '*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Voorbeeld antwoord:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Het antwoord bevat de lijst met alle niet-beveiligde bestands shares en bevat alle informatie die vereist is voor de Azure Recovery-service om de back-up te configureren.

### <a name="enable-backup-for-the-file-share"></a>Back-up voor de bestands share inschakelen

Nadat de relevante bestands share is geïdentificeerd met de beschrijvende naam, selecteert u het beleid dat u wilt beveiligen. Raadpleeg de [List Policy API](/rest/api/backup/backuppolicies/list)voor meer informatie over bestaande beleids regels in de kluis. Selecteer vervolgens het [relevante beleid](/rest/api/backup/protectionpolicies/get) door te verwijzen naar de naam van het beleid. Zie [zelf studie beleid maken](./backup-azure-arm-userestapi-createorupdatepolicy.md)voor het maken van beleid.

Het inschakelen van beveiliging is een asynchrone *put* -bewerking die een ' beveiligd item ' maakt.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Stel de variabelen **containerName** en **protecteditemname** in met behulp van het kenmerk id in de antwoord tekst van de bewerking Get backupprotectableitems.

In ons voor beeld is de ID van de bestands share die u wilt beveiligen:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containerName}- *storagecontainer; opslag; Azure files; testvault2*
- {protectedItemName}- *azurefileshare; testshare*

U kunt ook verwijzen naar het **naam** kenmerk van de beveiligings container en de beveiligings reacties van items.

>[!NOTE]
>Neem altijd het naam kenmerk van het antwoord en vul dit op in deze aanvraag. U hoeft geen vaste code in te stellen of de indeling van de naam van de beveiligde items te maken. Als u deze code maakt of inschakelt, mislukt de API-aanroep als de indeling van de container naam of de naam van het beveiligde item in de toekomst wordt gewijzigd.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Een aanvraag tekst maken:

De volgende aanvraag hoofdtekst definieert eigenschappen die vereist zijn voor het maken van een beveiligd item.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

De **sourceResourceId** is de **parentcontainerFabricID** als reactie op Get backupprotectableItems.

Voorbeeldreactie

Het maken van een beveiligd item is een asynchrone bewerking, waarmee een andere bewerking wordt gemaakt die moet worden gevolgd. Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer de bewerking is voltooid.

Zodra u de *put* -aanvraag voor het maken of bijwerken van beveiligde items verzendt, wordt het eerste antwoord 202 (geaccepteerd) met een locatie header.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation koptekst met een  *Get* -opdracht.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de inhoud van het beveiligde item in de antwoord tekst.

Hoofd tekst van voorbeeld bericht:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Hiermee wordt bevestigd dat de beveiliging is ingeschakeld voor de bestands share en de eerste back-up wordt geactiveerd volgens het beleids schema.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Een back-up op aanvraag voor de bestands share activeren

Zodra een Azure-bestands share is geconfigureerd voor back-up, worden back-ups uitgevoerd volgens het beleids schema. U kunt wachten op de eerste geplande back-up of op elk gewenst moment een back-up op aanvraag activeren.

Het activeren van een back-up op aanvraag is een POST-bewerking.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} en {protectedItemName} zijn eerder geconstrueerd tijdens het inschakelen van back-ups. In ons voor beeld is dit van toepassing op:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Hoofd tekst van aanvraag maken

Als u een back-up op aanvraag wilt activeren, volgt u de onderdelen van de hoofd tekst van de aanvraag.

| Naam       | Type                       | Description                       |
| ---------- | -------------------------- | --------------------------------- |
| Eigenschappen | AzurefilesharebackupReques | BackupRequestResource-eigenschappen |

Raadpleeg [back-ups activeren voor beveiligde items rest API document](/rest/api/backup/backups/trigger#request-body)voor een volledige lijst met definities van de aanvraag tekst en andere details.

Voor beeld van aanvraag tekst

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Antwoorden op een back-upbewerking op aanvraag

Het activeren van een back-up op aanvraag is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer de bewerking is voltooid.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Voor beelden van antwoorden op back-upbewerking op aanvraag

Zodra u de *post* -aanvraag voor een back-up op aanvraag hebt verzonden, is het eerste antwoord 202 (geaccepteerd) met een locatie header of Azure-async-header.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation koptekst met een  *Get* -opdracht.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de ID van de resulterende back-uptaak in de hoofd tekst van het antwoord.

#### <a name="sample-response-body"></a>Hoofd tekst van voorbeeld antwoord

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Omdat de back-uptaak een langlopende bewerking is, moet deze worden gevolgd zoals uitgelegd in de [taken bewaken met behulp van rest API document](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [herstellen van Azure-bestands shares met behulp van rest API](restore-azure-file-share-rest-api.md).
