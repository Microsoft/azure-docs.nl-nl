---
title: Bestaande virtuele-machineschaalsets bijwerken of verwijderen load balancer virtuele-machineschaalsets
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: In dit artikel gaat u aan de slag met Azure Standard Load Balancer en virtuele-machineschaalsets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 2079eeb97ac935ba24c6ff0616a58fbb28a962f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480083"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Een virtuele machine bijwerken of load balancer virtuele-machineschaalsets

Wanneer u werkt met virtuele-machineschaalsets en een exemplaar van Azure Load Balancer, kunt u het volgende doen:

- Regels toevoegen, bijwerken en verwijderen.
- Voeg configuraties toe.
- Verwijder de load balancer.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Een load balancer voor het uitschalen van virtuele-machineschaalsets

Zorg ervoor dat voor het exemplaar van Azure Load Balancer een [inkomende NAT-pool](/cli/azure/network/lb/inbound-nat-pool) is ingesteld en dat de virtuele-machineschaalset in de back-load balancer. Load Balancer maakt automatisch nieuwe inkomende NAT-regels in de binnenkomende NAT-pool wanneer nieuwe exemplaren van virtuele machines worden toegevoegd aan de virtuele-machineschaalset.

Controleren of de inkomende NAT-pool correct is ingesteld:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer alle resources in het menu **aan de linkerkant.** Selecteer vervolgens **MyLoadBalancer in** de lijst met resources.
1. Selecteer **onder Instellingen** de optie **Inkomende NAT-regels.** Als u in het rechterdeelvenster een lijst ziet met regels die zijn gemaakt voor elk afzonderlijk exemplaar in de virtuele-machineschaalset, kunt u op elk moment omhoog schalen.

## <a name="add-inbound-nat-rules"></a>Inkomende NAT-regels toevoegen

Afzonderlijke binnenkomende NAT-regels kunnen niet worden toegevoegd. U kunt echter een set inkomende NAT-regels met gedefinieerd front-endpoortbereik en back-endpoort toevoegen voor alle exemplaren in de virtuele-machineschaalset.

Als u een hele set inkomende NAT-regels voor de virtuele-machineschaalsets wilt toevoegen, maakt u eerst een inkomende NAT-pool in de load balancer. Verwijs vervolgens vanuit het netwerkprofiel van de virtuele-machineschaalset naar de binnenkomende NAT-pool. Er wordt een volledig voorbeeld weergegeven van het gebruik van de CLI.

De nieuwe binnenkomende NAT-pool mag geen overlappend front-endpoortbereik hebben met bestaande binnenkomende NAT-pools. Gebruik deze CLI-opdracht om bestaande inkomende NAT-pools weer te geven die [zijn ingesteld:](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)
  
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
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Inkomende NAT-regels bijwerken

Afzonderlijke binnenkomende NAT-regels kunnen niet worden bijgewerkt. U kunt echter een set inkomende NAT-regels bijwerken met een gedefinieerd front-endpoortbereik en een back-endpoort voor alle exemplaren in de virtuele-machineschaalset.

Als u een hele set inkomende NAT-regels voor virtuele-machineschaalsets wilt bijwerken, moet u de binnenkomende NAT-pool in de load balancer.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Inkomende NAT-regels verwijderen

Afzonderlijke binnenkomende NAT-regels kunnen niet worden verwijderd, maar u kunt de hele set binnenkomende NAT-regels verwijderen door de binnenkomende NAT-pool te verwijderen.

Als u de NAT-pool wilt verwijderen, verwijdert u deze eerst uit de schaalset. Hier wordt een volledig voorbeeld van het gebruik van de CLI weergegeven:

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
       -â€“lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Meerdere IP-configuraties toevoegen

Meerdere IP-configuraties toevoegen:

1. Selecteer alle resources in het **menu aan de linkerkant.** Selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
1. Selecteer **onder Instellingen** de optie **Front-end-IP-configuratie.** Selecteer vervolgens **Toevoegen**.
1. Voer op **de pagina FRONT-adres toevoegen** de waarden in en selecteer **OK.**
1. Volg [stap 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) en [stap 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) in deze zelfstudie als er nieuwe taakverdelingsregels nodig zijn.
1. Maak indien nodig een nieuwe set inkomende NAT-regels met behulp van de zojuist gemaakte front-end-IP-configuraties. Een voorbeeld vindt u in de vorige sectie.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Meerdere Virtual Machine Scale Sets achter één Load Balancer

Maak een inkomende NAT-pool in Load Balancer, verwijs naar de binnenkomende NAT-pool in het netwerkprofiel van een virtuele-machineschaalset en werk ten slotte de exemplaren bij om de wijzigingen door te voeren. Herhaal de stappen voor alle Virtual Machine Scale Sets.

Zorg ervoor dat u afzonderlijke binnenkomende NAT-pools met niet-overlappende front-endpoortbereiken maakt.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>De front-end-IP-configuratie verwijderen die wordt gebruikt door de virtuele-machineschaalset

De front-end-IP-configuratie verwijderen die wordt gebruikt door de schaalset:

 1. Verwijder eerst de binnenkomende NAT-pool (de set binnenkomende NAT-regels) die verwijst naar de front-end-IP-configuratie. Instructies voor het verwijderen van de regels voor binnenkomende gegevens vindt u in de vorige sectie.
 1. Verwijder de taakverdelingsregel die verwijst naar de front-end-IP-configuratie.
 1. Verwijder de front-end-IP-configuratie.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Een virtuele load balancer die wordt gebruikt door een virtuele-machineschaalset verwijderen

De front-end-IP-configuratie verwijderen die wordt gebruikt door de schaalset:

 1. Verwijder eerst de binnenkomende NAT-pool (de set binnenkomende NAT-regels) die verwijst naar de front-end-IP-configuratie. Instructies voor het verwijderen van de regels voor binnenkomende gegevens vindt u in de vorige sectie.
 1. Verwijder de taakverdelingsregel die verwijst naar de back-endpool die de virtuele-machineschaalset bevat.
 1. Verwijder de `loadBalancerBackendAddressPool` verwijzing uit het netwerkprofiel van de virtuele-machineschaalset.
 
 Hier wordt een volledig voorbeeld van het gebruik van de CLI weergegeven:

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

Lees meer over de concepten Azure Load Balancer over virtuele-machineschaalsets voor meer informatie over virtuele-machineschaalsets.

> [Azure Load Balancer virtuele-machineschaalsets](load-balancer-standard-virtual-machine-scale-sets.md)
