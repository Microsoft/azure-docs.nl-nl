---
title: 'ExpressRoute-en S2S-VPN-verbindingen configureren: klassiek'
description: Dit artikel begeleidt u bij het configureren van ExpressRoute- en site-naar-site-VPN-verbindingen die naast elkaar kunnen worden gebruikt in het klassieke implementatiemodel.
documentationcenter: na
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: 70407d726b697dd6c4dfb6777cf6c445092d3b73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206322"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Gelijktijdige ExpressRoute- en site-to-site-verbindingen configureren (klassiek)
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [Power shell-klassiek](expressroute-howto-coexist-classic.md)
> 
> 

In dit artikel leest u hoe u ExpressRoute- en site-naar-site-VPN-verbindingen configureert die naast elkaar kunnen worden gebruikt. De mogelijkheid om site-naar-site-VPN en ExpressRoute te configureren heeft verschillende voordelen. U kunt een site-naar-site-VPN configureren als een beveiligd failoverpad voor ExpressRoute, of site-naar-site-VPN's gebruiken om verbinding te maken met sites die niet via ExpressRoute zijn verbonden. In dit artikel gaan we in op de stappen voor het configureren van beide scenario's. Dit artikel is van toepassing op het klassieke implementatiemodel. Deze configuratie is niet beschikbaar in de portal.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> ExpressRoute-circuits moeten vooraf worden geconfigureerd voordat u de onderstaande instructies volgt. Zorg ervoor dat u de handleidingen hebt gevolgd [om een ExpressRoute-circuit te maken](expressroute-howto-circuit-classic.md) en [routering te configureren](expressroute-howto-routing-classic.md) voordat u de volgende stappen volgt.
> 
> 

