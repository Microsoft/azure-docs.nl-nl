---
title: Virtuele-machine schaal sets configureren met een bestaande Azure Load Balancer-Azure CLI
description: Meer informatie over het configureren van een schaalset voor virtuele machines met een bestaande Azure Load Balancer met behulp van de Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518206"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Een schaalset voor virtuele machines configureren met een bestaande Azure Load Balancer met behulp van de Azure CLI

In dit artikel leert u hoe u een schaalset voor virtuele machines kunt configureren met een bestaande Azure Load Balancer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten 

- U hebt een bestaande standaard SKU nodig load balancer in het abonnement waarin de schaalset van de virtuele machine wordt geïmplementeerd.

- U hebt een Azure-Virtual Network nodig voor de schaalset van de virtuele machine.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Een schaalset voor virtuele machines implementeren met bestaande load balancer

Vervang de waarden tussen vier Kante haken door de namen van de resources in uw configuratie.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

In het onderstaande voor beeld wordt een schaalset voor virtuele machines geïmplementeerd met:

- Virtuele-machine schaalset met de naam **myVMSS**
- Azure Load Balancer met de naam **myLoadBalancer**
- Back-end van Load Balancer-groep met de naam **myBackendPool**
- Azure Virtual Network met de naam **myVnet**
- Subnet met de naam **mySubnet**
- Resource groep met de naam **myResourceGroup**
- Ubuntu-Server installatie kopie voor de schaalset voor virtuele machines

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Nadat de schaalset is gemaakt, kan de backend-poort niet worden gewijzigd voor een taakverdelings regel die wordt gebruikt door een status test van de load balancer. Als u de poort wilt wijzigen, kunt u de status test verwijderen door de schaalset voor virtuele Azure-machines bij te werken, de poort bij te werken en de status test vervolgens opnieuw te configureren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een schaalset voor virtuele machines geïmplementeerd met een bestaande Azure Load Balancer.  Zie voor meer informatie over virtuele-machine schaal sets en load balancer:

- [Wat is Azure Load Balancer?](load-balancer-overview.md)
- [Wat zijn schaalsets voor virtuele machines?](../virtual-machine-scale-sets/overview.md)
                                