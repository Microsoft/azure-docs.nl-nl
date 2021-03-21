---
title: 'Zelfstudie: Aangepaste installatiekopieën voor virtuele machines maken met de Azure CLI'
description: In deze zelfstudie leert u hoe u Azure CLI gebruikt om een aangepaste installatiekopie van een virtuele machine te maken in Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 40ec86030e136a06fe240e473a469681dcfa7c4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557758"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Zelfstudie: Een aangepaste installatiekopie van een Azure-VM maken met Azure CLI

Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. In deze zelfstudie maakt u uw eigen aangepaste installatiekopie van een virtuele Azure-machine. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie voor de installatiekopie maken
> * De versie van een installatiekopie maken
> * Een VM vanuit de installatiekopie maken 
> * Een galerie met installatiekopieën delen


In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.4.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="overview"></a>Overzicht

Met een [Shared Image Gallery](../shared-image-galleries.md) kunt u het delen van aangepaste installatiekopieën in uw organisatie vereenvoudigen. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de Shared Image Gallery kunt u aangepaste VM-installatiekopieën met anderen delen. U kunt kiezen welke installatiekopieën u wilt delen, in welke regio’s u ze beschikbaar wilt maken en met wie u ze wilt delen. 

De functie Shared Image Gallery heeft meerdere resourcetypen:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Voordat u begint

In de onderstaande stappen wordt gedetailleerd beschreven hoe u van een bestaande virtuele machine een herbruikbare aangepaste installatiekopie maakt die u kunt gebruiken om nieuwe virtuele machines te maken.

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Indien nodig raadpleegt u de [CLI-snelstartgids](quick-create-cli.md) om een VM te maken voor deze zelfstudie. Vervang tijdens het volgen van de zelfstudie de resourcenamen waar nodig.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-an-image-gallery"></a>Een galerie met installatiekopieën maken 

Een galerie met installatiekopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van installatiekopieën. 

De naam van de galerie kan bestaan uit hoofdletters en kleine letters, cijfers en punten. De naam van de galerie kan geen liggende streepjes bevatten.   De naam van de galerie moet uniek zijn binnen uw abonnement. 

Een galerie met installatiekopieën maken met [az sig create](/cli/azure/sig#az-sig-create). In het volgende voorbeeld wordt een resourcegroep gemaakt genaamd galerie met de naam *myGalleryRG* in *US -oost* en een galerie met de naam *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Informatie over de VM ophalen

U kunt een lijst weergeven met virtuele machines die beschikbaar zijn met [az vm list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Zodra u de naam van de virtuele machine weet en in welke resourcegroep die zich bevindt kunt u de id van de virtuele machine ophalen met [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Kopieer de id van uw VM voor later gebruik.

## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Die worden gebruikt voor het beheren van informatie over de installatiekopieversies die daarbinnen worden gemaakt. 

Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Zie [Installatiekopiedefinities](../shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create). 

In dit voorbeeld heeft de definitie van de installatiekopie de naam *myImageDefinition* en is deze voor een [gespecialiseerde](../shared-image-galleries.md#generalized-and-specialized-images) installatiekopie van een Linux-besturingssysteem. 

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

Kopieer de id van de installatiekopiedefinitie uit de uitvoer om later te gebruiken.

## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een installatiekopieversie van de virtuele machine met [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

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
> U kunt uw installatiekopie ook opslaan in Premium Storage door een `--storage-account-type  premium_lrs` toe te voegen, of in [Zone-redundante opslag](../../storage/common/storage-redundancy.md) door `--storage-account-type  standard_zrs` toe te voegen wanneer u de installatiekopieversie maakt.
>

 
## <a name="create-the-vm"></a>De virtuele machine maken

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

## <a name="share-the-gallery"></a>De galerie delen

U kunt installatiekopieën delen met meerdere abonnementen met behulp van toegangsbeheer op basis van rollen (Azure RBAC). U kunt installatiekopieën delen op het niveau van de galerie, de installatiekopiedefinitie of de installatiekopieversie. Elke gebruiker die leesmachtigingen heeft voor een installatiekopieversie, ook in meerdere abonnementen, kan een virtuele machine implementeren op basis van de installatiekopieversie.

We raden aan om te delen met andere gebruikers op galerieniveau. Gebruik [az sig show](/cli/azure/sig#az-sig-show) om de object-id van uw galerie op te halen.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Gebruik de object-id als een bereik, samen met een e-mailadres en [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) om een gebruiker toegang te geven tot de gedeelde galerie met installatiekopieën. Vervang `<email-address>` en `<gallery iD>` door uw eigen gegevens.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Zie [Azure-roltoewijzingen toevoegen of verwijderen met de Azure CLI](../../role-based-access-control/role-assignments-cli.md) voor meer informatie over het delen van resources met behulp van Azure RBAC.

## <a name="azure-image-builder"></a>Azure Image Builder

Azure biedt ook de service [Azure VM Image Builder](../image-builder-overview.md), die is gebouwd op Packer. U hoeft slechts uw aanpassingen in een sjabloon te beschrijven en de service handelt het maken van de installatiekopie af. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een aangepaste installatiekopie voor een virtuele machine gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een definitie voor de installatiekopie maken
> * De versie van een installatiekopie maken
> * Een VM vanuit de installatiekopie maken 
> * Een galerie met installatiekopieën delen

Ga naar de volgende zelfstudie voor meer informatie over virtuele machines met hoge beschikbaarheid.

> [!div class="nextstepaction"]
> [Virtuele machines met hoge beschikbaarheid maken](tutorial-availability-sets.md)