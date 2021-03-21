---
title: Niet-gekoppelde Azure-Nic's zoeken en verwijderen
description: Azure-Nic's zoeken en verwijderen die niet zijn gekoppeld aan Vm's met de Azure CLI
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016196"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Niet-gekoppelde netwerk interface kaarten (Nic's) zoeken en verwijderen voor Azure-Vm's
Wanneer u een virtuele machine (VM) in azure verwijdert, worden de netwerk interface kaarten (Nic's) niet standaard verwijderd. Als u meerdere Vm's maakt en verwijdert, blijven de interne IP-adresleases door de ongebruikte Nic's worden gebruikt. Wanneer u andere VM-Nic's maakt, kunnen ze mogelijk geen IP-lease verkrijgen in de adres ruimte van het subnet. In dit artikel wordt beschreven hoe u niet-gekoppelde Nic's zoekt en verwijdert.

## <a name="find-and-delete-unattached-nics"></a>Niet-gekoppelde NIC's zoeken en verwijderen

De eigenschap *virtualMachine* voor een NIC slaat de id en de resource groep op van de virtuele machine waaraan de NIC is gekoppeld. Met het volgende script worden alle Nic's in een abonnement door lopen en wordt gecontroleerd of de eigenschap *virtualMachine* null is. Als deze eigenschap Null is, is de NIC niet gekoppeld aan een virtuele machine.

Als u alle niet-gekoppelde Nic's wilt weer geven, is het raadzaam om eerst het script met de variabele *deleteUnattachedNics* uit te voeren op *0*. Als u alle niet-gekoppelde Nic's wilt verwijderen nadat u de lijst uitvoer hebt gecontroleerd, voert u het script uit met *deleteUnattachedNics* in *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Volgende stappen

Zie [VM-netwerken maken en beheren](tutorial-virtual-network.md)voor meer informatie over het maken en beheren van virtuele netwerken in Azure.
