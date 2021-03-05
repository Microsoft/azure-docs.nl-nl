---
title: Schijf bursting op aanvraag inschakelen
description: Schakel op aanvraag schijf bursting op uw beheerde schijf in.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f5865646200a783e7139bb5e22576ea404f58203
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216646"
---
# <a name="enable-on-demand-bursting"></a>Bursting op aanvraag inschakelen

Premium Solid-state drives (SSD) hebben twee beschik bare burst-modellen. op Credit gebaseerde bursting en op aanvraag bursting. In dit artikel wordt beschreven hoe u kunt overschakelen naar bursting op aanvraag. Schijven die gebruikmaken van het on-demand-model, kunnen de oorspronkelijke ingerichte doelen niet overschrijden. Op aanvraag bursting gebeurt zo vaak als nodig is voor de werk belasting, tot aan het maximum aantal burst-doelen. Bij bursting op aanvraag worden extra kosten in rekening gebracht.

Zie [Managed Disk bursting](disk-bursting.md)(Engelstalig) voor meer informatie over schijf bursting.

> [!IMPORTANT]
> U hoeft de stappen in dit artikel niet uit te voeren om burstisatie op basis van credit te gebruiken. Op Credit gebaseerde bursting is standaard ingeschakeld op alle in aanmerking komende schijven.

Voordat u bursting op aanvraag inschakelt, moet u het volgende weten:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Aan de slag

Bursting op aanvraag kan worden ingeschakeld met de module Azure PowerShell, de Azure CLI of Azure Resource Manager sjablonen. De volgende voor beelden laten zien hoe u een nieuwe schijf maakt met on-demand burstisatie ingeschakeld en op aanvraag bursting op bestaande schijven inschakelt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Op aanvraag burstive-cmdlets zijn beschikbaar in versie 5.5.0 en hoger van de AZ-module. U kunt ook de [Azure Cloud shell](https://shell.azure.com/)gebruiken.
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Een lege gegevens schijf maken met burstisatie op aanvraag

Een beheerde schijf moet groter zijn dan 512 GiB om burstisatie op aanvraag in te scha kelen. Vervang de `<myResourceGroupDisk>` `<myDataDisk>` para meters en en voer vervolgens het volgende script uit om een Premium SSD te maken met burstisatie op aanvraag:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Op aanvraag burstisatie op een bestaande schijf inschakelen

Een beheerde schijf moet groter zijn dan 512 GiB om burstisatie op aanvraag in te scha kelen. Vervang de `<myResourceGroupDisk>` `<myDataDisk>` para meters en voer deze opdracht uit om burstisatie op aanvraag in te scha kelen op een bestaande schijf:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Op aanvraag burstive-cmdlets zijn beschikbaar in versie 2.19.0 en hoger van de [Azure cli-module](https://docs.microsoft.com/cli/azure/install-azure-cli). U kunt ook de [Azure Cloud shell](https://shell.azure.com/)gebruiken.

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Een op-aanvraag bursting-gegevens schijf maken en koppelen

Een beheerde schijf moet groter zijn dan 512 GiB om burstisatie op aanvraag in te scha kelen. Vervang de `<yourDiskName>` `<yourResourceGroup>` `<yourVMName>` para meters, en en voer vervolgens de volgende opdrachten uit om een Premium SSD te maken met burstisatie op aanvraag:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Bursting op aanvraag inschakelen op een bestaande schijf-CLI

Een beheerde schijf moet groter zijn dan 512 GiB om burstisatie op aanvraag in te scha kelen. Vervang de `<myResourceGroupDisk>` `<yourDiskName>` para meters en en voer deze opdracht uit om burstisatie op aanvraag in te scha kelen op een bestaande schijf:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Met de `2020-09-30` schijf-API kunt u op aanvraag bursting inschakelen op nieuw gemaakte of bestaande Premium-ssd's die groter zijn dan 512 GiB. De `2020-09-30` API heeft een nieuwe eigenschap geïntroduceerd `burstingEnabled` . Deze eigenschap is standaard ingesteld op ONWAAR. Met de volgende voorbeeld sjabloon wordt een 1TiB Premium-SSD gemaakt in West-Centraal VS, waarbij schijf bursting is ingeschakeld:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Volgende stappen

Zie [metrische gegevens over schijf bursting](disks-metrics.md)voor meer informatie over het verkrijgen van inzicht in uw burst-resources.