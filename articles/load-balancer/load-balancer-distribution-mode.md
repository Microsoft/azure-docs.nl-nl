---
title: De Azure Load Balancer configureren
titleSuffix: Azure Load Balancer
description: In dit artikel gaat u aan de slag met het configureren van de distributiemodus voor Azure Load Balancer ondersteuning van bron-IP-affiniteit.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 0c6b845a8176054dc5ec6cfc239e609f568c925d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483610"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>De distributiemodus configureren voor Azure Load Balancer

Azure Load Balancer ondersteunt twee distributiemodi voor het distribueren van verkeer naar uw toepassingen:

* Op basis van hash
* Bron-IP-affiniteit

In dit artikel leert u hoe u de distributiemodus voor uw Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Distributiemodus configureren

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

U kunt de configuratie van de distributiemodus wijzigen door de taakverdelingsregel in de portal te wijzigen.

1. Meld u aan bij de Azure Portal zoek de resourcegroep met de load balancer u wilt wijzigen door te klikken op **Resourcegroepen.**
2. Selecteer in load balancer overzichtsscherm **Taakverdelingsregels onder** **Instellingen.**
3. Selecteer in het scherm Taakverdelingsregels de taakverdelingsregel die u de distributiemodus wilt wijzigen.
4. Onder de regel wordt de distributiemodus gewijzigd door het vervolgkeuzevak **Sessie persistentie** te wijzigen. 

De volgende opties zijn beschikbaar: 

* **Geen (op basis van hash)** - Geeft aan dat opeenvolgende aanvragen van dezelfde client kunnen worden verwerkt door een virtuele machine.
* **Client-IP (bron-IP-affiniteit twee tuples)** : hiermee geeft u op dat opeenvolgende aanvragen van hetzelfde client-IP-adres door dezelfde virtuele machine worden verwerkt.
* **Client-IP en -protocol (bron-IP-affiniteit** met drie tuples) : hiermee geeft u op dat opeenvolgende aanvragen van dezelfde combinatie van client-IP-adres en protocol door dezelfde virtuele machine worden verwerkt.

5. Kies de distributiemodus en selecteer vervolgens **Opslaan.**

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Wijzig de sessiepersistence voor load balancer regel." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik PowerShell om de distributie-instellingen voor de load balancer te wijzigen voor een bestaande taakverdelingsregel. Met de volgende opdracht wordt de distributiemodus bijgewerkt: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Stel de waarde van het `LoadDistribution` element in voor het type vereiste taakverdeling. 

* Geef **SourceIP op** voor taakverdeling met twee tuples (bron-IP en doel-IP). 

* Geef **SourceIPProtocol op** voor taakverdeling met drie tuples (bron-IP, doel-IP en protocoltype). 

* Geef **Standaard op** voor het standaardgedrag van taakverdeling met vijf tuples.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Gebruik Azure CLI om de distributie-instellingen voor de load balancer voor een bestaande taakverdelingsregel te wijzigen.  Met de volgende opdracht wordt de distributiemodus bijgewerkt:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Stel de waarde van `--load-distribution` in voor het vereiste type taakverdeling.

* Geef **SourceIP op** voor taakverdeling met twee tuples (bron-IP en doel-IP). 

* Geef **SourceIPProtocol op** voor taakverdeling met drie tuples (bron-IP, doel-IP en protocoltype). 

* Geef **Standaard op** voor het standaardgedrag van taakverdeling met vijf tuples.

Zie az [network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update) voor meer informatie over de opdracht die in dit artikel wordt gebruikt

---

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Load Balancer](load-balancer-overview.md)
* [Aan de slag met het configureren van een internet-load balancer](quickstart-load-balancer-standard-public-powershell.md)
* [TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)
