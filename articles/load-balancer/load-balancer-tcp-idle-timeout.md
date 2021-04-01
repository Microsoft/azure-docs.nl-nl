---
title: load balancer TCP Reset en time-out voor inactiviteit configureren
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over het configureren van Azure Load Balancer TCP-time-out voor inactiviteit en opnieuw instellen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747214"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>TCP-opnieuw instellen en time-out voor inactiviteit voor Azure Load Balancer configureren

Azure Load Balancer heeft het volgende time-outbereik voor inactiviteit:

* 4 minuten tot 100 minuten voor uitgaande regels
* 4 minuten tot 30 minuten voor Load Balancer regels en binnenkomende NAT-regels

Standaard is deze ingesteld op 4 minuten. Als een periode van inactiviteit langer is dan de time-outwaarde, is er geen garantie dat de TCP-of HTTP-sessie tussen de client en uw service wordt gehandhaafd. 

In de volgende secties wordt beschreven hoe u de instellingen voor time-out voor inactiviteit en TCP-Reset wijzigt voor load balancer-resources.

## <a name="set-tcp-reset-and-idle-timeout"></a>TCP-Reset en time-out voor inactiviteit instellen
---
# <a name="portal"></a>[**Portal**](#tab/tcp-reset-idle-portal)

Als u de time-out voor inactiviteit en TCP-Reset voor een load balancer wilt instellen, bewerkt u de regel met gelijke taak verdeling. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **resource groepen** in het menu aan de linkerkant.

3. Selecteer de resource groep voor uw load balancer. In dit voor beeld heeft de resource groep de naam **myResourceGroup**.

4. Selecteer uw load balancer. In dit voor beeld heeft de load balancer de naam **myLoadBalancer**.

5. Selecteer bij **instellingen** de optie **taakverdelings regels**.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Load balancer regels bewerken." border="true":::

6. Selecteer de taakverdelings regel. In dit voor beeld heeft de taakverdelings regel de naam **myLBrule**.

7. In de taakverdelings regel verplaatst u de schuif regelaar in **time-out voor inactiviteit (minuten)** naar uw time-outwaarde.  

8. Onder **TCP Reset** selecteert u **ingeschakeld**.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Time-out voor inactiviteit en TCP Reset instellen." border="true":::

9. Selecteer **Opslaan**.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Als u de time-out voor inactiviteit en TCP Reset wilt instellen, stelt u waarden in de volgende para meters voor de taakverdelings regel in met [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Vervang de volgende voor beelden door de waarden van uw resources:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

Als u de time-out voor inactiviteit en TCP-Reset wilt instellen, gebruikt u de volgende para meters voor [AZ Network lb Rule update](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--time-out voor inactiviteit**
* **--Enable-TCP-Reset**

Valideer uw omgeving voordat u begint:

* Meld u aan bij de Azure-portal en controleer of uw abonnement actief is door `az login` uit te voeren.
* Controleer uw Azure CLI-versie in een terminal- of opdrachtvenster door `az --version` uit te voeren. Bekijk de [meest recente releaseopmerkingen](/cli/azure/release-notes-azure-cli?tabs=azure-cli) voor de nieuwste versie.
  * Als u de nieuwste versie niet hebt, werkt u uw installatie bij door de [installatiehandleiding voor uw besturingssysteem of platform](/cli/azure/install-azure-cli) te volgen.

Vervang de volgende voor beelden door de waarden van uw resources:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Volgende stappen

Zie [Load BALANCER TCP Reset en time-out voor inactiviteit](load-balancer-tcp-reset.md) voor meer informatie over TCP-time-out voor inactiviteit en opnieuw instellen

Zie [een Load Balancer distributie modus configureren](load-balancer-distribution-mode.md)voor meer informatie over het configureren van de Load Balancer distributie modus.
