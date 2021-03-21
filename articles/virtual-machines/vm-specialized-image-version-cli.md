---
title: Een virtuele machine maken op basis van een gespecialiseerde installatie kopie versie met behulp van de Azure CLI
description: Maak een virtuele machine met behulp van een gespecialiseerde installatie kopie versie in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe081b0e74acf771e10406c15a3dea4e09956c37
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560957"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Een virtuele machine maken met behulp van een gespecialiseerde installatie kopie versie met de Azure CLI

Maak een virtuele machine op basis van een [gespecialiseerde afbeeldings versie](./shared-image-galleries.md#generalized-and-specialized-images) die is opgeslagen in een galerie met gedeelde afbeeldingen. Zie [een virtuele machine maken op basis van een gegeneraliseerde](vm-generalized-image-version-cli.md)installatie kopie, als u een virtuele machine wilt maken met behulp van een gegeneraliseerde installatie kopie versie.

Vervang resourcenamen naar behoefte in dit voorbeeld. 

De afbeeldings definities in een galerie weer geven met behulp van [AZ sig Image List-definitie lijst](/cli/azure/sig/image-definition#az-sig-image-definition-list) om de naam en id van de definities weer te geven.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Maak de virtuele machine met [az vm create](/cli/azure/vm#az-vm-create) en gebruik de parameter --specialized om aan te geven dat de installatiekopie een gespecialiseerde installatiekopie is. 

Gebruik de id van de installatiekopiedefinitie voor `--image` om de virtuele machine te maken op basis van de laatste beschikbare versie van de installatiekopie. U kunt de virtuele machine ook maken op basis van een specifieke versie door de id van de installatiekopieversie op te geven voor `--image`. 

In dit voorbeeld maken we een virtuele machine op basis van de nieuwste versie van de *myImageDefinition*-installatiekopie.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Volgende stappen
Met [Azure Image Builder (preview)](./image-builder-overview.md) kunt u het maken van de installatie kopie versie automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](./linux/image-builder-gallery-update-image-version.md). 

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure-quickstart-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken van een installatiekopieversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)