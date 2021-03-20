---
title: Een schaalset maken op basis van een gespecialiseerde installatie kopie versie met behulp van de Azure CLI
description: Een schaalset maken met behulp van een gespecialiseerde afbeeldings versie in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ecc2bfe6bdc06ede61e6c4d1e6eccfc9ef6323a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98877999"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Een schaalset maken met behulp van een gespecialiseerde installatie kopie versie met de Azure CLI

Een schaalset maken op basis van een [gespecialiseerde afbeeldings versie](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) die is opgeslagen in een galerie met gedeelde afbeeldingen. Zie [een schaalset maken op basis van een gegeneraliseerde afbeelding](instance-generalized-image-version-cli.md)als u een schaalset wilt maken met behulp van een gegeneraliseerde installatie kopie.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.4.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

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

Een schaalset maken met [`az vmss create`](/cli/azure/vmss#az-vmss-create) behulp van de `--specialized` para meter om aan te geven dat de afbeelding een gespecialiseerde afbeelding is.

Gebruik de id van de installatiekopiedefinitie voor `--image` om de schaalsetinstanties te maken op basis van de meest recente beschikbare versie van de installatiekopie. U kunt de schaalsetinstantie ook maken op basis van een specifieke versie door de id van de installatiekopieversie op te geven voor `--image`. Houd er rekening mee dat automatisering met behulp van een specifieke versie van de installatie kopie kan mislukken als deze specifieke installatie kopie versie niet beschikbaar is omdat deze is verwijderd of uit de regio is verwijderd. U kunt het beste de definitie-ID van de installatie kopie gebruiken voor het maken van uw nieuwe VM, tenzij een specifieke installatie kopie versie vereist is.

In dit voor beeld maken we instanties van de nieuwste versie van de *myImageDefinition* -installatie kopie.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```


## <a name="next-steps"></a>Volgende stappen
Met [Azure Image Builder (preview)](../virtual-machines/image-builder-overview.md) kunt u het maken van de installatie kopie versie automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure-quickstart-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)