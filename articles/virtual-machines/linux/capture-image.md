---
title: Een beheerde installatie kopie van een virtuele Linux-machine vastleggen met behulp van Azure CLI
description: Een beheerde installatie kopie van een virtuele Azure-machine vastleggen die moet worden gebruikt voor grootschalige implementaties met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: 8e81c204c1f05b7fc6bdf1efc7060e2094c648e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630619"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Een beheerde installatie kopie van een virtuele machine of VHD maken

Als u meerdere exemplaren van een virtuele machine (VM) wilt maken voor gebruik in azure voor ontwikkeling en testen, moet u een beheerde installatie kopie van de VM of van de VHD van het besturings systeem vastleggen. Zie voor meer informatie over het maken, opslaan en delen [van installatie kopieën](../shared-images-cli.md)op schaal.

Eén beheerde installatie kopie ondersteunt Maxi maal 20 gelijktijdige implementaties. Als u probeert om meer dan 20 Vm's gelijktijdig te maken, vanuit dezelfde beheerde installatie kopie, kan dit leiden tot het inrichten van time-outs als gevolg van de opslag prestatie beperkingen van één VHD. Als u meer dan 20 Vm's gelijktijdig wilt maken, gebruikt u een afbeelding voor [gedeelde afbeeldings galerieën](../shared-image-galleries.md) die is geconfigureerd met 1 replica voor elke 20 gelijktijdige VM-implementaties.

Als u een beheerde installatie kopie wilt maken, moet u persoonlijke account gegevens verwijderen. In de volgende stappen kunt u de inrichting van een bestaande virtuele machine ongedaan maken, de toewijzing ervan opheffen en een installatie kopie maken. U kunt deze installatie kopie gebruiken om Vm's te maken voor elke resource groep in uw abonnement.

Zie [een virtuele Linux-machine uploaden en maken op basis van een aangepaste schijf kopie](upload-vhd.md)om een kopie te maken van uw bestaande virtuele Linux-machine voor back-up of fout opsporing of om een speciale Linux-VHD te uploaden vanaf een on-premises VM.  

U kunt de **Azure VM Image Builder-service (open bare preview)** gebruiken om uw aangepaste installatie kopie te bouwen, geen hulp middelen meer te leren of door pijp lijnen voor het bouwen van een installatie kopie te maken, simpelweg een configuratie voor de installatie kopieën te bieden en de installatie kopie wordt gemaakt met de opbouw functie voor installatie kopieën. Zie aan de slag [met Azure VM Image Builder](../image-builder-overview.md)voor meer informatie.

Voordat u een installatie kopie maakt, hebt u de volgende items nodig:

* Een virtuele Azure-machine die is gemaakt in het Resource Manager-implementatie model dat gebruikmaakt van beheerde schijven. Als u nog geen virtuele Linux-machine hebt gemaakt, kunt u de [Portal](quick-create-portal.md), de [Azure cli](quick-create-cli.md)of de [Resource Manager-sjablonen](create-ssh-secured-vm-from-template.md)gebruiken. Configureer de virtuele machine als nodig. U kunt bijvoorbeeld [gegevens schijven toevoegen](add-disk.md), updates Toep assen en toepassingen installeren. 

