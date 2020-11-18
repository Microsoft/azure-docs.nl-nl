---
title: 'Een VNet verbinden met meerdere sites met behulp van VPN Gateway: klassiek'
description: Meerdere lokale on-premises sites verbinden met een klassiek virtueel netwerk met behulp van een VPN Gateway.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 168bb9e06c73ec27ec1304813023889c9549b8e6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660692"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Een site-naar-site-verbinding met een VNet toevoegen met een bestaande VPN-gateway verbinding (klassiek)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassiek)](vpn-gateway-multi-site.md)
>
>

Dit artikel helpt u bij het gebruik van Power shell om S2S-verbindingen (site-naar-site) toe te voegen aan een VPN-gateway die een bestaande verbinding heeft. Dit type verbinding wordt vaak een ' multi-site ' configuratie genoemd. De stappen in dit artikel zijn van toepassing op virtuele netwerken die zijn gemaakt met het klassieke implementatie model (ook wel bekend als Service beheer). Deze stappen zijn niet van toepassing op ExpressRoute/site-to-site naast elkaar bestaande verbindings configuraties.

### <a name="deployment-models-and-methods"></a>Implementatiemodellen en -methoden

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Deze tabel wordt bijgewerkt naar nieuwe artikelen en er worden extra hulpprogram ma's beschikbaar voor deze configuratie. Wanneer er een artikel beschikbaar is, wordt er rechtstreeks vanuit deze tabel een koppeling naar de sjabloon.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Verbinding maken

U kunt meerdere on-premises sites verbinden met één virtueel netwerk. Dit is met name handig voor het bouwen van hybride cloud oplossingen. Het maken van een multi-site-verbinding met uw virtuele Azure-netwerk gateway is vergelijkbaar met het maken van andere site-naar-site-verbindingen. U kunt een bestaande Azure VPN-gateway gebruiken, zolang de gateway dynamisch is (op route gebaseerd).

Als u al een statische gateway hebt verbonden met uw virtuele netwerk, kunt u het gateway type wijzigen in dynamisch, zonder dat u het virtuele netwerk opnieuw hoeft op te bouwen om ruimte te bieden aan meerdere locaties. Voordat u het routerings type wijzigt, moet u ervoor zorgen dat uw on-premises VPN-gateway op route gebaseerde VPN-configuraties ondersteunt.

![diagram voor meerdere locaties](./media/vpn-gateway-multi-site/multisite.png "meerdere locaties")

## <a name="points-to-consider"></a>Punten om in overweging te nemen

**U kunt de portal niet gebruiken om wijzigingen aan te brengen in dit virtuele netwerk.** U moet wijzigingen aanbrengen in het netwerk configuratie bestand in plaats van de portal te gebruiken. Als u wijzigingen aanbrengt in de portal, worden de referentie-instellingen voor meerdere sites voor dit virtuele netwerk overschreven.

U kunt het beste het netwerk configuratie bestand gebruiken op het moment dat u de procedure voor meerdere locaties hebt voltooid. Als u echter meerdere mensen aan uw netwerk configuratie wilt laten werken, moet u ervoor zorgen dat iedereen over deze beperking weet. Dit betekent niet dat u de portal helemaal niet kunt gebruiken. U kunt het gebruiken voor alle andere, behalve dat u configuratie wijzigingen aanbrengt in dit specifieke virtuele netwerk.

## <a name="before-you-begin"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u over het volgende beschikt:

