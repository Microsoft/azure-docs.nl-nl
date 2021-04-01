---
title: 'Azure-ExpressRoute: asymmetrische route ring'
description: In dit artikel worden problemen met asymmetrische routering besproken die u kunt tegenkomen in een netwerk met meerdere koppelingen naar een bestemming.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97560505"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Asymmetrische routering met meerdere netwerkpaden
In dit artikel wordt uitgelegd hoe netwerk verkeer verschillende paden kan aannemen wanneer er meerdere routes beschikbaar zijn tussen de netwerk bron en de doel locatie.

Er zijn twee concepten die u moet weten om asymmetrische route ring te begrijpen. De eerste is het effect van meerdere netwerk paden. Het andere is hoe apparaten, zoals een firewall, een status hebben. Dit soort apparaten worden stateful apparaten genoemd. Wanneer deze twee factoren worden gecombineerd, kunnen ze een scenario maken waarin netwerk verkeer wordt verwijderd door het stateful apparaat.  Het verkeer wordt verwijderd omdat het niet heeft gedetecteerd dat het verkeer afkomstig is van zichzelf.

## <a name="multiple-network-paths"></a>Meerdere netwerkpaden
Wanneer een bedrijfs netwerk slechts één koppeling met internet heeft via een Internet provider, worden al het verkeer naar en van Internet verplaatst naar hetzelfde pad. Het is gebruikelijk dat bedrijven meerdere circuits aanschaffen om redundante paden te maken om de uptime van het netwerk te verbeteren. Met dit type configuratie is het mogelijk dat er met het verkeer één koppeling naar Internet wordt verzonden en dat er via een andere koppeling wordt geretourneerd. Dit scenario wordt aangeduid als asymmetrische route ring. In asymmetrische route ring neemt het netwerk verkeer van de retour een ander pad van de oorspronkelijke stroom.

