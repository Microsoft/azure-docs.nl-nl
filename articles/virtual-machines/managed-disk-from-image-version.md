---
title: Een beheerde schijf maken op basis van een installatie kopie versie
description: Een beheerde schijf maken op basis van een installatie kopie-versie in een galerie met gedeelde afbeeldingen.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 40d3ff736194c4634b949af52ee7b09db20dd06d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026128"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Een beheerde schijf maken op basis van een installatie kopie versie

Indien nodig kunt u het besturings systeem of één gegevens schijf exporteren uit een installatie kopie versie als een beheerde schijf van een installatie kopie versie die is opgeslagen in een galerie met gedeelde afbeeldingen.


## <a name="cli"></a>CLI

De afbeeldings versies in een galerie weer geven met de [lijst AZ sig image-version](/cli/azure/sig/image-version#az_sig_image_version_list). In dit voor beeld zoeken we naar alle installatie kopieën die deel uitmaken van de definitie van de *myImageDefinition* -installatie kopie in de galerie met installatie kopieën van *myGallery* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Stel de `source` variabele in op de id van de installatie kopie en gebruik [AZ Disk Create](/cli/azure/disk?view=azure-cli-latest#az_disk_create) om de beheerde schijf te maken. 

In dit voor beeld exporteren we de besturingssysteem schijf van de versie van de installatie kopie voor het maken van een beheerde schijf met de naam *myManagedOSDisk*, in de regio *oostus* , in een resource groep met de naam *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Als u een gegevens schijf uit de versie van de installatie kopie wilt exporteren, voegt u `--gallery-image-reference-lun` deze toe om de LUN-locatie op te geven van de gegevens schijf die u wilt exporteren. 

In dit voor beeld exporteren we de gegevens schijf die zich bevindt op LUN 0 van de versie van de installatie kopie om een beheerde schijf te maken met de naam *myManagedDataDisk*, in de regio *Oost* -eenheid, in een resource groep met de naam *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

De afbeeldings versies in een galerie weer geven met [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Wanneer u alle informatie hebt die u nodig hebt, kunt u [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) gebruiken om de versie van de bron installatie kopie die u wilt gebruiken, op te halen en toe te wijzen aan een variabele. In dit voor beeld wordt de `1.0.0` installatie kopie versie van de `myImageDefinition` definitie in de `myGallery` bron galerie in de `myResourceGroup` resource groep opgehaald.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Nadat u de `source` variabele hebt ingesteld op de id van de installatie kopie versie, gebruikt u [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) om een schijf configuratie te maken en [nieuwe-AzDisk](/powershell/module/az.compute/new-azdisk) om de schijf te maken. 

In dit voor beeld exporteren we de besturingssysteem schijf van de versie van de installatie kopie voor het maken van een beheerde schijf met de naam *myManagedOSDisk*, in de regio *oostus* , in een resource groep met de naam *myResourceGroup*. 

Een schijf configuratie maken.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Maak de schijf.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Als u een gegevens schijf wilt exporteren naar de versie van de installatie kopie, voegt u een LUN-ID toe aan de schijf configuratie om de LUN-locatie op te geven van de gegevens schijf die u wilt exporteren. 

In dit voor beeld exporteren we de gegevens schijf die zich bevindt op LUN 0 van de versie van de installatie kopie om een beheerde schijf te maken met de naam *myManagedDataDisk*, in de regio *Oost* -eenheid, in een resource groep met de naam *myResourceGroup*. 

Een schijf configuratie maken.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Maak de schijf.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Volgende stappen

U kunt ook een installatie kopie versie maken op basis van een beheerde schijf met behulp van de [Azure cli](image-version-managed-image-cli.md) of [Power shell](image-version-managed-image-powershell.md).


