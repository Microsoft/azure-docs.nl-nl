---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 32978257c3e209dc78a29c6e8ae0d1c4ae016a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561045"
---
## <a name="update-resources"></a>Resources bijwerken

Er zijn enkele beperkingen ten aanzien van wat er kan worden bijgewerkt. De volgende items kunnen worden bijgewerkt: 

Galerie met gedeelde installatiekopieën:
- Beschrijving

Definitie van installatiekopie:
- Aanbevolen vCPU's
- Aanbevolen geheugen
- Beschrijving
- Datum einde levensduur

Versie van installatiekopie:
- Aantal regionale replica's
- Doelregio's
- Uitsluiting van laatste
- Datum einde levensduur

Als u van plan bent om replica regio's toe te voegen, moet u de door de bron beheerde installatie kopie niet verwijderen. De door de bron beheerde installatie kopie is nodig voor het repliceren van de installatie kopie versie naar extra regio's. 

Als u de beschrijving van een galerie wilt bijwerken, gebruikt u [Update-AzGallery](/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

In dit voor beeld ziet u hoe u [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) gebruikt om de einddatum datum bij te werken voor de definitie van de installatie kopie.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

In dit voor beeld ziet u hoe u met [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) de versie van de installatie kopie kunt uitsluiten van gebruik als de *nieuwste* afbeelding.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

In dit voor beeld ziet u hoe u met [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) deze versie van de installatie kopie opneemt in overweging voor de *nieuwste* afbeelding.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>Resources opschonen

Bij het verwijderen van resources moet u beginnen met het laatste item in de geneste resources, de versie van de installatie kopie. Zodra de versies zijn verwijderd, kunt u de definitie van de installatie kopie verwijderen. U kunt de galerie pas verwijderen als alle onderliggende resources zijn verwijderd.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```