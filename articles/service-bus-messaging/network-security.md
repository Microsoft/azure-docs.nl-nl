---
title: Netwerk beveiliging voor Azure Service Bus
description: In dit artikel worden de functies voor netwerk beveiliging beschreven, zoals service tags, IP-firewall regels, service-eind punten en privé-eind punten.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: db0dd89d1f902699c27b724609505ba681757454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310462"
---
# <a name="network-security-for-azure-service-bus"></a>Netwerk beveiliging voor Azure Service Bus 
In dit artikel wordt beschreven hoe u de volgende beveiligings functies gebruikt met Azure Service Bus: 

- Servicetags
- IP-firewall regels
- Netwerk service-eind punten
- Privé-eindpunten


## <a name="service-tags"></a>Servicetags
Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen, zodat de complexiteit van regel matige updates voor netwerk beveiligings regels wordt geminimaliseerd. Zie [service Tags Overview](../virtual-network/service-tags-overview.md)(Engelstalig) voor meer informatie over service tags.

U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules) of [Azure firewall](../firewall/service-tags.md). Gebruik service tags in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de naam van de service label (bijvoorbeeld **ServiceBus**) op te geven in het juiste *bron* -of *doel* veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren.

| Servicetag | Doel | Kunt u inkomend of uitgaand gebruiken? | Kan regionaal worden? | Kunt gebruiken met Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus verkeer dat gebruikmaakt van de Premium-servicelaag. | Uitgaand | Ja | Ja |


> [!NOTE]
> U kunt alleen service tags gebruiken voor **Premium** -naam ruimten. Als u een **standaard** naam ruimte gebruikt, gebruikt u het IP-adres dat u ziet wanneer u de volgende opdracht uitvoert: `nslookup <host name for the namespace>` . Bijvoorbeeld: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>IP-firewall 
Service Bus naam ruimten zijn standaard toegankelijk vanuit Internet zolang de aanvraag een geldige verificatie en autorisatie heeft. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(klasseloze Inter-Domain route ring)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Deze functie is handig in scenario's waarin Azure Service Bus alleen toegankelijk moet zijn vanaf bepaalde bekende sites. Met firewall regels kunt u regels configureren voor het accepteren van verkeer dat afkomstig is van specifieke IPv4-adressen. Als u bijvoorbeeld Service Bus gebruikt met [Azure Express route] [Express-route], kunt u een **firewall regel** maken om alleen verkeer toe te staan van uw on-premises IP-adressen of adressen van een bedrijfs NAT-gateway. 

De IP-firewall regels worden toegepast op het niveau van de Service Bus naam ruimte. Daarom gelden de regels voor alle verbindingen van clients die gebruikmaken van elk ondersteund protocol. Een verbindings poging van een IP-adres dat niet overeenkomt met een toegestane IP-regel op de Service Bus naam ruimte, wordt geweigerd als niet-geautoriseerd. De IP-regel wordt niet vermeld in het antwoord. IP-filter regels worden in volg orde toegepast en de eerste regel die overeenkomt met het IP-adres, bepaalt de accepteren of afwijzen.

Zie [IP-Firewall configureren voor een service bus naam ruimte](service-bus-ip-filtering.md) voor meer informatie

## <a name="network-service-endpoints"></a>Netwerk service-eind punten
Dankzij de integratie van Service Bus met de [service-eind punten van Virtual Network (VNet)](service-bus-service-endpoints.md) is beveiligde toegang mogelijk tot berichten mogelijkheden van werk belastingen, zoals virtuele machines die zijn gebonden aan virtuele netwerken, waarbij het netwerkpad van het netwerk verkeer aan beide uiteinden wordt beveiligd.

Zodra het is geconfigureerd om te worden gebonden aan ten minste één subnet-service-eind punt van een virtueel netwerk, accepteert de respectieve Service Bus naam ruimte geen verkeer meer vanaf een wille keurige locatie, maar geautoriseerde virtuele netwerken. Vanuit het perspectief van het virtuele netwerk moet u een Service Bus naam ruimte binden aan een service-eind punt een geïsoleerde netwerk tunnel van het subnet van het virtuele netwerk naar de berichten service configureren.

Het resultaat is een privé-en geïsoleerde relatie tussen de werk belastingen die zijn gebonden aan het subnet en de betreffende Service Bus naam ruimte, ondanks het waarneem bare netwerk adres van het berichten service-eind punt in een openbaar IP-bereik.

