---
title: Azure Load Balancer distributie modus configureren
titleSuffix: Azure Load Balancer
description: In dit artikel gaat u aan de slag met het configureren van de distributie modus voor Azure Load Balancer om de bron-IP-affiniteit te ondersteunen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 22d7af4f307a99d2d2e29bc1f494d327394e4f10
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594279"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>De distributie modus voor Azure Load Balancer configureren

Azure Load Balancer ondersteunt twee distributie modi voor het distribueren van verkeer naar uw toepassingen:

* Op basis van hash
* Bron-IP-affiniteit

In dit artikel leert u hoe u de distributie modus voor uw Azure Load Balancer kunt configureren.


## <a name="configure-distribution-mode"></a>Distributie modus configureren

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

U kunt de configuratie van de distributie modus wijzigen door de taakverdelings regel in de portal te wijzigen.

1. Meld u aan bij de Azure Portal en zoek de resource groep met de load balancer die u wilt wijzigen door te klikken op **resource groepen**.
2. Selecteer in het scherm load balancer overzicht de optie **taakverdelings regels** onder **instellingen**.
3. Selecteer in het scherm taakverdelings regels de taakverdelings regel waarvan u de distributie modus wilt wijzigen.
4. Onder de regel wordt de distributie modus gewijzigd door de vervolg keuzelijst **sessie persistentie** te wijzigen. 

De volgende opties zijn beschikbaar: 

* **Geen (op hash gebaseerd)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde client door elke virtuele machine kunnen worden verwerkt.
* **Client-IP (bron-IP-affiniteit Two-tuple)** : Hiermee geeft u op dat opeenvolgende aanvragen van hetzelfde client-IP-adres worden verwerkt door dezelfde virtuele machine.
* **Client-IP en protocol (bron-IP-affiniteit drie Tuples)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde combi natie van client-IP-adres en-protocol worden verwerkt door dezelfde virtuele machine.

5. Kies de distributie modus en selecteer vervolgens **Opslaan**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Wijzig de sessie persistentie op load balancer regel." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik Power shell om de distributie-instellingen voor Load Balancer te wijzigen voor een bestaande regel voor taak verdeling. De volgende opdracht werkt de distributie modus bij: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Stel de waarde van het `LoadDistribution` element in voor het type taak verdeling dat vereist is. 

* Geef **SourceIP** op voor de taak verdeling van twee Tuples (bron-IP en doel-IP). 

* Geef **SourceIPProtocol** op voor de taak verdeling voor drie Tuples (bron-IP, doel-IP en protocol type). 

* **Standaard waarde** voor het standaard gedrag van de taak verdeling van vijf Tuples opgeven.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Gebruik Azure CLI om de distributie-instellingen voor Load Balancer te wijzigen voor een bestaande regel voor taak verdeling.  De volgende opdracht werkt de distributie modus bij:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution SourceIP \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Stel de waarde van in `--load-distribution` voor het type taak verdeling dat vereist is.

* Geef **SourceIP** op voor de taak verdeling van twee Tuples (bron-IP en doel-IP). 

* Geef **SourceIPProtocol** op voor de taak verdeling voor drie Tuples (bron-IP, doel-IP en protocol type). 

* **Standaard waarde** voor het standaard gedrag van de taak verdeling van vijf Tuples opgeven.

Zie [AZ Network lb Rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update) (Engelstalig) voor meer informatie over de opdracht die in dit artikel wordt gebruikt.

---

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Load Balancer](load-balancer-overview.md)
* [Aan de slag met het configureren van een Internet gerichte load balancer](quickstart-load-balancer-standard-public-powershell.md)
* [TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)