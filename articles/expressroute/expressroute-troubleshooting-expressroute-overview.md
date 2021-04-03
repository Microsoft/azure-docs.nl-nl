---
title: 'Azure-ExpressRoute: Controleer de connectiviteit-probleemoplossings gids'
description: Op deze pagina vindt u instructies voor het oplossen van problemen en het valideren van end-to-end-connectiviteit van een ExpressRoute-circuit.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: af4ef156cccded6afe2db09628446a6ffe1ad53a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204636"
---
# <a name="verifying-expressroute-connectivity"></a>Connectiviteit ExpressRoute controleren
Dit artikel helpt u bij het controleren en oplossen van problemen met ExpressRoute-connectiviteit. Met ExpressRoute wordt een on-premises netwerk uitgebreid naar de micro soft-Cloud via een particuliere verbinding die meestal wordt vereenvoudigd door een connectiviteits provider. Voor ExpressRoute-connectiviteit is traditioneel drie afzonderlijke netwerk zones vereist:

-   Klantnetwerk
-   Provider netwerk
-   Micro soft Data Center

> [!NOTE]
> In het ExpressRoute direct Connectivity-model (aangeboden voor een band breedte van 10/100 Gbps) kunnen klanten rechtstreeks verbinding maken met de poort van de micro soft Enter prise Edge (MSEE)-routers. Daarom zijn er alleen klant-en micro soft-netwerk zones in het model voor directe connectiviteit.
>


Het doel van dit document is om gebruikers te helpen te bepalen of en waar een connectiviteits probleem zich voordoet. Om ondersteuning te helpen van het juiste team om een probleem op te lossen. Als er ondersteuning voor micro soft nodig is om een probleem op te lossen, opent u een ondersteunings ticket met [Microsoft ondersteuning][Support].

> [!IMPORTANT]
> Dit document is bedoeld om eenvoudige problemen op te sporen en te verhelpen. Het is niet bedoeld als vervanging van micro soft-ondersteuning. Open een ondersteunings ticket met [Microsoft ondersteuning][Support] als u het probleem niet kunt oplossen met behulp van de meegeleverde richt lijnen.
>
>

## <a name="overview"></a>Overzicht
In het volgende diagram ziet u de logische verbinding van een klanten netwerk met het micro soft-netwerk met behulp van ExpressRoute.
[![i]][1]

In het voor gaande diagram duiden de getallen op belang rijke netwerk punten. In dit artikel wordt naar deze netwerk punten gerefereerd op basis van het bijbehorende nummer. Afhankelijk van het ExpressRoute-connectiviteits model: de co-locatie van de Cloud uitwisseling, Point-to-Point Ethernet-verbinding of any-to-any (IPVPN), kunnen de netwerk punten 3 en 4 switches zijn (laag 2 apparaten) of routers (laag 3-apparaten). In het model voor directe connectiviteit bevinden zich geen netwerk punten 3 en 4; in plaats daarvan (2) zijn rechtstreeks verbonden met Msee's via donker glas. De belangrijkste netwerk punten worden als volgt geïllustreerd:

1.  Klant Compute Device (bijvoorbeeld een server of PC)
2.  CEs: klanten Edge-routers 
3.  PEs (CE gericht): provider Edge routers/switches die zijn gericht op klanten Edge-routers. Wordt in dit document aangeduid als PE-CEs.
4.  PEs (MSEE gericht): provider Edge routers/switches die zijn gericht op Msee's. Wordt in dit document aangeduid als PE-MSEEs.
5.  Msee's: micro soft Enter prise Edge (MSEE) ExpressRoute-routers
6.  Virtual Network (VNet)-gateway
7.  Compute-apparaat op het Azure-VNet

Als de co-locatie van de Cloud uitwisseling, Point-to-Point Ethernet of directe connectiviteits modellen worden gebruikt, is CEs (2) BGP-peering tot stand gebracht met Msee's (5). 

Als het verbindings model any-to-any (IPVPN) wordt gebruikt, PE-MSEEs (4) BGP-peering tot stand brengen met Msee's (5). PE-MSEEs de routes die van micro soft zijn ontvangen via het netwerk van de IPVPN-service provider door geven aan het klant netwerk.

