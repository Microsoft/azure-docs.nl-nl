---
title: Productie netwerk van Azure
description: Meer informatie over het productie netwerk van Azure. Zie methoden voor beveiligings toegang en beveiligings mechanismen voor het tot stand brengen van een verbinding met het netwerk.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 724c2ee2d74ca9cab11280e92faac71c2cb2218e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543806"
---
# <a name="the-azure-production-network"></a>Het Azure-productie netwerk
De gebruikers van het productie netwerk van Azure bevatten zowel externe klanten die toegang hebben tot hun eigen Azure-toepassingen als interne ondersteunings medewerkers van Azure die het productie netwerk beheren. In dit artikel worden de methoden voor beveiligings toegang en beveiligings mechanismen beschreven voor het tot stand brengen van verbindingen met het productie netwerk van Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Internet Routering en fout tolerantie
Een wereld wijd redundante interne en externe Azure-Domain Name Service (DNS)-infra structuur, gecombineerd met meerdere primaire en secundaire DNS-server clusters, biedt fout tolerantie. Op hetzelfde moment worden extra Azure-netwerk beveiligings mechanismen, zoals NetScaler, gebruikt om DDoS-aanvallen (Distributed Denial of service) te voor komen en de integriteit van Azure DNS services te beveiligen.

De Azure DNS-servers bevinden zich in meerdere datacenter faciliteiten. De Azure DNS-implementatie bevat een hiërarchie van secundaire en primaire DNS-servers voor het openbaar oplossen van domein namen van Azure-klanten. De domein namen worden meestal omgezet in een CloudApp.net-adres, dat het VIP-adres (virtuele IP) voor de service van de klant inpakt. De VIP die overeenkomt met het interne toegewezen IP-adres (DIP) van de Tenant omzetting, wordt door de micro soft load balancers die verantwoordelijk zijn voor die VIP, uitgevoerd voor Azure.

Azure wordt gehost in geografisch gedistribueerde Azure-data centers binnen de VS en is gebouwd op basis van geavanceerde routerings platforms die robuuste, schaal bare architectuur standaarden implementeren. De belangrijkste functies zijn:

- Multi protocol label switching (MPLS) is gebaseerd op verkeers techniek, die een efficiënt koppelings gebruik en een ongeschikte degradatie van de service biedt als er sprake is van een storing.
- Netwerken worden geïmplementeerd met ' behoefte plus één ' (N + 1) redundantie architecturen of beter.
- Extern worden data centers bediend door speciale netwerk circuits met een hoge band breedte die redundante verbinding met de eigenschappen met meer dan 1.200 Internet serviceproviders op meerdere peering punten. Deze verbinding is groter dan 2.000 gigabytes per seconde (GBps) aan de capaciteit van de rand.

Omdat micro soft eigenaar is van zijn eigen netwerk circuits tussen data centers, kunnen ze met deze kenmerken het Azure-aanbod 99,9 + procent Beschik baarheid behalen zonder dat er traditionele Internet serviceproviders van derden nodig zijn.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Verbinding met productie netwerk en gekoppelde firewalls
Het beleid voor Internet verkeer van Azure Network stuurt verkeer naar het Azure-productie netwerk dat zich in het dichtstbijzijnde regionale Data Center binnen de Verenigde Staten bevindt. Omdat Azure-productie centra consistente netwerk architectuur en-hardware onderhouden, wordt de beschrijving van de verkeers stroom op consistente wijze toegepast op alle data centers.

