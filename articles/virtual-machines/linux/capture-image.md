---
title: Een beheerde afbeelding van een linux-VM vastleggen met behulp van Azure CLI
description: Leg een beheerde installatie van een Azure-VM vast die moet worden gebruikt voor massaimplementaties met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: dddbad2403734bc749497a7acca16b2a5b6076f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792251"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Een beheerde afbeelding van een virtuele machine of VHD maken

Als u meerdere kopieën van een virtuele machine (VM) wilt maken voor gebruik in Azure voor ontwikkeling en testen, moet u een beheerde afbeelding van de virtuele machine of van de besturingssysteem-VHD vastleggen. Zie Galerieën met gedeelde afbeeldingen als u afbeeldingen op schaal wilt maken, opslaan [en delen.](../shared-images-cli.md)

Eén beheerde installatie afbeelding ondersteunt maximaal 20 gelijktijdige implementaties. Het gelijktijdig maken van meer dan 20 VM's op dezelfde beheerde afbeelding kan leiden tot time-outs vanwege de beperkingen van de opslagprestaties van één VHD. Als u meer dan 20 VM's tegelijk wilt maken, gebruikt u een installatie kopie van Galerieën met gedeelde installatie kopie die is geconfigureerd met 1 replica voor elke 20 gelijktijdige VM-implementaties. [](../shared-image-galleries.md)

Als u een beheerde afbeelding wilt maken, moet u persoonlijke accountgegevens verwijderen. In de volgende stappen maakt u de toewijzing van een bestaande VM op, maakt u de toewijzing ervan op en maakt u een -afbeelding. U kunt deze afbeelding gebruiken om VM's te maken in elke resourcegroep binnen uw abonnement.

Als u een kopie wilt maken van uw bestaande Linux-VM voor back-up of debuggen, of als u een gespecialiseerde Linux-VHD wilt uploaden vanaf een on-premises VM, gaat u naar [Upload and create a Linux VM from custom disk image (Een virtuele Linux-VM](upload-vhd.md)uploaden en maken op basis van een aangepaste schijfkopie).  

U kunt de **Azure VM Image Builder-service (openbare preview)** gebruiken om uw aangepaste installatiebestand te bouwen, u hoeft geen hulpprogramma's te leren of build-pijplijnen in te stellen. U hoeft alleen maar een installatieprogrammaconfiguratie op te geven, en de Image Builder maakt de installatie afbeelding. Zie voor meer informatie [Aan de slag met Azure VM Image Builder.](../image-builder-overview.md)

U hebt de volgende items nodig voordat u een afbeelding maakt:

* Een Azure-VM die is gemaakt in Resource Manager implementatiemodel dat gebruikmaakt van beheerde schijven. Als u nog geen linux-VM hebt gemaakt, kunt u de [portal,](quick-create-portal.md) [de Azure CLI](quick-create-cli.md)of de Resource Manager [gebruiken.](create-ssh-secured-vm-from-template.md) Configureer de VM naar behoefte. Voeg bijvoorbeeld [gegevensschijven toe,](add-disk.md)pas updates toe en installeer toepassingen. 

