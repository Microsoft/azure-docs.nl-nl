---
title: Uw Virtual Machine Scale Sets beheren met de Azure CLI
description: Algemene Azure CLI-opdrachten voor het beheren Virtual Machine Scale Sets, zoals het starten en stoppen van een exemplaar, of het wijzigen van de capaciteit van de schaalset.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9c2b2217fc6b32e5191bb67ffdaa10b796adf84b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762763"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Een virtuele-machineschaalset beheren met de Azure CLI
Gedurende de levenscyclus van een schaalset voor virtuele machines moet u mogelijk een of meer beheertaken uitvoeren. Bovendien wilt u misschien scripts maken die verschillende levenscyclustaken automatiseren. In dit artikel worden enkele van de algemene Azure CLI-opdrachten beschreven voor het uitvoeren van deze taken.

U hebt de nieuwste Azure CLI nodig om deze beheertaken uit te voeren. Zie Azure CLI installeren [voor meer informatie.](/cli/azure/install-azure-cli) Als u een virtuele-machineschaalset moet maken, kunt u [een schaalset maken met de Azure CLI.](quick-create-cli.md)


## <a name="view-information-about-a-scale-set"></a>Informatie over een schaalset weergeven
Gebruik [az vmss show](/cli/azure/vmss)om de algemene informatie over een schaalset weer te geven. In het volgende voorbeeld wordt informatie over de schaalset met de *naam myScaleSet* in de resourcegroep *myResourceGroup.* Voer als volgt uw eigen namen in:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Virtuele machines weergeven in een schaalset
Gebruik [az vmss list-instances](/cli/azure/vmss)om een lijst met VM-exemplaren in een schaalset weer te geven. In het volgende voorbeeld worden alle VM-exemplaren in de schaalset met de *naam myScaleSet* in de resourcegroep *myResourceGroup* vermeld. Geef uw eigen waarden op voor deze namen:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Als u aanvullende informatie over een specifiek VM-exemplaar wilt weergeven, voegt u de parameter toe aan `--instance-id` [az vmss get-instance-view](/cli/azure/vmss) en geeft u een exemplaar op dat u wilt weergeven. In het volgende voorbeeld wordt informatie over VM-exemplaar *0* in de schaalset met de naam *myScaleSet en* de resourcegroep *myResourceGroup* bekeken. Voer als volgt uw eigen namen in:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

U kunt ook gedetailleerde *instanceView-informatie* voor alle exemplaren in één API-aanroep krijgen, waarmee u API-beperking voor grote installaties kunt voorkomen. Geef uw eigen waarden op voor `--resource-group` `--subscription` , en `--name` .

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>Verbindingsgegevens voor VM's opslijst
Als u verbinding wilt maken met de VM's in een schaalset, maakt u via SSH of RDP verbinding met een toegewezen openbaar IP-adres en poortnummer. Standaard worden nat-regels (Network Address Translation) toegevoegd aan de Azure-load balancer die verkeer van externe verbindingen doorsturen naar elke VM. Gebruik [az vmss list-instance-connection-info](/cli/azure/vmss)om het adres en de poorten weer te geven die verbinding moeten maken met VM-exemplaren in een schaalset. In het volgende voorbeeld worden de verbindingsgegevens voor VM-exemplaren in de schaalset met de naam *myScaleSet en* in de resourcegroep *myResourceGroup* vermeld. Geef uw eigen waarden op voor deze namen:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>De capaciteit van een schaalset wijzigen
De voorgaande opdrachten bevatten informatie over uw schaalset en de VM-exemplaren. Als u het aantal exemplaren in de schaalset wilt vergroten of verlagen, kunt u de capaciteit wijzigen. De schaalset maakt of verwijdert het vereiste aantal VM's en configureert vervolgens de VM's om toepassingsverkeer te ontvangen.

Als u het aantal instanties wilt weergeven dat zich momenteel in een schaalset bevindt, gebruikt u [az vmss show](/cli/azure/vmss) en voert u een query uit op *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

U kunt vervolgens het aantal virtuele machines in de schaalset handmatig vergroten of verkleinen met [az vmss scale](/cli/azure/vmss). In het volgende voorbeeld wordt het aantal VM's in uw schaalset ingesteld *op 5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Het duurt een paar minuten om de capaciteit van de schaalset bij te werken. Als u de capaciteit van een schaalset verlaagt, worden de VM's met de hoogste instantie-ID's eerst verwijderd.


## <a name="stop-and-start-vms-in-a-scale-set"></a>VM’s in een schaalset stoppen en opstarten
Gebruik [az vmss stop](/cli/azure/vmss#az_vmss_stop)om een of meer VM's in een schaalset te stoppen. Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om te stoppen. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestopt. Als u meerdere VM's wilt stoppen, scheidt u elke exemplaar-id met een spatie.

In het volgende voorbeeld wordt exemplaar *0* gestopt in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Gestopte VM's blijven toegewezen en er worden nog steeds rekenkosten in rekening gebracht. Als u in plaats daarvan wilt dat de toewijzing van de VM's wordt teruggeplaatst en alleen opslagkosten in rekening worden gebracht, gebruikt u [az vmss deallocate](/cli/azure/vmss). Als u de toewijzing van meerdere VM's wilt herstellen, scheidt u elke exemplaar-id met een spatie. In het volgende voorbeeld wordt het exemplaar *0* in de schaalset *myScaleSet* en de resourcegroep *myResourceGroup* gestopt en wordt de toewijzing van de instantie in de schaalset weer in gebruik. Geef uw eigen waarden als volgt op:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>VM's starten in een schaalset
Gebruik [az vmss start](/cli/azure/vmss)om een of meer VM's in een schaalset te starten. Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestart. Als u meerdere VM's wilt starten, scheidt u elke exemplaar-id met een spatie.

In het volgende voorbeeld wordt exemplaar *0* gestart in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>VM’s in een schaalset opnieuw opstarten
Gebruik [az vmss restart](/cli/azure/vmss)om een of meer VM's in een schaalset opnieuw op te starten. Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om opnieuw op te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset opnieuw opgestart. Als u meerdere VM's opnieuw wilt opstarten, scheidt u elke exemplaar-id met een spatie.

In het volgende voorbeeld wordt exemplaar *0* opnieuw opgestart in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>VM's uit een schaalset verwijderen
Gebruik [az vmss delete-instances](/cli/azure/vmss)om een of meer VM's in een schaalset te verwijderen. Met `--instance-ids` de parameter kunt u een of meer VM's opgeven die u wilt verwijderen. Als u * opgeeft als exemplaar-id, worden alle VM's in de schaalset verwijderd. Als u meerdere VM's wilt verwijderen, scheidt u elke exemplaar-id met een spatie.

In het volgende voorbeeld wordt exemplaar *0* verwijderd uit de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Volgende stappen
Andere veelvoorkomende taken voor schaalsets zijn het [implementeren van een toepassing](virtual-machine-scale-sets-deploy-app.md)en het [upgraden van VM-exemplaren.](virtual-machine-scale-sets-upgrade-scale-set.md) U kunt Azure CLI ook gebruiken om regels [voor automatisch schalen te configureren.](virtual-machine-scale-sets-autoscale-overview.md)