* De nieuwste [Azure cli](/cli/azure/install-az-cli2) is geïnstalleerd en u moet zijn aangemeld bij een Azure-account met [AZ login](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Wilt u in plaats daarvan een zelf studie?

Zie [een aangepaste installatie kopie van een virtuele Azure-machine maken met behulp van de CLI](tutorial-custom-images.md)voor een vereenvoudigde versie van dit artikel en voor het testen, evalueren of leren over Vm's in Azure.  Als dat niet het geval is, kunt u hier lezen om de volledige afbeelding op te halen.


## <a name="step-1-deprovision-the-vm"></a>Stap 1: de inrichting van de virtuele machine ongedaan maken
Eerst moet u de inrichting van de virtuele machine ongedaan maken met behulp van de Azure VM-agent om computerspecifieke bestanden en gegevens te verwijderen. Gebruik de `waagent`-opdracht met de parameter `-deprovision+user` op uw virtuele Linux-machine. Zie de [Gebruikershandleiding voor Azure Linux Agent](../extensions/agent-linux.md) voor meer informatie. Dit proces kan niet ongedaan worden gemaakt.

1. Maak verbinding met uw virtuele Linux-machine met een SSH-client.
2. Voer in het SSH-venster de volgende opdracht in:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Voer deze opdracht alleen uit op een virtuele machine die u vastlegt als een installatiekopie. Met deze opdracht wordt niet gegarandeerd dat de installatiekopie van alle gevoelige informatie wordt gewist of geschikt is voor herdistributie. Met de parameter `+user` wordt ook het laatste ingerichte gebruikersaccount verwijderd. Gebruik alleen `-deprovision` om de referenties van het gebruikersaccount in de virtuele machine te blijven gebruiken.
 
3. Voer **y** in om door te gaan. U kunt de parameter `-force` toevoegen om deze bevestigings stap te voorkomen.
4. Nadat de opdracht is voltooid, voert u **afsluiten** in om de SSH-client te sluiten.  De virtuele machine wordt nog steeds uitgevoerd.

## <a name="step-2-create-vm-image"></a>Stap 2: VM-installatie kopie maken
Gebruik de Azure CLI om de virtuele machine als gegeneraliseerd te markeren en de installatie kopie vast te leggen. Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myVnet* en *myVM*.

1. Maak de toewijzing van de virtuele machine ongedaan die u hebt opgedaan met [AZ VM deallocate](/cli/azure/vm). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup* ongedaan gemaakt.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Wacht tot de virtuele machine volledig is vrijgegeven voordat u doorgaat met. Dit kan een paar minuten duren.  De virtuele machine wordt afgesloten tijdens de toewijzing.

2. Markeer de virtuele machine als gegeneraliseerd met [AZ VM generalize](/cli/azure/vm). In het volgende voor beeld wordt de virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup* als gegeneraliseerd gemarkeerd.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Een gegeneraliseerde VM kan niet meer opnieuw worden gestart.

3. Maak een installatie kopie van de VM-resource met [AZ image Create](/cli/azure/image#az-image-create). In het volgende voor beeld wordt een installatie kopie gemaakt met de naam *myImage* in de resource groep met de naam *MYRESOURCEGROUP* met de VM-resource met de naam *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > De installatie kopie wordt gemaakt in dezelfde resource groep als de bron-VM. U kunt in deze installatie kopie Vm's maken in elke resource groep in uw abonnement. Vanuit een beheer perspectief wilt u mogelijk een specifieke resource groep maken voor uw VM-resources en installatie kopieën.
   >
   > Als u uw installatie kopie wilt opslaan in zone-flexibele opslag, moet u deze maken in een regio die [beschikbaarheids zones](../../availability-zones/az-overview.md) ondersteunt en de `--zone-resilient true` para meter bevat.
   
Deze opdracht retourneert een JSON-bestand met een beschrijving van de VM-installatie kopie. Sla deze uitvoer op voor later naslag doeleinden.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Stap 3: een virtuele machine maken op basis van de vastgelegde installatie kopie
Maak een virtuele machine met behulp van de installatie kopie die u hebt gemaakt met [AZ VM Create](/cli/azure/vm). In het volgende voor beeld wordt een VM gemaakt met de naam *myVMDeployed* uit de installatie kopie met de naam *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>De virtuele machine in een andere resource groep maken 

U kunt Vm's maken op basis van een installatie kopie in een resource groep in uw abonnement. Als u een virtuele machine in een andere resource groep wilt maken dan de installatie kopie, geeft u de volledige Resource-ID op voor uw installatie kopie. Gebruik [AZ Image List](/cli/azure/image#az-image-list) om een lijst met installatie kopieën weer te geven. De uitvoer lijkt op die in het volgende voorbeeld.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

In het volgende voor beeld wordt [AZ VM Create](/cli/azure/vm#az-vm-create) gebruikt om een virtuele machine te maken in een andere resource groep dan de bron installatie kopie, door de resource-id van de installatie kopie op te geven.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Stap 4: de implementatie controleren

SSH naar de virtuele machine die u hebt gemaakt om de implementatie te controleren en te beginnen met het gebruik van de nieuwe VM. Als u verbinding wilt maken via SSH, zoekt u het IP-adres of de FQDN van uw virtuele machine met [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het maken, opslaan en delen [van installatie kopieën](../shared-images-cli.md)op schaal.
