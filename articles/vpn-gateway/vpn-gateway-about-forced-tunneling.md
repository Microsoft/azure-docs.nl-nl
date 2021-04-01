---
title: 'Azure VPN Gateway: geforceerde tunneling configureren-site-naar-site-verbindingen: klassiek'
description: Meer informatie over het configureren van geforceerde tunneling voor virtuele netwerken die zijn gemaakt met het klassieke implementatie model.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: af4359efb48898c12bb8ee7ffb882448b5012d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151342"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Geforceerde tunneling met het klassieke implementatiemodel configureren

Met geforceerde tunneling kunt u alle voor internet bestemde verkeer geforceerd terugsturen naar uw on-premises locatie via een site-naar-site-VPN-tunnel voor inspectie en controle. Dit is een kritieke beveiligings vereiste voor de meeste IT-beleids regels van ondernemingen. Zonder geforceerde tunneling gaat Internet gebonden verkeer van uw virtuele machines in azure altijd rechtstreeks door naar Internet, zonder de optie om het verkeer te controleren of te controleren. Onbevoegde toegang tot internet kan mogelijk leiden tot vrijgeven van informatie of andere typen beveiligings Risico's.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Dit artikel begeleidt u bij het configureren van geforceerde tunneling voor virtuele netwerken die zijn gemaakt met het klassieke implementatie model. Geforceerde tunneling kan worden geconfigureerd met behulp van Power shell, niet via de portal. Als u geforceerde tunneling wilt configureren voor het Resource Manager-implementatie model, selecteert u Resource Manager-artikel in de volgende vervolg keuzelijst:

> [!div class="op_single_selector"]
> * [Klassieke](vpn-gateway-about-forced-tunneling.md)
> * [Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 

## <a name="requirements-and-considerations"></a>Vereisten en overwegingen

Geforceerde Tunneling in azure wordt geconfigureerd via door de gebruiker gedefinieerde routes van het virtuele netwerk (UDR). Het omleiden van verkeer naar een on-premises site wordt uitgedrukt als een standaard route naar de Azure VPN-gateway. In de volgende sectie worden de huidige beperkingen van de routerings tabel en routes voor een Azure-Virtual Network vermeld:

* Elk subnet van het virtuele netwerk heeft een ingebouwde systeem routerings tabel. De systeem routerings tabel heeft de volgende drie groepen routes:

  * **Lokale VNet-routes:** Rechtstreeks naar de doel-Vm's in hetzelfde virtuele netwerk.
  * **On-premises routes:** Naar de Azure VPN-gateway.
  * **Standaard route:** Rechtstreeks op internet. Pakketten die bestemd zijn voor de privé-IP-adressen die niet worden gedekt door de vorige twee routes, worden verwijderd.
* Met de release van door de gebruiker gedefinieerde routes kunt u een routerings tabel maken om een standaard route toe te voegen en vervolgens de routerings tabel koppelen aan uw VNet-subnet ('s) om geforceerde Tunneling in te scha kelen voor deze subnetten.
* U moet een "standaard site" instellen tussen de cross-premises lokale sites die zijn verbonden met het virtuele netwerk.
* Geforceerde tunneling moet worden gekoppeld aan een VNet dat een VPN-gateway voor dynamische route ring (geen statische gateway) heeft.
* ExpressRoute geforceerde tunneling is niet geconfigureerd via dit mechanisme, maar wordt in plaats daarvan ingeschakeld door een standaard route te adverteren via de BGP-peering-sessies van ExpressRoute. Zie [Wat is ExpressRoute?](../expressroute/expressroute-introduction.md)voor meer informatie.

## <a name="configuration-overview"></a>Configuratieoverzicht

In het volgende voor beeld wordt het frontend-subnet niet geforceerd getunneld. De werk belastingen in het frontend-subnet kunnen rechtstreeks blijven accepteren en reageren op aanvragen van de klant via internet. De subnetten van de middelste laag en de back-end worden geforceerd getunneld. Uitgaande verbindingen van deze twee subnetten met Internet worden via een van de S2S VPN-tunnels geforceerd of teruggeleid naar een on-premises site.

Hierdoor kunt u Internet toegang beperken en inspecteren vanuit uw virtuele machines of Cloud Services in azure, terwijl u doorgaat met het inschakelen van de service architectuur met meerdere lagen. U kunt geforceerde tunneling ook Toep assen op de volledige virtuele netwerken als er geen Internet werk belastingen aanwezig zijn in uw virtuele netwerken.

![Geforceerde tunneling](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="prerequisites"></a>Vereisten

Controleer of u beschikt over de volgende items voordat u begint met de configuratie:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* Een geconfigureerd virtueel netwerk. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="configure-forced-tunneling"></a>Geforceerde tunneling configureren

De volgende procedure helpt u bij het opgeven van geforceerde tunneling voor een virtueel netwerk. De configuratie stappen komen overeen met het VNet-netwerk configuratie bestand.  In dit voor beeld heeft het virtuele netwerk ' Multi laag-VNet ' drie subnetten: ' front-end ', ' het midtier ' en ' back-end '-subnetten, met vier cross-premises verbindingen: ' DefaultSiteHQ ' en drie vertakkingen.


```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Met de volgende stappen wordt de ' DefaultSiteHQ ' ingesteld als de standaard site verbinding voor geforceerde tunneling en worden de het midtier-en back-end-subnetten geconfigureerd voor het gebruik van geforceerde tunneling.

1. Open de Power shell-console met verhoogde bevoegdheden. Maak verbinding met uw account met behulp van het volgende voor beeld:

   ```powershell
   Add-AzureAccount
   ```

1. Een routerings tabel maken. Gebruik de volgende cmdlet om uw route tabel te maken.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

1. Een standaard route toevoegen aan de routerings tabel. 

   In het volgende voor beeld wordt een standaard route toegevoegd aan de routerings tabel die u in stap 1 hebt gemaakt. Houd er rekening mee dat de enige route die wordt ondersteund het doel voorvoegsel van ' 0.0.0.0/0 ' is voor de NextHop ' VPNGateway '.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

1. Koppel de routerings tabel aan de subnetten. 

   Nadat een routerings tabel is gemaakt en een route is toegevoegd, gebruikt u het volgende voor beeld om de route tabel toe te voegen aan of te koppelen aan een VNet-subnet. In het voor beeld wordt de route tabel ' MyRouteTable ' toegevoegd aan de het midtier-en back-end-subnetten van VNet meerdere lagen-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

1. Wijs een standaard site toe voor geforceerde tunneling. 

   In de vorige stap hebben de voor beeld-cmdlet scripts de routerings tabel gemaakt en de route tabel gekoppeld aan twee van de VNet-subnetten. De resterende stap bestaat uit het selecteren van een lokale site tussen de multi-site-verbindingen van het virtuele netwerk als de standaard site of-tunnel.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Extra Power shell-cmdlets

### <a name="to-delete-a-route-table"></a>Een route tabel verwijderen

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Een route tabel weer geven

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Een route uit een route tabel verwijderen

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Een route uit een subnet verwijderen

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>De route tabel weer geven die is gekoppeld aan een subnet

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Een standaard site verwijderen uit een VNet VPN-gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