Wanneer het Internet verkeer voor Azure wordt doorgestuurd naar het dichtstbijzijnde Data Center, wordt er verbinding gemaakt met de toegangs routers. Deze toegangs routers fungeren voor het isoleren van verkeer tussen Azure-knoop punten en door de klant gemaakte Vm's. Netwerk infrastructuur apparaten op de locaties voor toegang en rand zijn de grens punten waar ingangs-en uitgangs filters worden toegepast. Deze routers worden geconfigureerd via een trapsgewijs gelaagde toegangs beheer lijst (ACL) voor het filteren van het ongewenste netwerk verkeer en het Toep assen van verkeers limieten, indien nodig. Verkeer dat door ACL wordt toegestaan, wordt doorgestuurd naar de load balancers. Distributie routers zijn ontworpen om alleen door micro soft goedgekeurde IP-adressen toe te staan, anti-spoofing te bieden en TCP-verbindingen tot stand te brengen die gebruikmaken van Acl's.

Externe apparaten voor taak verdeling bevinden zich achter de toegangs routers om Network Address Translation (NAT) uit te voeren van Internet Routeer bare IP-adressen naar interne Ip's van Azure. De apparaten routeren ook pakketten naar geldige productie-interne Ip's en poorten, en ze fungeren als een beveiligings mechanisme om het weer geven van de adres ruimte van het interne productie netwerk te beperken.

Standaard dwingt micro soft Hypertext Transfer Protocol Secure (HTTPS) af voor al het verkeer dat wordt verzonden naar webbrowsers van klanten, inclusief aanmelding en alle verkeer daarna. Door het gebruik van TLS v 1.2 kan een beveiligde tunnel voor verkeer worden door lopen. Acl's op toegangs-en kern routers zorgen ervoor dat de bron van het verkeer consistent is met wat er wordt verwacht.

Een belang rijk verschil in deze architectuur, wanneer deze is vergeleken met de traditionele beveiligings architectuur, is dat er geen specifieke hardware firewalls, gespecialiseerde apparaten voor inbraak detectie of voor komen of andere beveiligings apparaten die normaal gesp roken worden verwacht voordat verbindingen worden gemaakt met de Azure-productie omgeving. Klanten verwachten doorgaans deze hardwarematige firewall apparaten in het Azure-netwerk. Er worden echter geen werkzaamheden in azure toegepast. Bijna uitsluitend, deze beveiligings functies zijn ingebouwd in de software die de Azure-omgeving uitvoert om robuuste beveiligings mechanismen met meerdere lagen te bieden, inclusief firewall mogelijkheden. Daarnaast is het bereik van de grens en de bijbehorende komen van kritieke beveiligings apparaten eenvoudiger te beheren en te inventariseren, zoals wordt weer gegeven in de vorige afbeelding, omdat deze wordt beheerd door de software waarop Azure wordt uitgevoerd.

## <a name="core-security-and-firewall-features"></a>Kern beveiliging en Firewall functies
Azure implementeert robuuste functies voor software beveiliging en firewall op verschillende niveaus voor het afdwingen van beveiligings functies die doorgaans worden verwacht in een traditionele omgeving om de grens van de beveiligings autorisatie van de kern te beveiligen.

### <a name="azure-security-features"></a>Azure-beveiligings functies
Azure implementeert op de host gebaseerde software firewalls binnen het productie netwerk. Een aantal kern beveiliging en Firewall functies bevinden zich in de Azure-basis omgeving. Deze beveiligings functies zijn gebaseerd op een ingrijpende strategie binnen de Azure-omgeving. Klant gegevens in Azure worden beveiligd door de volgende firewalls:

**Hyper Visor firewall (pakket filter)**: deze firewall is geïmplementeerd in de Hyper Visor en geconfigureerd door de FC-agent (Fabric controller). Deze firewall beveiligt de Tenant die in de virtuele machine wordt uitgevoerd tegen onbevoegde toegang. Wanneer een virtuele machine wordt gemaakt, wordt standaard al het verkeer geblokkeerd en vervolgens voegt de FC-agent regels en uitzonde ringen in het filter toe om geautoriseerd verkeer toe te staan.

Er zijn twee soorten regels geprogrammeerd:

