---
title: Back-ups van Azure-bestands shares beheren met rest-API
description: Meer informatie over het gebruik van REST API voor het beheren en bewaken van Azure-bestands shares waarvan een back-up is gemaakt door Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 5e2823472c6a7bdd6b3f9819db3079d7efa78c4e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88892844"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Back-ups van Azure-bestands shares beheren met REST API

In dit artikel wordt uitgelegd hoe u taken kunt uitvoeren voor het beheren en bewaken van de Azure-bestands shares waarvan een back-up is gemaakt door [Azure backup](./backup-overview.md).

## <a name="monitor-jobs"></a>Taken controleren

De Azure Backup-service activeert taken die op de achtergrond worden uitgevoerd. Dit omvat scenario's zoals het activeren van back-ups, herstel bewerkingen en het uitschakelen van back-ups. Deze taken kunnen worden gevolgd met hun Id's.

### <a name="fetch-job-information-from-operations"></a>Taak gegevens ophalen uit bewerkingen

Een bewerking zoals het activeren van een back-up retourneert altijd een jobID in het antwoord.

De laatste reactie van een [trigger back-up rest API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) bewerking is bijvoorbeeld als volgt:

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

De back-uptaak voor Azure-bestands share wordt geïdentificeerd door het veld **jobId** en kan worden gevolgd als [hier](/rest/api/backup/jobdetails/) wordt beschreven met BEhulp van een GET-aanvraag.

### <a name="tracking-the-job"></a>De taak bijhouden

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{JobName} is de ' jobId ' die hierboven wordt vermeld. Het antwoord is altijd ' 200 OK ' met het veld **status** om de status van de taak aan te geven. Zodra het ' voltooid ' of ' CompletedWithWarnings ' is, toont de sectie **extendedInfo** meer informatie over de taak.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Antwoord

Naam  | Type  |  Beschrijving
--- | --- | ----
200 OK |  JobResource  | OK

#### <a name="response-example"></a>Voorbeeld van antwoord

Zodra de *Get* -URI is verzonden, wordt een 200-antwoord geretourneerd.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Beleid wijzigen

Als u het beleid wilt wijzigen waarmee de bestands share is beveiligd, kunt u dezelfde indeling gebruiken als voor het inschakelen van de beveiliging. Geef alleen de nieuwe beleids-ID op in het aanvraag beleid en verzend de aanvraag.

Bijvoorbeeld: als u het beveiligings beleid van *testshare* wilt wijzigen van *schedule1* naar *schedule2*, geeft u de *schedule2* -id op in de aanvraag tekst.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Beveiliging stoppen, maar bestaande gegevens behouden

U kunt de beveiliging op een beveiligde bestands share verwijderen, maar de gegevens waarvan al een back-up is gemaakt, behouden. U doet dit door het beleid te verwijderen in de hoofd tekst van de aanvraag die u hebt gebruikt om[back-up in te scha kelen](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) en de aanvraag te verzenden. Zodra de koppeling met het beleid is verwijderd, worden back-ups niet meer geactiveerd en worden er geen nieuwe herstel punten gemaakt.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Voorbeeldantwoord

Het stoppen van de beveiliging voor een bestands share is een asynchrone bewerking. Met deze bewerking wordt een andere bewerking gemaakt die moet worden gevolgd. Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 wanneer deze bewerking is voltooid.

Reactie header wanneer de bewerking is geaccepteerd:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation koptekst met een GET-opdracht:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Hoofdtekst van de reactie

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Beveiliging stoppen en gegevens verwijderen

Als u de beveiliging op een beveiligde bestands share wilt verwijderen en ook de back-upgegevens wilt verwijderen, moet u een Verwijder bewerking uitvoeren, zoals [hier](/rest/api/backup/protecteditems/delete)wordt beschreven.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

De para meters {containerName} en {protectedItemName} zijn [hier](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)ingesteld.

In het volgende voor beeld wordt een bewerking geactiveerd voor het stoppen van de beveiliging van de *testshare* -bestands share die wordt beveiligd met *azurefilesvault*.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Antwoorden

Beveiliging verwijderen is een asynchrone bewerking. Met deze bewerking wordt een andere bewerking gemaakt die afzonderlijk moet worden bijgehouden.
Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en 204 (geen inhoud) wanneer deze bewerking is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [oplossen van problemen bij het configureren van back-ups voor Azure-bestands shares](troubleshoot-azure-files.md).
