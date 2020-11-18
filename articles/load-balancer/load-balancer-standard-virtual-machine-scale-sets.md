---
title: Azure Standard Load Balancer en Virtual Machine Scale Sets
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Met dit leer traject kunt u aan de slag met Azure Standard Load Balancer en Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: fdca40d5113f06d185255be2e237cb52b47e9793
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697438"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer met schaal sets voor virtuele Azure-machines

Wanneer u werkt met schaal sets voor virtuele machines en load balancer, moet u rekening houden met de volgende richt lijnen:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Poort door sturen en binnenkomende NAT-regels:
  * Nadat de schaalset is gemaakt, kan de backend-poort niet worden gewijzigd voor een taakverdelings regel die wordt gebruikt door een status test van de load balancer. Als u de poort wilt wijzigen, kunt u de status test verwijderen door de schaalset voor virtuele Azure-machines bij te werken, de poort bij te werken en de status test vervolgens opnieuw te configureren.
  * Wanneer u de schaalset voor virtuele machines in de back-endadresgroep van de load balancer gebruikt, worden de standaard binnenkomende NAT-regels automatisch gemaakt.
## <a name="inbound-nat-pool"></a>Binnenkomende NAT-groep:
  * Elke schaalset voor virtuele machines moet ten minste één binnenkomende NAT-groep hebben. 
  * De binnenkomende NAT-pool is een verzameling van binnenkomende NAT-regels. Eén binnenkomende NAT-groep kan geen ondersteuning bieden voor meerdere schaal sets voor virtuele machines.
  * Als u een NAT-groep wilt verwijderen uit een bestaande schaalset voor virtuele machines, moet u eerst de NAT-groep verwijderen uit de schaalset. Hieronder ziet u een volledig voor beeld van het gebruik van CLI:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>Taakverdelings regels:
  * Wanneer u de schaalset voor virtuele machines in de back-endadresgroep van de load balancer gebruikt, wordt de standaard regel voor taak verdeling automatisch gemaakt.
## <a name="outbound-rules"></a>Uitgaande regels:
  *  Als u een uitgaande regel wilt maken voor een back-end-groep waarnaar al wordt verwezen door een taakverdelings regel, moet u eerst **"impliciete uitgaande regels maken"** als **Nee** in de portal markeren wanneer de regel voor binnenkomende taak verdeling wordt gemaakt.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Taak verdelings regel maken" border="true":::

De volgende methoden kunnen worden gebruikt voor het implementeren van een schaalset voor virtuele machines met een bestaande Azure-load balancer.

* [Een schaalset voor virtuele machines configureren met een bestaande Azure Load Balancer met behulp van de Azure Portal](./configure-vm-scale-set-portal.md).
* [Een schaalset voor virtuele machines configureren met een bestaande Azure Load Balancer met behulp van Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Een schaalset voor virtuele machines configureren met een bestaande Azure Load Balancer met behulp van de Azure cli](./configure-vm-scale-set-cli.md).