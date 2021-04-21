---
title: Een VM maken op basis van een gespecialiseerde versie van een afbeelding met behulp van de Azure CLI
description: Maak een VM met behulp van een gespecialiseerde versie van de Shared Image Gallery met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3498037f3d2088459784ab066b8e94ba344a275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792179"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Een VM maken met behulp van een gespecialiseerde versie van de afbeelding met de Azure CLI

Maak een VM op een [gespecialiseerde versie van de opgeslagen](./shared-image-galleries.md#generalized-and-specialized-images) Shared Image Gallery. Zie Create [a VM from a generalized image version](vm-generalized-image-version-cli.md)(Een VM maken op basis van een ge generaliseerde versie van de VM) als u een VM wilt maken met behulp van een ge generaliseerde versie van de afbeelding.

Vervang resourcenamen naar behoefte in dit voorbeeld. 

Gebruik az [sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) om de naam en id van de definities weer te geven in een galerie.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Maak de virtuele machine met [az vm create](/cli/azure/vm#az_vm_create) en gebruik de parameter --specialized om aan te geven dat de installatiekopie een gespecialiseerde installatiekopie is. 

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
[Azure Image Builder (preview)](./image-builder-overview.md) kan helpen bij het automatiseren van het maken van de versie van de afbeelding. U kunt deze zelfs gebruiken om een nieuwe versie van de afbeelding bij te werken en te maken van een [bestaande versie van de afbeelding](./linux/image-builder-gallery-update-image-version.md). 

U kunt ook een Shared Image Gallery maken met behulp van sjablonen. Er zijn verschillende Azure-quickstart-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken van een installatiekopieversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)