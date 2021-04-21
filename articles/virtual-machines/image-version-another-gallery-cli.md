---
title: Een versie van een afbeelding kopiëren uit een andere galerie met behulp van de CLI
description: Kopieer een versie van een afbeelding uit een andere galerie met de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e2cd885d886a0f13783e61a04c7243efdf12967e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784979"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Een afbeelding kopiëren uit een andere galerie met behulp van de Azure CLI

Als u meerdere galerieën in uw organisatie hebt, kunt u ook versies van de afbeelding maken van bestaande versies van de afbeelding die zijn opgeslagen in andere galerieën. U hebt bijvoorbeeld een ontwikkelings- en testgalerie voor het maken en testen van nieuwe afbeeldingen. Wanneer ze klaar zijn voor gebruik in productie, kunt u ze kopiëren naar een productiegalerie met behulp van dit voorbeeld. U kunt ook een afbeelding maken van een afbeelding in een andere galerie met behulp [van Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, moet u een bestaande brongalerie, definitie van de afbeelding en versie van de afbeelding hebben. U moet ook een doelgalerie hebben. 

De versie van de bronafbeelding moet worden gerepliceerd naar de regio waar uw doelgalerie zich bevindt. 

Wanneer u dit artikel door werkt, vervangt u waar nodig de resourcenamen.



## <a name="get-information-from-the-source-gallery"></a>Informatie verkrijgen uit de brongalerie

U hebt informatie uit de definitie van de bronafbeelding nodig, zodat u er een kopie van kunt maken in uw nieuwe galerie.

Lijst met informatie over de beschikbare galerieën met afbeeldingen [met az sig list](/cli/azure/sig#az_sig_list) om informatie over de brongalerie te vinden.

```azurecli-interactive 
az sig list -o table
```

Gebruik [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list)om de definities van de afbeeldingen in een galerie weer te geven. In dit voorbeeld zoeken we naar definities van afbeeldingen in de galerie met de naam *myGallery* in de resourcegroep *myGalleryRG.*

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Vermeld de versies van een afbeelding in een galerie met [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) om de versie van de afbeelding te vinden die u naar uw nieuwe galerie wilt kopiëren. In dit voorbeeld zijn we op zoek naar alle versies van de afbeelding die deel uitmaken van de definitie van de *afbeelding myImageDefinition.*

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Zodra u alle informatie hebt die u nodig hebt, kunt u de id van de versie van de bronafbeelding verkrijgen met [az sig image-version show.](/cli/azure/sig/image-version#az_sig_image_version_show)

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>De definitie van de afbeelding maken 

U moet een definitie van een afbeelding maken die overeenkomt met de definitie van de afbeelding van de bronversie van de afbeelding. U kunt alle informatie zien die u nodig hebt om de definitie van de afbeelding opnieuw te maken in uw nieuwe galerie met [az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show).

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

Maak een nieuwe definitie van de afbeelding in uw nieuwe galerie met behulp van de informatie uit de bovenstaande uitvoer.


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

Maak versies met [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). U moet de id van de beheerde afbeelding doorgeven om te gebruiken als basislijn voor het maken van de versie van de afbeelding. U kunt [az image list gebruiken om](/cli/azure/image?view#az_image_list) informatie op te halen over afbeeldingen in een resourcegroep. 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld is de versie van onze afbeelding *1.0.0* en gaan we 1 replica maken  in de regio *VS* - zuid-centraal en 1 replica in de regio VS - oost met behulp van zone-redundante opslag.


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
> U kunt uw afbeelding ook opslaan in Premium Storage door , of `--storage-account-type  premium_lrs` [Zone-redundante](../storage/common/storage-redundancy.md) opslag toe te voegen door toe te voegen `--storage-account-type  standard_zrs` wanneer u de versie van de afbeelding maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een VM van een [ge generaliseerde of](vm-generalized-image-version-cli.md) gespecialiseerde [versie](vm-specialized-image-version-cli.md) van de afbeelding.

Azure Image Builder [(preview)](./image-builder-overview.md) kan ook helpen bij het automatiseren van het maken van de versie van de afbeelding. U kunt deze zelfs gebruiken om een nieuwe versie van de afbeelding bij te werken en te maken op een bestaande versie van de [afbeelding.](./linux/image-builder-gallery-update-image-version.md) 

Zie Informatie over het aankoopplan leveren bij het maken van Azure Marketplace voor informatie over het leveren van informatie over het [aankoopplan.](marketplace-images.md)