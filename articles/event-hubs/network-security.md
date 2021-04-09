---
title: Netwerk beveiliging voor Azure Event Hubs
description: In dit artikel wordt beschreven hoe u toegang kunt configureren vanaf privé-eind punten
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 9503fc26c22d7dbff13c5754288f577b7bb3242f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010993"
---
# <a name="network-security-for-azure-event-hubs"></a>Netwerk beveiliging voor Azure Event Hubs 
In dit artikel wordt beschreven hoe u de volgende beveiligings functies gebruikt met Azure Event Hubs: 

- Servicetags
- IP-firewall regels
- Netwerk service-eind punten
- Privé-eindpunten


## <a name="service-tags"></a>Servicetags
Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen, zodat de complexiteit van regel matige updates voor netwerk beveiligings regels wordt geminimaliseerd. Zie [service Tags Overview](../virtual-network/service-tags-overview.md)(Engelstalig) voor meer informatie over service tags.

U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules) of [Azure firewall](../firewall/service-tags.md). Gebruik service tags in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de naam van de service label (bijvoorbeeld **EventHub**) op te geven in het juiste *bron* -of *doel* veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren.

| Servicetag | Doel | Kunt u inkomend of uitgaand gebruiken? | Kan regionaal worden? | Kunt gebruiken met Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | Uitgaand | Ja | Ja |


## <a name="ip-firewall"></a>IP-firewall 
Event Hubs naam ruimten zijn standaard toegankelijk vanuit Internet zolang de aanvraag een geldige verificatie en autorisatie heeft. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(klasseloze Inter-Domain route ring)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Deze functie is handig in scenario's waarin Azure Event Hubs alleen toegankelijk moet zijn vanaf bepaalde bekende sites. Met firewall regels kunt u regels configureren voor het accepteren van verkeer dat afkomstig is van specifieke IPv4-adressen. Als u bijvoorbeeld Event Hubs met [Azure Express route](../expressroute/expressroute-faqs.md#supported-services)gebruikt, kunt u een **firewall regel** maken om alleen verkeer toe te staan van uw on-premises IP-adressen van de infra structuur. 

De IP-firewall regels worden toegepast op het niveau van de Event Hubs naam ruimte. Daarom gelden de regels voor alle verbindingen van clients die gebruikmaken van elk ondersteund protocol. Een verbindings poging van een IP-adres dat niet overeenkomt met een toegestane IP-regel op de Event Hubs naam ruimte, wordt geweigerd als niet-geautoriseerd. De IP-regel wordt niet vermeld in het antwoord. IP-filter regels worden in volg orde toegepast en de eerste regel die overeenkomt met het IP-adres, bepaalt de accepteren of afwijzen.

Zie [IP-Firewall configureren voor een event hub](event-hubs-ip-filtering.md) voor meer informatie

## <a name="network-service-endpoints"></a>Netwerk service-eind punten
Dankzij de integratie van Event Hubs met de [service-eind punten van Virtual Network (VNet)](../virtual-network/virtual-network-service-endpoints-overview.md) is beveiligde toegang mogelijk tot berichten mogelijkheden van werk belastingen, zoals virtuele machines die zijn gebonden aan virtuele netwerken, waarbij het netwerkpad van het netwerk verkeer aan beide uiteinden wordt beveiligd.

Eenmaal geconfigureerd om te zijn gebonden aan ten minste één subnet-service-eind punt van een virtueel netwerk, accepteert de respectieve Event Hubs naam ruimte geen verkeer meer vanaf een wille keurige locatie, maar gemachtigd subnetten in virtuele netwerken. Vanuit het perspectief van het virtuele netwerk moet u een Event Hubs naam ruimte binden aan een service-eind punt een geïsoleerde netwerk tunnel van het subnet van het virtuele netwerk naar de berichten service configureren. 

Het resultaat is een privé-en geïsoleerde relatie tussen de werk belastingen die zijn gebonden aan het subnet en de betreffende Event Hubs naam ruimte, ondanks het waarneem bare netwerk adres van het berichten service-eind punt in een openbaar IP-bereik. Er is een uitzonde ring op dit gedrag. Als u een service-eind punt inschakelt, wordt de `denyall` regel in de [IP-firewall](event-hubs-ip-filtering.md) die aan het virtuele netwerk is gekoppeld, standaard ingeschakeld. U kunt specifieke IP-adressen toevoegen aan de IP-firewall om toegang tot het open bare eind punt van Event hub mogelijk te maken. 

