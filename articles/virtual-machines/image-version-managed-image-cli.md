---
title: Een beheerde installatie kopie klonen naar een installatie kopie versie met behulp van de Azure CLI
description: Meer informatie over het klonen van een beheerde installatie kopie naar een installatie kopie versie in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: cae82072785838d410453b2eb83685905b0ba04e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553779"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Een beheerde installatie kopie klonen naar een installatie kopie versie met behulp van de Azure CLI
Als u een bestaande beheerde installatie kopie hebt die u in een galerie met gedeelde afbeeldingen wilt klonen, kunt u rechtstreeks vanuit de beheerde installatie kopie een installatie kopie van een gedeelde installatie kopie maken. Wanneer u de nieuwe installatie kopie hebt getest, kunt u de door de bron beheerde installatie kopie verwijderen. U kunt ook migreren van een beheerde installatie kopie naar een galerie met gedeelde afbeeldingen met behulp van [Power shell](image-version-managed-image-powershell.md).

Afbeeldingen in een afbeeldings galerie hebben twee onderdelen, die we in dit voor beeld gaan maken:
- Een **definitie van een installatie kopie** bevat informatie over de installatie kopie en vereisten voor het gebruik ervan. Dit omvat of de installatie kopie Windows of Linux, gespecialiseerde of gegeneraliseerde, release opmerkingen en minimale en maximale geheugen vereisten zijn. Het is een definitie van een type installatiekopie. 
- Een **installatie kopie versie** is wat wordt gebruikt om een virtuele machine te maken wanneer u een galerie met gedeelde afbeeldingen gebruikt. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een virtuele machine maakt, wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt volt ooien, moet u beschikken over een bestaande [Galerie met gedeelde installatie kopieën](shared-images-cli.md). 

Om het voor beeld in dit artikel te volt ooien, moet u een bestaande beheerde installatie kopie van een gegeneraliseerde virtuele machine hebben. Zie [een beheerde installatie kopie vastleggen](./linux/capture-image.md)voor meer informatie. Als de beheerde installatie kopie een gegevens schijf bevat, kan de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, moet u de namen van de resource groep en de VM vervangen, indien nodig.



## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Omdat beheerde installatie kopieën altijd gegeneraliseerde installatie kopieën zijn, maakt u een definitie van een installatie kopie met behulp `--os-state generalized` van een gegeneraliseerde installatie kopie.

Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Zie [Installatiekopiedefinities](./shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In dit voor beeld heeft de definitie van de installatie kopie de naam *myImageDefinition* en is voor een [gegeneraliseerde](./shared-image-galleries.md#generalized-and-specialized-images) installatie kopie van Linux-besturings systeem. Als u een definitie wilt maken voor installatiekopieën met een Windows-besturingssysteem, gebruikt u `--os-type Windows`. 

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

Maak een versie met behulp van [AZ Image Gallery Create-Image galerie-version](/cli/azure/sig/image-version#az-sig-image-version-create). U moet de ID van de beheerde installatie kopie door geven om te gebruiken als basis voor het maken van de installatie kopie versie. U kunt [AZ Image List](/cli/azure/image?view#az-image-list) gebruiken om de id's van uw installatie kopieën op te halen. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voor beeld is de versie van onze installatie kopie *1.0.0* en we gaan 1 replica maken in de regio *Zuid-Centraal VS* en 1 replica in de regio *VS-Oost 2* met zone-redundante opslag. Houd er rekening mee dat u bij het kiezen van doel regio's voor replicatie ook de *bron* regio moet toevoegen als doel voor replicatie.

Geef de ID van de beheerde installatie kopie op in de `--managed-image` para meter.


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
> U kunt ook al uw afbeeldings versie replica's opslaan in [zone redundante opslag](../storage/common/storage-redundancy.md) door toe te voegen `--storage-account-type standard_zrs` Wanneer u de versie van de installatie kopie maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een virtuele machine op basis van een [gegeneraliseerde installatie kopie versie](vm-generalized-image-version-cli.md).

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).