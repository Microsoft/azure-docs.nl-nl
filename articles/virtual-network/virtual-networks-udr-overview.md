---
title: Routering van verkeer in virtuele Azure-netwerken
titlesuffix: Azure Virtual Network
description: Lees hier hoe Azure verkeer in virtuele netwerken routeert en hoe u de routering van Azure kunt aanpassen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2021
ms.author: aldomel
ms.openlocfilehash: 232b83fef2da312828f4f9f332ab2505e3a68100
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503640"
---
# <a name="virtual-network-traffic-routing"></a>Routering van verkeer in virtuele netwerken

Lees hier hoe Azure verkeer routeert tussen Azure, on-premises en resources op internet. Azure maakt automatisch een routetabel voor elk subnet binnen een virtueel Azure-netwerk en voegt standaardsysteemroutes toe aan de tabel. Zie [Azure Virtual Network](virtual-networks-overview.md) voor meer informatie over virtuele netwerken en subnetten. U kunt een aantal systeemroutes van Azure vervangen door [aangepaste routes](#custom-routes). Daarnaast kunt u aanvullende aangepaste routes toevoegen aan routetabellen. De route van uitgaand verkeer van een subnet wordt gebaseerd op de routes in de routetabel van een subnet.

## <a name="system-routes"></a>Systeemroutes

Azure maakt automatisch systeemroutes en wijst de routes toe aan elk subnet in een virtueel netwerk. U kunt zelf geen systeemroutes maken en ook niet verwijderen. Het is wel mogelijk om bepaalde systeemroutes te vervangen door [aangepaste routes](#custom-routes). Azure maakt standaardsysteemroutes voor elk subnet en voegt extra, [optionele standaardroutes](#optional-default-routes) naar specifieke subnetten toe, of elk subnet, wanneer u specifieke mogelijkheden van Azure gebruikt.

### <a name="default"></a>Standaard

Elke route bevat een adresvoorvoegsel en het volgende hoptype. Wanneer uitgaand verkeer van een subnet wordt verzonden naar een IP-adres binnen het adresvoorvoegsel van een route, is de route met het voorvoegsel de route die door Azure wordt gebruikt. Lees [hoe Azure een route selecteert](#how-azure-selects-a-route) wanneer meerdere routes dezelfde voorvoegsels bevatten, of overlappende voorvoegsels. Wanneer er een virtueel netwerk wordt gemaakt, maakt Azure automatisch de volgende standaardsysteemroutes voor elk subnet in het virtuele netwerk:

|Bron |Adresvoorvoegsels                                        |Volgend hoptype  |
|-------|---------                                               |---------      |
|Standaard|Uniek voor het virtuele netwerk                           |Virtueel netwerk|
|Standaard|0.0.0.0/0                                               |Internet       |
|Standaard|10.0.0.0/8                                              |Geen           |
|Standaard|192.168.0.0/16                                          |Geen           |
|Standaard|100.64.0.0/10                                           |Geen           |

De 'volgende hoptypen' in de bovenstaande tabel bepalen hoe Azure verkeer routeert dat bestemd is voor het vermelde adresvoorvoegsel. Hieronder worden de 'volgende hoptypen' toegelicht:

* **Virtueel netwerk**: routeert verkeer tussen adresbereiken in de [adresruimte](manage-virtual-network.md#add-or-remove-an-address-range) van een virtueel netwerk. Azure maakt een route met een adresvoorvoegsel dat overeenkomt met elk-adresbereik dat is gedefinieerd in de adresruimte van een virtueel netwerk. Als in de adresruimte van het virtuele netwerk meerdere adresbereiken zijn gedefinieerd, maakt Azure een afzonderlijke route voor elk adresbereik. Verkeer tussen subnetten wordt door Azure automatisch gerouteerd met behulp van de routes die voor elk adresbereik zijn gemaakt. U hoeft geen gateways te definiëren voor Azure voor het routeren van verkeer tussen subnetten. Hoewel een virtueel netwerk subnetten bevat, en elk subnet een gedefinieerd adresbereik heeft, worden er door Azure *geen* standaardroutes gemaakt voor adresbereiken van een subnet. De reden hiervoor is dat elk adresbereik van een subnet binnen een adresbereik valt van de adresruimte van een virtueel netwerk.<br>
* **Internet**: routeert verkeer dat is opgegeven met het adresvoorvoegsel naar internet. De standaardsysteemroute is gekoppeld aan het adresvoorvoegsel 0.0.0.0/0. Als u de standaardroutes van Azure niet overschrijft, stuurt Azure verkeer voor een adres dat niet is opgegeven door een adresbereik binnen een virtueel netwerk, naar internet. Met één uitzondering. Als het doeladres hoort bij een service van Azure, stuurt Azure het verkeer rechtstreeks naar de service. Het verkeer loopt dan via het backbone-netwerk van Azure en wordt dus niet naar internet gerouteerd. Verkeer tussen Azure-services loopt niet via internet, ongeacht de Azure-regio waarin het virtuele netwerk zich bevindt of in welke Azure-regio een instantie van de Azure-service is geïmplementeerd. U kunt de standaardsysteemroute van Azure voor het adresvoorvoegsel 0.0.0.0/0 vervangen door een [aangepaste route](#custom-routes).<br>
* **Geen**: verkeer dat wordt doorgestuurd naar het 'volgende hoptype' **Geen**, wordt verwijderd en niet buiten het subnet gerouteerd. Azure maakt automatisch standaardroutes voor de volgende adresvoorvoegsels:<br>

    * **10.0.0.0/8 en 192.168.0.0/16:** gereserveerd voor privégebruik in RFC 1918.<br>
    * **100.64.0.0/10**: gereserveerd in RFC 6598.

    Als u een van de bovenstaande adresbereiken toewijst binnen de adresruimte van een virtueel netwerk, wijzigt Azure het 'volgende hoptype' voor de route automatisch van **Geen** in **Virtueel netwerk**. Als u een adresbereik toewijst aan de adresruimte van een virtueel netwerk dat weliswaar een van de vier gereserveerde adresvoorvoegsels bevat, maar dat niet hetzelfde is, verwijdert Azure de route voor het voorvoegsel en wordt er een route toegevoegd voor het adresvoorvoegsel dat u hebt toegevoegd, met **Virtueel netwerk** als het 'volgende hoptype'.

### <a name="optional-default-routes"></a>Optionele standaardroutes

Azure voegt aanvullende standaardsysteemroutes toe voor verschillende mogelijkheden van Azure, maar alleen als u de mogelijkheden inschakelt. Afhankelijk van de mogelijkheid, voegt Azure optionele standaardroutes toe naar specifieke subnetten in het virtuele netwerk of naar alle subnetten in een virtueel netwerk. Dit zijn de aanvullende systeemroutes en 'volgende hoptypen' die Azure kan toevoegen wanneer u verschillende mogelijkheden inschakelt:

|Bron                 |Adresvoorvoegsels                       |Volgend hoptype|Subnet binnen het virtuele netwerk waarnaar een route wordt toegevoegd|
|-----                  |----                                   |---------                    |--------|
|Standaard                |Uniek is voor het virtuele netwerk, bijvoorbeeld: 10.1.0.0/16|VNet-peering                 |Alles|
|Gateway van een virtueel netwerk|Voorvoegsels geadverteerd van on-premises via BGP of geconfigureerd in de lokale netwerkgateway     |Gateway van een virtueel netwerk      |Alles|
|Standaard                |Meerdere                               |VirtualNetworkServiceEndpoint|Alleen het subnet waarvoor een service-eindpunt is ingeschakeld.|

* **VNet-peering**: wanneer u een VNet-peering maakt tussen twee virtuele netwerken, wordt er een route toegevoegd voor elk adresbereik in de adresruimte van elk virtueel netwerk waarvoor een peering wordt gemaakt. Meer informatie over [peering van virtuele netwerken](virtual-network-peering-overview.md).<br>
* **Gateway van een virtueel netwerk**: er worden een of meer routes met *Gateway van een virtueel netwerk* als het 'volgende hoptype' toegevoegd wanneer er een gateway van een virtueel netwerk wordt toegevoegd aan een virtueel netwerk. De bron is ook *Gateway van virtueel netwerk* omdat de gateway de routes naar het subnet toevoegt. Als uw on-premises netwerkgateway[BGP-routes](#border-gateway-protocol)(Border Gateway Protocol) uitwisselt met een gateway van een virtueel Azure-netwerk, wordt er een route toegevoegd voor elke route die wordt doorgegeven vanuit de on-premises netwerkgateway. Het is raadzaam dat u on-premises routes samenvat tot de grootst mogelijke adresbereiken, zodat het kleinste aantal routes wordt doorgegeven aan de gateway van een virtueel Azure-netwerk. Er gelden beperkingen voor het aantal routes dat kan worden doorgegeven aan de gateway van een virtueel Azure-netwerk. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) voor meer informatie.<br>
* **VirtualNetworkServiceEndpoint**: de openbare IP-adressen voor bepaalde services worden door Azure aan de routetabel toegevoegd wanneer u een service-eindpunt voor de service inschakelt. Service-eindpunten worden ingeschakeld voor afzonderlijke subnetten in een virtueel netwerk, zodat de route alleen wordt toegevoegd aan de routetabel van een subnet waarvoor een service-eindpunt is ingeschakeld. De openbare IP-adressen van Azure-services worden periodiek gewijzigd. Azure beheert de adressen in de routetabel automatisch als de adressen worden gewijzigd. Lees hier meer over [service-eindpunten van virtuele netwerken](virtual-network-service-endpoints-overview.md), en de services waarvoor u service-eindpunten kunt maken.<br>

    > [!NOTE]
    > De 'volgende hoptypen' **VNet-peering** en **VirtualNetworkServiceEndpoint** worden alleen toegevoegd aan routetabellen van subnetten in virtuele netwerken die zijn gemaakt via het implementatiemodel Azure Resource Manager. De 'volgende hoptypen' worden niet toegevoegd aan routetabellen die zijn gekoppeld aan subnetten van virtuele netwerken die zijn gemaakt met behulp van het klassieke implementatiemodel. Meer informatie over [Azure-implementatiemodellen.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="custom-routes"></a>Aangepaste routes

U maakt aangepaste routes door [door de gebruiker gedefinieerde](#user-defined) routes te maken of door [BGP-routes](#border-gateway-protocol) (Border Gateway Protocol) uit te wisselen tussen de gateway van uw on-premises netwerk en de gateway van een virtueel Azure-netwerk. 

### <a name="user-defined"></a>Door de gebruiker gedefinieerde routes

U kunt aangepaste of door de gebruiker gedefinieerde (statische) routes maken in Azure om de standaardsysteemroutes van Azure te overschrijven of om extra routes toe te voegen aan de routetabel van een subnet. In Azure maakt u een routetabel, waarna u de routetabel koppelt aan nul of meer subnetten van een virtueel netwerk. Elk subnet kan zijn gekoppeld aan geen enkele of één routetabel. Zie [Netwerklimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) voor meer informatie over het maximum aantal routes dat u kunt toevoegen aan een routetabel en het maximum aantal door de gebruiker gedefinieerde routetabellen dat u per Azure-abonnement kunt maken. Als u een routetabel maakt en deze aan een subnet koppelt, worden de routes in de tabel gecombineerd met de standaardroutes die Azure standaard toevoegt aan een subnet, of vervangen ze deze routes.

U kunt de onderstaande 'volgende hoptypen' opgeven wanneer u een door de gebruiker gedefinieerde route maakt:

* **Virtueel apparaat**: een virtueel apparaat is een virtuele machine waarop meestal een netwerktoepassing wordt uitgevoerd, zoals een firewall. Ga naar de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) voor meer informatie over een aantal vooraf geconfigureerde virtuele netwerkapparaten die u in een virtueel netwerk kunt implementeren. Wanneer u een route maakt met het hoptype **Virtueel apparaat**, moet u ook het IP-adres van de volgende hop opgeven. Het IP-adres kan bestaan uit:

    * Het [privé-IP-adres](./private-ip-addresses.md) van een netwerkinterface die is gekoppeld aan een virtuele machine. Als een netwerkinterface is gekoppeld aan een virtuele machine die netwerkverkeer doorstuurt naar een ander adres dan het eigen adres, moet in Azure de optie *Doorsturen via IP inschakelen* zijn ingeschakeld voor de interface. Deze instelling zorgt ervoor dat Azure de bron en bestemming voor een netwerkinterface niet controleert. Lees hier meer over het [inschakelen van doorsturen via IP voor een netwerkinterface](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Hoewel *Doorsturen via IP inschakelen* een instelling van Azure is, moet u doorsturen via IP mogelijk ook inschakelen in het besturingssysteem van de virtuele machine voor het apparaat om verkeer door te sturen tussen privé-IP-adressen die zijn toegewezen aan Azure-netwerkinterfaces. Als het apparaat verkeer moet routeren naar een openbaar IP-adres, moet het een proxy uitvoeren op het verkeer of het netwerkadres omzetten in het privé IP-adres van het privé IP-adres van de bron in een eigen privé-IP-adres, waarvan Azure het netwerkadres vervolgens omzet in een openbaar IP-adres, voordat het verkeer naar internet wordt verzonden. Raadpleeg de documentatie voor uw besturingssysteem of netwerktoepassing om de vereiste instellingen voor de virtuele machine te bepalen. Zie [Uitleg over uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over uitgaande verbindingen in Azure.<br>

        > [!NOTE]
        > Het is belangrijk dat u een virtueel apparaat in een ander subnet implementeert dan het subnet waarin de resources zijn geïmplementeerd die gegevens via het virtuele apparaat routeren. Als u het virtuele apparaat in hetzelfde subnet implementeert en vervolgens een routetabel toepast op het subnet dat verkeer via het virtuele apparaat leidt, kan dit routeringslussen veroorzaken, waardoor verkeer het subnet nooit verlaat.

    * Het privé IP-adres van een [interne load balancer](../load-balancer/quickstart-load-balancer-standard-internal-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Azure. Een load balancer wordt vaak gebruikt als onderdeel van een [strategie voor hoge beschikbaarheid van virtuele netwerkapparaten](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    U kunt een route met 0.0.0.0/0 als het adresvoorvoegsel definiëren en het 'volgende hoptype' Virtueel apparaat. Het apparaat kan dan het gegevensverkeer inspecteren en bepalen of dit moet worden doorgestuurd of verwijderd. Als u van plan bent een door de gebruiker gedefinieerde route te maken met het adresvoorvoegsel 0.0.0.0/0, moet u eerst [Adresvoorvoegsel 0.0.0.0/0](#default-route) lezen.

* **Gateway van virtueel netwerk**: gebruik dit type als u verkeer dat is bestemd voor specifieke adresvoorvoegsels wilt doorsturen naar de gateway van een virtueel netwerk. De gateway van het virtuele netwerk moet worden gemaakt met het type **VPN**. U kunt in een door de gebruiker gedefinieerde route geen gateway van het type **ExpressRoute** opgeven omdat u met ExpressRoute BGP moet gebruiken voor aangepaste routes. U kunt een route definiëren om ervoor te zorgen dat verkeer dat is bestemd voor het adresvoorvoegsel 0.0.0.0/0 wordt omgeleid naar de gateway van een virtueel netwerk die [op routes is gebaseerd](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). Het is mogelijk dat u on-premises een apparaat hebt dat het verkeer inspecteert en vervolgens bepaalt of dit moet worden doorgestuurd of verwijderd. Als u van plan bent een door de gebruiker gedefinieerde route te maken voor het adresvoorvoegsel 0.0.0.0/0, moet u eerst [Adresvoorvoegsel 0.0.0.0/0](#default-route) lezen. In plaats van een door de gebruiker gedefinieerde route te configureren voor het adresvoorvoegsel 0.0.0.0/0, kunt u een route met het voorvoegsel 0.0.0.0/0 adverteren via BGP, maar alleen als u [BGP hebt ingeschakeld voor de gateway van een virtueel netwerk](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>
* **Geen**: gebruik dit type als u het verkeer naar een adresvoorvoegsel wilt verwijderen, in plaats van het verkeer door te sturen naar een bestemming. Als u een mogelijkheid van Azure niet volledig hebt geconfigureerd, kan voor sommige optionele systeemroutes *Geen* worden vermeld. Als u bijvoorbeeld *Geen* ziet staan bij **IP-adres van volgende hop** met **Volgende hoptype** ingesteld op *Gateway van virtueel netwerk* of *Virtueel apparaat*, kan het zijn dat het apparaat niet wordt uitgevoerd of niet volledig is geconfigureerd. Azure maakt [standaardsysteemroutes](#default) voor gereserveerde adresvoorvoegsels met **Geen** als het 'volgende hoptype'.<br>
* **Virtueel netwerk**: kies dit type wanneer u de standaardroutering binnen een virtueel netwerk wilt vervangen. Zie [Voorbeeld van routering](#routing-example) voor een voorbeeld van waarom u een route met het hoptype **Virtueel netwerk** zou maken.<br>
* **Internet**: gebruik dit type als u verkeer dat is bestemd voor een adresvoorvoegsel expliciet naar internet wilt routeren, of als u verkeer dat is bestemd voor Azure-services met openbare IP-adressen binnen het backbone-netwerk van Azure wilt houden.

**VNet-peering** en **VirtualNetworkServiceEndpoint** kunt u niet opgeven als het 'volgende hoptype' in door de gebruiker gedefinieerde routes. Routes met het hoptype **VNet-peering** of **VirtualNetworkServiceEndpoint** worden alleen gemaakt door Azure, wanneer u peering van virtuele netwerken of een service-eindpunt configureert.

### <a name="service-tags-for-user-defined-routes-preview"></a>Servicetags voor door de gebruiker gedefinieerde routes (preview)

U kunt nu een [servicetag](service-tags-overview.md) opgeven als adres voor een door de gebruiker gedefinieerde route in plaats van een expliciet IP-bereik. Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Microsoft beheert de adres voorvoegsels die zijn opgenomen in de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen, waardoor de complexiteit van regelmatige updates voor door de gebruiker gedefinieerde routes wordt geminimaleerd en het aantal routes dat u moet maken, wordt beperkt. U kunt momenteel 25 of minder routes maken met servicetags in elke routetabel. </br>

> [!IMPORTANT]
> Servicetags voor door de gebruiker gedefinieerde routes zijn momenteel beschikbaar als preview-versie. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

#### <a name="exact-match"></a>Exacte overeenkomst
Wanneer er een exacte overeenkomst is tussen een route met een expliciet IP-voorvoegsel en een route met een servicetag, krijgt de route de voorkeur met het expliciete voorvoegsel. Wanneer meerdere routes met servicetags overeenkomende IP-voorvoegsels hebben, worden routes geëvalueerd in de volgende volgorde: 

   1. Regionale tags (bijvoorbeeld Storage.EastUS, AppService.AustraliaCentral)
   2. Tags op het hoogste niveau (bijvoorbeeld Storage, AppService)
   3. Regionale tags van AzureCloud (bijvoorbeeld AzureCloud.canadacentral, AzureCloud.eastasia)
   4. De AzureCloud-tag </br></br>

Als u deze functie wilt gebruiken, geeft u een servicetagnaam op voor de parameter voor het adres voorvoegsel in routetabelopdrachten. In PowerShell kunt u bijvoorbeeld een nieuwe route maken om verkeer dat wordt verzonden naar een Azure Storage IP-voorvoegsel naar een virtueel apparaat te sturen met behulp van: </br></br>

```azurepowershell-interactive
New-AzRouteConfig -Name "StorageRoute" -AddressPrefix "Storage" -NextHopType "VirtualAppliance" -NextHopIpAddress "10.0.100.4"
```

Dezelfde opdracht voor CLI is: </br>

```azurecli-interactive
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n StorageRoute --address-prefix Storage --next-hop-type VirtualAppliance --next-hop-ip-address 10.0.100.4
```
</br>

#### <a name="known-issues-april-2021"></a>Bekende problemen (april 2021)

Wanneer BGP-routes aanwezig zijn of een service-eindpunt is geconfigureerd op uw subnet, worden routes mogelijk niet geëvalueerd met de juiste prioriteit. Er wordt momenteel een oplossing voor deze scenario's uitgevoerd </br>


> [!NOTE] 
> In de openbare preview zijn er verschillende beperkingen. De functie wordt momenteel niet ondersteund in Azure Portal en is alleen beschikbaar via Powershell en CLI. Er is geen ondersteuning voor gebruik met containers. 

## <a name="next-hop-types-across-azure-tools"></a>'Volgende hoptypen' in Azure-hulpprogramma's

De naam die wordt weergegeven en waarnaar wordt verwezen voor 'volgende hoptypen' is verschillend voor de Azure-portal en voor opdrachtregelprogramma's, evenals voor het implementatiemodel Azure Resource Manager en het klassieke implementatiemodel. De onderstaande tabel bevat de namen die worden gebruikt om te verwijzen naar elk 'volgend hoptype' in de verschillende hulpprogramma's en [implementatiemodellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Volgend hoptype                   |Azure CLI en PowerShell (Resource Manager) |Azure CLI (klassiek) en PowerShell (klassiek)|
|-------------                   |---------                                       |-----|
|Gateway van een virtueel netwerk         |VirtualNetworkGateway                           |VPNGateway|
|Virtueel netwerk                 |VNetLocal                                       |VNETLocal (niet beschikbaar in de klassieke CLI in de asm-modus)|
|Internet                        |Internet                                        |Internet (niet beschikbaar in de klassieke CLI in de asm-modus)|
|Virtueel apparaat               |VirtualAppliance                                |VirtualAppliance|
|Geen                            |Geen                                            |Null (niet beschikbaar in de klassieke CLI in de asm-modus)|
|Peering op virtueel netwerk         |VNet-peering                                    |Niet van toepassing|
|Service-eindpunt voor virtueel netwerk|VirtualNetworkServiceEndpoint                   |Niet van toepassing|



### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Een on-premises netwerkgateway kan via BGP (Border Gateway Protocol) routes uitwisselen met de gateway van een virtueel Azure-netwerk. Het gebruik van BGP met de gateway van een virtueel Azure-netwerk is afhankelijk van het type dat u hebt geselecteerd tijdens het maken van de gateway. Als het geselecteerde type is:

* **ExpressRoute:** U moet BGP gebruiken om on-premises routes te adverteren naar Microsoft Edge router. U kunt geen door de gebruiker gedefinieerde routes maken om af te dwingen dat verkeer naar de gateway van een virtueel ExpressRoute-netwerk wordt geleid, wanneer u de gateway van een virtueel netwerk implementeert als het type ExpressRoute. U kunt door de gebruiker gedefinieerde routes gebruiken om af te dwingen dat verkeer van Express Route naar bijvoorbeeld een Network Virtual Appliance wordt geleid.<br>
* **VPN**: u kunt desgewenst BGP gebruiken. Zie [Overzicht van BGP met Azure VPN-gateways](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie.

Wanneer u routes met Azure uitwisselt via BGP, wordt er voor elk geadverteerd voorvoegsel een afzonderlijke route toegevoegd aan de routetabel van alle subnetten in een virtueel netwerk. De route wordt toegevoegd met *Gateway van virtueel netwerk* als de bron en het 'volgende hoptype'. 

Er en VPN Gateway route doorgeven kan worden uitgeschakeld op een subnet met behulp van een eigenschap in een routetabel. Wanneer u routes met Azure uitwisselt met behulp van BGP, worden routes niet toegevoegd aan de routetabel van alle subnetten met route-door propagatie van virtuele netwerkgateway uitgeschakeld. Connectiviteit met VPN-verbindingen wordt bereikt met behulp van [aangepaste routes](#custom-routes) met een volgend hoptype van *Virtueel-netwerkgateway*. **Door doorgeven van route mag niet worden uitgeschakeld op het GatewaySubnet. De gateway werkt niet als deze instelling is uitgeschakeld.** Zie Door propagatie van route van virtuele netwerkgateway uitschakelen [voor meer informatie.](manage-route-table.md#create-a-route-table)

## <a name="how-azure-selects-a-route"></a>Hoe Azure een route selecteert

Als er uitgaand verkeer wordt verzonden vanuit een subnet, selecteert Azure een route op basis van het doel-IP-adres, met behulp van een algoritme voor het matchen van het langste voorvoegsel. Een routetabel heeft bijvoorbeeld twee routes: één route met het adresvoorvoegsel 10.0.0.0/24 en een andere route met het adresvoorvoegsel 10.0.0.0/16. Azure stuurt verkeer dat is bestemd voor 10.0.0.5 naar het 'volgende hoptype' dat is opgegeven in de route met het adresvoorvoegsel 10.0.0.0/24 omdat 10.0.0.0/24 een langer voorvoegsel is dan 10.0.0.0/16, ondanks dat 10.0.0.5 is opgenomen in beide adresvoorvoegsels. Azure stuurt verkeer dat is bestemd voor 10.0.1.5 naar het 'volgende hoptype' dat is opgegeven in de route met het adresvoorvoegsel 10.0.0.0/16 omdat 10.0.1.5 niet is opgenomen in het adresvoorvoegsel 10.0.0.0/24. Om die reden is de route met het adresvoorvoegsel 10.0.0.0/16 het langste overeenkomende voorvoegsel.

Als meerdere routes hetzelfde adresvoorvoegsel bevatten, selecteert Azure het routetype op basis van de volgende prioriteit:

1. Door de gebruiker gedefinieerde route
1. BGP-route
1. Systeemroute

> [!NOTE]
> Systeemroutes voor verkeer dat is gerelateerd aan virtuele netwerken, peerings voor virtuele netwerken of service-eindpunten voor virtuele netwerken zijn voorkeursroutes, zelfs als BGP-routes specifieker zijn.

Een routetabel bevat bijvoorbeeld de volgende routes:


|Bron   |Adresvoorvoegsels  |Volgend hoptype           |
|---------|---------         |-------                 |
|Standaard  | 0.0.0.0/0        |Internet                |
|Gebruiker     | 0.0.0.0/0        |Gateway van een virtueel netwerk |

Wanneer verkeer bestemd is voor een IP-adres buiten de adresvoorvoegsels van alle andere routes in de routetabel, selecteert Azure de route met de bron **Gebruiker** omdat door de gebruiker gedefinieerde routes een hogere prioriteit hebben dan standaardsysteemroutes.

Zie [Voorbeeld van routering](#routing-example) voor een uitgebreide routeringstabel met uitleg van de routes in de tabel.

## <a name="00000-address-prefix"></a><a name="default-route"></a>Adresvoorvoegsel 0.0.0.0/0

Een route met het adresvoorvoegsel 0.0.0.0/0 wordt gebruikt door Azure voor het routeren van verkeer dat is bestemd voor een IP-adres dat zich niet in het adresvoorvoegsel van een andere route in de routetabel van een subnet bevindt. Wanneer er een subnet wordt gemaakt, maakt Azure een [standaard](#default)route naar het adresvoorvoegsel 0.0.0.0/0 van het 'volgende hoptype' **Internet**. Als u deze route niet overschrijft, stuurt Azure al het verkeer dat is bestemd voor IP-adressen die niet zijn opgenomen in het adresvoorvoegsel van een andere route naar internet. De uitzondering op de regel is dat verkeer naar de openbare IP-adressen van Azure-services het backbone-netwerk van Azure niet verlaten en dus niet wordt doorgestuurd naar internet. Als u deze route overschrijft door een [aangepaste](#custom-routes) route, wordt verkeer dat is bestemd voor adressen die geen deel uitmaken van de adresvoorvoegsels van een andere route in de routetabel verzonden naar een virtueel netwerkapparaat of een virtuele netwerkgateway, afhankelijk van wat u opgeeft in de aangepaste route.

Wanneer u het adresvoorvoegsel 0.0.0.0/0 overschrijft, wordt de standaardroutering van Azure als volgt aangepast, in aanvulling op het omleiden van het uitgaande verkeer van het subnet via de virtuele netwerkgateway of het virtuele apparaat: 

* Azure verzendt alle verkeer naar het 'volgende hoptype' dat is opgegeven in de route, om ook verkeer op te nemen dat is bestemd voor openbare IP-adressen van Azure-services. Wanneer het 'volgende hoptype' voor de route met het adresvoorvoegsel 0.0.0.0/0 **Internet** is, blijft verkeer van het subnet dat is bestemd voor de openbare IP-adressen van Azure-services altijd in het backbone-netwerk, ongeacht de Azure-regio waarin het virtuele netwerk of de Azure-serviceresource zich bevindt. Wanneer u echter een door de gebruiker gedefinieerde route of BGP-route maakt met **Gateway van virtueel netwerk** of **Virtueel apparaat** als het 'volgende hoptype', wordt al het verkeer, dus ook het verkeer dat wordt verzonden naar openbare IP-adressen van Azure services waarvoor u geen [service-eindpunten](virtual-network-service-endpoints-overview.md) hebt ingeschakeld, verzonden naar het 'volgende hoptype' dat is opgegeven in de route. Als u een service-eindpunt voor een service hebt ingeschakeld, wordt verkeer naar de service niet doorgestuurd naar het 'volgende hoptype' in een route met het adresvoorvoegsel 0.0.0.0/0 omdat adresvoorvoegsels voor de service zijn opgegeven in de route die Azure maakt wanneer u het service-eindpunt inschakelt, en de adresvoorvoegsels voor de service langer zijn dan 0.0.0.0/0.
* U kunt niet meer rechtstreeks vanaf internet toegang krijgen tot resources in het subnet. U kunt indirect vanaf internet toegang krijgen tot resources in het subnet als binnenkomend verkeer wordt doorgegeven via het apparaat dat is opgegeven door het 'volgende hoptype' voor een route met het adresvoorvoegsel 0.0.0.0/0 voordat de resource in het virtuele netwerk wordt bereikt. Als de route de volgende waarden voor het 'volgende hoptype' bevat:<br>

    * **Virtueel apparaat**: de volgende voorwaarden moeten van toepassing zijn op het apparaat:<br>

        * Het apparaat is toegankelijk vanaf internet<br>
        * Het apparaat heeft een openbaar IP-adres<br>
        * Er mag geen regel uit een netwerkbeveiligingsgroep zijn gekoppeld aan het apparaat waarmee communicatie met het apparaat wordt voorkomen<br>
        * De communicatie mag niet worden geweigerd<br>
        * Het apparaat moet het netwerkadres kunnen omzetten en doorsturen, of het verkeer via een proxy omleiden naar de doelresource in het subnet, en het verkeer terugleiden naar internet.

    * **Gateway van virtueel netwerk**: als de gateway een ExpressRoute-gateway is, kan een on-premises apparaat dat met internet is verbonden het netwerkadres omzetten en doorsturen, of het verkeer via een proxy omleiden naar de doelresource in het subnet, via [privé-peering](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering) van ExpressRoute. 

Als uw virtuele netwerk is verbonden met een Azure VPN-gateway, koppelt u geen routetabel aan het [gatewaysubnet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) met een route die als bestemming heeft: 0.0.0.0/0. Als u dit wel doet, functioneert de gateway mogelijk niet juist. Zie de vraag *Waarom zijn bepaalde poorten geopend op mijn VPN-gateway?* in de [Veelgestelde vragen over VPN Gateways](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports) voor meer informatie.

Zie [DMZ tussen Azure en uw on-premises datacenter](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) voor implementatiedetails bij het gebruik van virtuele netwerkgateways tussen internet en Azure.

## <a name="routing-example"></a>Voorbeeld van routering

Ter illustratie van de concepten in dit artikel, wordt in de volgende secties aandacht besteed aan:

* Een scenario, inclusief vereisten<br>
* De aangepaste routes die nodig zijn om te voldoen aan de vereisten<br>
* De routetabel voor een subnet, met inbegrip van de standaard- en aangepaste routes die nodig zijn om aan de vereisten te voldoen

> [!NOTE]
> Dit voorbeeld is niet bedoeld als een aanbevolen of best practice-implementatie, maar uitsluitend ter illustratie van de concepten in dit artikel.

### <a name="requirements"></a>Vereisten

1. Implementeer twee virtuele netwerken in dezelfde Azure-regio en zorg dat resources kunnen communiceren tussen de virtuele netwerken.
1. Zorg dat een on-premises netwerk veilig kan communiceren met beide virtuele netwerken via een VPN-tunnel over internet. *U kunt ook een ExpressRoute-verbinding gebruiken, maar in dit voorbeeld wordt een VPN-verbinding gebruikt.*
1. Voor één subnet in één virtueel netwerk:

    * Laat al het uitgaande verkeer vanuit het subnet, behalve naar Azure Storage en binnen het subnet, geforceerd langs een virtueel netwerkapparaat lopen, voor controle en logboekregistratie.<br>
    * Controleer geen verkeer tussen privé IP-adressen binnen het subnet; sta rechtstreeks verkeer toe tussen alle resources.<br>
    * Verwijder al het uitgaande verkeer dat is bestemd voor het andere virtuele netwerk.<br>
    * Zorg dat uitgaand verkeer naar Azure Storage rechtstreeks naar de opslag wordt geleid, zonder geforceerde omleiding via een virtueel netwerkapparaat.

1. Sta al het verkeer tussen alle andere subnetten en virtuele netwerken toe.

### <a name="implementation"></a>Implementatie

De volgende afbeelding laat een implementatie via het Azure Resource Manager-implementatiemodel zien die voldoet aan de bovenstaande vereisten:

![Netwerkdiagram](./media/virtual-networks-udr-overview/routing-example.png)

De pijlen geven de richting van het verkeer aan. 

### <a name="route-tables"></a>Routetabellen

#### <a name="subnet1"></a>Subnet1

De routetabel voor *Subnet1* in de afbeelding bevat de volgende routes:

|Id  |Bron |Staat  |Adresvoorvoegsels    |Volgend hoptype          |IP-adres van volgende hop|Naam van door gebruiker gedefinieerde route| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Standaard|Ongeldig|10.0.0.0/16         |Virtueel netwerk        |                   |              |
|2   |Gebruiker   |Actief |10.0.0.0/16         |Virtueel apparaat      |10.0.100.4         |Within-VNet1  |
|3   |Gebruiker   |Actief |10.0.0.0/24         |Virtueel netwerk        |                   |Within-Subnet1|
|4   |Standaard|Ongeldig|10.1.0.0/16         |VNet-peering           |                   |              |
|5   |Standaard|Ongeldig|10.2.0.0/16         |VNet-peering           |                   |              |
|6   |Gebruiker   |Actief |10.1.0.0/16         |Geen                   |                   |ToVNet2-1-Drop|
|7   |Gebruiker   |Actief |10.2.0.0/16         |Geen                   |                   |ToVNet2-2-Drop|
|8   |Standaard|Ongeldig|10.10.0.0/16        |Gateway van een virtueel netwerk|[X.X.X.X]          |              |
|9   |Gebruiker   |Actief |10.10.0.0/16        |Virtueel apparaat      |10.0.100.4         |To-On-Prem    |
|10  |Standaard|Actief |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Standaard|Ongeldig|0.0.0.0/0           |Internet               |                   |              |
|12  |Gebruiker   |Actief |0.0.0.0/0           |Virtueel apparaat      |10.0.100.4         |Default-NVA   |

Hier volgt een uitleg van elke route-id:

1. Azure heeft deze route automatisch toegevoegd voor alle subnetten in *Virtual-network-1* omdat 10.0.0.0/16 het enige adresbereik is dat is gedefinieerd in de adresruimte voor het virtuele netwerk. Als de door de gebruiker gedefinieerde route in route ID2 niet zou zijn gemaakt, zou verkeer dat wordt verzonden naar een adres tussen 10.0.0.1 en 10.0.255.254 worden gerouteerd binnen het virtuele netwerk omdat het voorvoegsel langer is dan 0.0.0.0/0 en niet overeenkomt met de adresvoorvoegsels van een van de andere routes. Azure heeft de status automatisch gewijzigd van *Actief* in *Ongeldig* op het moment dat ID2, een door de gebruiker gedefinieerde route, werd toegevoegd. De reden hiervoor is dat de route hetzelfde voorvoegsel heeft als de standaardroute, en door de gebruiker gedefinieerde routes hebben prioriteit boven standaardroutes. De status van deze route is nog steeds *Actief* voor *Subnet2*, omdat de routetabel waarin die door de gebruiker gedefinieerde route zich bevindt, ID2, niet is gekoppeld aan *Subnet2*.
2. Azure heeft deze route toegevoegd op het moment dat een door de gebruiker gedefinieerde route voor het adresvoorvoegsel 10.0.0.0/16 werd gekoppeld aan het subnet *Subnet1* in het virtuele netwerk *Virtual-network-1*. In de door de gebruiker gedefinieerde route is 10.0.100.4 ingesteld als het IP-adres van het virtuele apparaat omdat het adres het privé IP-adres is dat is toegewezen aan het virtuele apparaat. De routetabel van deze route is niet gekoppeld aan *Subnet2* en wordt dus niet weergegeven in de routetabel voor *Subnet2*. Deze route overschrijft de standaardroute voor het adresvoorvoegsel 10.0.0.0/16 (ID1), waarmee verkeer dat is geadresseerd aan 10.0.0.1 en 10.0.255.254 automatisch binnen het virtuele netwerk wordt doorgestuurd via het 'volgende hoptype' Virtueel netwerk. Deze route bestaat om te voldoen aan [vereiste](#requirements) 3; al het uitgaande verkeer geforceerd omleiden via een virtueel apparaat.
3. Azure heeft deze route toegevoegd op het moment dat een door de gebruiker gedefinieerde route voor het adresvoorvoegsel 10.0.0.0/24 werd gekoppeld aan het subnet *Subnet1*. Verkeer dat is bestemd voor adressen tussen 10.0.0.1 en 10.0.0.254 blijft binnen het subnet en wordt niet doorgestuurd naar het virtuele apparaat dat is opgegeven in de vorige regel (ID2). De reden hiervoor is dat deze route een langer voorvoegsel heeft dan de route ID2. Deze route is niet gekoppeld aan *Subnet2* en wordt dus niet weergegeven in de routetabel voor *Subnet2*. In feite vervangt deze route de route ID2 voor verkeer binnen *Subnet1*. Deze route bestaat om te voldoen aan [vereiste](#requirements) 3.
4. Azure heeft de routes in de id's 4 en 5 automatisch toegevoegd voor alle subnetten in *Virtual-network-1* op het moment dat het virtuele netwerk via peering werd gekoppeld met *Virtual-network-2.* *Virtual-network-2* heeft twee adresbereiken in de eigen adresruimte: 10.1.0.0/16 en 10.2.0.0/16, en dus heeft Azure voor elk bereik een route toegevoegd. Als de door de gebruiker gedefinieerde routes in route-id's 6 en 7 niet zouden zijn gemaakt, zou verkeer dat wordt verzonden naar een adres tussen 10.1.0.1-10.1.255.254 en 10.2.0.1-10.2.255.254 worden gerouteerd naar het via peering gekoppelde virtuele netwerk omdat het voorvoegsel langer is dan 0.0.0.0/0 en niet overeenkomt met de adresvoorvoegsels van een van de andere routes. Azure heeft de status automatisch gewijzigd van *Actief* in *Ongeldig* op het moment dat de routes in id's 6 en 7 werden toegevoegd. De reden hiervoor is dat deze routes hetzelfde voorvoegsel hebben als de routes in id's 4 en 5, en door de gebruiker gedefinieerde routes hebben prioriteit boven standaardroutes. De status van de routes in de ID's 4 en 5 is nog steeds Actief voor *Subnet2,* omdat de routetabel waarin de door de gebruiker gedefinieerde routes in de ID's 6 en 7 zich in hebben, niet is gekoppeld aan *Subnet2.*  Er is een peering van virtuele netwerken gemaakt om te voldoen aan [vereiste](#requirements) 1.
5. Zie de uitleg voor ID4.
6. Azure heeft deze route en de route in ID7 toegevoegd op het moment dat door de gebruiker gedefinieerde routes voor de adresvoorvoegsels 10.1.0.0/16 en 10.2.0.0/16 werden gekoppeld aan het subnet *Subnet1*. Verkeer dat is bestemd voor adressen tussen 10.1.0.1-10.1.255.254 en 10.2.0.1-10.2.255.254 wordt verwijderd door Azure, en wordt dus niet doorgestuurd naar de via peering gekoppelde virtuele netwerken. De reden hiervoor is dat door de gebruiker gedefinieerde routes standaardroutes vervangen. De routes worden niet gekoppeld aan *Subnet2* en worden dus niet weergegeven in de routetabel voor *Subnet2*. De routes overschrijven de routes ID4 en ID5 voor verkeer dat *Subnet1* verlaat. De routes ID6 en ID7 bestaan om te voldoen aan [vereiste](#requirements) 3; verkeer verwijderen dat is bestemd voor het andere virtuele netwerk.
7. Zie de uitleg voor ID6.
8. Azure heeft deze routes automatisch toegevoegd voor alle subnetten in *Virtual-network-1* op het moment dat er binnen het virtuele netwerk een gateway van het type VPN werd gemaakt. Azure heeft het openbare IP-adres van de virtuele netwerkgateway toegevoegd aan de routetabel. Verkeer dat wordt verzonden naar een adres tussen 10.10.0.1 en 10.10.255.254 wordt doorgestuurd naar de virtuele netwerkgateway. Het voorvoegsel is langer dan 0.0.0.0/0 en komt niet overeen met de adresvoorvoegsels van een van de andere routes. Er is een virtuele netwerkgateway gemaakt om te voldoen aan [vereiste](#requirements) 2.
9. Azure heeft deze route toegevoegd op het moment dat een door de gebruiker gedefinieerde route voor het adresvoorvoegsel 10.10.0.0/16 werd toegevoegd aan de routetabel van *Subnet1*. Deze route vervangt ID8. De route verzendt al het verkeer dat is bestemd voor het on-premises netwerk naar een NVA voor inspectie, in plaats van verkeer rechtstreeks on-premises te routeren. Deze route is gemaakt om te voldoen aan [vereiste](#requirements) 3.
10. Azure heeft deze route automatisch toegevoegd aan het subnet op het moment dat een service-eindpunt voor een Azure-service werd ingeschakeld voor het subnet. Verkeer vanuit het subnet wordt door Azure omgeleid naar een openbaar IP-adres van de service, via het infrastructuurnetwerk van Azure. Het voorvoegsel is langer dan 0.0.0.0/0 en komt niet overeen met de adresvoorvoegsels van een van de andere routes. Er is een service-eindpunt gemaakt om te voldoen aan [vereiste](#requirements) 3, waarmee verkeer dat is bestemd voor Azure Storage rechtstreeks naar Azure-opslag kan worden gestuurd.
11. Azure heeft deze route automatisch toegevoegd aan de routetabel van alle subnetten in *Virtual-network-1* en *Virtual-network-2.* Het adresvoorvoegsel 0.0.0.0/0 is het kortste voorvoegsel. Verkeer dat wordt verzonden naar adressen met een langer adresvoorvoegsel worden gerouteerd op basis van andere routes. De standaardinstelling is dat Azure al het verkeer dat is bestemd voor andere adressen dan de adressen die zijn opgegeven in een van de andere routes naar internet stuurt. Azure heeft de status voor *Subnet1* automatisch gewijzigd van *Actief* in *Ongeldig* op het moment dat een door de gebruiker gedefinieerde route voor het adresvoorvoegsel 0.0.0.0/0 (ID12) werd gekoppeld aan het subnet. De status van deze route is nog steeds *Actief* voor alle andere subnetten in beide virtuele netwerken. De reden hiervoor is dat de route niet is gekoppeld aan andere subnetten van andere virtuele netwerken.
12. Azure heeft deze route toegevoegd op het moment dat een door de gebruiker gedefinieerde route voor het adresvoorvoegsel 0.0.0.0/0 werd gekoppeld aan het subnet *Subnet1*. In de door de gebruiker gedefinieerde route is 10.0.100.4 ingesteld als het IP-adres van het virtuele apparaat. Deze route is niet gekoppeld aan *Subnet2* en wordt dus niet weergegeven in de routetabel voor *Subnet2*. Al het verkeer voor adressen die niet overeenkomen met de adresvoorvoegsels van een andere route wordt verzonden naar het virtuele apparaat. Door het toevoegen van deze route is de status van de standaardroute voor het adresvoorvoegsel 0.0.0.0/0 (ID11) gewijzigd van *Actief* in *Ongeldig* voor *Subnet1*. De reden hiervoor is dat een door de gebruiker gedefinieerde route prioriteit heeft boven een standaardroute. Deze route bestaat om te voldoen aan de derde [vereiste](#requirements).

#### <a name="subnet2"></a>Subnet2

De routetabel voor *Subnet2* in de afbeelding bevat de volgende routes:

|Bron  |Staat  |Adresvoorvoegsels    |Volgend hoptype             |IP-adres van volgende hop|
|------- |-------|------              |-------                   |--------           
|Standaard |Actief |10.0.0.0/16         |Virtueel netwerk           |                   |
|Standaard |Actief |10.1.0.0/16         |VNet-peering              |                   |
|Standaard |Actief |10.2.0.0/16         |VNet-peering              |                   |
|Standaard |Actief |10.10.0.0/16        |Gateway van een virtueel netwerk   |[X.X.X.X]          |
|Standaard |Actief |0.0.0.0/0           |Internet                  |                   |
|Standaard |Actief |10.0.0.0/8          |Geen                      |                   |
|Standaard |Actief |100.64.0.0/10       |Geen                      |                   |
|Standaard |Actief |192.168.0.0/16      |Geen                      |                   |

De routetabel voor *Subnet2* bevat alle standaardroutes van Azure, plus de optionele routes Virtual network peering en VPN Gateway. Azure heeft de optionele routes toegevoegd aan alle subnetten in het virtuele netwerk op het moment dat de gateway en peering werden toegevoegd aan het virtuele netwerk. Azure heeft de routes voor de adresvoegsels 10.0.0.0/8, 192.168.0.0/16 en 100.64.0.0/10 verwijderd uit de *subnet1-routetabel* toen de door de gebruiker gedefinieerde route voor het adresvoegsel 0.0.0.0/0 werd toegevoegd aan *Subnet1*.  

## <a name="next-steps"></a>Volgende stappen

* [Maken van een gebruiker gedefinieerde route - Azure-portal](tutorial-create-route-table-portal.md)<br>
* [Het configureren van BGP op Azure VPN-Gateways met behulp van PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
* [BGP gebruiken met ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)<br>
* [Alle routes weergeven voor een subnet](diagnose-network-routing-problem.md). Een door de gebruiker gedefinieerde routetabel bevat alleen door de gebruiker gedefinieerde routes, niet de standaard- en BGP-routes voor een subnet. Als u alle routes weergeeft, ziet u de standaardroutes, de BGP-routes en de door de gebruiker gedefinieerde routes voor het subnet waarin een netwerkinterface zich bevindt.<br>
* [Het 'volgende hoptype' bepalen](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tussen een virtuele machine en een doel-IP-adres. De functie Azure Network Watcher maakt het mogelijk om te bepalen of verkeer een subnet verlaat en wordt doorgestuurd naar wat volgens u de bestemming moet zijn.
