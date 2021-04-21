---
title: Een afbeelding maken van een VM met behulp van Azure CLI
description: Meer informatie over het maken van een afbeelding in een Shared Image Gallery van een VM in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7bfe8b1255c88878c2dc4661e9daa3e16397e9f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792269"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Een versie van een afbeelding maken op basis van een VM in Azure met behulp van de Azure CLI

Als u een bestaande VM hebt die u wilt gebruiken om meerdere identieke VM's te maken, kunt u die VM gebruiken om een afbeelding te maken in een Shared Image Gallery met behulp van de Azure CLI. U kunt ook een VM-afbeelding maken met behulp [van Azure PowerShell.](image-version-vm-powershell.md)

Een **versie van een** afbeelding is wat u gebruikt om een VM te maken wanneer u een Shared Image Gallery. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Wanneer u een versie van een afbeelding gebruikt om een VM te maken, wordt de versie van de afbeelding gebruikt om schijven voor de nieuwe VM te maken. Versies van installatiekopieën kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, moet u een bestaande Shared Image Gallery. 

U moet ook een bestaande VM in Azure hebben, in dezelfde regio als uw galerie. 

Als aan de VM een gegevensschijf is gekoppeld, mag de grootte van de gegevensschijf niet groter zijn dan 1 TB.

Wanneer u dit artikel door werkt, vervangt u waar nodig de resourcenamen.

## <a name="get-information-about-the-vm"></a>Informatie over de VM ophalen

U kunt een lijst weergeven met virtuele machines die beschikbaar zijn met [az vm list](/cli/azure/vm#az_vm_list). 

```azurecli-interactive
az vm list --output table
```

Zodra u de naam van de virtuele machine weet en in welke resourcegroep die zich bevindt kunt u de id van de virtuele machine ophalen met [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Die worden gebruikt voor het beheren van informatie over de installatiekopieversies die daarbinnen worden gemaakt. 

Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Controleer of uw installatiekopiedefinitie het juiste type heeft. Als u de VM hebt gegeneraliseerd (met behulp van Sysprep voor Windows of waagent-deprovision voor Linux), moet u een gegeneraliseerde installatiekopiedefinitie maken met behulp van `--os-state generalized`. Als u de VM wilt gebruiken zonder bestaande gebruikersaccounts te verwijderen, maakt u een gespecialiseerde installatiekopiedefinitie met behulp van `--os-state specialized`.

Zie [Installatiekopiedefinities](./shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

In dit voorbeeld heeft de definitie van de installatiekopie de naam *myImageDefinition* en is deze voor een [gespecialiseerde](./shared-image-galleries.md#generalized-and-specialized-images) installatiekopie van een Linux-besturingssysteem. Als u een definitie wilt maken voor installatiekopieën met een Windows-besturingssysteem, gebruikt u `--os-type Windows`. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een installatiekopieversie van de virtuele machine met [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create).  

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld is de installatiekopieversie *1.0.0* en gaan we 2 replica's maken in de regio *VS - west-centraal*, 1 replica in de regio *VS - zuid-centraal* regio en 1 replica in de regio *US - oost 2* met zone-redundante opslag. De replicatieregio’s moeten de regio omvatten waarin de bron-VM zich bevindt.

Vervang de waarde van `--managed-image` in dit voorbeeld door de id van uw virtuele machine uit de vorige stap.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt uw afbeelding ook opslaan in Premium Storage door , of `--storage-account-type  premium_lrs` [Zone-redundante](../storage/common/storage-redundancy.md) opslag toe te voegen door toe te voegen `--storage-account-type  standard_zrs` wanneer u de versie van de afbeelding maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een VM op basis van [de ge generaliseerde afbeelding](vm-generalized-image-version-cli.md) met behulp van de Azure CLI.

Zie Informatie over het aankoopplan leveren bij het maken van Azure Marketplace voor informatie over het leveren van informatie over het [aankoopplan.](marketplace-images.md)