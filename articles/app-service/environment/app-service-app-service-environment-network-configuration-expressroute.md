---
title: Azure ExpressRoute v1 configureren
description: Netwerk configuratie voor App Service Environment voor PowerApps met Azure ExpressRoute. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88961819"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Details van de netwerk configuratie voor het App Service Environment van PowerApps met Azure ExpressRoute

Klanten kunnen een [Azure ExpressRoute][ExpressRoute] -circuit verbinden met hun virtuele netwerk infrastructuur om hun on-premises netwerk uit te breiden naar Azure. App Service Environment wordt gemaakt in een subnet van de infra structuur van het [virtuele netwerk][virtualnetwork] . Apps die worden uitgevoerd op App Service Environment maken beveiligde verbindingen met back-end-bronnen die alleen toegankelijk zijn via de ExpressRoute-verbinding.  

In deze scenario's kunnen App Service Environment worden gemaakt:
- Azure Resource Manager virtuele netwerken.
- Het klassieke implementatie model Virtual Networks.
- Virtuele netwerken die gebruikmaken van open-adresbereiken of RFC1918 adres ruimten (dat wil zeggen persoonlijke adressen). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Vereiste netwerk verbinding

App Service Environment heeft vereisten voor de netwerk verbinding die in eerste instantie niet kan worden gehaald in een virtueel netwerk dat is verbonden met ExpressRoute.

App Service Environment moeten de volgende instellingen voor netwerk connectiviteit goed functioneren:

* Uitgaande netwerk verbinding met Azure Storage eind punten wereld wijd op poort 80 en poort 443. Deze eind punten bevinden zich in dezelfde regio als App Service Environment en ook andere Azure-regio's. Azure Storage-eind punten worden omgezet onder de volgende DNS-domeinen: table.core.windows.net, blob.core.windows.net, queue.core.windows.net en file.core.windows.net.  

* Uitgaande netwerk verbinding met de Azure Files-service op poort 445.

* Uitgaande netwerk verbinding met Azure SQL Database eind punten die zich in dezelfde regio bevinden als App Service Environment. SQL Database-eind punten worden omgezet onder het domein database.windows.net, waarvoor open toegang is vereist voor de poorten 1433, 11000-11999 en 14000-14999. Zie [poorten na 1433 voor ADO.NET 4,5](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md)voor meer informatie over het gebruik van SQL database V12 poort.

* Uitgaande netwerk verbinding met de Azure-beheer vlak-eind punten (zowel het klassieke Azure-implementatie model als Azure Resource Manager-eind punten). Connectiviteit met deze eind punten omvat de domeinen management.core.windows.net en management.azure.com. 

* Uitgaande netwerk verbinding met het ocsp.msocsp.com-, mscrl.microsoft.com-en crl.microsoft.com-domein. Voor de ondersteuning van TLS-functionaliteit is connectiviteit met deze domeinen vereist.

* De DNS-configuratie voor het virtuele netwerk moet alle eind punten en domeinen kunnen omzetten die in dit artikel worden vermeld. Als de eind punten niet kunnen worden opgelost, mislukt het App Service Environment maken. Alle bestaande App Service Environment is als beschadigd gemarkeerd.

* Uitgaande toegang op poort 53 is vereist voor communicatie met DNS-servers.

* Als er een aangepaste DNS-server bestaat aan het andere uiteinde van een VPN-gateway, moet de DNS-server bereikbaar zijn vanaf het subnet dat App Service Environment bevat. 

* Het uitgaande netwerkpad kan niet worden gereist via interne bedrijfs proxy's en kan niet on-premises geforceerd worden getunneld. Met deze acties wordt het effectief NAT-adres van het uitgaande netwerk verkeer gewijzigd van App Service Environment. Wijzigingen in het NAT-adres van App Service Environment uitgaand netwerk verkeer veroorzaken verbindings fouten aan veel van de eind punten. Het maken van App Service Environment mislukt. Alle bestaande App Service Environment is als beschadigd gemarkeerd.

* Inkomende netwerk toegang tot de vereiste poorten voor App Service Environment moet zijn toegestaan. Zie [inkomend verkeer voor app service Environment beheren][requiredports]voor meer informatie.

