---
title: Bestaande Azure Load Balancer bijwerken of verwijderen die wordt gebruikt door de virtuele-machineschaalset
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Aan de hand van dit artikel kunt u aan de slag met Azure Standard Load Balancer en Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790086"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Azure Load Balancer die door Virtual Machine Scale Sets worden gebruikt, bijwerken/verwijderen

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Azure Load Balancer instellen voor uitschalen Virtual Machine Scale Sets
  * Zorg ervoor dat de Load Balancer de [binnenkomende NAT-pool](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) heeft ingesteld en dat de schaalset van de virtuele machine in de back-endadresgroep van de Load Balancer wordt geplaatst. Azure Load Balancer maakt automatisch nieuwe binnenkomende NAT-regels in de binnenkomende NAT-groep wanneer er nieuwe exemplaren van virtuele machines worden toegevoegd aan de Schaalset voor virtuele machines. 
  * Om te controleren of de binnenkomende NAT-groep juist is ingesteld, 
  1. Meld u aan bij Azure Portal op https://portal.azure.com.
  
  1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
  
  1. Selecteer onder **instellingen** **binnenkomende NAT-regels**.
Als u in het rechterdeel venster ziet, een lijst met regels die zijn gemaakt voor elk afzonderlijk exemplaar in de Schaalset van de virtuele machine, is de gefeliciteerd dat u alles op elk gewenst moment kunt schalen.

## <a name="how-to-add-inbound-nat-rules"></a>Hoe kan ik binnenkomende NAT-regels toevoegen? 
  * Er kan geen afzonderlijke binnenkomende NAT-regel worden toegevoegd. U kunt echter een set binnenkomende NAT-regels met het gedefinieerde frontend-poort bereik en de back-endserver toevoegen voor alle exemplaren in de Schaalset voor virtuele machines.
  * Als u een volledige set binnenkomende NAT-regels voor de Virtual Machine Scale Sets wilt toevoegen, moet u eerst een binnenkomende NAT-groep maken in de Load Balancer en vervolgens verwijzen naar de binnenkomende NAT-groep van het netwerk profiel van de virtuele-machine Schaalset. Hieronder ziet u een volledig voor beeld van het gebruik van CLI.
  * De nieuwe binnenkomende NAT-pool mag geen overlappend frontend-poort bereik met bestaande binnenkomende NAT-groepen hebben. Als u bestaande binnenkomende NAT-groepen wilt weer geven, kunt u deze [cli-opdracht](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list) gebruiken
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>Hoe kan ik binnenkomende NAT-regels bijwerken? 
  * Afzonderlijke binnenkomende NAT-regel kan niet worden bijgewerkt. U kunt echter een set binnenkomende NAT-regels met het gedefinieerde frontend-poort bereik en de back-endserver bijwerken voor alle exemplaren in de Schaalset van de virtuele machine.
  * Als u een hele set binnenkomende NAT-regels voor de Virtual Machine Scale Sets wilt bijwerken, moet u de binnenkomende NAT-groep bijwerken in de Load Balancer. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Hoe kan ik binnenkomende NAT-regels verwijderen? 
* Afzonderlijke binnenkomende NAT-regel kan niet worden verwijderd. U kunt echter wel de volledige set binnenkomende NAT-regels verwijderen.
* Als u de volledige set binnenkomende NAT-regels wilt verwijderen die worden gebruikt door de Schaalset, moet u de NAT-pool eerst verwijderen uit de schaalset. Hieronder ziet u een volledig voor beeld van het gebruik van CLI:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Meerdere IP-configuraties toevoegen:
1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
   
1. Selecteer bij **instellingen** de optie **frontend IP-configuraties** en selecteer vervolgens **toevoegen**.
   
1. Typ op de pagina **frontend IP-adres toevoegen** de waarden en selecteer **OK** .

1. Volg [stap 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) en [stap 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) in deze zelf studie als er nieuwe taakverdelings regels nodig zijn

1. Maak indien nodig een nieuwe set binnenkomende NAT-regels met de nieuwe front-end-IP-configuraties. Hier vindt u een voor beeld in de [vorige sectie].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>De frontend-IP-configuratie verwijderen die wordt gebruikt door de Schaalset voor virtuele machines: 
 1. Als u de front-end-IP-configuratie die wordt gebruikt door de Schaalset wilt verwijderen, moet u eerst de binnenkomende NAT-groep (set met binnenkomende NAT-regels) verwijderen die verwijst naar de frontend-IP-configuratie. Instructies voor het verwijderen van de regels voor binnenkomende verbindingen vindt u in de vorige sectie.
 1. Verwijder de taakverdelings regel die verwijst naar de frontend-IP-configuratie. 
 1. Verwijder de frontend-IP-configuratie.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Azure Load Balancer verwijderen die worden gebruikt door de Schaalset voor virtuele machines: 
 1. Als u de front-end-IP-configuratie die wordt gebruikt door de Schaalset wilt verwijderen, moet u eerst de binnenkomende NAT-groep (set met binnenkomende NAT-regels) verwijderen die verwijst naar de frontend-IP-configuratie. Instructies voor het verwijderen van de regels voor binnenkomende verbindingen vindt u in de vorige sectie.
 
 1. Verwijder de taakverdelings regel die verwijst naar de back-end-groep met de Schaalset voor de virtuele machine.
 
 1. Verwijder de loadBalancerBackendAddressPool-verwijzing van het netwerk profiel van de virtuele-machine Schaalset. Hieronder ziet u een volledig voor beeld van het gebruik van CLI:
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
Verwijder ten slotte de Load Balancer resource.
 
## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Load Balancer en de Schaalset voor virtuele machines vindt u meer informatie over de concepten.

> [Azure Load Balancer met schaal sets voor virtuele Azure-machines](load-balancer-standard-virtual-machine-scale-sets.md)