- **Machine config-of infrastructuur regels**: standaard wordt alle communicatie geblokkeerd. Er zijn uitzonde ringen die een VM toestaan om Dynamic Host Configuration Protocol (DHCP)-communicatie en DNS-informatie te verzenden en ontvangen en verkeer naar het ' open bare ' Internet te verzenden naar andere Vm's binnen het FC-cluster en de activerings server van het besturings systeem. Omdat de lijst met toegestane uitgaande doelen van de virtuele machines geen Azure router-subnetten en andere micro soft-eigenschappen bevat, fungeren de regels als een beveiligingslaag.
- **Regels voor de functie configuratie bestand**: definieert de inkomende acl's op basis van het service model van de tenants. Als een Tenant bijvoorbeeld een web-front-end heeft op poort 80 op een bepaalde VM, wordt poort 80 geopend voor alle IP-adressen. Als op de virtuele machine een werk rollen wordt uitgevoerd, wordt de rol werk nemer alleen geopend voor de virtuele machine binnen dezelfde Tenant.

**Systeem eigen host firewall**: Azure Service Fabric en Azure Storage worden uitgevoerd op een systeem eigen besturings systeem, dat geen Hyper Visor heeft en daarom Windows Firewall is geconfigureerd met de voor gaande twee sets met regels.

**Host-firewall**: de host-firewall beveiligt de host-partitie, waarmee de Hyper Visor wordt uitgevoerd. De regels zijn geprogrammeerd zodat alleen de vakken FC en Jump kunnen communiceren met de host-partitie op een specifieke poort. De andere uitzonde ringen zijn het toestaan van DHCP-reacties en DNS-antwoorden. Azure maakt gebruik van een computer configuratie bestand, dat een sjabloon bevat met de firewall regels voor de host-partitie. Er bestaat ook een uitzonde ring voor de firewall van de host, waarmee Vm's kunnen communiceren met host-onderdelen, Wire-server en meta gegevens server via specifieke protocol/poorten.

**Gast firewall**: het Windows Firewall-gedeelte van het gast besturingssysteem, dat door klanten kan worden geconfigureerd op vm's en opslag van klanten.

Aanvullende beveiligings functies die zijn ingebouwd in de Azure-mogelijkheden zijn onder andere:

- Infrastructuur onderdelen die zijn toegewezen aan IP-adressen van spannings dips. Een aanvaller op internet kan geen verkeer naar deze adressen door lopen omdat micro soft dit niet zou bereiken. Internet Gateway-routers filteren pakketten die alleen worden geadresseerd aan interne adressen, zodat ze het productie netwerk niet invoeren. De enige onderdelen die verkeer accepteren dat is omgeleid naar Vip's, zijn load balancers.
- Firewalls die op alle interne knoop punten zijn geïmplementeerd, hebben drie belang rijke beveiligings architectuur voor elk scenario:

   - Firewalls worden achter de load balancer geplaatst en accepteren pakketten vanaf elke locatie. Deze pakketten zijn bedoeld om extern te worden blootgesteld en komen overeen met de open poorten in een traditionele perimeter firewall.
   - Firewalls accepteren pakketten alleen uit een beperkt aantal adressen. Deze overweging maakt deel uit van de strategie voor verdediging in de diepte tegen DDoS-aanvallen. Dergelijke verbindingen zijn cryptografisch geverifieerd.
   - Firewalls zijn alleen toegankelijk via interne knoop punten selecteren. Ze accepteren alleen pakketten van een geïnventariseerde lijst met IP-adressen van de bron, die allemaal zijn spannings dips in het Azure-netwerk. Een aanval op het bedrijfs netwerk kan bijvoorbeeld aanvragen sturen naar deze adressen, maar de aanvallen zouden worden geblokkeerd, tenzij het bron adres van het pakket in de genummerde lijst in het Azure-netwerk is opgenomen.
     - De toegangs router op het perimeter netwerk blokkeert uitgaande pakketten die zijn geadresseerd aan een adres dat zich in het Azure Network bevindt vanwege de geconfigureerde statische routes.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van micro soft voor het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)
