---
title: Een beheerde afbeelding klonen naar een versie van een afbeelding met de Azure CLI
description: Meer informatie over het klonen van een beheerde afbeelding naar een versie van een Shared Image Gallery met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d0644b9ec9009fe5d1db7701834cb9788f86ab0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790163"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Een beheerde afbeelding klonen naar een versie van een afbeelding met behulp van de Azure CLI
Als u een bestaande beheerde afbeelding hebt die u wilt klonen in een Shared Image Gallery, kunt u een Shared Image Gallery rechtstreeks vanuit de beheerde afbeelding maken. Nadat u de nieuwe afbeelding hebt getest, kunt u de door de bron beheerde afbeelding verwijderen. U kunt ook migreren van een beheerde afbeelding naar een Shared Image Gallery met [behulp van PowerShell.](image-version-managed-image-powershell.md)

Afbeeldingen in een galerie met afbeeldingen hebben twee onderdelen, die we in dit voorbeeld gaan maken:
- Een **definitie van een** afbeelding bevat informatie over de afbeelding en de vereisten voor het gebruik ervan. Dit omvat of de afbeelding Windows of Linux is, gespecialiseerd of ge generaliseerd, releasenotities en minimale en maximale geheugenvereisten. Het is een definitie van een type installatiekopie. 
- Een **versie van een** afbeelding wordt gebruikt om een VM te maken wanneer u een Shared Image Gallery. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een VM maakt, wordt de versie van de afbeelding gebruikt om nieuwe schijven voor de VM te maken. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, moet u een bestaande [Shared Image Gallery.](shared-images-cli.md) 

Als u het voorbeeld in dit artikel wilt voltooien, moet u een bestaande beheerde afbeelding van een ge generaliseerde VM hebben. Zie Een beheerde afbeelding [vastleggen voor meer informatie.](./linux/capture-image.md) Als de beheerde afbeelding een gegevensschijf bevat, mag de grootte van de gegevensschijf niet groter zijn dan 1 TB.

Wanneer u dit artikel door werkt, vervangt u waar nodig de namen van de resourcegroep en de VM.



## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Omdat beheerde afbeeldingen altijd ge generaliseerde afbeeldingen zijn, maakt u een definitie van een afbeelding met `--os-state generalized` voor een ge generaliseerde afbeelding.

Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Zie [Installatiekopiedefinities](./shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

In dit voorbeeld heet de definitie van de afbeelding *myImageDefinition* en is deze voor een ge generaliseerde [](./shared-image-galleries.md#generalized-and-specialized-images) Linux-besturingssysteemafbeelding. Als u een definitie wilt maken voor installatiekopieën met een Windows-besturingssysteem, gebruikt u `--os-type Windows`. 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak versies met [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). U moet de id van de beheerde afbeelding doorgeven om te gebruiken als basislijn voor het maken van de versie van de afbeelding. U kunt [az image list gebruiken om](/cli/azure/image?view#az_image_list) de ID's voor uw afbeeldingen op te halen. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld is de versie van de afbeelding *1.0.0* en gaan we 1 replica maken in de regio *VS* - zuid-centraal en 1 replica in de regio VS - oost *2* met zone-redundante opslag. Wanneer u doelregio's voor replicatie kiest, moet u ook de *bronregio* als replicatiedoel opnemen.

Geef de id van de beheerde afbeelding door in de `--managed-image` parameter .


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt ook al uw replica's van de versie van de afbeelding opslaan in [Zone-redundante](../storage/common/storage-redundancy.md) opslag door toe te voegen `--storage-account-type standard_zrs` wanneer u de versie van de afbeelding maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een VM van een [ge generaliseerde versie van de -afbeelding.](vm-generalized-image-version-cli.md)

Zie Informatie over het aankoopplan leveren bij het maken van afbeeldingen voor Azure Marketplace informatie over het leveren van informatie over het [aankoopplan.](marketplace-images.md)