---
title: Zelfstudie - Een aangepaste VM-installatiekopie gebruiken in een schaalset met Azure CLI
description: Informatie over het gebruik van Azure CLI voor het maken van een aangepaste VM-installatiekopie die u kunt gebruiken om een virtuele-machineschaalset te implementeren
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: b12715e299f523d7ace56a72b0098b5d7ffac0ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98683049"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Zelfstudie: Een aangepaste installatiekopie voor virtuele-machineschaalsets maken en gebruiken met Azure CLI
Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. Om het aantal taken na de implementatie van VM-exemplaren te verminderen, kunt u een aangepaste VM-installatiekopie gebruiken. Deze aangepaste VM-installatiekopie bevat alle geïnstalleerde toepassingen of configuraties die vereist zijn. Alle VM-exemplaren die in de schaalset zijn gemaakt, gebruiken de aangepaste VM-installatiekopie en zijn gereed voor uw toepassingsverkeer. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een gespecialiseerde installatiekopiedefinitie maken
> * De versie van een installatiekopie maken
> * Een schaalset maken op basis van een gespecialiseerde installatiekopie
> * Een galerie met installatiekopieën delen


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.4.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="overview"></a>Overzicht

Met een [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) kunt u het delen van aangepaste installatiekopieën in uw organisatie vereenvoudigen. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de Shared Image Gallery kunt u aangepaste VM-installatiekopieën met anderen delen. U kunt kiezen welke installatiekopieën u wilt delen, in welke regio’s u ze beschikbaar wilt maken en met wie u ze wilt delen. 

## <a name="create-and-configure-a-source-vm"></a>Een bron-VM maken en configureren

Voordat u een virtuele machine kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group) en daarna een virtuele machine maken met [az vm create](/cli/azure/vm). Deze VM wordt vervolgens gebruikt als bron voor de installatiekopie. In het volgende voorbeeld wordt een virtuele machine gemaakt met de naam *myVM* in de resourcegroep met de naam *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> De **id** van uw virtuele machine wordt weergegeven in de uitvoer van de opdracht [az vm create](/cli/azure/vm). Kopieer de waarde en bewaar deze op een veilige plek zodat u de waarde later in deze zelfstudie kunt gebruiken.

Het openbare IP-adres van uw virtuele machine wordt ook weergegeven in de uitvoer van de opdracht [az vm create](/cli/azure/vm). SSH in het openbare IP-adres van uw VM als volgt:

```console
ssh azureuser@<publicIpAddress>
```

We gaan een eenvoudige webserver voor het aanpassen van uw VM installeren. Bij implementatie van het VM-exemplaar in de schaalset heeft dit hiermee alle vereiste pakketten om een webtoepassing uit te voeren. Gebruik `apt-get` om *NGINX* als volgt te installeren:

```bash
sudo apt-get install -y nginx
```

Wanneer u klaar bent, typt u `exit` om de SSH-verbinding te verbreken.

## <a name="create-an-image-gallery"></a>Een galerie met installatiekopieën maken 

Een galerie met installatiekopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van installatiekopieën. 

De naam van de galerie kan bestaan uit hoofdletters en kleine letters, cijfers en punten. De naam van de galerie kan geen liggende streepjes bevatten.   De naam van de galerie moet uniek zijn binnen uw abonnement. 

