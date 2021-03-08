---
title: Virtuele Azure-machines terugzetten met behulp van REST API
description: In dit artikel vindt u informatie over het beheren van herstel bewerkingen van back-ups van virtuele Azure-machines met behulp van REST API.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: da6b4cd6134f0cd1fd3d6e04e814bbf8aec9b07d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452149"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Virtuele Azure-machines herstellen met behulp van REST API

Wanneer de back-up van een virtuele machine van Azure met Azure Backup is voltooid, kunt u de volledige virtuele machines of schijven of bestanden van Azure herstellen vanaf dezelfde back-upkopie. In dit artikel wordt beschreven hoe u een virtuele machine of schijven van Azure kunt herstellen met behulp van REST API.

Bij een herstel bewerking moet eerst het relevante herstel punt worden geïdentificeerd.

## <a name="select-recovery-point"></a>Herstel punt selecteren

De beschik bare herstel punten van een back-upitem kunnen worden weer gegeven met behulp van de [lijst herstel punt rest API](/rest/api/backup/recoverypoints/list). Het is een eenvoudige *Get* -bewerking met alle relevante waarden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

De `{containerName}` en `{protectedItemName}` zijn [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)gemaakt. `{fabricName}` is ' Azure '.

De *Get* -URI heeft alle vereiste para meters. Er is geen aanvullende aanvraag tekst nodig.

### <a name="responses"></a>Antwoorden

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de *Get* -URI is verzonden, wordt een antwoord van 200 (OK) geretourneerd.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Het herstel punt wordt aangeduid met het `{name}` veld in het bovenstaande antwoord.

## <a name="restore-operations"></a>Herstel bewerkingen

Nadat u het [relevante herstel punt](#select-recovery-point)hebt geselecteerd, gaat u door met het activeren van de herstel bewerking.

***Alle herstel bewerkingen voor het back-upitem worden uitgevoerd met dezelfde *bericht* -API. Alleen de hoofd tekst van de aanvraag wordt gewijzigd met de herstel scenario's.***

> [!IMPORTANT]
> [Hier](./backup-azure-arm-restore-vms.md#restore-options)worden alle details van de verschillende opties voor terugzetten en de bijbehorende afhankelijkheden beschreven. Controleer voordat u doorgaat met het activeren van deze bewerkingen.

Het activeren van herstel bewerkingen is een *post* -aanvraag. Raadpleeg de [rest API trigger herstellen](/rest/api/backup/restores/trigger)voor meer informatie over de API.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

De `{containerName}` en `{protectedItemName}` zijn [hier](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)gemaakt. `{fabricName}` is ' Azure ' en het `{recoveryPointId}` is het `{name}` veld van het [hierboven](#example-response)vermelde herstel punt.

Zodra het herstel punt is verkregen, moeten we de aanvraag tekst voor het relevante herstel scenario maken. De volgende secties bevatten een overzicht van de aanvraag tekst voor elk scenario.

- [Schijven herstellen](#restore-disks)
- [Schijven vervangen](#replace-disks-in-a-backed-up-virtual-machine)
- [Herstellen als een nieuwe virtuele machine](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Antwoord herstellen

Het activeren van een herstel bewerking is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer deze bewerking is voltooid.

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|202 geaccepteerd     |         |     Geaccepteerd    |

#### <a name="example-responses"></a>Voorbeeld reacties

Zodra u de *post* -URI voor het activeren van herstel schijven hebt verzonden, is het eerste antwoord 202 (geaccepteerd) met een locatie header of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation koptekst met een eenvoudige *Get* -opdracht.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de ID van de resulterende terugzet taak in de hoofd tekst van het antwoord.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Omdat de herstel taak een langlopende bewerking is, moet deze worden gevolgd zoals uitgelegd in de [taken bewaken met behulp van rest API document](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Schijven herstellen

Als u het maken van een virtuele machine van de back-upgegevens wilt aanpassen, kunt u alleen schijven herstellen naar een gekozen opslag account en een virtuele machine maken op basis van de vereisten van die schijven. Het opslag account moet zich in dezelfde regio bevinden als de Recovery Services kluis en mag niet zone redundant zijn. De schijven, evenals de configuratie van de back-up van de virtuele machine (vmconfig.jsop), worden opgeslagen in het opgegeven opslag account. Zoals [hierboven](#restore-operations)is uitgelegd, wordt de relevante aanvraag tekst voor herstel schijven hieronder weer gegeven.

#### <a name="create-request-body"></a>Hoofd tekst van aanvraag maken

Als u een schijf herstel wilt activeren vanuit een back-up van een Azure-VM, volgt u de onderdelen van de hoofd tekst van de aanvraag.

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Raadpleeg voor de volledige lijst met definities van de hoofd tekst van de aanvraag en andere details verwijzen naar [herstel rest API document activeren](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Voorbeeldaanvraag

De volgende aanvraag hoofdtekst definieert eigenschappen die vereist zijn om een schijf herstel te activeren.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Schijven selectief herstellen

Als u [selectief back-ups](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)maakt van schijven, wordt de huidige lijst met back-upschijven weer gegeven in de [herstel punt samenvatting](#select-recovery-point) en het [gedetailleerde antwoord](/rest/api/backup/recoverypoints/get). U kunt ook selectieve schijven herstellen en meer informatie vindt u [hier](selective-disk-backup-restore.md#selective-disk-restore). Als u een schijf selectief wilt herstellen uit de lijst met back-upschijven, zoekt u het LUN van de schijf vanaf het herstel punt antwoord en voegt u de eigenschap **restoreDiskLunList** toe aan de [bovenstaande aanvraag tekst](#example-request) , zoals hieronder wordt weer gegeven.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Zodra u het antwoord hebt gevolgd zoals [hierboven](#responses)is uitgelegd en de langlopende taak is voltooid, zijn de schijven en de configuratie van de back-up van de virtuele machine (VMConfig.jsop) aanwezig in het opgegeven opslag account.

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Schijven vervangen in een back-up van een virtuele machine

Bij het terugzetten van schijven van het herstel punt worden schijven vervangen door de schijven van de back-up van de virtuele machine die is gemaakt met de schijf van het herstel punt. Zoals [hierboven](#restore-operations)is uitgelegd, wordt de relevante aanvraag tekst voor het vervangen van schijven hieronder vermeld.

#### <a name="create-request-body"></a>Hoofd tekst van aanvraag maken

Als u een schijf vervanging wilt activeren vanuit een back-up van een Azure-VM, volgt u de onderdelen van de hoofd tekst van de aanvraag.

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Raadpleeg voor de volledige lijst met definities van de hoofd tekst van de aanvraag en andere details verwijzen naar [herstel rest API document activeren](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Voorbeeldaanvraag

De volgende aanvraag hoofdtekst definieert eigenschappen die vereist zijn om een schijf herstel te activeren.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Herstellen als een andere virtuele machine

Zoals [hierboven](#restore-operations)is uitgelegd, definieert de volgende aanvraag tekst eigenschappen die nodig zijn om het terugzetten van een virtuele machine te activeren.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
     }
 }
```

Het antwoord moet worden afgehandeld op dezelfde manier als [hierboven beschreven voor het herstellen van schijven](#responses).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie over de Azure Backup REST-Api's:

- [REST API Azure Recovery Services provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
