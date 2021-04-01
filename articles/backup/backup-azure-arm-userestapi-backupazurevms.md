---
title: Back-ups van virtuele Azure-machines maken met REST API
description: In dit artikel vindt u informatie over het configureren, initiëren en beheren van back-upbewerkingen van Azure VM-back-ups met behulp van REST API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92171450"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Maak een back-up van een Azure-VM met behulp van Azure Backup via REST API

In dit artikel wordt beschreven hoe u back-ups beheert voor een Azure-VM met behulp van Azure Backup via REST API. Configureer beveiliging voor de eerste keer voor een eerder onbeveiligde Azure-VM, Activeer een back-up op aanvraag voor een beveiligde Azure-VM en wijzig de back-upeigenschappen van een back-up van een virtuele machine via REST API zoals hier wordt uitgelegd.

Raadpleeg voor het maken van de [kluis](backup-azure-arm-userestapi-createorupdatevault.md) en het [maken van beleid](backup-azure-arm-userestapi-createorupdatepolicy.md) rest API zelf studies voor het maken van nieuwe kluizen en beleids regels.

We gaan ervan uit dat u een VM ' testVM ' wilt beveiligen onder een resource groep ' testRG ' in een Recovery Services kluis ' testVault ', die in de resource groep ' testVaultRG ' aanwezig is, met het standaard beleid (met de naam ' Defaultpolicy bij ').

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Back-up configureren voor een niet-beveiligde Azure-VM met behulp van REST API

### <a name="discover-unprotected-azure-vms"></a>Niet-beveiligde Azure-Vm's detecteren

Ten eerste moet de kluis de virtuele machine van Azure kunnen identificeren. Dit wordt geactiveerd met behulp van de [vernieuwings bewerking](/rest/api/backup/protectioncontainers/refresh). Het is een asynchrone *post*  -bewerking die ervoor zorgt dat de kluis de meest recente lijst met alle niet-beveiligde virtuele machines in het huidige abonnement en ' caches ' in de sjabloon krijgt. Zodra de virtuele machine in de cache is opgeslagen, hebben de Recovery Services toegang tot de virtuele machine en kunnen deze worden beveiligd.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

De post-URI heeft `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` , `{fabricName}` para meters. De `{fabricName}` is ' Azure '. Volgens ons voor beeld `{vaultName}` is ' testVault ' en `{vaultresourceGroupName}` is ' testVaultRG '. Aangezien alle vereiste para meters in de URI worden opgegeven, is er geen afzonderlijke aanvraag tekst nodig.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>Reacties op de vernieuwings bewerking

De bewerking vernieuwen is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer deze bewerking is voltooid.

|Naam  |Type  |Description  |
|---------|---------|---------|
|204 geen inhoud     |         |  OK zonder geretourneerde inhoud      |
|202 geaccepteerd     |         |     Geaccepteerd    |

##### <a name="example-responses-to-refresh-operation"></a>Voorbeeld reacties voor de vernieuwings bewerking

Zodra de *post* -aanvraag is verzonden, wordt een antwoord van 202 (geaccepteerd) geretourneerd.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

