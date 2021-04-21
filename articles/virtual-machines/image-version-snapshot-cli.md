---
title: 'CLI: een afbeelding maken van een momentopname of beheerde schijf in een Shared Image Gallery'
description: Meer informatie over het maken van een afbeelding op basis van een momentopname of beheerde schijf in een Shared Image Gallery met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2dc6d99b8b1c913479fc584b52f6ff919dfac675
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792287"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Een afbeelding maken van een beheerde schijf of momentopname in een Shared Image Gallery azure CLI

Als u een bestaande momentopname of beheerde schijf hebt die u wilt migreren naar een Shared Image Gallery, kunt u rechtstreeks vanuit de beheerde schijf of momentopname een Shared Image Gallery maken. Nadat u de nieuwe afbeelding hebt getest, kunt u de beheerde bronschijf of momentopname verwijderen. U kunt ook een afbeelding maken van een beheerde schijf of momentopname in een Shared Image Gallery met behulp van [Azure PowerShell](image-version-snapshot-powershell.md).

Afbeeldingen in een galerie met afbeeldingen bevatten twee onderdelen, die we in dit voorbeeld gaan maken:
- Een **definitie van een** afbeelding bevat informatie over de afbeelding en de vereisten voor het gebruik ervan. Dit omvat of de afbeelding Windows of Linux is, gespecialiseerd of ge generaliseerd, releasenotities en minimale en maximale geheugenvereisten. Het is een definitie van een type installatiekopie. 
- Een **versie van een** afbeelding wordt gebruikt om een VM te maken wanneer u een Shared Image Gallery. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een VM maakt, wordt de versie van de afbeelding gebruikt om nieuwe schijven voor de VM te maken. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, moet u een momentopname of beheerde schijf hebben. 

Als u een gegevensschijf wilt opnemen, mag de grootte van de gegevensschijf niet groter zijn dan 1 TB.

Wanneer u dit artikel door werkt, vervangt u waar nodig de resourcenamen.

## <a name="find-the-snapshot-or-managed-disk"></a>De momentopname of beheerde schijf zoeken 

U kunt een lijst met momentopnamen bekijken die beschikbaar zijn in een resourcegroep met [behulp van az snapshot list](/cli/azure/snapshot#az_snapshot_list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

U kunt ook een beheerde schijf gebruiken in plaats van een momentopname. Gebruik az disk list om een beheerde [schijf op te halen.](/cli/azure/disk#az_disk_list) 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Zodra u de id van de momentopname of beheerde schijf hebt en deze hebt toegewezen aan een variabele met de naam `$source` voor later gebruik.

U kunt hetzelfde proces gebruiken om alle gegevensschijven op te halen die u in uw afbeelding wilt opnemen. Wijs ze toe aan variabelen en gebruik deze variabelen later wanneer u de versie van de afbeelding maakt.


## <a name="find-the-gallery"></a>De galerie zoeken

U hebt informatie nodig over de galerie met afbeeldingen om de definitie van de afbeelding te kunnen maken.

Vermeld informatie over de beschikbare galerieën met afbeeldingen [met az sig list](/cli/azure/sig#az_sig_list). Noteer de naam van de galerie in welke resourcegroep de galerie zich later moet gebruiken.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Ze worden gebruikt voor het beheren van informatie over de afbeelding. Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Wanneer u de definitie van uw afbeelding maakt, moet u ervoor zorgen dat alle juiste informatie is opgenomen. In dit voorbeeld gaan we ervan uit dat de momentopname of beheerde schijf afkomstig is van een VM die in gebruik is en niet is ge generaliseerd. Als de beheerde schijf of momentopname is gemaakt van een gegeneraliseerd besturingssysteem (na het uitvoeren van Sysprep voor Windows of [waagent](https://github.com/Azure/WALinuxAgent) of `-deprovision` `-deprovision+user` voor Linux), wijzigt u `-OsState` de in `generalized` . 

Zie [Installatiekopiedefinities](./shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

In dit voorbeeld heeft de definitie van de installatiekopie de naam *myImageDefinition* en is deze voor een [gespecialiseerde](./shared-image-galleries.md#generalized-and-specialized-images) installatiekopie van een Linux-besturingssysteem. Als u een definitie wilt maken voor installatiekopieën met een Windows-besturingssysteem, gebruikt u `--os-type Windows`. 

In dit voorbeeld heeft de galerie de naam *myGallery,* deze staat in de resourcegroep *myGalleryRG* en de naam van de definitie van de afbeelding is *mImageDefinition.*

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
   --os-state specialized
```


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een versie van de afbeelding [met az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld is de versie van onze afbeelding *1.0.0* en gaan we 1 replica maken in de regio *VS* - zuid-centraal en 1 replica in de regio VS - oost *2* met zone-redundante opslag. Wanneer u doelregio's voor replicatie kiest,  moet u ook de bronregio van de beheerde schijf of momentopname opnemen als replicatiedoel.

Geef de id van de momentopname of beheerde schijf door in de `--os-snapshot` parameter .


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Als u gegevensschijven wilt opnemen in de afbeelding, moet u zowel de parameter opnemen die is ingesteld op het LUN-nummer als de ingesteld op de id van de gegevensschijf `--data-snapshot-luns` `--data-snapshots` of momentopname.

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt ook al uw versiereplica's van de afbeelding opslaan in [zone-redundante](../storage/common/storage-redundancy.md) opslag door toe te voegen `--storage-account-type standard_zrs` wanneer u de versie van de afbeelding maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een VM van een [gespecialiseerde versie van de -afbeelding.](vm-specialized-image-version-cli.md)

Zie Informatie over het aankoopplan leveren bij het maken van afbeeldingen voor Azure Marketplace informatie over het leveren van informatie over het [aankoopplan.](marketplace-images.md)