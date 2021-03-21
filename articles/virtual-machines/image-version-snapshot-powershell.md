---
title: 'Power shell: een installatie kopie maken van een moment opname of een beheerde schijf in een galerie met gedeelde afbeeldingen'
description: Meer informatie over het maken van een installatie kopie van een moment opname of een beheerde schijf in een galerie met gedeelde afbeeldingen met behulp van Power shell.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 70ef194ab7f7403f31ad431eba25db5f71dfb580
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556900"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Een installatie kopie maken van een beheerde schijf of moment opname in een galerie met gedeelde afbeeldingen met behulp van Power shell

Als u een bestaande moment opname of beheerde schijf hebt die u wilt migreren naar een gedeelde installatie kopie galerie, kunt u rechtstreeks vanuit de beheerde schijf of moment opname een installatie kopie van een gedeelde installatie kopie maken. Nadat u de nieuwe installatie kopie hebt getest, kunt u de door de bron beheerde schijf of moment opname verwijderen. U kunt ook een installatie kopie maken van een beheerde schijf of moment opname in een galerie met gedeelde afbeeldingen met behulp van de [Azure cli](image-version-snapshot-cli.md).

Afbeeldingen in een afbeeldings galerie hebben twee onderdelen, die we in dit voor beeld gaan maken:
- Een **definitie van een installatie kopie** bevat informatie over de installatie kopie en vereisten voor het gebruik ervan. Dit omvat of de installatie kopie Windows of Linux, gespecialiseerde of gegeneraliseerde, release opmerkingen en minimale en maximale geheugen vereisten zijn. Het is een definitie van een type installatiekopie. 
- Een **installatie kopie versie** is wat wordt gebruikt om een virtuele machine te maken wanneer u een galerie met gedeelde afbeeldingen gebruikt. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een virtuele machine maakt, wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

U moet een moment opname of beheerde schijf hebben om dit artikel te volt ooien. 

Als u een gegevens schijf wilt toevoegen, mag de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, vervangt u de resource namen waar nodig.


## <a name="get-the-snapshot-or-managed-disk"></a>De moment opname of Managed Disk ophalen

U kunt een lijst met moment opnamen die beschikbaar zijn in een resource groep weer geven met behulp van [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot). 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

Zodra u de naam van de moment opname kent en de resource groep waarin deze zich bevindt, kunt u `Get-AzSnapshot` opnieuw gebruiken om het momentopname object op te halen en op te slaan in een variabele, zodat u het later kunt gebruiken. In dit voor beeld wordt een moment opname met de naam *mySnapshot* opgehaald uit de resource groep myResourceGroup en toegewezen aan de variabele *$Source*. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

U kunt ook een beheerde schijf gebruiken in plaats van een moment opname. Gebruik [Get-AzDisk](/powershell/module/az.compute/get-azdisk)om een beheerde schijf te verkrijgen. 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Haal vervolgens de beheerde schijf op en wijs deze toe aan de `$source` variabele.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

U kunt dezelfde cmdlets gebruiken voor het ophalen van alle gegevens schijven die u wilt toevoegen aan uw installatie kopie. Wijs ze toe aan variabelen en gebruik deze variabelen later wanneer u de versie van de installatie kopie maakt.


## <a name="get-the-gallery"></a>De galerie ophalen

U kunt alle galerieën en afbeeldings definities op naam weer geven. De resultaten hebben de indeling `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Wanneer u de juiste galerie hebt gevonden, maakt u een variabele die u later kunt gebruiken. In dit voor beeld wordt de galerie met de naam *myGallery* in de resource groep *myResourceGroup* opgehaald.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken 

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Ze worden gebruikt om informatie over de installatie kopie te beheren. Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Wanneer u de definitie van de installatie kopie maakt, moet u ervoor zorgen dat alle juiste gegevens worden verstrekt. In dit voor beeld gaan we ervan uit dat de moment opname of beheerde schijf afkomstig is van een virtuele machine die in gebruik is en niet is gegeneraliseerd. Als de beheerde schijf of moment opname is gemaakt van een gegeneraliseerd besturings systeem (na het uitvoeren van Sysprep voor Windows of [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` of `-deprovision+user` voor Linux), wijzigt u vervolgens de `-OsState` to `generalized` . 

Zie [Installatiekopiedefinities](./shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Maak de definitie van de installatiekopie met behulp van [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). In dit voor beeld heeft de definitie van de installatie kopie de naam *myImageDefinition* en is voor een gespecialiseerd Windows-besturings systeem. Als u een definitie voor installatie kopieën wilt maken met behulp van een Linux-besturings systeem, gebruikt u `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

### <a name="purchase-plan-information"></a>Aankoopplaninformatie

In sommige gevallen moet u de informatie van het aankoop plan door geven bij het maken van een virtuele machine op basis van een installatie kopie die is gebaseerd op een installatie kopie van Azure Marketplace. In deze gevallen raden we u aan om de informatie over het aankoop plan op te nemen in de definitie van de installatie kopie. In dit geval raadpleegt [u informatie over het aankoop plan van Azure Marketplace opgeven bij het maken van installatie kopieën](marketplace-images.md).


## <a name="create-an-image-version"></a>De versie van een installatiekopie maken

Maak een installatie kopie versie van de moment opname met behulp van [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

Als u wilt dat uw installatie kopie naast de besturingssysteem schijf een gegevens schijf bevat, voegt u de `-DataDiskImage` para meter toe en stelt u deze in op de id van de gegevens schijf momentopname of beheerde schijf.

In dit voor beeld is de versie van de installatie kopie *1.0.0* en wordt deze gerepliceerd naar zowel *West-Centraal VS* -en *Zuid-Centraal VS* -data centers. Houd er rekening mee dat u bij het kiezen van doel regio's voor replicatie ook de *bron* regio moet toevoegen als doel voor replicatie.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Het kan even duren om de installatie kopie te repliceren naar alle doel regio's. Daarom hebben we een taak gemaakt zodat we de voortgang kunnen volgen. Als u de voortgang van de taak wilt zien, typt u `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> U moet wachten tot de versie van de installatie kopie volledig is gebouwd en gerepliceerd voordat u dezelfde moment opname kunt gebruiken om een andere afbeeldings versie te maken. 
>
> U kunt ook uw installatie kopie versie opslaan in [zone redundante opslag](../storage/common/storage-redundancy.md) door toe te voegen `-StorageAccountType Standard_ZRS` Wanneer u de installatie kopie versie maakt.
>

## <a name="delete-the-source"></a>De bron verwijderen

Nadat u hebt gecontroleerd of de nieuwe versie van de installatie kopie correct werkt, kunt u de bron voor de installatie kopie verwijderen met behulp van [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) of [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk).


## <a name="next-steps"></a>Volgende stappen

Nadat u hebt gecontroleerd of de replicatie is voltooid, kunt u een virtuele machine maken op basis van de [gespecialiseerde installatie kopie](vm-specialized-image-version-powershell.md).

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).