Zorg ervoor dat een geldige DNS-infra structuur is geconfigureerd en onderhouden voor het virtuele netwerk om te voldoen aan de DNS-vereisten. Als de DNS-configuratie wordt gewijzigd nadat App Service Environment is gemaakt, kunnen ontwikkel aars afdwingen App Service Environment om de nieuwe DNS-configuratie op te halen. U kunt het opnieuw opstarten van een doorlopende omgeving activeren met behulp van het pictogram **opnieuw starten** onder App service Environment beheer in de [Azure Portal][NewPortal]. Bij het opnieuw opstarten wordt de nieuwe DNS-configuratie door de omgeving opgehaald.

Als u wilt voldoen aan de vereisten voor inkomende netwerk toegang, configureert u een [netwerk beveiligings groep (NSG)][NetworkSecurityGroups] op het subnet app service environment. De NSG biedt de vereiste toegang [om inkomend verkeer naar app service Environment te beheren][requiredports].

## <a name="outbound-network-connectivity"></a>Uitgaande netwerk verbinding

Een nieuw gemaakt ExpressRoute-circuit adverteert standaard een standaard route die uitgaande internet connectiviteit mogelijk maakt. App Service Environment kunt deze configuratie gebruiken om verbinding te maken met andere Azure-eind punten.

Een veelvoorkomende klant configuratie is het definiëren van een eigen standaard route (0.0.0.0/0), waardoor uitgaand Internet verkeer wordt afgedwongen om on-premises te stroom. Deze verkeers stroom invariably pauzes App Service Environment. Het uitgaande verkeer is geblokkeerd on-premises of NAT is een niet-herken bare set met adressen die niet meer werken met verschillende Azure-eind punten.

