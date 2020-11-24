---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 76a602ae722bd975e634631819ebc703e8896c98
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560415"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk - geen gatewayverbinding

Ga als volgt te werk om aanvullende voorvoegsels toe te voegen:

1. Stel de variabele in voor LocalNetworkGateway.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. Wijzig de voorvoegsels.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

Ga als volgt te werk om adresvoorvoegsels te verwijderen:

  Laat de voorvoegsels weg die u niet langer nodig hebt. In dit voor beeld hebben we voor voegsel 10.101.2.0/24 (uit het vorige voor beeld) niet langer nodig, dus werken we de lokale netwerk gateway bij, met uitzonde ring van het voor voegsel.

1. Stel de variabele in voor LocalNetworkGateway.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. Stel de gateway in met de bijgewerkte voor voegsels.

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk - bestaande gatewayverbinding

Als u een gatewayverbinding hebt en u IP-adresvoorvoegsels wilt toevoegen aan of verwijderen uit uw lokale netwerkgateway, moet u de volgende stappen uitvoeren in de volgorde waarin ze staan vermeld. Dit veroorzaakt enige downtime in uw VPN-verbinding. Als u IP-adresvoorvoegsels wijzigt, hoeft u de VPN-gateway niet te verwijderen. U hoeft alleen de verbinding te verwijderen.

1. Verwijder de verbinding.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Stel de lokale netwerk gateway in met de gewijzigde adres voorvoegsels.
   
   Stel de variabele in voor LocalNetworkGateway.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
   
   Wijzig de voorvoegsels.
   
   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
3. Maak de verbinding. In dit voorbeeld configureren we een IPsec-verbindingstype. Wanneer u uw verbinding opnieuw maakt, gebruikt u het verbindingstype dat is opgegeven voor uw configuratie. Zie de pagina [PowerShell-cmdlet](/powershell/module/Azurerm.Network/New-AzureRmVirtualNetworkGatewayConnection) voor aanvullende verbindingstypen.
   
   Stel de variabele in voor VirtualNetworkGateway.

   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
   ```
   
   Maak de verbinding. In dit voorbeeld wordt de variabele $local gebruikt die u in stap 2 hebt ingesteld.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1 -Location 'East US' `
   -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec `
   -RoutingWeight 10 -SharedKey 'abc123'
   ```