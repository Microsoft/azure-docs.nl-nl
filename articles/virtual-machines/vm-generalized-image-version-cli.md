---
title: Een VM maken op basis van een ge generaliseerde afbeelding met behulp van de Azure CLI
description: Maak een VM op basis van een ge generaliseerde versie van de afbeelding met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5e0e5544c5e66f43b56de49beaa3ef3932d33f9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776875"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Een VM maken op basis van een ge generaliseerde versie van de afbeelding met behulp van de Azure CLI

Maak een VM van een [ge generaliseerde versie van de afbeelding die](./shared-image-galleries.md#generalized-and-specialized-images) is opgeslagen in een Shared Image Gallery. Zie Een VM maken op basis van een gespecialiseerde afbeelding als u een VM wilt maken met behulp van een [gespecialiseerde afbeelding.](vm-specialized-image-version-powershell.md) 


## <a name="get-the-image-id"></a>De afbeeldings-id op halen

Gebruik az [sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) om de naam en id van de definities weer te geven in een galerie.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>De VM maken

Maak een VM met [az vm create](/cli/azure/vm#az_vm_create). Als u de nieuwste versie van de afbeelding wilt gebruiken, stelt u in `--image` op de id van de definitie van de afbeelding. 

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

U kunt ook een specifieke versie gebruiken met behulp van de versie-id van de afbeelding voor de `--image` parameter . Als u bijvoorbeeld versie *1.0.0 van* de afbeelding wilt gebruiken, typt u: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Volgende stappen

[Azure Image Builder (preview)](./image-builder-overview.md) kan helpen bij het automatiseren van het maken van de versie van de afbeelding. U kunt deze zelfs gebruiken om een nieuwe versie van de afbeelding bij te werken en te maken van een [bestaande versie van de afbeelding](./linux/image-builder-gallery-update-image-version.md).