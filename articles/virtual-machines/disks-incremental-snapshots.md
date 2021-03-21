---
title: Een incrementele momentopname maken
description: Meer informatie over incrementele moment opnamen voor beheerde schijven, met inbegrip van hoe u deze maakt met behulp van de Azure Portal, Azure PowerShell module en Azure Resource Manager.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/15/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 52e491c88d3483f21aa74f1a9f176246033bee3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735789"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Een incrementele moment opname maken voor beheerde schijven

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt Azure PowerShell gebruiken om een incrementele moment opname te maken. U hebt de nieuwste versie van Azure PowerShell nodig. de volgende opdracht wordt geïnstalleerd of de bestaande installatie wordt bijgewerkt naar de laatste:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Als dat is gebeurd, meldt u zich aan bij uw Power shell-sessie met `Connect-AzAccount` .

Als u een incrementele moment opname met Azure PowerShell wilt maken, stelt u de configuratie met [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) met de `-Incremental` para meter in en geeft u die als een variabele door aan [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) door de `-Snapshot` para meter.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

U kunt incrementele moment opnamen identificeren van dezelfde schijf met de `SourceResourceId` en de `SourceUniqueId` Eigenschappen van moment opnamen. `SourceResourceId` is de Azure Resource Manager Resource-ID van de bovenliggende schijf. `SourceUniqueId` is de waarde die wordt overgenomen van de `UniqueId` eigenschap van de schijf. Als u een schijf verwijdert en vervolgens een nieuwe schijf met dezelfde naam maakt, verandert de waarde van de `UniqueId` eigenschap.

U kunt `SourceResourceId` en gebruiken `SourceUniqueId` om een lijst te maken met alle moment opnamen die zijn gekoppeld aan een bepaalde schijf. Vervang door `<yourResourceGroupNameHere>` uw waarde en u kunt het volgende voor beeld gebruiken om uw bestaande incrementele moment opnamen weer te geven:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Resource Manager-sjabloon](#tab/azure-resource-manager)

U kunt ook Azure Resource Manager sjablonen gebruiken om een incrementele moment opname te maken. U moet ervoor zorgen dat de apiVersion is ingesteld op **2019-03-01** en dat de incrementele eigenschap ook is ingesteld op True. Het volgende code fragment is een voor beeld van het maken van een incrementele moment opname met Resource Manager-sjablonen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-Managed disks back-ups kopiëren naar een andere regio met differentiële mogelijkheden van incrementele moment opnamen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)als u voorbeeld code wilt zien met behulp van de differentiële mogelijkheden van incrementele moment opnamen.
