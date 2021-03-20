---
title: Virtual Machine Scale Sets beheren met de Azure CLI
description: Algemene Azure CLI-opdrachten voor het beheren van Virtual Machine Scale Sets, zoals het starten en stoppen van een exemplaar of het wijzigen van de capaciteit van de schaalset.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d954f7cdda4cae65f822489828226e0364d0fc29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570527"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Een schaalset voor virtuele machines beheren met de Azure CLI
Gedurende de levenscyclus van een schaalset voor virtuele machines moet u mogelijk een of meer beheertaken uitvoeren. Bovendien wilt u misschien scripts maken die verschillende levenscyclustaken automatiseren. In dit artikel vindt u meer informatie over de algemene Azure CLI-opdrachten waarmee u deze taken kunt uitvoeren.

U hebt de nieuwste Azure CLI nodig om deze beheer taken te volt ooien. Zie [de Azure cli installeren](/cli/azure/install-azure-cli)voor meer informatie. Als u een schaalset voor virtuele machines wilt maken, kunt u [een schaalset maken met de Azure cli](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Informatie over een schaalset weer geven
Gebruik [AZ vmss show](/cli/azure/vmss)om de algemene informatie over een schaalset weer te geven. In het volgende voor beeld wordt informatie opgehaald over de schaalset met de naam *myScaleSet* in de resource groep *myResourceGroup* . Voer uw eigen namen als volgt in:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Virtuele machines weergeven in een schaalset
Gebruik [AZ vmss list-instances](/cli/azure/vmss)om een lijst met VM-exemplaren in een schaalset weer te geven. In het volgende voor beeld worden alle VM-exemplaren in de schaalset met de naam *myScaleSet* in de resource groep *myResourceGroup* weer gegeven. Geef uw eigen waarden op voor deze namen:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Als u meer informatie wilt weer geven over een specifiek VM-exemplaar, voegt `--instance-id` u de para meter toe aan [AZ vmss Get-instance-View](/cli/azure/vmss) en geeft u een instantie op die u wilt weer geven. In het volgende voor beeld wordt informatie weer gegeven over het VM-exemplaar *0* in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . Voer uw eigen namen als volgt in:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

U kunt ook gedetailleerde informatie over *instanceView* ophalen voor alle exemplaren in één API-aanroep, waarmee u de API-beperking voor grote installaties kunt voor komen. Geef uw eigen waarden op voor `--resource-group` , `--subscription` en `--name` .

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

## <a name="list-connection-information-for-vms"></a>Verbindings gegevens voor Vm's weer geven
Als u verbinding wilt maken met de virtuele machines in een schaalset, moet u SSH of RDP naar een toegewezen openbaar IP-adres en poort nummer. Network Address Translation (NAT) regels worden standaard toegevoegd aan de Azure-load balancer die het verkeer van externe verbindingen naar elke virtuele machine doorstuurt. Als u het adres en de poorten wilt weer geven om verbinding te maken met VM-exemplaren in een schaalset, gebruikt u [AZ vmss List-instance-Connection-info](/cli/azure/vmss). In het volgende voor beeld ziet u verbindings gegevens voor VM-exemplaren in de schaalset met de naam *myScaleSet* en in de resource groep *myResourceGroup* . Geef uw eigen waarden op voor deze namen:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>De capaciteit van een schaalset wijzigen
De voor gaande opdrachten hebben informatie over de schaalset en de VM-exemplaren. Als u het aantal instanties in de schaalset wilt verg Roten of verkleinen, kunt u de capaciteit wijzigen. De schaalset maakt of verwijdert het vereiste aantal Vm's en configureert vervolgens de virtuele machines om toepassings verkeer te ontvangen.

Als u het aantal instanties wilt weergeven dat zich momenteel in een schaalset bevindt, gebruikt u [az vmss show](/cli/azure/vmss) en voert u een query uit op *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

U kunt vervolgens het aantal virtuele machines in de schaalset handmatig vergroten of verkleinen met [az vmss scale](/cli/azure/vmss). In het volgende voor beeld wordt het aantal Vm's in de schaalset ingesteld op *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Het duurt een paar minuten om de capaciteit van de schaalset bij te werken. Als u de capaciteit van een schaalset verlaagt, worden de Vm's met de hoogste exemplaar-Id's eerst verwijderd.


## <a name="stop-and-start-vms-in-a-scale-set"></a>VM’s in een schaalset stoppen en opstarten
Gebruik [AZ vmss stop](/cli/azure/vmss#az-vmss-stop)om een of meer vm's in een schaalset te stoppen. Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om te stoppen. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestopt. Als u meerdere Vm's wilt stoppen, scheidt u elke exemplaar-ID met een spatie.

In het volgende voor beeld wordt instance *0* in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* gestopt. U kunt als volgt uw eigen waarden opgeven:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Gestopt Vm's blijven toegewezen en blijven reken kosten in beslag. Als u in plaats daarvan wilt dat de toewijzing van Vm's ongedaan wordt gemaakt en alleen opslag kosten in rekening worden gebracht, gebruikt u [AZ vmss deallocate](/cli/azure/vmss). Als u meerdere Vm's wilt detoewijzen, moet u elke exemplaar-ID scheiden met een spatie. In het volgende voor beeld wordt de toewijzing van instantie *0* in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* gestopt en ongedaan gemaakt. U kunt als volgt uw eigen waarden opgeven:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Vm's in een schaalset starten
Gebruik [AZ vmss start](/cli/azure/vmss)om een of meer virtuele machines in een schaalset te starten. Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestart. Als u meerdere Vm's wilt starten, scheidt u elke exemplaar-ID met een spatie.

In het volgende voor beeld wordt instantie *0* gestart in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . U kunt als volgt uw eigen waarden opgeven:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>VM’s in een schaalset opnieuw opstarten
Gebruik [AZ vmss restart](/cli/azure/vmss)om een of meer vm's in een schaalset opnieuw op te starten. Met de parameter `--instance-ids` kunt u een of meer VM's opgeven om opnieuw op te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset opnieuw opgestart. Als u meerdere Vm's opnieuw wilt opstarten, scheidt u elke exemplaar-ID met een spatie.

In het volgende voor beeld wordt instantie *0* opnieuw gestart in de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . U kunt als volgt uw eigen waarden opgeven:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Vm's uit een schaalset verwijderen
Als u een of meer virtuele machines in een schaalset wilt verwijderen, gebruikt u [AZ vmss delete-instances](/cli/azure/vmss). Met de `--instance-ids` para meter kunt u een of meer vm's opgeven die moeten worden verwijderd. Als u * opgeeft voor de exemplaar-ID, worden alle Vm's in de schaalset verwijderd. Als u meerdere Vm's wilt verwijderen, moet u elke exemplaar-ID scheiden met een spatie.

In het volgende voor beeld wordt instantie *0* verwijderd uit de schaalset met de naam *myScaleSet* en de resource groep *myResourceGroup* . U kunt als volgt uw eigen waarden opgeven:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Volgende stappen
Andere algemene taken voor schaal sets zijn onder andere het [implementeren van een toepassing](virtual-machine-scale-sets-deploy-app.md)en het [bijwerken van VM-exemplaren](virtual-machine-scale-sets-upgrade-scale-set.md). U kunt ook Azure CLI gebruiken voor het [configureren van regels voor automatisch schalen](virtual-machine-scale-sets-autoscale-overview.md).