De oplossing is het definiëren van een (of meer) door de gebruiker gedefinieerde routes (Udr's) op het subnet dat App Service Environment bevat. Een UDR definieert subnet-specifieke routes die worden uitgevoerd in plaats van de standaard route.

Gebruik, indien mogelijk, de volgende configuratie:

* De ExpressRoute-configuratie adverteert 0.0.0.0/0. Standaard tunnelt de configuratie geforceerd al het uitgaande verkeer on-premises.
* De UDR die wordt toegepast op het subnet met App Service Environment definieert 0.0.0.0/0 met het volgende hop-type Internet. Verderop in dit artikel wordt een voor beeld van deze configuratie beschreven.

Het gecombineerde effect van deze configuratie is dat de UDR van het subnet prioriteit heeft boven de ExpressRoute Force tunneling. Uitgaande internet toegang van App Service Environment is gegarandeerd.

> [!IMPORTANT]
> De routes die in een UDR zijn gedefinieerd, moeten specifiek genoeg zijn om voor rang te hebben op routes die worden geadverteerd door de ExpressRoute-configuratie. In het voor beeld in de volgende sectie wordt het brede adres bereik 0.0.0.0/0 gebruikt. Dit bereik kan per ongeluk worden overschreven door route advertenties die gebruikmaken van meer specifieke adresbereiken.
> 
> App Service Environment wordt niet ondersteund met ExpressRoute-configuraties die cross-Advertising routes van het open bare pad naar het privé-peering-pad. ExpressRoute-configuraties waarvoor open bare peering is geconfigureerd, ontvangen route-advertisements van micro soft voor een groot aantal Microsoft Azure IP-adresbereiken. Als deze adresbereiken door kruisen worden geadverteerd op het pad naar de privé-peering, worden alle uitgaande netwerk pakketten van het subnet van de App Service Environment geforceerd getunneld naar de on-premises netwerk infrastructuur van de klant. Deze netwerk stroom wordt momenteel niet ondersteund met App Service Environment. Eén oplossing is het stoppen van cross-Advertising routes van het open bare pad naar het privé-peering-pad.
> 
> 

Zie [virtueel netwerk verkeer routeren][UDROverview]voor achtergrond informatie over door de gebruiker gedefinieerde routes.  

Zie [netwerk verkeer routeren met een route tabel met behulp van Power shell][UDRHowTo]voor meer informatie over het maken en configureren van door de gebruiker gedefinieerde routes.

## <a name="udr-configuration"></a>UDR-configuratie

In deze sectie ziet u een voor beeld van een UDR-configuratie voor App Service Environment.

### <a name="prerequisites"></a>Vereisten

* Installeer Azure PowerShell vanaf de [pagina Azure down loads][AzureDownloads]. Kies een down load met een datum van 2015 juni of hoger. Onder **opdracht regel Programma's**  >  **Windows Power shell** selecteert u **installeren** om de meest recente Power shell-cmdlets te installeren.

* Maak een uniek subnet voor exclusief gebruik door App Service Environment. Het unieke subnet zorgt ervoor dat de Udr's die op het subnet wordt toegepast, het uitgaande verkeer alleen voor App Service Environment heeft geopend.

> [!IMPORTANT]
> Implementeer App Service Environment alleen nadat u de configuratie stappen hebt voltooid. De stappen zorgen ervoor dat uitgaande netwerk verbinding beschikbaar is voordat u App Service Environment probeert te implementeren.

### <a name="step-1-create-a-route-table"></a>Stap 1: een route tabel maken

Maak een route tabel met de naam **DirectInternetRouteTable** in de regio vs West Azure, zoals wordt weer gegeven in dit fragment:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Stap 2: routes maken in de tabel

Routes toevoegen aan de route tabel om uitgaande internet toegang in te scha kelen.  

Uitgaande toegang tot Internet configureren. Definieer een route voor 0.0.0.0/0, zoals wordt weer gegeven in dit fragment:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 is een breed adres bereik. Het bereik wordt overschreven door de adresbereiken die worden geadverteerd door ExpressRoute die specifieker zijn. Een UDR met de route 0.0.0.0/0 moet worden gebruikt in combi natie met een ExpressRoute-configuratie die alleen 0.0.0.0/0 adverteert. 

U kunt ook een actuele, uitgebreide lijst met CIDR-bereiken downloaden die worden gebruikt door Azure. Het XML-bestand voor alle Azure IP-adresbereiken is beschikbaar via het [micro soft Download centrum][DownloadCenterAddressRanges].  

> [!NOTE]
>
> De IP-adresbereiken van Azure veranderen in de loop van de tijd. Door de gebruiker gedefinieerde routes hebben periodieke hand matige updates nodig om gesynchroniseerd te blijven.
>
> Eén UDR heeft een standaard bovengrens van 100 routes. U moet de IP-adresbereiken van het Azure samenvatten zodat deze binnen de routerings limiet van 100 passen. UDR routes moeten specifieker zijn dan routes die worden geadverteerd door uw ExpressRoute-verbinding.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Stap 3: de tabel koppelen aan het subnet

Koppel de route tabel aan het subnet waar u App Service Environment wilt implementeren. Met deze opdracht wordt de tabel **DirectInternetRouteTable** gekoppeld aan het **ASESubnet** -subnet dat app service Environment bevat.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Stap 4: de route testen en bevestigen

Nadat de route tabel is gebonden aan het subnet, test en bevestig de route.

Implementeer een virtuele machine in het subnet en bevestig deze voor waarden:

* Uitgaand verkeer naar de Azure-en niet-Azure-eind punten die in dit artikel worden beschreven, stroomt **niet** het ExpressRoute-circuit. Als uitgaand verkeer van het subnet on-premises geforceerde tunneling is, mislukt het maken van App Service Environment altijd.
* DNS-Zoek opdrachten voor de eind punten die in dit artikel worden beschreven, worden correct omgezet. 

Nadat u de configuratie stappen hebt voltooid en de route hebt bevestigd, verwijdert u de virtuele machine. Het subnet moet leeg zijn als App Service Environment is gemaakt.

U bent nu klaar om App Service Environment te implementeren.

## <a name="next-steps"></a>Volgende stappen

Zie [Inleiding tot app service Environment][IntroToAppServiceEnvironment]om aan de slag te gaan met app service Environment voor PowerApps.

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->