* Compatibele VPN-hardware voor elke on-premises locatie. Controleer [over VPN-apparaten op Virtual Network connectiviteit](vpn-gateway-about-vpn-devices.md) om te controleren of het apparaat dat u wilt gebruiken, iets is dat bekend is om compatibel te zijn.
* Een extern gericht openbaar IPv4-IP-adres voor elk VPN-apparaat. Het IP-adres kan zich niet achter een NAT bevinden. Dit is vereist.
* Iemand die zich kan bezig zijn met het configureren van uw VPN-hardware. U moet een duidelijk beeld hebben van de manier waarop u uw VPN-apparaat kunt configureren of met iemand die wel of niet werkt.
* De IP-adresbereiken die u wilt gebruiken voor het virtuele netwerk (als u er nog geen hebt gemaakt).
* De IP-adresbereiken voor elk van de lokale netwerk sites waarmee u verbinding maakt. U moet ervoor zorgen dat de IP-adresbereiken voor elk van de lokale netwerk sites waarmee u verbinding wilt maken, elkaar niet overlappen. Anders wordt de configuratie die wordt geüpload geweigerd door de portal of het REST API.<br>Als u bijvoorbeeld twee lokale netwerk sites hebt die het IP-adres bereik 10.2.3.0/24 bevatten en u een pakket hebt met een doel adres 10.2.3.3, weet Azure niet welke site u het pakket wilt sturen, omdat de adresbereiken elkaar overlappen. Om routerings problemen te voor komen, is het niet toegestaan om een configuratie bestand met overlappende bereiken te uploaden met Azure.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Maak een site-naar-site-VPN
Als u al een site-naar-site-VPN hebt met een dynamische routerings gateway, fantastisch! U kunt door gaan met [het exporteren van de configuratie-instellingen van het virtuele netwerk](#export). Als dat niet het geval is, gaat u als volgt te werk:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Als u al een site-naar-site-virtueel netwerk hebt, maar dit een statische routerings gateway (op beleid gebaseerd) heeft:
1. Wijzig uw gateway type in dynamische route ring. Een VPN voor meerdere locaties vereist een dynamische routerings gateway (ook wel bekend als op route gebaseerd). Als u het gateway type wilt wijzigen, moet u eerst de bestaande gateway verwijderen en vervolgens een nieuwe maken.
2. Configureer uw nieuwe gateway en maak uw VPN-tunnel. Zie voor instructies [het type SKU en VPN opgeven](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Zorg ervoor dat u het routerings type opgeeft als dynamisch.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Als u geen site-naar-site-virtueel netwerk hebt:
1. Maak uw site-naar-site-virtueel netwerk met behulp van deze instructies: [Maak een Virtual Network met een site-naar-site-VPN-verbinding](./vpn-gateway-howto-site-to-site-classic-portal.md).  
2. Een dynamische routerings gateway configureren met behulp van deze instructies: [Configure a VPN gateway](./vpn-gateway-howto-site-to-site-classic-portal.md). Zorg ervoor dat u **dynamische route ring** selecteert voor uw gateway type.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. het netwerk configuratie bestand exporteren

Open de Power shell-console met verhoogde bevoegdheden. Als u wilt overschakelen naar Service beheer, gebruikt u deze opdracht:

```powershell
azure config mode asm
```

Maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Add-AzureAccount
```

Exporteer uw Azure-netwerk configuratie bestand door de volgende opdracht uit te voeren. Als dat nodig is, kunt u de locatie van het bestand wijzigen zodat het naar een andere locatie kan worden geëxporteerd.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Open het netwerk configuratie bestand
Open het netwerk configuratie bestand dat u in de laatste stap hebt gedownload. Gebruik een XML-editor die u wilt gebruiken. Het bestand moet er ongeveer als volgt uitzien:

```xml
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
          <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site2">
        <AddressSpace>
          <AddressPrefix>10.2.0.0/16</AddressPrefix>
          <AddressPrefix>10.3.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
        <AddressSpace>
          <AddressPrefix>10.20.0.0/16</AddressPrefix>
          <AddressPrefix>10.21.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FE">
            <AddressPrefix>10.20.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BE">
            <AddressPrefix>10.20.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.20.2.0/29</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

## <a name="4-add-multiple-site-references"></a>4. Voeg meerdere site verwijzingen toe
Wanneer u site referentie gegevens toevoegt of verwijdert, moet u configuratie wijzigingen aanbrengen in de ConnectionsToLocalNetwork/LocalNetworkSiteRef. Als u een nieuwe lokale site verwijzing toevoegt, wordt Azure geactiveerd om een nieuwe tunnel te maken. In het onderstaande voor beeld is de netwerk configuratie voor een verbinding met één site. Sla het bestand op wanneer u klaar bent met het maken van de wijzigingen.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Als u aanvullende site verwijzingen wilt toevoegen (Maak een configuratie met meerdere locaties), voegt u extra ' LocalNetworkSiteRef-regels toe, zoals wordt weer gegeven in het voor beeld hieronder:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importeer het netwerk configuratie bestand
Importeer het netwerk configuratie bestand. Wanneer u dit bestand importeert met de wijzigingen, worden de nieuwe tunnels toegevoegd. De tunnels gebruiken de dynamische gateway die u eerder hebt gemaakt. U kunt Power shell gebruiken om het bestand te importeren.

## <a name="6-download-keys"></a>6. sleutels downloaden
Zodra de nieuwe tunnels zijn toegevoegd, gebruikt u de Power shell-cmdlet Get-AzureVNetGatewayKey om de vooraf gedeelde IPsec/IKE-sleutels voor elke tunnel op te halen.

Bijvoorbeeld:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Als u wilt, kunt u ook de *gedeelde rest API sleutel Virtual Network gateway ophalen* gebruiken om de vooraf gedeelde sleutels op te halen.

## <a name="7-verify-your-connections"></a>7. Controleer uw verbindingen
Controleer de tunnel status voor meerdere sites. Nadat u de sleutels voor elke tunnel hebt gedownload, wilt u de verbindingen controleren. Gebruik ' Get-AzureVnetConnection ' om een lijst met virtuele netwerk tunnels op te halen, zoals wordt weer gegeven in het onderstaande voor beeld. VNet1 is de naam van het VNet.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Voor beeld van resultaat:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Volgende stappen

Zie [over VPN-gateways](vpn-gateway-about-vpngateways.md)voor meer informatie over VPN-gateways.