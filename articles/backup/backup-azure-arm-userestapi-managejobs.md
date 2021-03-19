---
title: Back-uptaken beheren met REST API
description: In dit artikel leert u hoe u back-up-en herstel taken van Azure Backup kunt bijhouden en beheren met behulp van REST API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: ced0e0020fe955734bf6cc767480fbadd6eaffc1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88890277"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Back-up-en herstel taken bijhouden met behulp van REST API

Azure Backup-service activeert taken die op de achtergrond worden uitgevoerd in verschillende scenario's, zoals het activeren van back-ups, herstel bewerkingen, het uitschakelen van back-ups. Deze taken kunnen worden gevolgd met hun Id's.

## <a name="fetch-job-information-from-operations"></a>Taak gegevens ophalen uit bewerkingen

Een bewerking zoals het activeren van een back-up retourneert altijd een jobID. Bijvoorbeeld: de laatste reactie van een [trigger back-up rest API bewerking](backup-azure-arm-userestapi-backupazurevms.md#example-responses-for-on-demand-backup) is als volgt:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

De Azure VM-back-uptaak wordt [aangeduid met het](/rest/api/backup/jobdetails/) veld jobId en kan worden gevolgd door een eenvoudige *Get* -aanvraag.

## <a name="tracking-the-job"></a>De taak bijhouden

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

De `{jobName}` is de ' jobId ' die hierboven wordt vermeld. Het antwoord is altijd 200 OK met het veld Status om de huidige status van de taak aan te geven. Zodra het ' voltooid ' of ' CompletedWithWarnings ' is, toont de sectie ' extendedInfo ' meer informatie over de taak.

### <a name="response"></a>Antwoord

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     | [JobResource](/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de *Get* -URI is verzonden, wordt een antwoord van 200 (OK) geretourneerd.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