![Netwerk met meerdere paden](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Hoewel asymmetrische route ring zich doorgaans voordoet wanneer u naar Internet gaat. Dit gebeurt ook wanneer een combi natie van meerdere paden wordt geïntroduceerd. Het eerste voor beeld is wanneer u een Internet-pad en een persoonlijk pad naar dezelfde bestemming hebt. Het tweede voor beeld is wanneer u meerdere persoonlijke paden hebt die ook naar dezelfde bestemming gaan.

Elke router langs het pad tussen de bron en de bestemming berekent het beste pad om de bestemming te bereiken. De router bepaalt het best mogelijke pad op basis van twee belang rijke factoren:

* Routering tussen externe netwerken is gebaseerd op een routeringsprotocol, het Border Gateway Protocol (BGP). BGP kijkt naar aankondigingen van neighbors en voert hier een aantal stappen op uit om het beste pad naar de bestemming te bepalen. Het beste pad wordt opgeslagen in de routeringstabel.
* De routeringspaden worden beïnvloed door de lengte van een subnetmasker dat aan een route is gekoppeld. Als een router meerdere aankondigingen voor hetzelfde IP-adres ontvangt, selecteert de router het pad met het langere subnetmasker omdat het als een meer specifieke route wordt beschouwd.

## <a name="stateful-devices"></a>Stateful apparaten
Routers kijken naar de IP-header van een pakket voor routeringsdoeleinden. Er zijn echter apparaten die nog dieper in het pakket kijken. Normaal gesp roken kijken deze apparaten naar Layer 4-Transmission Control Protocol (TCP) of UDP (User Data gram Protocol) of zelfs laag 7-headers (toepassingslaag). Dergelijke apparaten zijn beveiligingsapparaten of apparaten die de bandbreedte optimaliseren. 

Een firewall is een algemeen voorbeeld van een stateful apparaat. Met een firewall kunnen pakketten worden toegestaan of geweigerd door de interfaces op basis van verschillende criteria. Deze criteria omvatten, maar zijn niet beperkt tot Protocol, TCP/UDP-poort en URL-headers. Dit niveau van pakket inspectie kan een zware verwerkings belasting op het apparaat hebben. 

Om de prestaties te verbeteren, inspecteert de firewall het eerste pakket van een stroom. Als het pakket toestaat dat het door de interfaces wordt door gegeven, worden de stroom gegevens in de status tabel bewaard. Alle aankomende pakketten met betrekking tot deze stroom zijn dan toegestaan op basis van de eerste bepaling. Een pakket dat deel uitmaakt van een bestaande stroom kan aankomen op de firewall waarvan het niet afkomstig is. Omdat deze geen eerdere status informatie over de eerste stroom heeft, breekt de firewall het pakket niet.

## <a name="asymmetric-routing-with-expressroute"></a>Asymmetrische routering met ExpressRoute
Wanneer u via ExpressRoute verbinding maakt met Microsoft, verandert het netwerk als volgt:

* U hebt meerdere koppelingen naar Microsoft. De ene koppeling is uw bestaande Internet verbinding en de andere is via uw ExpressRoute-verbinding. Bepaalde verkeer dat is bestemd voor micro soft, kan via de Internet verbinding worden uitgevoerd, maar u kunt teruggaan naar uw ExpressRoute-verbinding. Dit kan ook gebeuren als verkeer overgaat op ExpressRoute, maar terugkeert via het Internet pad.
* U hebt meer specifieke IP-adressen ontvangen van het ExpressRoute-circuit. Wanneer het verkeer van uw netwerk naar micro soft gaat voor services die via ExpressRoute worden aangeboden, zullen uw routers altijd de voor keur geven aan de ExpressRoute-verbinding.

Als u wilt weten hoe deze twee wijzigingen zich op een netwerk voordoen, kunt u een aantal scenario's overwegen. Als voor beeld hebt u een circuit op internet en gebruikt u alle micro soft-Services via internet. Het verkeer van uw netwerk naar en van micro soft gaat over dezelfde Internet koppeling en passeert via een firewall. De firewall registreert de stroom wanneer deze het eerste pakket ziet. Elk gegeven pakket van die conversatie is toegestaan, omdat de stroom in de status tabel bestaat.

![Asymmetrische routering met ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Vervolgens brengt u een ExpressRoute-circuit om services te gebruiken die door micro soft worden aangeboden via ExpressRoute. Alle andere services van micro soft worden gebruikt via internet. U implementeert een afzonderlijke firewall aan de rand die is verbonden met de ExpressRoute-verbinding. Micro soft adverteert specifiekere voor voegsels naar het netwerk via ExpressRoute voor bepaalde services. De routeringsinfrastructuur kiest ExpressRoute als het voorkeurspad voor die voorvoegsels. 

Als u uw open bare IP-adressen niet aan micro soft adverteert via ExpressRoute. Micro soft communiceert via internet met uw open bare IP-adressen. Verkeer dat vanuit uw netwerk naar micro soft wordt verzonden, maakt gebruik van de ExpressRoute-verbinding, maar het retour verkeer van micro soft gebruikt dan het Internet pad. Wanneer de firewall aan de rand een antwoord pakket ziet voor een stroom die niet bekend is, worden deze pakketten verwijderd.

Als u ervoor kiest om dezelfde Network Address Translation groep (NAT) voor ExpressRoute en voor Internet te adverteren. U ziet vergelijk bare problemen met de clients in uw netwerk op privé-IP-adressen. Aanvragen voor services zoals Windows Update gaan via internet, omdat IP-adressen voor deze services niet worden geadverteerd via ExpressRoute. Het retour verkeer gaat echter terug via ExpressRoute. Aangezien micro soft een IP-adres heeft ontvangen met hetzelfde subnetmasker van Internet en ExpressRoute, is het voorkeurs pad altijd ExpressRoute. Als een firewall of een ander stateful apparaat op de netwerk rand van de ExpressRoute-verbinding geen eerdere informatie over een stroom heeft, worden deze pakketten verwijderd.

## <a name="asymmetric-routing-solutions"></a>Oplossingen voor asymmetrische routering
U hebt twee beschik bare opties om het probleem van asymmetrische route ring op te lossen. De eerste is via route ring en de tweede is met behulp van een op een bron gebaseerd NAT (SNAT).

### <a name="routing"></a>Routering
Zorg ervoor dat uw open bare IP-adressen worden geadverteerd naar de juiste WAN-koppelingen (Wide Area Network). Als u bijvoorbeeld het Internet wilt gebruiken voor verificatie verkeer en ExpressRoute voor uw e-mail verkeer. Adverteer uw open bare IP-adressen van Active Directory Federation Services (AD FS) niet via ExpressRoute. Zorg er ook voor dat u uw on-premises AD FS-server niet beschikbaar maakt voor IP-adressen die de router via ExpressRoute ontvangt. Routes die worden ontvangen via ExpressRoute zijn specifieker, zodat ExpressRoute het voorkeurspad wordt voor verificatieverkeer naar Microsoft. Als u geen aandacht besteedt aan de wijze waarop route ring wordt uitgevoerd in uw netwerk asymmetrische routerings problemen kunnen zich voordoen.

Als u ExpressRoute wilt gebruiken voor verificatie, moet u ervoor zorgen dat u AD FS open bare IP-adressen via ExpressRoute zonder NAT adverteert. Wanneer deze manier is geconfigureerd, gaat het verkeer dat afkomstig is van micro soft naar uw on-premises AD FS-server via ExpressRoute. Het retour verkeer van uw netwerk dat naar micro soft gaat, gebruikt ExpressRoute omdat het de voor keur heeft voor de route via internet.

### <a name="source-based-nat"></a>Brongebaseerde NAT
Een andere manier om het probleem met asymmetrische route ring op te lossen is door gebruik te maken van SNAT. U kunt bijvoorbeeld het open bare IP-adres van een on-premises Simple Mail Transfer Protocol-server (SMTP) niet adverteren via ExpressRoute. In plaats daarvan bent u van plan om Internet te gebruiken voor dit type communicatie. Een aanvraag die afkomstig is van micro soft die naar uw on-premises SMTP-server gaat, passeert Internet. U verzendt de inkomende aanvraag via SNAT naar een intern IP-adres. Het retour verkeer van de SMTP-server gaat naar de Edge-firewall (die u gebruikt voor NAT) in plaats van via ExpressRoute. Als gevolg hiervan krijgt het retour verkeer het Internet pad.

![Brongebaseerde NAT-netwerkconfiguratie](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Detectie van asymmetrische routering
Traceroute is de beste manier om ervoor te zorgen dat uw netwerkverkeer via het verwachte pad loopt. Als u verkeer van uw on-premises SMTP-server naar micro soft verwacht om het Internet pad te halen, is de verwachte traceroute van de SMTP-server naar Microsoft 365. Het resultaat valideert dat verkeer inderdaad uw netwerk naar het Internet vertrekt en niet naar ExpressRoute.