> [!NOTE]
>Micro soft brengt voor hoge Beschik baarheid een volledig redundante parallelle verbinding tussen Msee's (5) en PE-MSEEs (4) paren tot stand. Er wordt ook een volledig redundant parallel netwerkpad aanbevolen tussen het netwerk van de klant en het PE-CEs-paar. Zie het artikel [ontwerpen voor hoge Beschik baarheid met ExpressRoute][HA] voor meer informatie over hoge Beschik baarheid.
>
>

Hieronder vindt u de logische stappen in Troubleshooting ExpressRoute-circuit:

* [Circuit inrichting en-status controleren](#verify-circuit-provisioning-and-state)
  
* [Peering-configuratie valideren](#validate-peering-configuration)
  
* [ARP valideren](#validate-arp)
  
* [BGP en routes op de MSEE valideren](#validate-bgp-and-routes-on-the-msee)
  
* [De verkeers stroom bevestigen](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Circuit inrichting en-status controleren
Bij het inrichten van een ExpressRoute-circuit wordt een redundante Layer 2-verbinding tot stand gebracht tussen CEs/PE-Msee's (2)/(4) en Msee's (5). Zie het artikel [een ExpressRoute-circuit maken en wijzigen][CreateCircuit]voor meer informatie over het maken, wijzigen, inrichten en verifiëren van een ExpressRoute-circuit.

>[!TIP]
>Een service sleutel is een unieke aanduiding voor een ExpressRoute-circuit. Als u hulp nodig hebt van micro soft of van een ExpressRoute-partner om een ExpressRoute-probleem op te lossen, geeft u de service sleutel op om het circuit eenvoudig te identificeren.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificatie via de Azure Portal
Open in de Azure Portal de Blade ExpressRoute circuit. In het gedeelte ![3][3] van de Blade worden de ExpressRoute Essentials weer gegeven, zoals wordt weer gegeven in de volgende scherm afbeelding:

![4][4]    

In de ExpressRoute Essentials geeft de status van het *circuit* de status aan van het circuit aan de kant van micro soft. *Provider status* geeft aan of het circuit is *ingericht/niet is ingericht* op de kant van de service provider. 

Voor een ExpressRoute-circuit moet de status van *het circuit* zijn *ingeschakeld* en moet de *status* van de provider worden *ingericht*.

> [!NOTE]
> Nadat u een ExpressRoute-circuit hebt geconfigureerd, neemt u contact op met [Microsoft ondersteuning][Support]als de status van het *circuit* niet is ingeschakeld. Als de status van de *provider* echter niet is ingericht, neemt u contact op met uw service provider.
>
>

### <a name="verification-via-powershell"></a>Verificatie via Power shell
Als u alle ExpressRoute-circuits in een resource groep wilt weer geven, gebruikt u de volgende opdracht:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Als u de naam van een resource groep zoekt, kunt u deze ophalen door alle resource groepen in uw abonnement op te geven met behulp van de opdracht *Get-AzResourceGroup*
>


Als u een bepaald ExpressRoute-circuit wilt selecteren in een resource groep, gebruikt u de volgende opdracht:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

Een voor beeld van een antwoord is:

```output
Name                             : Test-ER-Ckt
ResourceGroupName                : Test-ER-RG
Location                         : westus2
Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_UnlimitedData",
                                    "Tier": "Standard",
                                    "Family": "UnlimitedData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : {
                                    "ServiceProviderName": "****",
                                    "PeeringLocation": "******",
                                    "BandwidthInMbps": 100
                                    }
ServiceKey                       : **************************************
Peerings                         : []
Authorizations                   : []
```

Ga als volgt te werk om te controleren of een ExpressRoute-circuit operationeel is, Let vooral op de volgende velden:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> Nadat u een ExpressRoute-circuit hebt geconfigureerd, neemt u contact op met [Microsoft ondersteuning][Support]als de status van het *circuit* niet is ingeschakeld. Als de status van de *provider* echter niet is ingericht, neemt u contact op met uw service provider.
>
>

## <a name="validate-peering-configuration"></a>Peering-configuratie valideren
Nadat de service provider het ExpressRoute-circuit heeft voltooid, kunnen meerdere routerings configuraties op basis van eBGP worden gemaakt over het ExpressRoute-circuit tussen CEs/MSEE-PEs (2)/(4) en Msee's (5). Elk ExpressRoute-circuit kan het volgende hebben: persoonlijke Azure-peering (verkeer naar particuliere virtuele netwerken in Azure) en/of micro soft-peering (verkeer naar open bare eind punten van PaaS en SaaS). Zie voor meer informatie over het maken en wijzigen van routerings configuratie het artikel [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verificatie via de Azure Portal

> [!NOTE]
> In IPVPN-connectiviteits model worden service providers de verantwoordelijkheid voor het configureren van de peerings (Layer 3-Services) afgehandeld. In een dergelijk model, nadat de service provider een peering heeft geconfigureerd en als de peering leeg is in de portal, kunt u de circuit configuratie vernieuwen met behulp van de knop Vernieuwen in de portal. Met deze bewerking wordt de huidige routerings configuratie van het circuit opgehaald. 
>

In de Azure Portal, de status van een ExpressRoute-circuit peering kan worden gecontroleerd onder de Blade ExpressRoute-circuit. In het gedeelte ![3][3] van de Blade worden de ExpressRoute-peerings weer gegeven, zoals wordt weer gegeven in de volgende scherm afbeelding:

![5][5]

In het vorige voor beeld, zoals aangegeven dat persoonlijke Azure-peering is ingericht, terwijl Azure open bare en micro soft-peerings niet zijn ingericht. Voor een geslaagde peering context zijn ook de primaire en secundaire Point-to-Point-subnetten opgenomen. De/30 subnetten worden gebruikt voor het IP-adres van de interface van de Msee's en CEs/PE-Msee's. Voor de peerings die zijn ingericht, wordt in de vermelding ook aangegeven wie de configuratie voor het laatst heeft gewijzigd. 

> [!NOTE]
> Als het inschakelen van een peering mislukt, controleert u of de primaire en secundaire subnetten overeenkomen met de configuratie van het gekoppelde CE/PE-MSEE. Controleer ook of de juiste *VlanId*, *AzureASN* en *PeerASN* worden gebruikt op msee's en of deze waarden worden toegewezen aan de velden die worden gebruikt op de gekoppelde CE/PE-MSEE. Als MD5-hashing is gekozen, moet de gedeelde sleutel gelijk zijn aan het MSEE-en PE-MSEE/CE-paar. De eerder geconfigureerde gedeelde sleutel wordt om veiligheids redenen niet weer gegeven. Als u een van deze configuraties wilt wijzigen op een MSEE-router, raadpleegt u [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].  
>

> [!NOTE]
> In een/30-subnet dat is toegewezen aan interface, kiest micro soft het tweede bruikbare IP-adres van het subnet voor de MSEE-interface. Zorg er daarom voor dat het eerste bruikbare IP-adres van het subnet is toegewezen op de peered CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Verificatie via Power shell
Gebruik de volgende opdrachten om de configuratie gegevens van de persoonlijke Azure-peering te verkrijgen:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

Een voor beeld van een antwoord voor een correct geconfigureerde persoonlijke peering is:

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 Voor een geslaagde peering context zouden de primaire en secundaire adres voorvoegsels worden weer gegeven. De/30 subnetten worden gebruikt voor het IP-adres van de interface van de Msee's en CEs/PE-Msee's.

Gebruik de volgende opdrachten om de configuratie gegevens voor de open bare Azure-peering te verkrijgen:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Gebruik de volgende opdrachten om de configuratie gegevens van de micro soft-peering te verkrijgen:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Als er geen peering is geconfigureerd, wordt er een fout bericht weer gegeven. Een voor beeld van een antwoord wanneer de vermelde peering (open bare Azure-peering in dit voor beeld) niet is geconfigureerd binnen het circuit:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Als het inschakelen van een peering mislukt, controleert u of de primaire en secundaire subnetten overeenkomen met de configuratie van het gekoppelde CE/PE-MSEE. Controleer ook of de juiste *VlanId*, *AzureASN* en *PeerASN* worden gebruikt op msee's en of deze waarden worden toegewezen aan de velden die worden gebruikt op de gekoppelde CE/PE-MSEE. Als MD5-hashing is gekozen, moet de gedeelde sleutel gelijk zijn aan het MSEE-en PE-MSEE/CE-paar. De eerder geconfigureerde gedeelde sleutel wordt om veiligheids redenen niet weer gegeven. Als u een van deze configuraties wilt wijzigen op een MSEE-router, raadpleegt u [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].  
>
>

> [!NOTE]
> In een/30-subnet dat is toegewezen aan interface, kiest micro soft het tweede bruikbare IP-adres van het subnet voor de MSEE-interface. Zorg er daarom voor dat het eerste bruikbare IP-adres van het subnet is toegewezen op de peered CE/PE-MSEE.
>

## <a name="validate-arp"></a>ARP valideren

De ARP-tabel biedt een toewijzing van het IP-adres en MAC-adres voor een bepaalde peering. De ARP-tabel voor een ExpressRoute-circuit peering biedt de volgende informatie voor elke interface (primair en secundair):
* Toewijzing van het IP-adres van de on-premises router interface aan het MAC-adres
* Toewijzing van IP-adres van de ExpressRoute-router interface aan het MAC-adres
* De leeftijd van de toewijzing ARP-tabellen kunnen helpen bij het valideren van de configuratie van laag 2 en het oplossen van problemen met de basis verbinding met laag 2.


Zie [ARP-tabellen ophalen in het Resource Manager-implementatie model][ARP] document voor informatie over het weer geven van de ARP-tabel van een ExpressRoute-peering en voor het oplossen van problemen met het probleem met laag 2-connectiviteit.


## <a name="validate-bgp-and-routes-on-the-msee"></a>BGP en routes op de MSEE valideren

Gebruik de volgende opdracht om de routerings tabel op te halen uit MSEE op het *primaire* pad voor de context van de *privé* router:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Een voor beeld van een antwoord is:

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> Als de status van een eBGP-peering tussen een MSEE en een CE/PE-MSEE actief of inactief is, controleert u of de subnetten van de primaire en de secundaire peer zijn toegewezen die overeenkomen met de configuratie van de gekoppelde CE/PE-MSEE. Controleer ook of de juiste *VlanId*, *AzureAsn* en *PeerAsn* worden gebruikt op msee's en of deze waarden worden toegewezen aan de velden die worden gebruikt op de gekoppelde PE-MSEE/CE. Als MD5-hashing is gekozen, moet de gedeelde sleutel hetzelfde zijn op het MSEE en het CE/PE-MSEE-paar. Als u een van deze configuraties wilt wijzigen op een MSEE-router, raadpleegt u [route ring maken en wijzigen voor een ExpressRoute-circuit][CreatePeering].
>


> [!NOTE]
> Als bepaalde doelen niet bereikbaar zijn via een peering, controleert u de route tabel van de Msee's voor de bijbehorende peering context. Als er een overeenkomend voor voegsel (mogelijk IP-adres) in de routerings tabel aanwezig is, controleert u of er firewalls/NSG/Acl's zijn op het pad dat het verkeer blokkeert.
>


In het volgende voor beeld ziet u het antwoord van de opdracht voor een peering die niet bestaat:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>De verkeers stroom bevestigen
Gebruik de volgende opdracht om de statistische gegevens over het gecombineerde primaire en secundaire traject te verkrijgen: bytes in en out--van een peering-context.

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

Een voor beeld van een uitvoer van de opdracht is:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

Een voorbeeld uitvoer van de opdracht voor een niet-bestaande peering is:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende koppelingen voor meer informatie of hulp:

- [Microsoft-ondersteuning][Support]
- [Een ExpressRoute-circuit maken en wijzigen][CreateCircuit]
- [Routering voor een ExpressRoute-circuit maken en wijzigen][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "connectiviteit voor logische Express-route"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Pictogram alle resources"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Pictogram overzicht"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Voorbeeld scherm van ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Voorbeeld scherm van ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md