> [!IMPORTANT]
> Virtuele netwerken worden ondersteund in de lagen **Standard** en **Dedicated** van Event Hubs. Het wordt niet ondersteund in de laag **basis** .

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Geavanceerde beveiligings scenario's ingeschakeld door VNet-integratie 

Oplossingen die een strakke en compartmentalized beveiliging vereisen en waarbij virtuele netwerk-subnetten de segmentatie tussen de compartmentalized-services bieden, is nog steeds communicatie paden nodig tussen services die zich in deze compartimenten bevinden.

Elke onmiddellijke IP-route tussen de compartimenten, waaronder die van HTTPS via TCP/IP, vormt het risico van misbruik van beveiligings problemen vanuit de netwerklaag. Berichten services bieden geïsoleerde communicatie paden, waar berichten zelfs naar de schijf worden geschreven wanneer ze tussen partijen worden overgezet. Werk belastingen in twee verschillende virtuele netwerken die beide zijn gebonden aan hetzelfde Event Hubs-exemplaar kunnen efficiënt en betrouwbaar communiceren via berichten, terwijl de respectieve integriteit van de isolatie grens van het netwerk behouden blijft.
 
Dit betekent dat uw beveiligings gevoelige cloud oplossingen niet alleen toegang krijgen tot de toonaangevende betrouw bare en schaal bare asynchrone berichten mogelijkheden van Azure, maar ze kunnen nu gebruikmaken van berichten om communicatie paden te maken tussen veilige oplossingen compartimenten die inherent veiliger zijn dan wat kan worden behaald met een peer-to-peer communicatie modus, inclusief HTTPS en andere met TLS beveiligde socket protocollen.

### <a name="bind-event-hubs-to-virtual-networks"></a>Event hubs binden aan virtuele netwerken

**Regels voor virtuele netwerken** zijn de firewall beveiligings functie waarmee wordt bepaald of de naam ruimte van uw Azure-Event hubs verbindingen accepteert van een bepaald subnet van een virtueel netwerk.

Het binden van een Event Hubs naam ruimte aan een virtueel netwerk is een proces dat uit twee stappen bestaat. U moet eerst een service- **eind punt voor een virtueel netwerk** maken in het subnet van een virtueel netwerk en deze inschakelen voor **micro soft. EventHub** , zoals wordt uitgelegd in het artikel [overzicht van service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) . Wanneer u het service-eind punt hebt toegevoegd, bindt u de naam ruimte van de Event Hubs met een regel voor het **virtuele netwerk**.

De regel van het virtuele netwerk is een koppeling van de Event Hubs naam ruimte met een subnet van een virtueel netwerk. Terwijl de regel bestaat, krijgen alle werk belastingen die aan het subnet zijn gebonden toegang tot de Event Hubs naam ruimte. Event Hubs zichzelf geen uitgaande verbindingen tot stand brengt, hoeft geen toegang te krijgen en wordt daarom nooit toegang tot uw subnet verleend door deze regel in te scha kelen.

Zie [service-eind punten voor virtuele netwerken configureren voor een event hub](event-hubs-service-endpoints.md) voor meer informatie

## <a name="private-endpoints"></a>Privé-eindpunten

Met [Azure Private Link service](../private-link/private-link-overview.md) kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Event Hubs, Azure Storage en Azure Cosmos DB) en door Azure gehoste klanten/partner services via een **persoonlijk eind punt** in uw virtuele netwerk.

Een persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

> [!IMPORTANT]
> Deze functie wordt ondersteund voor zowel de **standaard** als de **toegewezen** laag. Het wordt niet ondersteund in de laag **basis** .

Zie voor meer informatie [privé-eind punten configureren voor een event hub](private-link-service.md)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [IP-Firewall configureren voor een Event Hub](event-hubs-ip-filtering.md)
- [Service-eind punten voor virtuele netwerken configureren voor een Event Hub](event-hubs-service-endpoints.md)
- [Privé-eind punten configureren voor een Event Hub](private-link-service.md)