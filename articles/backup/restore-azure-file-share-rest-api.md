---
title: Azure-bestands shares herstellen met REST API
description: Meer informatie over het gebruik van REST API om Azure-bestands shares of specifieke bestanden te herstellen vanaf een herstel punt dat is gemaakt door Azure Backup
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 60c73caa5db684e38b94b4d5786f2fd24aa65d08
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761794"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Azure-bestands shares herstellen met behulp van REST API

In dit artikel wordt uitgelegd hoe u een volledige bestands share herstelt of specifieke bestanden van een herstel punt dat is gemaakt door [Azure backup](./backup-overview.md) met behulp van de rest API.

Aan het einde van dit artikel leert u hoe u de volgende bewerkingen kunt uitvoeren met behulp van REST API:

* Herstel punten weer geven voor een back-up van een Azure-bestands share.
* Een volledige Azure-bestands share herstellen.
* Afzonderlijke bestanden of mappen herstellen.

## <a name="prerequisites"></a>Vereisten

We gaan ervan uit dat u al een back-up hebt gemaakt van de bestands share die u wilt herstellen. Als dat niet het geval is, controleert u [back-up van de Azure-bestands share met rest API](backup-azure-file-share-rest-api.md) om te leren hoe u er een maakt.

Voor dit artikel gebruiken we de volgende bronnen:

* **RecoveryServicesVault**: *azurefilesvault*
* **Resource groep**: *Azure files*
* **Opslag account**: *afsaccount*
* **Bestands share**: *Azure files*

## <a name="fetch-containername-and-protecteditemname"></a>Containernaam en ProtectedItemName ophalen

Voor de meeste Restore-gerelateerde API-aanroepen moet u waarden door geven voor de URI-para meters {containerName} en {protectedItemName}. Gebruik het kenmerk ID in de antwoord tekst van de bewerking [Get backupprotectableitems](/rest/api/backup/protecteditems/get) om waarden voor deze para meters op te halen. In ons voor beeld is de ID van de bestands share die u wilt beveiligen:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

De waarden worden dus als volgt vertaald:

* {containerName}- *storagecontainer; opslag; Azure files; afsaccount*
* {protectedItemName}- *azurefileshare; Azure files*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Herstel punten ophalen voor het maken van een back-up van een Azure-bestands share

Als u een back-up van de bestands share of bestanden wilt herstellen, selecteert u eerst een herstel punt om de herstel bewerking uit te voeren. De beschik bare herstel punten van een back-upitem kunnen worden weer gegeven met behulp van de aanroep van de [herstel punt lijst](/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) rest API. Het is een GET-bewerking met alle relevante waarden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Stel de URI-waarden als volgt in:

* {fabricnaam}: *Azure*
* {kluisnaam}: *azurefilesvault*
* {containerName}: *storagecontainer; Storage; Azure files; afsaccount*
* {protectedItemName}: *azurefileshare; Azure files*
* {ResourceGroupName}: *Azure files*

De GET-URI heeft alle vereiste para meters. Er is geen aanvullende aanvraag tekst nodig.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response-for-fetch-recovery-points"></a>Voorbeeld reactie voor herstel punten voor ophalen

Zodra de GET-URI is verzonden, wordt een 200-antwoord geretourneerd:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

Het herstel punt wordt geïdentificeerd met het veld {name} in het bovenstaande antwoord.

## <a name="full-share-recovery-using-rest-api"></a>Herstel van volledige shares met behulp van REST API

Gebruik deze herstel optie om de volledige bestands share te herstellen op de oorspronkelijke of een alternatieve locatie.
Het activeren van een herstel bewerking is een POST-aanvraag en u kunt deze actie uitvoeren met de [trigger herstellen](/rest/api/backup/restores/trigger) rest API.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