* De meest [recente versie van Azure CLI](/cli/azure/install-az-cli2) is geïnstalleerd en is aangemeld bij een Azure-account met az [login](/cli/azure/reference-index#az_login).

## <a name="prefer-a-tutorial-instead"></a>Geeft u in plaats daarvan de voorkeur aan een zelfstudie?

Zie Create a custom image of an Azure VM by using the CLI (Een aangepaste afbeelding van een Azure-VM maken met behulp van de CLI) voor een vereenvoudigde versie van dit artikel en voor het testen, evalueren of leren van [VM's](tutorial-custom-images.md)in Azure.  Anders kunt u hier blijven lezen om een volledig beeld te krijgen.


## <a name="step-1-deprovision-the-vm"></a>Stap 1: deprovisioning van de VM opschorten
Eerst maakt u de deprovisioning van de virtuele machine op met behulp van de Azure VM-agent om machinespecifieke bestanden en gegevens te verwijderen. Gebruik de `waagent`-opdracht met de parameter `-deprovision+user` op uw virtuele Linux-machine. Zie de [Gebruikershandleiding voor Azure Linux Agent](../extensions/agent-linux.md) voor meer informatie. Dit proces kan niet worden omgekeerd.

1. Maak verbinding met uw virtuele Linux-machine met een SSH-client.
2. Voer in het SSH-venster de volgende opdracht in:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Voer deze opdracht alleen uit op een virtuele machine die u vastlegt als een installatiekopie. Met deze opdracht wordt niet gegarandeerd dat de installatiekopie van alle gevoelige informatie wordt gewist of geschikt is voor herdistributie. Met de parameter `+user` wordt ook het laatste ingerichte gebruikersaccount verwijderd. Gebruik alleen `-deprovision` om de referenties van het gebruikersaccount in de virtuele machine te blijven gebruiken.
 
3. Voer **y** in om door te gaan. U kunt de parameter `-force` toevoegen om deze bevestigings stap te voorkomen.
4. Nadat de opdracht is voltooid, voert u **afsluiten** in om de SSH-client te sluiten.  De virtuele machine wordt nog steeds uitgevoerd.

## <a name="step-2-create-vm-image"></a>Stap 2: VM-afbeelding maken
Gebruik de Azure CLI om de VM te markeren als ge generaliseerd en de afbeelding vast te leggen. Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeeld van parameternamen *zijn myResourceGroup,* *myVnet* en *myVM.*

1. Wijs de toewijzing van de VM die u hebt gedeprovisioneerd, af [met az vm deallocate](/cli/azure/vm). In het volgende voorbeeld wordt de toewijzing van de VM met de *naam myVM* in de resourcegroep *myResourceGroup opheffen.*  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Wacht tot de toewijzing van de VM volledig is verbreed voordat u verder gaat. Dit kan enkele minuten duren.  De VM wordt afgesloten tijdens de toewijzing.

2. Markeer de VM als ge generaliseerd met [az vm generalize](/cli/azure/vm). In het volgende voorbeeld wordt de VM met de *naam myVM* in de resourcegroep *myResourceGroup* als ge generaliseerd markeert.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Een ge generaliseerde VM kan niet meer opnieuw worden opgestart.

3. Maak een afbeelding van de VM-resource met [az image create.](/cli/azure/image#az_image_create) In het volgende voorbeeld wordt een afbeelding met de *naam myImage* gemaakt in de resourcegroep met de *naam myResourceGroup* met behulp van de VM-resource *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > De afbeelding wordt gemaakt in dezelfde resourcegroep als uw bron-VM. Met deze afbeelding kunt u VM's maken in elke resourcegroep binnen uw abonnement. Vanuit het oogpunt van beheer wilt u mogelijk een specifieke resourcegroep maken voor uw VM-resources en -afbeeldingen.
   >
   > Als u uw afbeelding wilt opslaan in zone-flexibele opslag, moet u [](../../availability-zones/az-overview.md) deze maken in een regio die beschikbaarheidszones ondersteunt en de `--zone-resilient true` parameter opgeeft.
   
Deze opdracht retourneert JSON die de VM-afbeelding beschrijft. Sla deze uitvoer op voor later gebruik.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Stap 3: een VM maken van de vastgelegde afbeelding
Maak een VM met behulp van de -afbeelding die u hebt gemaakt [met az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de *naam myVMDeployed gemaakt* op de afbeelding met de *naam myImage.*

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>De VM in een andere resourcegroep maken 

U kunt VM's maken van een afbeelding in elke resourcegroep binnen uw abonnement. Als u een VM wilt maken in een andere resourcegroep dan de afbeelding, geeft u de volledige resource-id voor uw afbeelding op. Gebruik [az image list om](/cli/azure/image#az_image_list) een lijst met afbeeldingen weer te geven. De uitvoer lijkt op die in het volgende voorbeeld.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

In het volgende voorbeeld wordt [az vm create gebruikt](/cli/azure/vm#az_vm_create) om een VM te maken in een andere resourcegroep dan de bronafbeelding, door de resource-id van de afbeelding op te geven.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Stap 4: de implementatie controleren

Ga met SSH naar de virtuele machine die u hebt gemaakt om de implementatie te controleren en gebruik te maken van de nieuwe VM. Als u verbinding wilt maken via SSH, gaat u naar het IP-adres of de FQDN van uw VM [met az vm show.](/cli/azure/vm#az_vm_show)

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Volgende stappen
Zie Galerieën met gedeelde afbeeldingen als u afbeeldingen op schaal wilt maken, opslaan [en delen.](../shared-images-cli.md)
