---
title: Een installatiekopie van een VM maken
description: Meer informatie over het gebruik van Azure PowerShell voor het maken van een installatie kopie in een galerie met gedeelde installatie kopieën van een bestaande virtuele machine in Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1a0bfb79b03b778696faef29597cabbcff47e6df
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601662"
---
# <a name="create-an-image-from-a-vm"></a>Een installatiekopie van een VM maken

Als u een bestaande VM hebt die u wilt gebruiken om meerdere, identieke Vm's te maken, kunt u die VM gebruiken om een installatie kopie in een galerie met gedeelde afbeeldingen te maken met behulp van Azure PowerShell. U kunt ook een installatie kopie van een virtuele machine maken met behulp van de [Azure cli](image-version-vm-cli.md).

U kunt een installatie kopie van zowel [gespecialiseerde als gegeneraliseerde](./windows/shared-image-galleries.md#generalized-and-specialized-images) vm's vastleggen met behulp van Azure PowerShell. 

Afbeeldingen in een afbeeldings galerie hebben twee onderdelen, die we in dit voor beeld gaan maken:
- Een **definitie van een installatie kopie** bevat informatie over de installatie kopie en vereisten voor het gebruik ervan. Dit omvat of de installatie kopie Windows of Linux, gespecialiseerde of gegeneraliseerde, release opmerkingen en minimale en maximale geheugen vereisten zijn. Het is een definitie van een type installatiekopie. 
- Een **installatie kopie versie** is wat wordt gebruikt om een virtuele machine te maken wanneer u een galerie met gedeelde afbeeldingen gebruikt. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een virtuele machine maakt, wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

Om dit artikel te volt ooien, moet u een bestaande galerie met gedeelde installatie kopieën hebben en een bestaande virtuele machine in azure gebruiken als bron. 

Als er een gegevens schijf aan de virtuele machine is gekoppeld, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, vervangt u de resource namen waar nodig.


## <a name="get-the-gallery"></a>De galerie ophalen

U kunt alle galerieën en afbeeldings definities op naam weer geven. De resultaten hebben de indeling `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Wanneer u de juiste galerie en afbeeldings definities hebt gevonden, kunt u variabelen maken die u later kunt gebruiken. In dit voor beeld wordt de galerie met de naam *myGallery* in de resource groep *myResourceGroup* opgehaald.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>De VM ophalen

U met behulp van [Get-AzVM](/powershell/module/az.compute/get-azvm) een lijst met VM's weergeven die beschikbaar zijn in een resourcegroep. Zodra u de naam van de virtuele machine kent en de resource groep waarin deze zich bevindt, kunt u `Get-AzVM` opnieuw gebruiken om het VM-object op te halen en op te slaan in een variabele, zodat u deze later kunt gebruiken. In dit voor beeld wordt een virtuele machine met de naam *sourceVM* van de resource groep ' myResourceGroup ' opgehaald en toegewezen aan de variabele *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

Het is een best practice voor het stop\deallocate van de virtuele machine voordat u een installatie kopie maakt met behulp van [Stop-AzVM](/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken 

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Ze worden gebruikt om informatie over de installatie kopie te beheren. Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Wanneer u de definitie van de installatie kopie maakt, moet u ervoor zorgen dat alle juiste gegevens worden verstrekt. Als u de virtuele machine hebt gegeneraliseerd (met behulp van Sysprep voor Windows of waagent-deprovision voor Linux), moet u een definitie van een installatie kopie maken met `-OsState generalized` . Als u de virtuele machine niet hebt gegeneraliseerd, maakt u een definitie van een installatie kopie met `-OsState specialized` .

Zie [Installatiekopiedefinities](./windows/shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Maak de definitie van de installatiekopie met behulp van [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

In dit voor beeld heeft de definitie van de installatie kopie de naam *myImageDefinition* en is voor een gespecialiseerde virtuele machine waarop Windows wordt uitgevoerd. Als u een definitie wilt maken voor installatie kopieën met Linux, gebruikt u `-OsType Linux` . 

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


## <a name="create-an-image-version"></a>De versie van een installatiekopie maken

Maak een installatie kopie versie met behulp van [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voor beeld is de versie van de installatie kopie *1.0.0* en wordt deze gerepliceerd naar zowel *West-Centraal VS* -en *Zuid-Centraal VS* -data centers. Houd er rekening mee dat u bij het kiezen van doel regio's voor replicatie ook de *bron* regio moet toevoegen als doel voor replicatie.

Als u een installatiekopieversie wilt maken op basis van de virtuele machine, gebruikt u `$vm.Id.ToString()` voor de `-SourceImageId`.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Het kan even duren om de installatie kopie te repliceren naar alle doel regio's. Daarom hebben we een taak gemaakt zodat we de voortgang kunnen volgen. Als u de voortgang van de taak wilt zien, typt u `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt uw installatie kopie ook opslaan in Premium Storage door `-StorageAccountType Premium_LRS` toe te voegen of [zone redundante opslag](../storage/common/storage-redundancy.md) te maken door toe te voegen `-StorageAccountType Standard_ZRS` Wanneer u de versie van de installatie kopie maakt.
>

## <a name="next-steps"></a>Volgende stappen

Wanneer u hebt gecontroleerd of de nieuwe versie van de installatie kopie correct werkt, kunt u een virtuele machine maken. Maak een virtuele machine op basis van een [gespecialiseerde installatie kopie versie](vm-specialized-image-version-powershell.md) of een [gegeneraliseerde installatie kopie versie](vm-generalized-image-version-powershell.md).

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).
