---
title: Schijf bursting op aanvraag inschakelen
description: Schakel bursting van schijf op aanvraag in op uw beheerde schijf.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5110e580bada7bb1090b17d6df22a9354622e8e4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483143"
---
# <a name="enable-on-demand-bursting"></a>Bursting op aanvraag inschakelen

Premium SSD-schijven (Solid-State Drives) hebben twee beschikbare bursting-modellen; bursting op basis van krediet en bursting op aanvraag. In dit artikel wordt beschreven hoe u overschakelt naar bursting op aanvraag. Schijven die gebruikmaken van het on-demand model kunnen bursten boven hun oorspronkelijke inrichtende doelen. Bursting op aanvraag vindt zo vaak plaats als nodig is voor de workload, tot het maximale burstdoel. Voor bursting op aanvraag worden extra kosten in rekening gebracht.

Zie Bursting van beheerde schijven voor meer informatie over [het bursten van schijven.](disk-bursting.md)

> [!IMPORTANT]
> U hoeft de stappen in dit artikel niet te volgen om bursting op basis van tegoeden te gebruiken. Standaard is bursting op basis van tegoeden ingeschakeld op alle in aanmerking komende schijven.

Voordat u bursting op aanvraag inschakelen, moet u het volgende begrijpen:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Aan de slag

Bursting op aanvraag kan worden ingeschakeld met de Azure PowerShell-module, de Azure CLI of Azure Resource Manager sjablonen. De volgende voorbeelden hebben betrekking op het maken van een nieuwe schijf met bursting op aanvraag ingeschakeld en het inschakelen van bursting op aanvraag op bestaande schijven.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Cmdlets voor bursting op aanvraag zijn beschikbaar in versie 5.5.0 en hoger van de Az-module. U kunt ook de [Azure Cloud Shell.](https://shell.azure.com/)
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Een lege gegevensschijf maken met bursting op aanvraag

Een beheerde schijf moet groter zijn dan 512 GiB om bursting op aanvraag mogelijk te maken. Vervang de parameters en en voer vervolgens het volgende script uit om een premium SSD te `<myResourceGroupDisk>` maken met `<myDataDisk>` bursting op aanvraag:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Bursting op aanvraag inschakelen op een bestaande schijf

Een beheerde schijf moet groter zijn dan 512 GiB om bursting op aanvraag mogelijk te maken. Vervang de parameters en voer deze opdracht uit om bursting op aanvraag in te stellen `<myResourceGroupDisk>` `<myDataDisk>` op een bestaande schijf:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

On-demand bursting-cmdlets zijn beschikbaar in versie 2.19.0 en nieuwer van de [Azure CLI-module](/cli/azure/install-azure-cli). U kunt ook de [Azure Cloud Shell.](https://shell.azure.com/)

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Een bursting-gegevensschijf op aanvraag maken en koppelen

Een beheerde schijf moet groter zijn dan 512 GiB om bursting op aanvraag mogelijk te maken. Vervang de parameters , en en voer vervolgens de volgende opdrachten uit om een Premium SSD te maken `<yourDiskName>` `<yourResourceGroup>` met `<yourVMName>` bursting op aanvraag:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Bursting op aanvraag inschakelen op een bestaande schijf - CLI

Een beheerde schijf moet groter zijn dan 512 GiB om bursting op aanvraag mogelijk te maken. Vervang de `<myResourceGroupDisk>` parameters en en voer deze opdracht uit om bursting op aanvraag in te stellen op een bestaande `<yourDiskName>` schijf:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Met de schijf-API kunt u bursting op aanvraag inschakelen voor nieuw gemaakte of bestaande Premium-SD's die groter zijn `2020-09-30` dan 512 GiB. De `2020-09-30` API heeft een nieuwe eigenschap geïntroduceerd, `burstingEnabled` . Deze eigenschap is standaard ingesteld op onwaar. Met de volgende voorbeeldsjabloon maakt u een 1TiB Premium SSD in VS - west-centraal, met schijf bursting ingeschakeld:

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

Zie Metrische gegevens over schijf bursting voor meer informatie over het verkrijgen van inzicht in uw [bursting-resources.](disks-metrics.md)
