---
title: bestand opnemen
description: bestand opnemen
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0c05eb88df27ba9e71c0f61bad75457d893a7ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558355"
---
> [!NOTE]
> Deze voor beelden zijn niet van toepassing op configuraties met S2S/ExpressRoute naast elkaar.
> Zie voor meer informatie over het werken met gateways in een naast elkaar [bestaande verbindingen configureren.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Een gateway toevoegen

Wanneer u een gateway aan een virtueel netwerk toevoegt met behulp van het klassieke resource model, wijzigt u het netwerk configuratie bestand direct voordat u de gateway maakt. De waarden in de onderstaande voor beelden moeten aanwezig zijn in het bestand om een gateway te kunnen maken. Als er eerder een gateway aan het virtuele netwerk is gekoppeld, zijn sommige van deze waarden al aanwezig. Wijzig het bestand om de onderstaande waarden weer te geven.

### <a name="download-the-network-configuration-file"></a>Het netwerk configuratie bestand downloaden

1. Down load het netwerk configuratie bestand met behulp van de stappen in het artikel [netwerk configuratie bestand](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) . Open het bestand met een tekst editor.
2. Voeg een lokale netwerk site toe aan het bestand. U kunt elk geldig adres voorvoegsel gebruiken. U kunt elk geldig IP-adres voor de VPN-gateway toevoegen. De adres waarden in deze sectie worden niet gebruikt voor ExpressRoute-bewerkingen, maar zijn vereist voor bestands validatie. In het voor beeld is ' branch1 ' de naam van de site. U kunt een andere naam gebruiken, maar u moet dezelfde waarde gebruiken in het gedeelte gateway van het bestand.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Ga naar de VirtualNetworkSites en wijzig de velden.

   * Controleer of het gateway-subnet bestaat voor het virtuele netwerk. Als dat niet het geval is, kunt u er op dit moment een toevoegen. De naam moet ' GatewaySubnet ' zijn.
   * Controleer of het gedeelte gateway van het bestand bestaat. Als dat niet het geval is, voegt u deze toe. Dit is vereist om het virtuele netwerk te koppelen aan de lokale netwerk site (dat staat voor het netwerk waarmee u verbinding maakt).
   * Controleer of het verbindings type = dedicated. Dit is vereist voor ExpressRoute-verbindingen.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Sla het bestand op en upload het naar Azure.

### <a name="create-the-gateway"></a>De gateway maken

Gebruik de onderstaande opdracht om een gateway te maken. Vervang elke waarde door uw eigen waarden.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Controleren of de gateway is gemaakt

Gebruik de onderstaande opdracht om te controleren of de gateway is gemaakt. Met deze opdracht wordt ook de Gateway-ID opgehaald die u nodig hebt voor andere bewerkingen.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Het formaat van een gateway wijzigen

Er zijn verschillende [gateway-SKU's](../articles/expressroute/expressroute-about-virtual-network-gateways.md). U kunt de volgende opdracht gebruiken om de gateway-SKU op elk gewenst moment te wijzigen.

> [!IMPORTANT]
> Deze opdracht werkt niet voor een UltraPerformance-gateway. Als u uw gateway wilt wijzigen in een UltraPerformance-gateway, verwijdert u eerst de bestaande ExpressRoute-gateway en maakt u vervolgens een nieuwe UltraPerformance-gateway. Als u uw UltraPerformance-gateway wilt downgraden, verwijdert u eerst de UltraPerformance-gateway en maakt u vervolgens een nieuwe gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Een gateway verwijderen

Gebruik de onderstaande opdracht om een gateway te verwijderen

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```