Een galerie met installatiekopieën maken met [az sig create](/cli/azure/sig#az-sig-create). In het volgende voorbeeld wordt een resourcegroep gemaakt genaamd galerie met de naam *myGalleryRG* in *US -oost* en een galerie met de naam *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Die worden gebruikt voor het beheren van informatie over de installatiekopieversies die daarbinnen worden gemaakt. 

Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Controleer of uw installatiekopiedefinitie het juiste type heeft. Als u de VM hebt gegeneraliseerd (met behulp van Sysprep voor Windows of waagent-deprovision voor Linux), moet u een gegeneraliseerde installatiekopiedefinitie maken met behulp van `--os-state generalized`. Als u de VM wilt gebruiken zonder bestaande gebruikersaccounts te verwijderen, maakt u een gespecialiseerde installatiekopiedefinitie met behulp van `--os-state specialized`.

Zie [Installatiekopiedefinities](../virtual-machines/shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In dit voorbeeld heeft de definitie van de installatiekopie de naam *myImageDefinition* en is deze voor een [gespecialiseerde](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) installatiekopie van een Linux-besturingssysteem. Als u een definitie wilt maken voor installatiekopieën met een Windows-besturingssysteem, gebruikt u `--os-type Windows`. 

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

> [!IMPORTANT]
> De **id** van uw installatiekopiedefinitie wordt weergegeven in de uitvoer van de opdracht. Kopieer de waarde en bewaar deze op een veilige plek zodat u de waarde later in deze zelfstudie kunt gebruiken.


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een installatiekopieversie van de virtuele machine met [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voorbeeld is de installatiekopieversie *1.0.0* en gaan we 1 replica maken in de regio *VS - zuid-centraal* en 1 replica in de regio *US - oost 2*. De replicatieregio’s moeten de regio omvatten waarin de bron-VM zich bevindt.

Vervang de waarde van `--managed-image` in dit voorbeeld door de id van uw virtuele machine uit de vorige stap.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt uw installatiekopie ook opslaan in Premium Storage door een `--storage-account-type  premium_lrs` toe te voegen, of in [Zone-redundante opslag](../storage/common/storage-redundancy.md) door `--storage-account-type  standard_zrs` toe te voegen wanneer u de installatiekopieversie maakt.
>




## <a name="create-a-scale-set-from-the-image"></a>Een schaalset maken op basis van een installatiekopie
Maak een schaalset op basis van een gespecialiseerde installatiekopie met behulp van [`az vmss create`](/cli/azure/vmss#az-vmss-create). 

Maak de schaalset met [`az vmss create`](/cli/azure/vmss#az-vmss-create) en gebruik de parameter --specialized om aan te geven dat de installatiekopie een gespecialiseerde installatiekopie is. 

Gebruik de id van de installatiekopiedefinitie voor `--image` om de schaalsetinstanties te maken op basis van de meest recente beschikbare versie van de installatiekopie. U kunt de schaalsetinstantie ook maken op basis van een specifieke versie door de id van de installatiekopieversie op te geven voor `--image`. 

Maak een schaalset met de naam *myScaleSet* met de meest recente versie van de installatiekopie *myImageDefinition* die u eerder hebt gemaakt.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u wilt dat verkeer de webserver kan bereiken, maakt u een load balancer-regel met behulp van [az network lb rule create](/cli/azure/network/lb/rule). In het volgende voorbeeld wordt een regel met de naam *myLoadBalancerRuleWeb* gemaakt die verkeer toestaat op *TCP*-poort *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Als u de schaalset in actie wilt zien, achterhaalt u het openbare IP-adres van uw load balancer met [az network public-ip show](/cli/azure/network/public-ip). In het volgende voorbeeld wordt het IP-adres voor *myScaleSetLBPublicIP* opgehaald, dat is gemaakt als onderdeel van de schaalset:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Voer in uw webbrowser het openbare IP-adres in. De standaard NGINX-webpagina wordt weergegeven, zoals in het volgende voorbeeld:

![NGINX uitgevoerd vanuit een aangepaste VM-installatiekopie](media/tutorial-use-custom-image-cli/default-nginx-website.png)



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

Zie [Azure-roltoewijzingen toevoegen of verwijderen met de Azure CLI](../role-based-access-control/role-assignments-cli.md) voor meer informatie over het delen van resources met behulp van Azure RBAC.


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [az group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een aangepaste VM-installatiekopie maakt en gebruikt voor uw schaalsets met Azure CLI:

> [!div class="checklist"]
> * Een gedeelde installatiekopiegalerie maken
> * Een gespecialiseerde installatiekopiedefinitie maken
> * De versie van een installatiekopie maken
> * Een schaalset maken op basis van een gespecialiseerde installatiekopie
> * Een galerie met installatiekopieën delen

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar uw schaalset.

> [!div class="nextstepaction"]
> [Toepassingen in uw schaalsets implementeren](tutorial-install-apps-cli.md)