De resulterende bewerking met behulp van de header ' Location ' met een eenvoudige *Get* -opdracht bijhouden

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Zodra alle virtuele Azure-machines zijn gedetecteerd, retourneert de GET-opdracht een reactie van 204 (geen inhoud). De kluis kan nu alle virtuele machines in het abonnement detecteren.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>De relevante Azure-VM selecteren

 U kunt controleren of ' caching ' wordt uitgevoerd door [alle Beveilig bare items](/rest/api/backup/backupprotectableitems/list) onder het abonnement te vermelden en de gewenste vm in het antwoord te vinden. [Het antwoord van deze bewerking](#example-responses-to-get-operation) geeft u informatie over de manier waarop Recovery Services een virtuele machine identificeert.  Zodra u vertrouwd bent met het patroon, kunt u deze stap overs Laan en direct door gaan met het inschakelen van de [beveiliging](#enabling-protection-for-the-azure-vm).

Deze bewerking is een *Get* -bewerking.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

De *Get* -URI heeft alle vereiste para meters. Er is geen aanvullende aanvraag tekst nodig.

#### <a name="responses-to-get-operation"></a>Antwoorden op Get-bewerking

|Naam  |Type  |Description  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Voorbeeld antwoorden op Get-bewerking

Zodra de *Get* -aanvraag is ingediend, wordt een antwoord van 200 (OK) geretourneerd.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Het aantal waarden in een *Get* -antwoord is beperkt tot 200 voor een ' page '. Gebruik het veld ' nextLink ' om de URL voor de volgende set antwoorden op te halen.

Het antwoord bevat de lijst met alle niet-beveiligde virtuele machines van Azure en `{value}` bevat alle informatie die nodig is voor de Azure Recovery-service voor het configureren van de back-up. Als u een back-up wilt configureren, noteert u het `{name}` veld en het `{virtualMachineId}` veld in het `{properties}` gedeelte. Maak twee variabelen van deze veld waarden zoals hieronder wordt vermeld.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "VM;" + `{name}`
- `{virtualMachineId}`wordt later in [de hoofd tekst van de aanvraag](#example-request-body) gebruikt

In het voor beeld worden de bovenstaande waarden vertaald naar:

- containerName = "iaasvmcontainer; iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "VM; iaasvmcontainerv2; testRG; testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Beveiliging inschakelen voor de Azure VM

Selecteer het beleid dat u wilt beveiligen nadat de relevante VM in de cache is opgeslagen en geïdentificeerd. Raadpleeg [API voor lijst beleid](/rest/api/backup/backuppolicies/list)voor meer informatie over bestaande beleids regels in de kluis. Selecteer vervolgens het [relevante beleid](/rest/api/backup/protectionpolicies/get) door te verwijzen naar de naam van het beleid. Zie [zelf studie beleid maken](backup-azure-arm-userestapi-createorupdatepolicy.md)voor het maken van beleid. ' Defaultpolicy bij ' is geselecteerd in het onderstaande voor beeld.

Het inschakelen van beveiliging is een asynchrone *put* -bewerking die een ' beveiligd item ' maakt.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

De `{containerName}` en `{protectedItemName}` zijn eerder gemaakt. De `{fabricName}` is ' Azure '. In ons voor beeld is dit van toepassing op:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>De aanvraag tekst maken

Als u een beveiligd item wilt maken, volgt u de onderdelen van de hoofd tekst van de aanvraag.

|Naam  |Type  |Description  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem-resource-eigenschappen         |

Raadpleeg voor de volledige lijst met definities van de hoofd tekst van de aanvraag en andere details het gedeelte [beveiligde items maken rest API document](/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Voorbeeld aanvraag tekst

De volgende aanvraag hoofdtekst definieert eigenschappen die vereist zijn voor het maken van een beveiligd item.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

De `{sourceResourceId}` `{virtualMachineId}` hierboven vermelde is van het antwoord op het [aanbieden van Beveilig bare items](#example-responses-to-get-operation).

#### <a name="responses-to-create-protected-item-operation"></a>Antwoorden om een beveiligde item bewerking te maken

Het maken van een beveiligd item is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer deze bewerking is voltooid.

|Naam  |Type  |Description  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 geaccepteerd     |         |     Geaccepteerd    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Voorbeeld antwoorden voor het maken van een beveiligde item bewerking

Zodra u de *put* -aanvraag voor het maken of bijwerken van beveiligde items verzendt, wordt het eerste antwoord 202 (geaccepteerd) met een locatie header of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation koptekst met een eenvoudige *Get* -opdracht.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de inhoud van het beveiligde item in de antwoord tekst.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Hiermee wordt bevestigd dat de beveiliging is ingeschakeld voor de virtuele machine en de eerste back-up wordt geactiveerd volgens het beleids schema.

### <a name="excluding-disks-in-azure-vm-backup"></a>Schijven uitsluiten in back-ups van Azure VM

Azure Backup biedt ook een manier om een back-up te maken van een subset schijven in azure VM. Meer informatie vindt u [hier](selective-disk-backup-restore.md). Als u tijdens het inschakelen van de beveiliging een selectief aantal schijven wilt maken, moet het volgende code fragment de [hoofd tekst](#example-request-body)van de aanvraag zijn tijdens het inschakelen van de beveiliging.

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

De lijst met schijven waarvan een back-up moet worden gemaakt, vindt u in de sectie uitgebreide eigenschappen van de hoofd tekst van de aanvraag.

|Eigenschap  |Waarde  |
|---------|---------|
|diskLunList     | De lijst schijf LUN bevat een lijst met *lun's van gegevens schijven*. **Er wordt altijd een back-up van de besturingssysteem schijf gemaakt en deze hoeft niet te worden vermeld**.        |
|IsInclusionList     | Moet **waar** zijn voor de lun's die moeten worden opgenomen tijdens het maken van een back-up. Als de waarde **False** is, worden de bovengenoemde lun's uitgesloten.         |

Als de nood zaak echter alleen een back-up van de besturingssysteem schijf maakt, moeten _alle_ gegevens schijven worden uitgesloten. Een eenvoudige manier om te zeggen dat er geen gegevens schijven moeten worden opgenomen. De schijf LUN-lijst is dus leeg en de **IsInclusionList** is **waar**. U kunt ook zien wat de eenvoudigste manier is om een subset te selecteren: een paar schijven moet altijd worden uitgesloten of een paar schijven moeten altijd worden opgenomen. Kies de LUN-lijst en de waarde van de Booleaanse variabele dienovereenkomstig.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Een back-up op aanvraag activeren voor een beveiligde Azure VM

Zodra een virtuele machine van Azure is geconfigureerd voor back-up, worden er back-ups gemaakt volgens het beleids schema. U kunt wachten op de eerste geplande back-up of op elk gewenst moment een back-up op aanvraag activeren. Retentie voor back-ups op aanvraag is gescheiden van de Bewaar termijn van het back-upbeleid en kan worden opgegeven voor een bepaalde datum/tijd. Als u niets opgeeft, wordt ervan uitgegaan dat deze 30 dagen na de dag van de trigger voor back-up op aanvraag is.

Het activeren van een back-up op aanvraag is een *post* -bewerking.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

De `{containerName}` en `{protectedItemName}` zijn [eerder](#responses-to-get-operation)gemaakt. De `{fabricName}` is ' Azure '. In ons voor beeld is dit van toepassing op:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>De aanvraag tekst voor back-ups op aanvraag maken

Als u een back-up op aanvraag wilt activeren, volgt u de onderdelen van de hoofd tekst van de aanvraag.

|Naam  |Type  |Description  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource-eigenschappen         |

Raadpleeg [back-ups activeren voor beveiligde items rest API document](/rest/api/backup/backups/trigger#request-body)voor een volledige lijst met definities van de aanvraag tekst en andere details.

#### <a name="example-request-body-for-on-demand-backup"></a>Voor beeld van aanvraag tekst voor back-ups op aanvraag

De volgende aanvraag hoofdtekst definieert eigenschappen die vereist zijn voor het activeren van een back-up voor een beveiligd item. Als de retentie niet is opgegeven, wordt deze 30 dagen na het tijdstip van de trigger van de back-uptaak bewaard.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Antwoorden voor back-ups op aanvraag

Het activeren van een back-up op aanvraag is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer deze bewerking is voltooid.

|Naam  |Type  |Description  |
|---------|---------|---------|
|202 geaccepteerd     |         |     Geaccepteerd    |

#### <a name="example-responses-for-on-demand-backup"></a>Voor beelden van antwoorden op back-ups op aanvraag

Zodra u de *post* -aanvraag voor een back-up op aanvraag hebt verzonden, is het eerste antwoord 202 (geaccepteerd) met een locatie header of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation koptekst met een eenvoudige *Get* -opdracht.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de ID van de resulterende back-uptaak in de hoofd tekst van het antwoord.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Omdat de back-uptaak een langlopende bewerking is, moet deze worden gevolgd zoals uitgelegd in de [taken bewaken met behulp van rest API document](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>De back-upconfiguratie voor een beveiligde Azure-VM wijzigen

### <a name="changing-the-policy-of-protection"></a>Het beveiligings beleid wijzigen

Als u het beleid wilt wijzigen waarmee de virtuele machine wordt beveiligd, kunt u dezelfde indeling gebruiken als voor het inschakelen van de [beveiliging](#enabling-protection-for-the-azure-vm). Geef de nieuwe beleids-ID op in [de hoofd tekst van de aanvraag](#example-request-body) en verzend de aanvraag. Bijvoorbeeld: als u het beleid van testVM van Defaultpolicy bij wilt wijzigen in ProdPolicy, geeft u de ID ' ProdPolicy ' op in de aanvraag tekst.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Het antwoord heeft dezelfde indeling als vermeld voor het [inschakelen van beveiliging](#responses-to-create-protected-item-operation)

#### <a name="excluding-disks-during-azure-vm-protection"></a>Schijven uitsluiten tijdens Azure VM-beveiliging

Als er al een back-up van de virtuele machine van Azure wordt gemaakt, kunt u de lijst met schijven opgeven waarvan u een back-up wilt maken of die moet worden uitgesloten door het beveiligings beleid te wijzigen. De aanvraag alleen voorbereiden in dezelfde indeling als [schijven uitsluiten tijdens het inschakelen van de beveiliging](#excluding-disks-in-azure-vm-backup)

> [!IMPORTANT]
> De bovenstaande hoofd tekst van de aanvraag is altijd de laatste kopie van de gegevens schijven die moeten worden uitgesloten of opgenomen. Hiermee wordt de vorige configuratie niet *toegevoegd* . Bijvoorbeeld: als u de beveiliging voor het eerst bijwerkt als ' gegevens schijf 1 uitsluiten ' en vervolgens herhalen met ' gegevens schijf 2 uitsluiten ', *wordt alleen gegevens schijf 2 uitgesloten* in de volgende back-ups en gegevens schijf 1 worden opgenomen. Dit is altijd de laatste lijst die wordt opgenomen/uitgesloten in de volgende back-ups.

Als u de huidige lijst met schijven die zijn uitgesloten of opgenomen, wilt ophalen, moet u de informatie over beveiligde items ophalen, zoals [hier](/rest/api/backup/protecteditems/get)wordt vermeld. In het antwoord wordt de lijst met gegevens schijf-Lun's verstrekt en wordt aangegeven of deze zijn opgenomen of uitgesloten.

### <a name="stop-protection-but-retain-existing-data"></a>Beveiliging stoppen, maar bestaande gegevens behouden

Verwijder het beleid in de hoofd tekst van de aanvraag en verzend de aanvraag om de beveiliging op een beveiligde virtuele machine te verwijderen, maar de gegevens waarvan al een back-up is gemaakt, te bewaren. Zodra de koppeling met het beleid wordt verwijderd, worden er geen back-ups meer geactiveerd en worden er geen nieuwe herstel punten gemaakt.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Het antwoord heeft dezelfde indeling als vermeld voor het [activeren van een back-up op aanvraag](#example-responses-for-on-demand-backup). De resulterende taak moet worden bijgehouden, zoals wordt uitgelegd in de [taken bewaken met behulp van rest API document](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Beveiliging stoppen en gegevens verwijderen

Als u de beveiliging op een beveiligde virtuele machine wilt verwijderen en ook de back-upgegevens wilt verwijderen, moet u een Verwijder bewerking uitvoeren, zoals [hier](/rest/api/backup/protecteditems/delete)wordt beschreven.

Het stoppen van de beveiliging en het verwijderen van gegevens is een *Verwijder* bewerking.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

De `{containerName}` en `{protectedItemName}` zijn [eerder](#responses-to-get-operation)gemaakt. `{fabricName}` is ' Azure '. In ons voor beeld is dit van toepassing op:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Antwoorden voor verwijderings beveiliging

Beveiliging *verwijderen* is een [asynchrone bewerking](../azure-resource-manager/management/async-operations.md). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 204 (geen inhoud) wanneer deze bewerking is voltooid.

|Naam  |Type  |Description  |
|---------|---------|---------|
|204-tekst     |         |  Geen inhoud       |
|202 geaccepteerd     |         |     Geaccepteerd    |

> [!IMPORTANT]
> Als u wilt beveiligen tegen onbedoelde verwijderings scenario's, is er een functie voor het [voorlopig verwijderen](use-restapi-update-vault-properties.md#soft-delete-state) van Recovery Services kluis beschikbaar. Als de voorlopig verwijderings status van de kluis is ingesteld op ingeschakeld, worden de gegevens niet meteen verwijderd met de verwijderings bewerking. Het wordt 14 dagen bewaard en vervolgens permanent verwijderd. Er worden geen kosten in rekening gebracht voor de opslag voor deze periode van 14 dagen. Als u de verwijderings bewerking ongedaan wilt maken, raadpleegt u de [sectie ongedaan maken-verwijderen](#undo-the-deletion).

### <a name="undo-the-deletion"></a>De verwijdering ongedaan maken

Onbedoeld verwijderen is vergelijkbaar met het maken van het back-upitem. Nadat de verwijdering ongedaan is gemaakt, wordt het item behouden, maar worden er geen toekomstige back-ups geactiveerd.

Verwijderen ongedaan maken is een *put* -bewerking die veel lijkt op [het wijzigen van het beleid](#changing-the-policy-of-protection) en/of [het inschakelen van de beveiliging](#enabling-protection-for-the-azure-vm). Geef de bedoeling op om de verwijdering ongedaan te maken met de variabele *isRehydrate*  in [de hoofd tekst van de aanvraag](#example-request-body) en dien de aanvraag in. Bijvoorbeeld: om het verwijderen van testVM ongedaan te maken, moet de volgende aanvraag tekst worden gebruikt.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Het antwoord heeft dezelfde indeling als vermeld voor het [activeren van een back-up op aanvraag](#example-responses-for-on-demand-backup). De resulterende taak moet worden bijgehouden, zoals wordt uitgelegd in de [taken bewaken met behulp van rest API document](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Volgende stappen

[Gegevens herstellen vanuit een back-up van een virtuele Azure-machine](backup-azure-arm-userestapi-restoreazurevms.md).

Raadpleeg de volgende documenten voor meer informatie over de Azure Backup REST-Api's:

- [REST API Azure Recovery Services provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)