---
title: Een beheerde installatie kopie migreren naar een galerie met gedeelde afbeeldingen
description: Meer informatie over het gebruik van Azure PowerShell voor het migreren van een beheerde installatie kopie naar een installatie kopie versie in een galerie met gedeelde installatie kopieën.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1c57d9d283714da6905335fd3167c4f8a69292f8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964878"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>Migreren van een beheerde installatie kopie naar een afbeelding van de galerie met gedeelde afbeeldingen

Als u een bestaande beheerde installatie kopie hebt die u wilt migreren naar een gedeelde installatie kopie galerie, kunt u rechtstreeks vanuit de beheerde installatie kopie een installatie kopie van een gedeelde installatie kopie maken. Wanneer u de nieuwe installatie kopie hebt getest, kunt u de door de bron beheerde installatie kopie verwijderen. U kunt ook migreren van een beheerde installatie kopie naar een galerie met gedeelde afbeeldingen met behulp van de [Azure cli](image-version-managed-image-cli.md).

Afbeeldingen in een afbeeldings galerie hebben twee onderdelen, die we in dit voor beeld gaan maken:
- Een **definitie van een installatie kopie** bevat informatie over de installatie kopie en vereisten voor het gebruik ervan. Dit omvat of de installatie kopie Windows of Linux, gespecialiseerde of gegeneraliseerde, release opmerkingen en minimale en maximale geheugen vereisten zijn. Het is een definitie van een type installatiekopie. 
- Een **installatie kopie versie** is wat wordt gebruikt om een virtuele machine te maken wanneer u een galerie met gedeelde afbeeldingen gebruikt. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een virtuele machine maakt, wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

U moet een bestaande beheerde installatie kopie hebben om dit artikel te volt ooien. Als de beheerde installatie kopie een gegevens schijf bevat, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, moet u de namen van de resource groep en de VM vervangen, indien nodig.

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

Wanneer u de definitie van de installatie kopie maakt, moet u ervoor zorgen dat alle juiste gegevens worden verstrekt. Omdat beheerde installatie kopieën altijd worden gegeneraliseerd, moet u instellen `-OsState generalized` . 

Zie [Installatiekopiedefinities](./windows/shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Maak de definitie van de installatiekopie met behulp van [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). In dit voor beeld wordt de definitie van de installatie kopie met de naam *myImageDefinition* en is deze voor een gegeneraliseerde Windows-besturings systeem. Als u een definitie voor installatie kopieën wilt maken met behulp van een Linux-besturings systeem, gebruikt u `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>De beheerde installatie kopie ophalen

U kunt een lijst met installatie kopieën die beschikbaar zijn in een resource groep weer geven met behulp van [Get-AzImage](/powershell/module/az.compute/get-azimage). Zodra u de naam van de installatie kopie kent en de resource groep waarin deze zich bevindt, kunt u `Get-AzImage` opnieuw gebruiken om het afbeeldings object op te halen en op te slaan in een variabele, zodat u het later kunt gebruiken. In dit voor beeld wordt een afbeelding opgehaald met de naam *myImage* uit de resource groep ' myResourceGroup ' en toegewezen aan de variabele *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>De versie van een installatiekopie maken

Maak een installatie kopie versie van de beheerde installatie kopie met behulp van [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voor beeld is de versie van de installatie kopie *1.0.0* en wordt deze gerepliceerd naar zowel *West-Centraal VS* -en *Zuid-Centraal VS* -data centers. Houd er rekening mee dat u bij het kiezen van doel regio's voor replicatie ook de *bron* regio moet toevoegen als doel voor replicatie. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Het kan even duren om de installatie kopie te repliceren naar alle doel regio's. Daarom hebben we een taak gemaakt zodat we de voortgang kunnen volgen. Als u de voortgang wilt zien, typt u `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken. 
>
> U kunt uw installatie kopie ook opslaan in Premium Storage door `-StorageAccountType Premium_LRS` toe te voegen of [zone redundante opslag](../storage/common/storage-redundancy.md) te maken door toe te voegen `-StorageAccountType Standard_ZRS` Wanneer u de versie van de installatie kopie maakt.
>

## <a name="delete-the-managed-image"></a>De beheerde installatie kopie verwijderen

Wanneer u hebt gecontroleerd of de nieuwe versie van de installatie kopie correct werkt, kunt u de beheerde installatie kopie verwijderen.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt gecontroleerd of de replicatie is voltooid, kunt u een virtuele machine maken op basis van de [gegeneraliseerde installatie kopie](vm-generalized-image-version-powershell.md).

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).