## <a name="limits-and-limitations"></a>Limieten en beperkingen
* **Transitroutering wordt niet ondersteund.** U kunt niet (via Azure) routeren tussen uw lokale netwerk dat is verbonden via site-naar-site-VPN en uw lokale netwerk dat is verbonden via ExpressRoute.
* **Punt-naar-site wordt niet ondersteund.** U kunt geen punt-naar-site-VPN-verbindingen inschakelen naar het hetzelfde VNet dat is verbonden met ExpressRoute. Punt-naar-site-VPN en ExpressRoute kunnen niet worden gecombineerd voor hetzelfde VNet.
* **Geforceerde tunneling kan niet worden ingeschakeld op de site-naar-site VPN-gateway.** U kunt alle internetverkeer alleen via ExpressRoute terug naar uw on-premises netwerk ‘forceren’.
* **Basic SKU-gateway wordt niet ondersteund.** U moet een niet-basic SKU-gateway gebruiken voor zowel de [ExpressRoute gateway](expressroute-about-virtual-network-gateways.md) als de [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Alleen een op route gebaseerde VPN-gateway wordt ondersteund.** U moet een op route gebaseerde [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) gebruiken.
* **Statische route moet worden geconfigureerd voor de VPN-gateway.** Als uw lokale netwerk is verbonden met ExpressRoute en een site-naar-site-VPN, moet u in uw lokale netwerk een statische route hebben geconfigureerd voor het routeren van de site-naar-site-VPN-verbinding met het openbare internet.
* **ExpressRoute gateway moet eerst worden geconfigureerd.** U moet eerst de ExpressRoute-gateway maken voordat u de site-naar-site-VPN-gateway kunt toevoegen.

## <a name="configuration-designs"></a>Configuratie-ontwerpen
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Een site-naar-site-VPN configureren als een failoverpad voor ExpressRoute
U kunt een site-naar-site-VPN-verbinding configureren als een back-up voor ExpressRoute. Dit geldt alleen voor virtuele netwerken die zijn gekoppeld aan het pad voor persoonlijke Azure-peering. Er is geen op VPN gebaseerde failoveroplossing voor services die toegankelijk zijn via openbare Azure- en Microsoft-peerings. Het ExpressRoute-circuit is altijd de primaire koppeling. Gegevens worden alleen via het site-naar-site-VPN-pad geleid als het ExpressRoute-circuit niet beschikbaar is. 

> [!NOTE]
> Hoewel een ExpressRoute-circuit de voorkeur heeft boven site-naar-site-VPN wanneer beide routes hetzelfde zijn, gebruikt Azure de langste voorvoegselovereenkomst om de route naar de bestemming van het pakket te kiezen.
> 
> 

![Diagram waarin een site-naar-site-VPN-verbinding wordt weer gegeven als back-up voor ExpressRoute.](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Een site-naar-site-VPN configureren om verbinding te maken met sites die niet zijn verbonden via ExpressRoute
U kunt uw netwerk zodanig configureren dat sommige sites rechtstreeks verbinding maken met Azure via site-naar-site-VPN en sommige sites verbinding maken via ExpressRoute. 

![Naast elkaar gebruiken](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> U kunt een virtueel netwerk niet configureren als transitrouter.
> 
> 

## <a name="selecting-the-steps-to-use"></a>De stappen selecteren die u gaat gebruiken
Er zijn twee sets met procedures waaruit u kunt kiezen om verbindingen te configureren die naast elkaar kunnen worden gebruikt. Welke configuratieprocedure u selecteert, is afhankelijk van het gegeven of u een bestaand virtueel netwerk hebt waarmee u verbinding wilt maken, of of u een nieuw virtueel netwerk wilt maken.

* Ik heb geen VNet en moet er een maken.
  
    Als u nog geen virtueel netwerk hebt, begeleidt deze procedure u bij het maken van een nieuw virtueel netwerk met behulp van het klassieke implementatiemodel, en bij het maken van nieuwe ExpressRoute- en site-naar-site-VPN-verbindingen. Volg voor de configuratie de stappen in het gedeelte [Een nieuw virtueel netwerk en naast elkaar bestaande verbindingen maken](#new) in dit artikel.
* Ik heb al een VNet met het klassieke implementatiemodel.
  
    Mogelijk hebt u al een virtueel netwerk met een bestaande site-naar-site-VPN-verbinding of ExpressRoute-verbinding. Het gedeelte [Naast elkaar bestaande verbindingen configureren voor een bestaand VNet](#add) in dit artikel begeleidt u bij het verwijderen van de gateway en vervolgens bij het maken van nieuwe ExpressRoute- en site-naar-site-VPN-verbindingen. Houd er rekening mee dat de stappen voor het maken van de nieuwe verbindingen moeten worden uitgevoerd in een specifieke volgorde. Gebruik geen instructies uit andere artikelen om uw gateways en verbindingen te maken.
  
    In deze procedure moet u uw gateway verwijderen en vervolgens nieuwe gateways configureren om verbindingen te maken die naast elkaar kunnen worden gebruikt. Dit betekent dat u tijdens het verwijderen en opnieuw maken van uw gateway en verbindingen rekening moet houden met uitvaltijd voor uw cross-premises verbindingen. U hoeft uw virtuele machines of services echter niet te migreren naar een nieuw virtueel netwerk. Terwijl u uw gateway configureert, kunnen uw virtuele machines en services nog steeds communiceren via de load balancer, mits ze hiervoor zijn geconfigureerd.

## <a name="install-powershell-cmdlets"></a>PowerShell-cmdlets installeren

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Een nieuw virtueel netwerk en naast elkaar bestaande verbindingen maken
Deze procedure helpt u bij het maken van een VNet en site-naar-site- en ExpressRoute-verbindingen die naast elkaar kunnen worden gebruikt.

1. U moet de meest recente versie van de Azure PowerShell-cmdlets installeren. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets. Houd er rekening mee dat de cmdlets die u voor deze configuratie gebruikt, mogelijk enigszins afwijken van de cmdlets waarmee u bekend bent. Zorg ervoor dat u de cmdlets gebruikt die in deze instructies worden vermeld. 
2. Maak een schema voor het virtuele netwerk. Zie [Azure Virtual Network-configuratieschema](/previous-versions/azure/reference/jj157100(v=azure.100)) voor meer informatie over het configuratieschema.
   
    Wanneer u uw schema maakt, moet u de volgende waarden gebruiken:
   
   * Het gatewaysubnet voor het virtuele netwerk moet /27 of een korter voorvoegsel (zoals /26 of /25) zijn.
   * Het verbindingstype van de gateway is Toegewezen.
     
    ```xml
    <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
      <AddressSpace>
        <AddressPrefix>10.17.159.192/26</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="Subnet-1">
          <AddressPrefix>10.17.159.192/27</AddressPrefix>
        </Subnet>
        <Subnet name="GatewaySubnet">
          <AddressPrefix>10.17.159.224/27</AddressPrefix>
          /Subnet>
      </Subnets>
      <Gateway>
        <ConnectionsToLocalNetwork>
          <LocalNetworkSiteRef name="MyLocalNetwork">
            <Connection type="Dedicated" />
          </LocalNetworkSiteRef>
        </ConnectionsToLocalNetwork>
      </Gateway>
    </VirtualNetworkSite>
    ```
3. Upload het bestand nadat u het XML-schemabestand hebt gemaakt en geconfigureerd. Hiermee maakt u het virtuele netwerk.
   
    Gebruik de volgende cmdlet voor het uploaden van het bestand en vervang de waarde door uw eigen waarde.
   
    ```azurepowershell
    Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
    ```
4. <a name="gw"></a>Maak een ExpressRoute-gateway. Zorg ervoor dat u de GatewaySKU opgeeft als *Standard*, *HighPerformance* of *UltraPerformance* en het GatewayType als *DynamicRouting*.
   
    Gebruik het volgende voorbeeld, waarbij u de waarden vervangt door uw eigen waarden.

    ```azurepowershell
    New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
    ```
5. Koppel de ExpressRoute-gateway aan het ExpressRoute-circuit. Nadat deze stap is voltooid, wordt de verbinding tussen uw on-premises netwerk en Azure tot stand gebracht via ExpressRoute.
   
    ```azurepowershell
    New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
    ```
6. <a name="vpngw"></a>Maak vervolgens uw site-naar-site-VPN-gateway. De GatewaySKU moet zijn ingesteld op *Standard*, *HighPerformance* of *UltraPerformance* en het GatewayType op *DynamicRouting*.
   
    ```azurepowershell
    New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
    ```
   
    Gebruik de cmdlet `Get-AzureVirtualNetworkGateway` om de instellingen van de gateway van het virtuele netwerk op te halen, waaronder de gateway-ID en het openbare IP-adres.
   
    ```azurepowershell
    Get-AzureVirtualNetworkGateway
   
    GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
    GatewayName          : S2SVPN
    LastEventData        :
    GatewayType          : DynamicRouting
    LastEventTimeStamp   : 5/29/2015 4:41:41 PM
    LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
    LastEventID          : 23002
    State                : Provisioned
    VIPAddress           : 104.43.x.y
    DefaultSite          :
    GatewaySKU           : HighPerformance
    Location             :
    VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
    SubnetId             :
    EnableBgp            : False
    OperationDescription : Get-AzureVirtualNetworkGateway
    OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
    OperationStatus      : Succeeded
    ```
7. Maak een lokaal exemplaar van de VPN-gateway van uw site. Deze opdracht wordt niet gebruikt om uw on-premises VPN-gateway te configureren. Met deze opdracht kunt u de instellingen voor de lokale gateway opgeven, zoals het openbare IP-adres en de on-premises-adresruimte, zodat de Azure VPN-gateway er verbinding mee kan maken.
   
   > [!IMPORTANT]
   > De lokale site voor de site-naar-site-VPN-verbinding is niet gedefinieerd in de netcfg. In plaats daarvan moet u deze cmdlet gebruiken om de parameters van de lokale site op te geven. U kunt deze niet definiëren met de portal of het netcfg-bestand.
   > 
   > 
   
    Gebruik het volgende voorbeeld, waarbij u de waarden vervangt door uw eigen waarden.
   
    ```azurepowershell
    New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
    ```
   
   > [!NOTE]
   > Als uw lokale netwerk meerdere routes heeft, kunt u ze doorgeven als een matrix.  $MyLocalNetworkAddress = @('10.1.2.0/24', '10.1.3.0/24', '10.2.1.0/24')  
   > 
   > 

    Gebruik de cmdlet `Get-AzureVirtualNetworkGateway` om de instellingen van de gateway van het virtuele netwerk op te halen, waaronder de gateway-ID en het openbare IP-adres. Zie het volgende voorbeeld

    ```azurepowershell
    Get-AzureLocalNetworkGateway

    GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
    GatewayName          : MyLocalNetwork
    IpAddress            : 23.39.x.y
    AddressSpace         : {10.1.2.0/24}
    OperationDescription : Get-AzureLocalNetworkGateway
    OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
    OperationStatus      : Succeeded
    ```


1. Configureer het lokale VPN-apparaat om verbinding te maken met de nieuwe gateway. Gebruik de informatie die u in stap 6 bij de configuratie van uw VPN-apparaat hebt opgehaald. Zie [VPN-apparaatconfiguratie](../vpn-gateway/vpn-gateway-about-vpn-devices.md) voor meer informatie over het configureren van een VPN-apparaat. 
2. Koppel de site-naar-site-VPN-gateway in Azure aan de lokale gateway.
   
    In dit voorbeeld is connectedEntityId de lokale gateway-ID. Deze kunt u vinden door `Get-AzureLocalNetworkGateway` uit te voeren. U kunt virtualNetworkGatewayId vinden met behulp van de cmdlet `Get-AzureVirtualNetworkGateway`. Na deze stap wordt de verbinding tussen uw lokale netwerk en Azure tot stand gebracht via de site-naar-site-VPN-verbinding.

    ```azurepowershell
    New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Naast elkaar bestaande verbindingen configureren voor een bestaand VNet
Als u een bestaand virtueel netwerk hebt, controleert u de grootte van het gatewaysubnet. Als het gatewaysubnet /28 of /29 is, moet u eerst de gateway van het virtuele netwerk verwijderen en het gatewaysubnet vergroten. In de stappen in dit gedeelte wordt beschreven hoe u dat doet.

Als het gatewaysubnet /27 of groter is en het virtuele netwerk is verbonden via ExpressRoute, kunt u onderstaande stappen overslaan en doorgaan met [Stap 6 - Een site-naar-site-VPN-gateway maken](#vpngw) in het vorige gedeelte.

> [!NOTE]
> Als u de bestaande gateway verwijdert terwijl u bezig bent met deze configuratie, wordt de verbinding tussen uw lokale site en uw virtuele netwerk verbroken.
> 
> 

1. U moet de meest recente versie van de Azure Resource Manager PowerShell-cmdlets installeren. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets. Houd er rekening mee dat de cmdlets die u voor deze configuratie gebruikt, mogelijk enigszins afwijken van de cmdlets waarmee u bekend bent. Zorg ervoor dat u de cmdlets gebruikt die in deze instructies worden vermeld. 
2. Verwijder de bestaande ExpressRoute- of site-naar-site-VPN-gateway. Gebruik de volgende cmdlet, waarbij u de waarden vervangt door uw eigen waarden.
   
    ```azurepowershell
    Remove-AzureVNetGateway –VnetName MyAzureVNET
    ```
3. Exporteer het schema van het virtuele netwerk. Gebruik de volgende PowerShell-cmdlet, waarbij u de waarden vervangt door uw eigen waarden.
   
    ```azurepowershell
    Get-AzureVNetConfig –ExportToFile "C:\NetworkConfig.xml"
    ```
4. Bewerk het schema van het netwerkconfiguratiebestand zodanig dat het gatewaysubnet /27 of een kortere voorvoegsel (zoals /26 of /25) is. Zie het volgende voorbeeld 
   
   > [!NOTE]
   > Als u in uw virtuele netwerk niet voldoende IP-adressen over hebt om het gatewaysubnet te vergroten, moet u meer IP-adresruimte toevoegen. Zie [Azure Virtual Network-configuratieschema](/previous-versions/azure/reference/jj157100(v=azure.100)) voor meer informatie over het configuratieschema.
   > 
   > 
   
    ```xml
    <Subnet name="GatewaySubnet">
      <AddressPrefix>10.17.159.224/27</AddressPrefix>
    </Subnet>
    ```
5. Als uw vorige gateway een site-naar-site-VPN was, moet u ook het verbindingstype wijzigen in **Toegewezen**.
   
    ```xml
    <Gateway>
      <ConnectionsToLocalNetwork>
        <LocalNetworkSiteRef name="MyLocalNetwork">
          <Connection type="Dedicated" />
        </LocalNetworkSiteRef>
      </ConnectionsToLocalNetwork>
    </Gateway>
    ```
6. U hebt op dit moment een VNet zonder gateways. Als u nieuwe gateways wilt maken en uw verbindingen wilt voltooien, kunt u doorgaan met [Stap 4 - Een ExpressRoute-gateway maken](#gw). Deze stap vindt u in de voorgaande reeks stappen.

## <a name="next-steps"></a>Volgende stappen
Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie over ExpressRoute.