> [!IMPORTANT]
> Virtuele netwerken worden alleen ondersteund in de [Premium-laag](service-bus-premium-messaging.md) service bus naam ruimten.
> 
> Wanneer u VNet-service-eind punten met Service Bus gebruikt, moet u deze eind punten niet inschakelen in toepassingen die gebruikmaken van de standaard-en Premium-laag Service Bus naam ruimten. Omdat de standaard tier geen VNets ondersteunt. Het eind punt is alleen toegestaan voor de naam ruimte van de Premium-laag.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiligings scenario's ingeschakeld door VNet-integratie 

Oplossingen die een strakke en compartmentalized beveiliging vereisen, en waarbij virtuele netwerk-subnetten de segmentatie tussen de compartmentalized-services bieden, is over het algemeen nog steeds communicatie paden nodig tussen services die zich in deze compartimenten bevinden.

Elke onmiddellijke IP-route tussen de compartimenten, waaronder die van HTTPS via TCP/IP, vormt het risico van misbruik van beveiligings problemen vanuit de netwerklaag. Berichten services bieden volledig geïsoleerde communicatie paden, waar berichten zelfs naar de schijf worden geschreven wanneer ze tussen partijen worden overgezet. Werk belastingen in twee verschillende virtuele netwerken die beide zijn gebonden aan hetzelfde Service Bus-exemplaar kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de isolatie grens van het netwerk behouden blijft.
 
Dit betekent dat uw beveiligings gevoelige cloud oplossingen niet alleen toegang krijgen tot de toonaangevende betrouw bare en schaal bare asynchrone berichten mogelijkheden van Azure, maar ze kunnen nu gebruikmaken van berichten om communicatie paden te maken tussen veilige oplossingen compartimenten die inherent veiliger zijn dan wat kan worden behaald met een peer-to-peer communicatie modus, inclusief HTTPS en andere met TLS beveiligde socket protocollen.

### <a name="bind-service-bus-to-virtual-networks"></a>Service Bus binden aan virtuele netwerken

*Regels voor virtuele netwerken* zijn de firewall beveiligings functie waarmee wordt bepaald of de Azure service bus-server verbindingen van een bepaald subnet van een virtueel netwerk accepteert.

Het binden van een Service Bus naam ruimte aan een virtueel netwerk is een proces dat uit twee stappen bestaat. U moet eerst een **Virtual Network Service-eind punt** maken op een Virtual Network subnet en inschakelen voor **micro soft. ServiceBus** , zoals wordt uitgelegd in het [overzicht van service-eind punten](service-bus-service-endpoints.md). Wanneer u het service-eind punt hebt toegevoegd, bindt u de naam ruimte van de Service Bus met een regel voor het **virtuele netwerk**.

De regel van het virtuele netwerk is een koppeling van de Service Bus naam ruimte met een subnet van een virtueel netwerk. Terwijl de regel bestaat, krijgen alle werk belastingen die aan het subnet zijn gebonden toegang tot de Service Bus naam ruimte. Service Bus zichzelf nooit uitgaande verbindingen tot stand brengt, geen toegang nodig heeft en daarom nooit toegang tot uw subnet verleend door deze regel in te scha kelen.

Zie [service-eind punten voor virtuele netwerken configureren voor een service bus naam ruimte](service-bus-service-endpoints.md) voor meer informatie

## <a name="private-endpoints"></a>Privé-eindpunten

Met Azure Private Link service kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Service Bus, Azure Storage en Azure Cosmos DB) en Azure hostende klanten/partner services via een **persoonlijk eind punt** in uw virtuele netwerk.

Een persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Zie [Wat is een Azure Private Link?](../private-link/private-link-overview.md) voor meer informatie.

> [!NOTE]
> Deze functie wordt ondersteund met de **Premium** -laag van Azure service bus. Zie het artikel [service Buss voor Premium en Standard Messa ging](service-bus-premium-messaging.md) voor meer informatie over de Premium-laag.


Zie [persoonlijke eind punten configureren voor een service bus naam ruimte](private-link-service.md) voor meer informatie


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [IP-Firewall configureren voor een Service Bus naam ruimte](service-bus-ip-filtering.md)
- [Service-eind punten voor virtuele netwerken configureren voor een Service Bus naam ruimte](service-bus-service-endpoints.md)
- [Privé-eind punten configureren voor een Service Bus naam ruimte](private-link-service.md)