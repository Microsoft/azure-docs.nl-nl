---
title: Een bestaande load balancer die wordt gebruikt door virtuele-machine schaal sets bijwerken of verwijderen
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Met dit artikel kunt u aan de slag met Azure Standard Load Balancer en virtuele-machine schaal sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 1228462dc6437ecce7718c4747d2acb9ae7332cb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593037"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Een load balancer die wordt gebruikt door virtuele-machine schaal sets bijwerken of verwijderen

Wanneer u werkt met schaal sets voor virtuele machines en een exemplaar van Azure Load Balancer, kunt u het volgende doen:

- Regels toevoegen, bijwerken en verwijderen.
- Configuraties toevoegen.
- Verwijder de load balancer.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Een load balancer instellen voor het uitschalen van virtuele-machine schaal sets

Zorg ervoor dat het exemplaar van Azure Load Balancer een [binnenkomende NAT-groep](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) heeft ingesteld en dat de schaalset van de virtuele machine in de back-endadresgroep van de Load Balancer wordt geplaatst. Load Balancer maakt automatisch nieuwe binnenkomende NAT-regels in de binnenkomende NAT-groep wanneer er nieuwe exemplaren van virtuele machines worden toegevoegd aan de schaalset voor virtuele machines.

Controleren of de binnenkomende NAT-groep juist is ingesteld:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **alle resources** in het menu links. Selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
1. Selecteer onder **instellingen** **binnenkomende NAT-regels**. Als u in het rechterdeel venster een lijst met regels hebt gemaakt voor elk afzonderlijk exemplaar in de schaalset van de virtuele machine, kunt u op elk gewenst moment op elke keer naar omhoog schalen.

## <a name="add-inbound-nat-rules"></a>Inkomende NAT-regels toevoegen

Er kunnen geen afzonderlijke binnenkomende NAT-regels worden toegevoegd. Maar u kunt een set binnenkomende NAT-regels met een gedefinieerd front-end poort bereik en een back-end-poort toevoegen voor alle exemplaren in de schaalset van de virtuele machine.

Als u een volledige set binnenkomende NAT-regels voor de virtuele-machine schaal sets wilt toevoegen, maakt u eerst een binnenkomende NAT-groep in de load balancer. Ga vervolgens naar de binnenkomende NAT-pool van het netwerk profiel van de virtuele-machine schaalset. Een volledig voor beeld van het gebruik van CLI wordt weer gegeven.

De nieuwe binnenkomende NAT-groep mag geen overlappende front-end-poort bereik met bestaande binnenkomende NAT-Pools hebben. Als u bestaande binnenkomende NAT-groepen wilt weer geven die zijn ingesteld, gebruikt u deze [cli-opdracht](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list):
  
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
## <a name="update-inbound-nat-rules"></a>Inkomende NAT-regels bijwerken

Afzonderlijke binnenkomende NAT-regels kunnen niet worden bijgewerkt. Maar u kunt een set binnenkomende NAT-regels met een gedefinieerd front-end poort bereik en een back-end-poort voor alle exemplaren in de schaalset voor virtuele machines bijwerken.

Als u een hele set binnenkomende NAT-regels voor virtuele-machine schaal sets wilt bijwerken, werkt u de binnenkomende NAT-groep bij in het load balancer.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Inkomende NAT-regels verwijderen

Afzonderlijke binnenkomende NAT-regels kunnen niet worden verwijderd, maar u kunt de volledige set binnenkomende NAT-regels verwijderen door de binnenkomende NAT-groep te verwijderen.

Als u de NAT-pool wilt verwijderen, moet u deze eerst verwijderen uit de schaalset. Hier wordt een volledig voor beeld van het gebruik van CLI weer gegeven:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Meerdere IP-configuraties toevoegen

Meerdere IP-configuraties toevoegen:

1. Selecteer **alle resources** in het menu links. Selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
1. Selecteer bij **instellingen** de optie Front **-end-IP-configuratie**. Selecteer vervolgens **Toevoegen**.
1. Op de pagina **frontend IP-adres toevoegen** voert u de waarden in en selecteert u **OK**.
1. Volg [stap 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) en [stap 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) in deze zelf studie als er nieuwe taakverdelings regels nodig zijn.
1. Maak een nieuwe set binnenkomende NAT-regels met behulp van de zojuist gemaakte front-end-IP-configuraties, indien nodig. In de vorige sectie vindt u een voor beeld.

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>De front-end-IP-configuratie die wordt gebruikt door de virtuele-machine schaalset verwijderen

De front-end-IP-configuratie verwijderen die wordt gebruikt door de schaalset:

 1. Verwijder eerst de binnenkomende NAT-groep (de set met binnenkomende NAT-regels) die verwijst naar de front-end-IP-configuratie. Instructies voor het verwijderen van de regels voor binnenkomende verbindingen vindt u in de vorige sectie.
 1. Verwijder de taakverdelings regel die verwijst naar de front-end-IP-configuratie.
 1. Verwijder de front-end-IP-configuratie.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Een load balancer verwijderen die wordt gebruikt door een schaalset voor virtuele machines

De front-end-IP-configuratie verwijderen die wordt gebruikt door de schaalset:

 1. Verwijder eerst de binnenkomende NAT-groep (de set met binnenkomende NAT-regels) die verwijst naar de front-end-IP-configuratie. Instructies voor het verwijderen van de regels voor binnenkomende verbindingen vindt u in de vorige sectie.
 1. Verwijder de taakverdelings regel die verwijst naar de back-end-pool die de schaalset voor virtuele machines bevat.
 1. Verwijder de `loadBalancerBackendAddressPool` verwijzing van het netwerk profiel van de virtuele-machine schaalset.
 
 Hier wordt een volledig voor beeld van het gebruik van CLI weer gegeven:

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
Verwijder ten slotte de load balancer resource.
 
## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Azure Load Balancer en virtuele-machine schaal sets vindt u meer informatie over de concepten.

> [Azure Load Balancer met schaal sets voor virtuele machines](load-balancer-standard-virtual-machine-scale-sets.md)