De waarden {containerName} en {protectedItemName} zijn [zo ingesteld,](#fetch-containername-and-protecteditemname) en recoveryPointID is het veld {name} van het hierboven vermelde herstel punt.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Hoofd tekst van aanvraag maken

Als u een herstel bewerking voor een Azure-bestands share wilt activeren, zijn de volgende onderdelen van de hoofd tekst van de aanvraag:

Naam |  Type   |   Beschrijving
--- | ---- | ----
Eigenschappen | AzureFileShareRestoreRequest | RestoreRequestResource-eigenschappen

Raadpleeg het [document trigger Restore rest API](/rest/api/backup/restores/trigger#request-body)voor de volledige lijst met definities van de aanvraag tekst en andere details.

### <a name="restore-to-original-location"></a>Herstellen naar de oorspronkelijke locatie

#### <a name="request-body-example-for-restore-to-original-location"></a>Voor beeld van aanvraag tekst voor herstellen naar oorspronkelijke locatie

De volgende aanvraag hoofdtekst definieert eigenschappen die vereist zijn voor het activeren van een Azure-bestands share herstellen:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Herstellen naar alternatieve locatie

Geef de volgende para meters op voor herstel naar een alternatieve locatie:

* **targetResourceId**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **naam**: de bestands share binnen het doel-opslag account waarnaar de inhoud van de back-up wordt teruggezet.
* **targetFolderPath**: de map onder de bestands share waarop de gegevens worden teruggezet.

#### <a name="request-body-example-for-restore-to-alternate-location"></a>Voor beeld van aanvraag tekst voor herstellen naar alternatieve locatie

Met de volgende aanvraag tekst wordt de *Azure files* -bestands share in het *afsaccount* -opslag account teruggezet naar de *azurefiles1* -bestands share in het *afaccount1* -opslag account.

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Antwoord

Het activeren van een herstel bewerking is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Met deze bewerking wordt een andere bewerking gemaakt die afzonderlijk moet worden bijgehouden.
Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en 200 (OK) wanneer de bewerking is voltooid.

#### <a name="response-example"></a>Voorbeeld van antwoord

Wanneer u de *post* -URI voor het activeren van een herstel bewerking verzendt, wordt het eerste antwoord 202 (geaccepteerd) met een locatie header of Azure-async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation header met een GET-opdracht.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de ID van de resulterende terugzet taak in de hoofd tekst van het antwoord.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Voor herstel naar een alternatieve locatie ziet de antwoord tekst er als volgt uit:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Aangezien de back-uptaak een langlopende bewerking is, moet deze worden gevolgd zoals uitgelegd in de [taken bewaken met rest API document](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="item-level-recovery-using-rest-api"></a>Herstel op item niveau met REST API

U kunt deze terugzet optie gebruiken om afzonderlijke bestanden of mappen op de oorspronkelijke of een alternatieve locatie te herstellen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

De waarden {containerName} en {protectedItemName} zijn [zo ingesteld,](#fetch-containername-and-protecteditemname) en recoveryPointID is het veld {name} van het hierboven vermelde herstel punt.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body-for-item-level-recovery-using-rest-api"></a>Aanvraag tekst maken voor herstel op item niveau met REST API

Als u een herstel bewerking voor een Azure-bestands share wilt activeren, zijn de volgende onderdelen van de hoofd tekst van de aanvraag:

Naam |  Type   |   Beschrijving
--- | ---- | ----
Eigenschappen | AzureFileShareRestoreRequest | RestoreRequestResource-eigenschappen

Raadpleeg het [document trigger Restore rest API](/rest/api/backup/restores/trigger#request-body)voor de volledige lijst met definities van de aanvraag tekst en andere details.

### <a name="restore-to-original-location-for-item-level-recovery-using-rest-api"></a>Herstellen naar de oorspronkelijke locatie voor herstel op item niveau met behulp van REST API

De volgende aanvraag tekst is het herstellen van het *Restoretest.txt* -bestand in de *Azure files* -bestands share in de *afsaccount* -opslag account.

Hoofd tekst van aanvraag maken

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location-for-item-level-recovery-using-rest-api"></a>Herstellen naar een alternatieve locatie voor herstel op item niveau met REST API

De volgende aanvraag tekst is het herstellen van het *Restoretest.txt* -bestand in de *Azure files* -bestands share in de *afsaccount* -opslag account naar de map *restoredata* van de bestands share *azurefiles1* in het *afaccount1* -opslag account.

Hoofd tekst van aanvraag maken

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

Het antwoord moet worden afgehandeld op dezelfde manier als hierboven beschreven voor het terugzetten van de [volledige share](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van back-ups van Azure-bestands shares met rest API](manage-azure-file-share-rest-api.md).
