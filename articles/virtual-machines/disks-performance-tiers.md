---
title: De prestaties van Azure Managed disks wijzigen-CLI/Power shell
description: Meer informatie over het wijzigen van prestatie lagen voor bestaande beheerde schijven met behulp van de Azure PowerShell-module of de Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 161aafce1c04e5d09cf08529bcbf1baf6b8a86b1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674928"
---
# <a name="change-your-performance-tier-using-the-azure-powershell-module-or-the-azure-cli"></a>Uw prestatie niveau wijzigen met behulp van de Azure PowerShell-module of de Azure CLI

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Een lege gegevens schijf maken met een hogere laag dan de basislijn laag

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Een besturingssysteem schijf maken met een hogere laag dan de basislijn laag van een Azure Marketplace-installatie kopie

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>De laag van een schijf bijwerken

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>De laag van een schijf weer geven

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>De prestatie laag van een schijf zonder downtime wijzigen (preview)

U kunt de prestatie tier ook zonder downtime wijzigen. u hoeft de toewijzing van de virtuele machine niet ongedaan te maken of uw schijf te ontkoppelen om de laag te wijzigen. Zie de sectie [prestatie niveau wijzigen zonder downtime (preview)](#changing-performance-tier-without-downtime-preview) voor meer informatie en de registratie koppeling voor de preview-versie.


Met het volgende script wordt de laag van een schijf die hoger is dan de basislijn laag, bijgewerkt met behulp van de voorbeeld sjabloon [CreateUpdateDataDiskWithTier.jsop](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json). Vervang `<yourSubScriptionID>` ,,, `<yourResourceGroupName>` `<yourDiskName>` `<yourDiskSize>` en `<yourDesiredPerformanceTier>` Voer het script uit:

 ```cli
subscriptionId=<yourSubscriptionID>
resourceGroupName=<yourResourceGroupName>
diskName=<yourDiskName>
diskSize=<yourDiskSize>
performanceTier=<yourDesiredPerformanceTier>
region=EastUS2EUAP

 az login

 az account set --subscription $subscriptionId

 az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
--parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
```

Het kan tot vijf tien minuten duren voordat een wijziging in de laag is voltooid. Als u wilt controleren of de schijf is gewijzigd, gebruikt u de volgende opdracht:

```cli
az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-12-01 --query [properties.tier] -o tsv
```

## <a name="next-steps"></a>Volgende stappen

Als u de grootte van een schijf moet wijzigen om te profiteren van de hogere prestatie lagen, raadpleegt u de volgende artikelen:

- [Virtuele harde schijven op een Linux VM uitbreiden met de Azure CLI](linux/expand-disks.md)
- [Een beheerde schijf uitbreiden die is gekoppeld aan een virtuele Windows-machine](windows/expand-os-disk.md)
