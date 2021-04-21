---
title: Een schaalset maken van een ge generaliseerde afbeelding met Azure CLI
description: Maak een schaalset met behulp van een ge generaliseerde afbeelding in een Shared Image Gallery azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: de50540345ac6170d229549cad736dafb04e488c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792359"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Een schaalset maken van een ge generaliseerde afbeelding met Azure CLI

Maak een schaalset op basis van een ge generaliseerde versie van de afbeelding die is opgeslagen in [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) Azure CLI. Zie Exemplaren van schaalsets maken op basis van een gespecialiseerde afbeelding als u een schaalset wilt maken met behulp van een [gespecialiseerde versie van de -afbeelding.](instance-specialized-image-version-cli.md)

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.4.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

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

Maak de schaalset met [`az vmss create`](/cli/azure/vmss#az_vmss_create) behulp van . 

Gebruik de id van de installatiekopiedefinitie voor `--image` om de schaalsetinstanties te maken op basis van de meest recente beschikbare versie van de installatiekopie. U kunt de schaalsetinstantie ook maken op basis van een specifieke versie door de id van de installatiekopieversie op te geven voor `--image`. Het gebruik van een specifieke versie van een afbeelding betekent dat automatisering kan mislukken als die specifieke versie van de afbeelding niet beschikbaar is omdat deze is verwijderd uit de regio. We raden u aan de definitie-id van de afbeelding te gebruiken voor het maken van uw nieuwe VM, tenzij een specifieke versie van de afbeelding is vereist.

In dit voorbeeld maken we exemplaren van de nieuwste versie van de *myImageDefinition-afbeelding.*

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.

## <a name="next-steps"></a>Volgende stappen
[Azure Image Builder (preview)](../virtual-machines/image-builder-overview.md) kan helpen bij het automatiseren van het maken van de versie van de afbeelding. U kunt deze zelfs gebruiken om een nieuwe versie van de afbeelding bij te werken en te maken van een [bestaande versie van de afbeelding](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

U kunt ook een Shared Image Gallery maken met behulp van sjablonen. Er zijn verschillende Azure-quickstart-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Zie overzicht voor meer informatie over galerieën met gedeelde [afbeeldingen.](../virtual-machines/shared-image-galleries.md) Zie Problemen met galerieën met gedeelde afbeeldingen oplossen als u problemen [hebt.](../virtual-machines/troubleshooting-shared-images.md)