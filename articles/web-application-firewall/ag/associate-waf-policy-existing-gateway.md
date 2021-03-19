---
title: Een firewall beleid voor webtoepassingen koppelen aan een bestaande Azure-toepassing gateway
description: Meer informatie over het koppelen van een Web Application firewall-beleid met een bestaande Azure-toepassing gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74083912"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Een WAF-beleid koppelen aan een bestaande Application Gateway

U kunt Azure PowerShell gebruiken om [een WAF-beleid te maken](create-waf-policy-ag.md), maar mogelijk hebt u al een Application Gateway en wilt u hieraan gewoon een WAF-beleid koppelen. In dit artikel hebt u het volgende gedaan: u maakt een WAF-beleid en koppelt dit aan een al bestaande Application Gateway. 

1. Uw Application Gateway-en firewall-beleid ophalen. Als u geen bestaand firewall beleid hebt, raadpleegt u stap 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. Beschrijving Maak een firewall beleid.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Als u dit WAF-beleid maakt om de overgang van een WAF-configuratie naar een WAF-beleid te maken, moet het beleid een exacte kopie van de oude configuratie zijn. Dit betekent dat elke uitsluiting, aangepaste regel, uitgeschakelde regel groep, enzovoort moet exact hetzelfde zijn als in de WAF-configuratie.
3. Beschrijving U kunt het WAF-beleid configureren om aan uw behoeften aan te passen. Dit omvat aangepaste regels, het uitschakelen van regels/regel groepen, uitsluitingen, het instellen van limieten voor het uploaden van bestanden, enzovoort. Als u deze stap overs laat, worden alle standaard waarden geselecteerd. 
   
4. Sla het beleid op en koppel dit aan uw Application Gateway. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over aangepaste regels.](configure-waf-custom-rules.md)
