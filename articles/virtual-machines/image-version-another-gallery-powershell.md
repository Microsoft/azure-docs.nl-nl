---
title: Een afbeelding uit een andere galerie kopiëren met behulp van Power shell
description: Een afbeelding uit een andere galerie kopiëren met behulp van Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d9bbe40e35bdad6fac5c5ccb0b15b909e77b938c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564013"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Een afbeelding uit een andere galerie kopiëren met behulp van Power shell

Als uw organisatie meerdere galerieën bevat, kunt u installatie kopieën maken van afbeeldingen die zijn opgeslagen in andere galerieën. U kunt bijvoorbeeld een galerie voor ontwikkelen en testen hebben voor het maken en testen van nieuwe installatie kopieën. Wanneer ze klaar zijn om te worden gebruikt in de productie, kunt u deze naar een productie galerie kopiëren met behulp van dit voor beeld. U kunt ook een installatie kopie maken van een afbeelding in een andere galerie met behulp van de [Azure cli](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Voordat u begint

Om dit artikel te volt ooien, moet u een bestaande bron galerie, afbeeldings definitie en installatie kopie versie hebben. U moet ook een doel galerie hebben. 

De versie van de bron installatie kopie moet worden gerepliceerd naar de regio waar uw doel Galerie zich bevindt. 

We gaan een nieuwe definitie van de installatie kopie en installatie kopie maken in de doel galerie.


Wanneer u dit artikel doorwerkt, vervangt u de resource namen waar nodig.


## <a name="get-the-source-image"></a>De bron installatie kopie ophalen 

U hebt gegevens nodig van de definitie van de bron installatie kopie, zodat u een kopie ervan kunt maken in de doel galerie.

Informatie weer geven over de bestaande galerieën, afbeeldings definities en installatie kopie versies met de cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) .

De resultaten hebben de indeling `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Zodra u alle informatie hebt die u nodig hebt, kunt u de ID van de versie van de bron installatie kopie ophalen met [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). In dit voor beeld wordt de `1.0.0` installatie kopie versie van de `myImageDefinition` definitie in de `myGallery` bron galerie in de `myResourceGroup` resource groep opgehaald.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>De definitie van de installatie kopie maken 

U moet een nieuwe definitie van de installatie kopie maken die overeenkomt met de definitie van de installatie kopie van de bron. U kunt alle informatie die u nodig hebt, weer geven om de definitie van de installatie kopie opnieuw te maken met behulp van [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


De uitvoer ziet er ongeveer uit zoals in dit voorbeeld:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Maak in de doel Galerie een nieuwe definitie van de installatie kopie met behulp van de cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) en de informatie uit de bovenstaande uitvoer.


In dit voor beeld heeft de definitie van de installatie kopie de naam *myDestinationImgDef* in de galerie met de naam *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een installatie kopie versie met behulp van [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). U moet de ID van de bron installatie kopie door geven in de `--managed-image` para meter voor het maken van de installatie kopie versie in de doel galerie. 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voor beeld heeft de doel galerie de naam *myDestinationGallery*, in de resource groep *myDestinationRG* , op de locatie *VS-West* . De versie van onze installatie kopie is *1.0.0* en we gaan 1 replica maken in de regio *Zuid-Centraal VS* en 2 REPLICA'S in de regio *VS West* . 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
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
> U kunt uw installatiekopie ook opslaan in Premium Storage door een `-StorageAccountType Premium_LRS` toe te voegen, of in [Zone-redundante opslag](../storage/common/storage-redundancy.md) door `-StorageAccountType Standard_ZRS` toe te voegen wanneer u de installatiekopieversie maakt.
>


## <a name="next-steps"></a>Volgende stappen

Een virtuele machine maken van een [gegeneraliseerde](vm-generalized-image-version-powershell.md) of een [gespecialiseerde](vm-specialized-image-version-powershell.md) installatie kopie versie.

Met [Azure Image Builder (preview)](./image-builder-overview.md) kunt u het maken van de installatie kopie versie automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](./linux/image-builder-gallery-update-image-version.md). 

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).