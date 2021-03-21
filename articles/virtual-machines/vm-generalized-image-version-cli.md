---
title: Een virtuele machine maken op basis van een gegeneraliseerde installatie kopie met behulp van de Azure CLI
description: Maak een virtuele machine op basis van een gegeneraliseerde installatie kopie versie met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2bcaf85f61a4d8cf4d23c9c5be7f46d765d77dbb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551039"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-cli"></a>Een virtuele machine maken op basis van een gegeneraliseerde installatie kopie met behulp van de CLI

Een virtuele machine maken op basis van een [gegeneraliseerde installatie kopie-versie](./shared-image-galleries.md#generalized-and-specialized-images) die is opgeslagen in een galerie met gedeelde afbeeldingen. Als u een virtuele machine wilt maken met behulp van een gespecialiseerde installatie kopie, raadpleegt u [een virtuele machine maken op basis van een gespecialiseerde installatie kopie](vm-specialized-image-version-powershell.md). 


## <a name="get-the-image-id"></a>De afbeeldings-ID ophalen

De afbeeldings definities in een galerie weer geven met behulp van [AZ sig Image List-definitie lijst](/cli/azure/sig/image-definition#az-sig-image-definition-list) om de naam en id van de definities weer te geven.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>De VM maken

Maak een VM met [az vm create](/cli/azure/vm#az-vm-create). Als u de meest recente versie van de installatie kopie wilt gebruiken, stelt `--image` u de id van de definitie van de installatie kopie in. 

Vervang resourcenamen naar behoefte in dit voorbeeld. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

U kunt ook een specifieke versie gebruiken met de versie-ID van de installatie kopie voor de `--image` para meter. Als u bijvoorbeeld de afbeeldings versie *1.0.0* type: wilt gebruiken `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Volgende stappen

Met [Azure Image Builder (preview)](./image-builder-overview.md) kunt u het maken van de installatie kopie versie automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](./linux/image-builder-gallery-update-image-version.md).