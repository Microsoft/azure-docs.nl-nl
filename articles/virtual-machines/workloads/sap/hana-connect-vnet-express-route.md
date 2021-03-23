---
title: Connectiviteit ingesteld op basis van het virtuele netwerk naar SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: De connectiviteit is ingesteld op basis van een virtueel netwerk om SAP HANA te gebruiken op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 540247752be27af268a0485ea9eb68d121a25240
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775302"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Een virtueel netwerk verbinden met HANA grote instanties

Nadat u een virtueel Azure-netwerk hebt gemaakt, kunt u dat netwerk verbinden met SAP HANA op grote Azure-instanties. Maak een Azure ExpressRoute-gateway in het virtuele netwerk. Met deze gateway kunt u het virtuele netwerk koppelen aan het ExpressRoute-circuit dat verbinding maakt met de Tenant van de klant op de HANA grote instantie stempel.

> [!NOTE] 
> Het kan tot 30 minuten duren voordat deze stap is voltooid. De nieuwe gateway wordt gemaakt in het aangewezen Azure-abonnement en vervolgens verbonden met het opgegeven virtuele Azure-netwerk.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Als er al een gateway bestaat, controleert u of deze een ExpressRoute-gateway is of niet. Als het geen ExpressRoute-gateway is, verwijdert u de gateway en maakt u deze opnieuw als een ExpressRoute-gateway. Als er al een ExpressRoute-gateway is ingesteld, raadpleegt u de volgende sectie van dit artikel ' virtuele netwerken koppelen '. 

