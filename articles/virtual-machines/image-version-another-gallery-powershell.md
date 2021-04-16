---
title: Een afbeelding kopiëren uit een andere galerie met behulp van PowerShell
description: Kopieer een afbeelding uit een andere galerie met behulp van Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 35346836767bc1da8c498e23fd3b42afe7a9c350
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531195"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Een afbeelding kopiëren uit een andere galerie met behulp van PowerShell

Als uw organisatie meerdere galerieën heeft, kunt u afbeeldingen maken van afbeeldingen die zijn opgeslagen in andere galerieën. U hebt bijvoorbeeld een ontwikkelings- en testgalerie voor het maken en testen van nieuwe afbeeldingen. Wanneer ze klaar zijn voor gebruik in productie, kunt u ze kopiëren naar een productiegalerie met behulp van dit voorbeeld. U kunt ook een afbeelding maken van een afbeelding in een andere galerie met behulp van [de Azure CLI](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, moet u een bestaande brongalerie, definitie van de afbeelding en versie van de afbeelding hebben. U moet ook een doelgalerie hebben. 

De versie van de bronafbeelding moet worden gerepliceerd naar de regio waar uw doelgalerie zich bevindt. 

We maken een nieuwe definitie van de afbeelding en de versie van de afbeelding in uw doelgalerie.


Wanneer u dit artikel door werkt, vervangt u waar nodig de resourcenamen.


## <a name="get-the-source-image"></a>De bronafbeelding op halen 

U hebt informatie uit de definitie van de bronafbeelding nodig, zodat u er een kopie van kunt maken in de doelgalerie.

Maak een lijst met informatie over de bestaande galerieën, definities van afbeeldingen en versies van afbeeldingen met behulp van de cmdlet [Get-AzResource.](/powershell/module/az.resources/get-azresource)

De resultaten hebben de indeling `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Zodra u alle informatie hebt die u nodig hebt, kunt u de id van de versie van de bronafbeelding verkrijgen met behulp van [Get-AzGalleryImageVersion.](/powershell/module/az.compute/get-azgalleryimageversion) In dit voorbeeld krijgen we de versie van de afbeelding, van de `1.0.0` `myImageDefinition` definitie, in de `myGallery` brongalerie, in de `myResourceGroup` resourcegroep.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>De definitie van de afbeelding maken 

U moet een nieuwe definitie van de afbeelding maken die overeenkomt met de definitie van de afbeelding van uw bron. U kunt alle informatie zien die u nodig hebt om de definitie van de afbeelding opnieuw te maken met [behulp van Get-AzGalleryImageDefinition.](/powershell/module/az.compute/get-azgalleryimagedefinition)

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

Maak een nieuwe definitie van de afbeelding in de doelgalerie met behulp van de cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) en de informatie uit de bovenstaande uitvoer.


In dit voorbeeld heet de definitie van de afbeelding *myDestinationImgDef* in de galerie met de *naam myDestinationGallery.*


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

Maak een versie van de afbeelding [met New-AzGalleryImageVersion.](/powershell/module/az.compute/new-azgalleryimageversion) U moet de id van de bronafbeelding doorgeven in de parameter voor het maken van de versie van de afbeelding `-Source` in de doelgalerie. 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld heet de doelgalerie *myDestinationGallery* in de resourcegroep *myDestinationRG* op de locatie *VS -* west. De versie van onze afbeelding is *1.0.0* en we  gaan 1 replica maken in de regio VS - zuid-centraal en 2 replica's in de regio *VS -* west. 


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

Het kan even duren voordat de afbeelding naar alle doelregio's is gerepliceerd. Daarom hebben we een taak gemaakt, zodat we de voortgang kunnen volgen. Typ om de voortgang van de taak te `$job.State` bekijken.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt uw installatiekopie ook opslaan in Premium Storage door een `-StorageAccountType Premium_LRS` toe te voegen, of in [Zone-redundante opslag](../storage/common/storage-redundancy.md) door `-StorageAccountType Standard_ZRS` toe te voegen wanneer u de installatiekopieversie maakt.
>


## <a name="next-steps"></a>Volgende stappen

Maak een VM op een [ge generaliseerde of](vm-generalized-image-version-powershell.md) [gespecialiseerde versie](vm-specialized-image-version-powershell.md) van de afbeelding.

[Azure Image Builder (preview)](./image-builder-overview.md) kan helpen bij het automatiseren van het maken van de versie van de afbeelding. U kunt deze zelfs gebruiken om een nieuwe versie van de afbeelding bij te werken en te maken van een [bestaande versie van de afbeelding.](./linux/image-builder-gallery-update-image-version.md) 

Zie Informatie over het aankoopplan leveren bij het maken van afbeeldingen voor Azure Marketplace informatie over het leveren van [aankoopplangegevens.](marketplace-images.md)
