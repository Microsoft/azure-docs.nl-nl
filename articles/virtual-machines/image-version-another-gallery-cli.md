---
title: Een afbeeldings versie kopiëren uit een andere galerie met de CLI
description: Kopieer een installatie kopie versie vanuit een andere galerie met de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 0bea4fbac062b498dabe04e6e58d530d09b16d6d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553099"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Een afbeelding uit een andere galerie kopiëren met behulp van de Azure CLI

Als u meerdere galerieën in uw organisatie hebt, kunt u ook installatie kopieën maken op basis van bestaande installatie kopie versies die zijn opgeslagen in andere galerieën. U kunt bijvoorbeeld een galerie voor ontwikkelen en testen hebben voor het maken en testen van nieuwe installatie kopieën. Wanneer ze klaar zijn om te worden gebruikt in de productie, kunt u deze naar een productie galerie kopiëren met behulp van dit voor beeld. U kunt ook een installatie kopie maken van een afbeelding in een andere galerie met behulp van [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Voordat u begint

Om dit artikel te volt ooien, moet u een bestaande bron galerie, afbeeldings definitie en installatie kopie versie hebben. U moet ook een doel galerie hebben. 

De versie van de bron installatie kopie moet worden gerepliceerd naar de regio waar uw doel Galerie zich bevindt. 

Wanneer u dit artikel doorwerkt, vervangt u de resource namen waar nodig.



## <a name="get-information-from-the-source-gallery"></a>Informatie ophalen uit de bron galerie

U hebt gegevens nodig van de definitie van de bron installatie kopie, zodat u een kopie ervan kunt maken in uw nieuwe galerie.

Informatie weer geven over de beschik bare afbeeldings galerieën met de [lijst AZ sig](/cli/azure/sig#az-sig-list) om informatie over de bron galerie te vinden.

```azurecli-interactive 
az sig list -o table
```

De afbeeldings definities in een galerie weer geven met behulp van de [lijst met AZ sig image-definition](/cli/azure/sig/image-definition#az-sig-image-definition-list). In dit voor beeld zoeken we naar afbeeldings definities in de galerie met de naam *myGallery* in de resource groep *myGalleryRG* .

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

De versies van een installatie kopie in een galerie weer geven met behulp van [AZ sig installatie kopie versie lijst](/cli/azure/sig/image-version#az-sig-image-version-list) om de versie van de installatie kopie te zoeken die u wilt kopiëren naar de nieuwe galerie. In dit voor beeld zoeken we naar alle installatie kopieën die deel uitmaken van de definitie van de *myImageDefinition* -installatie kopie.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Zodra u alle informatie hebt die u nodig hebt, kunt u de ID van de bron installatie kopie ophalen met behulp van [AZ sig installatie kopie-versie weer geven](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>De definitie van de installatie kopie maken 

U moet een definitie van een installatie kopie maken die overeenkomt met de definitie van de installatie kopie van de versie van de bron installatie kopie. U kunt alle informatie die u nodig hebt voor het opnieuw maken van de definitie van de installatie kopie in uw nieuwe galerie weer geven met behulp van [AZ sig image-definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
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
  "osType": "Linux",
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

Maak in uw nieuwe galerie een nieuwe definitie van de installatie kopie met behulp van de informatie uit de bovenstaande uitvoer.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een versie met behulp van [AZ Image Gallery Create-Image galerie-version](/cli/azure/sig/image-version#az-sig-image-version-create). U moet de ID van de beheerde installatie kopie door geven om te gebruiken als basis voor het maken van de installatie kopie versie. U kunt [AZ Image List](/cli/azure/image?view#az-image-list) gebruiken om informatie over installatie kopieën in een resource groep op te halen. 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voor beeld is de versie van onze installatie kopie *1.0.0* en we gaan 1 replica maken in de regio *Zuid-Centraal VS* en 1 replica in de regio *VS-Oost* met zone-redundante opslag.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt uw installatie kopie ook opslaan in Premium Storage door `--storage-account-type  premium_lrs` toe te voegen of [zone redundante opslag](../storage/common/storage-redundancy.md) te maken door toe te voegen `--storage-account-type  standard_zrs` Wanneer u de versie van de installatie kopie maakt.
>

## <a name="next-steps"></a>Volgende stappen

Een virtuele machine maken van een [gegeneraliseerde](vm-generalized-image-version-cli.md) of een [gespecialiseerde](vm-specialized-image-version-cli.md) installatie kopie versie.

Probeer ook [Azure Image Builder uit (preview)](./image-builder-overview.md) om het maken van de installatie kopie versie te automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](./linux/image-builder-gallery-update-image-version.md). 

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).