- Gebruik de [Azure Portal](https://portal.azure.com/) of Power shell om een ExpressRoute VPN-gateway te maken die is verbonden met uw virtuele netwerk.
  - Als u de Azure Portal gebruikt, voegt u een nieuwe **Virtual Network gateway** toe en selecteert u vervolgens **ExpressRoute** als gateway type.
  - Als u Power shell gebruikt, moet u eerst de nieuwste [Azure POWERSHELL SDK](https://azure.microsoft.com/downloads/)downloaden en gebruiken. 
 
Met de volgende opdrachten maakt u een ExpressRoute-gateway. De tekst voorafgegaan door a _$_ zijn door de gebruiker gedefinieerde variabelen die moeten worden bijgewerkt met uw specifieke informatie.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

De enige ondersteunde gateway-SKU voor SAP HANA op Azure (grote exemplaren) is **Ultra Performance**.

## <a name="link-virtual-networks"></a>Virtuele netwerken koppelen

Het virtuele netwerk van Azure heeft nu een ExpressRoute-gateway. Gebruik de verificatie gegevens die door micro soft zijn verstrekt om de ExpressRoute-gateway te verbinden met het ExpressRoute-circuit van SAP HANA Large Instances. U kunt verbinding maken met behulp van de Azure Portal of Power shell. De Power shell-instructies zijn als volgt. 

Voer de volgende opdrachten uit voor elke ExpressRoute-gateway met behulp van een andere AuthGUID voor elke verbinding. De eerste twee vermeldingen die worden weer gegeven in het volgende script, zijn afkomstig uit de informatie die door micro soft wordt verstrekt. De AuthGUID is ook specifiek voor elk virtueel netwerk en de bijbehorende gateway. Als u nog een virtueel Azure-netwerk wilt toevoegen, moet u een andere AuthID voor uw ExpressRoute-circuit verkrijgen dat HANA grote instanties verbindt met Azure van micro soft. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> De laatste para meter in de opdracht New-AzVirtualNetworkGatewayConnection, **ExpressRouteGatewayBypass** is een nieuwe para meter waarmee het snelle pad ExpressRoute wordt ingeschakeld. Een functionaliteit die de netwerk latentie beperkt tussen uw HANA-grote exemplaar eenheden en Azure-Vm's. De functionaliteit is toegevoegd in mei 2019. Raadpleeg het artikel [SAP Hana (grote exemplaren) netwerk architectuur](./hana-network-architecture.md)voor meer informatie. Zorg ervoor dat u de meest recente versie van Power shell-cmdlets gebruikt voordat u de opdrachten uitvoert.

Als u de gateway wilt verbinden met meer dan één ExpressRoute-circuit dat is gekoppeld aan uw abonnement, moet u deze stap mogelijk meermaals uitvoeren. Zo wilt u waarschijnlijk dezelfde virtuele netwerk gateway verbinden met het ExpressRoute-circuit dat het virtuele netwerk verbindt met uw on-premises netwerk.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>ExpressRoute snelle pad Toep assen op bestaande HANA grote instantie ExpressRoute-circuits
In de documentatie wordt uitgelegd hoe u een nieuw ExpressRoute-circuit maakt dat is gemaakt met een een HANA-implementatie van een grote instantie in een Azure ExpressRoute-gateway van een van uw Azure Virtual Networks. Maar veel klanten hebben al hun ExpressRoute-circuits Setup al en hebben hun virtuele netwerken al verbonden met HANA grote exemplaren. Wanneer het nieuwe ExpressRoute-snelle pad de netwerk latentie reduceert, is het raadzaam de wijziging toe te passen op het gebruik van deze functionaliteit. De opdrachten voor het verbinden van een nieuw ExpreesRoute-circuit en het wijzigen van een bestaand ExpressRoute-circuit zijn hetzelfde. Als gevolg hiervan moet u deze volg orde van Power shell-opdrachten uitvoeren om een bestaand circuit te wijzigen dat moet worden gebruikt 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

Het is belang rijk dat u de laatste para meter toevoegt zoals hierboven wordt weer gegeven om de ExpressRoute Fast Path-functionaliteit in te scha kelen


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Als u Global Reach wilt inschakelen voor een of beide van de volgende twee scenario's:

 - HANA-systeem replicatie zonder extra proxy's of firewalls
 - Back-ups kopiëren tussen HANA grote instantie-eenheden in twee verschillende regio's om systeem kopieën of systeem vernieuwingen uit te voeren

u moet rekening houden met het volgende:

- U moet een adres ruimte bereik van een/29 adres ruimte opgeven. Het adres bereik mag niet overlappen met een van de andere adres ruimte bereiken die u hebt gebruikt, waardoor de HANA grote instanties worden verbonden met Azure en mag niet overlappen met een van de IP-adresbereiken die u ergens anders in azure of on-premises hebt gebruikt.
- Er is een beperking voor het Asn's (autonoom systeem nummer) dat kan worden gebruikt om uw on-premises routes naar HANA grote instanties te adverteren. Uw on-premises mogen geen routes adverteren met persoonlijke Asn's in het bereik van 65000 – 65020 of 65515. 
- Voor het scenario voor de verbinding van on-premises directe toegang tot HANA grote instanties moet u kosten berekenen voor het circuit waarmee u verbinding maakt met Azure. Controleer de prijzen voor [Global Reach-invoeg toepassing](https://azure.microsoft.com/pricing/details/expressroute/)voor prijzen.

Als u een of beide van de scenario's die worden toegepast op uw implementatie wilt ontvangen, opent u een ondersteunings bericht met Azure, zoals beschreven in [een ondersteunings aanvraag openen voor Hana grote instanties](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)

Gegevens die nodig zijn en de tref woorden die u voor micro soft moet gebruiken om uw aanvraag te kunnen door sturen en uitvoeren, ziet er als volgt uit:

- Service: SAP HANA grote instantie
- Probleem type: configuratie en installatie
- Subtype van probleem: mijn probleem wordt hierboven niet vermeld
- Onderwerp ' mijn netwerk wijzigen-Global Reach toevoegen '
- Details: ' Voeg Global Reach toe aan de HANA grote instantie naar de Hana-Tenant voor grote instanties of Voeg Global Reach toe aan on-premises naar de HANA-Tenant van het grote exemplaar.
- Aanvullende Details voor de HANA grote instantie naar HANA grote instantie Tenant Case: u moet de **twee Azure-regio's** definiëren waarin de twee tenants die u wilt verbinden zich bevinden **en** u moet het **/29 IP-adres bereik** verzenden
- Aanvullende Details voor de on-premises naar HANA grote instantie Tenant Case: u moet de **Azure-regio** definiëren waarin de Hana-Tenant voor grote instanties wordt geïmplementeerd waarmee u rechtstreeks verbinding wilt maken. Daarnaast moet u de verificatie- **GUID** en de **naam van het circuit peer-id** opgeven die u hebt ontvangen toen u uw ExpressRoute-circuit hebt gemaakt tussen on-premises en Azure. Bovendien moet u uw **ASN** een naam hebben. Het laatste product is een **/29 IP-adres bereik** voor ExpressRoute Global REACH.

> [!NOTE]
> Als u beide cases wilt laten afhandelen, moet u twee verschillende/29 IP-adresbereiken opgeven die niet overlappen met een ander IP-adres bereik dat tot nu toe is gebruikt. 




## <a name="next-steps"></a>Volgende stappen

- [Aanvullende netwerk vereisten voor HLI](hana-additional-network-requirements.md)
