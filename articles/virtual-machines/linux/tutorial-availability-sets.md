---
title: VM's implementeren in een beschikbaarheidsset met behulp van Azure CLI
description: In deze zelfstudie leert u hoe u de Azure CLI gebruikt om maximaal beschikbare virtuele machines in beschikbaarheidssets te implementeren
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7c45f08a339ca8878bb9e2840faa8a412f3e60e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765967"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Virtuele machines maken en implementeren in een beschikbaarheidsset met behulp van Azure CLI

In deze zelfstudie leert u hoe u de beschikbaarheid en betrouwbaarheid van uw Virtual Machine-oplossingen op Azure kunt verhogen met behulp van beschikbaarheidssets. Beschikbaarheidssets zorgen ervoor dat de VM's die u op Azure implementeert, verdeeld worden over meerdere geïsoleerde hardwareclusters. Dit zorgt ervoor dat als er zich binnen Azure een hardware- of softwarestoring voordoet, er slechts een subset van uw VM's wordt beïnvloed en dat uw totale oplossing beschikbaar en operationeel blijft.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een beschikbaarheidsset maken
> * Een VM maken in een beschikbaarheidsset
> * Beschikbare VM-grootten controleren

In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-availability-set"></a>Een beschikbaarheidsset maken

U kunt een beschikbaarheidsset maken met behulp van [az vm availability-set create](/cli/azure/vm/availability-set). In dit voorbeeld is het aantal update- en foutdomeinen ingesteld op *2* voor de beschikbaarheidsset met de naam *myAvailabilitySet* in de resourcegroep *ResourceGroupAvailability*.

Maak eerst een resourcegroep met [az group create](/cli/azure/group#az_group_create) en maak daarna de beschikbaarheidsset:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Met beschikbaarheidssets kunt u bronnen isoleren tussen foutdomeinen en domeinen bijwerken. Een **foutdomein** vertegenwoordigt een geïsoleerde verzameling van server- plus netwerk- en opslagresources. In het voorgaande voorbeeld wordt de beschikbaarheidsset verdeeld over minstens twee foutdomeinen wanneer de VM's worden geïmplementeerd. De beschikbaarheidsset is ook verdeeld over twee **updatedomeinen**. Twee updatedomeinen zorgen ervoor dat wanneer er software-updates worden uitgevoerd op Azure, de VM-resources worden geïsoleerd, zodat wordt voorkomen dat alle software onder de VM tegelijk wordt bijgewerkt.


## <a name="create-vms-inside-an-availability-set"></a>VM's maken in een beschikbaarheidsset

VM's moeten worden gemaakt binnen de beschikbaarheidsset om ervoor te zorgen dat ze correct over de hardware worden verdeeld. U kunt een bestaande VM niet toevoegen aan een beschikbaarheidsset nadat deze is gemaakt.

Wanneer er een VM is gemaakt met [az vm create](/cli/azure/vm), gebruikt u de parameter `--availability-set` om de naam van de beschikbaarheidsset op te geven.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

De beschikbaarheidsset bevat nu twee virtuele machines. Omdat ze zich in dezelfde beschikbaarheidsset bevinden, zorgt Azure ervoor dat de virtuele machines en alle bijbehorende resources (inclusief gegevensschijven) worden gedistribueerd over geïsoleerde fysieke hardware. Door deze verdeling is de beschikbaarheid van de algehele VM-oplossing veel hoger.

U kunt de verdeling van de beschikbaarheidsset bekijken in de portal door te gaan naar Resourcegroepen > myResourceGroupAvailability > myAvailabilitySet. De VM's zijn verdeeld over de twee fout- en updatedomeinen, zoals wordt weergegeven in het volgende voorbeeld:

![Beschikbaarheidsset in de portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Beschikbare VM-grootten controleren

Extra VM's kunnen later worden toegevoegd aan de beschikbaarheidsset, als VM-grootten beschikbaar zijn op de hardware. Gebruik [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) om een lijst weer te geven met alle beschikbare grootten voor de beschikbaarheidsset op de hardwarecluster:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een beschikbaarheidsset maken
> * Een VM maken in een beschikbaarheidsset
> * Beschikbare VM-grootten controleren

Ga naar de volgende zelfstudie voor meer informatie over virtuele-machineschaalsets.

> [!div class="nextstepaction"]
> [Een virtuele-machineschaalset maken](tutorial-create-vmss.md)

* Ga naar de [documentatie over beschikbaarheidszones](../../availability-zones/az-overview.md) voor meer informatie over beschikbaarheidszones.
* Meer documentatie over zowel beschikbaarheidssets als beschikbaarheidszones is ook [hier](../availability.md) beschikbaar.
* Als u beschikbaarheidszones wilt uitproberen, gaat u naar [Create a Linux virtual machine in an availability zone with the Azure CLI](./create-cli-availability-zone.md) (Een virtuele Linux-machine maken in een beschikbaarheidszone met de Azure CLI)