---
title: Azure SQL Database beveiligings functies
description: Dit artikel bevat een algemene beschrijving van hoe Azure SQL Database klant gegevens in azure beveiligt.
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
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: e0e7089e7c674f324c2c3d293661c518b41731b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84021854"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL Database beveiligings functies    
Azure SQL Database biedt een relationele database service in Azure. SQL Database heeft een eigen set beveiligings mogelijkheden om klant gegevens te beschermen en krachtige beveiligings functies te bieden die klanten verwachten van een relationele database service. Deze mogelijkheden zijn gebaseerd op de besturings elementen die worden overgenomen van Azure.

## <a name="security-capabilities"></a>Beveiligingsmogelijkheden

### <a name="usage-of-the-tds-protocol"></a>Gebruik van het TDS-protocol
Azure SQL Database ondersteunt alleen het tabular data stream (TDS)-protocol, waarmee de data base alleen toegankelijk is via de standaard poort van TCP-1433.

### <a name="azure-sql-database-firewall"></a>Azure SQL Database firewall
Azure SQL Database een firewall functionaliteit biedt om klant gegevens te helpen beveiligen, die standaard niet alle toegang tot SQL Database voor komt, zoals hieronder wordt weer gegeven.

![Azure SQL Database firewall](./media/infrastructure-sql/sql-database-firewall.png)

De firewall van de gateway kan adressen beperken, waardoor klanten nauw keurig kunnen bepalen hoeveel IP-adressen moeten worden opgegeven. De firewall verleent toegang op basis van het oorspronkelijke IP-adres van elke aanvraag.

Klanten kunnen de firewall configureren met behulp van een beheer portal of programmatisch met behulp van de Azure SQL Database beheer REST API. De Azure SQL Database gateway firewall voor komt standaard dat de TDS-toegang van klanten tot Azure SQL Database. Klanten moeten toegang configureren met behulp van toegangs beheer lijsten (Acl's) voor het toestaan van Azure SQL Database verbindingen op basis van bron-en doel-Internet adressen,-protocollen en-poort nummers.

### <a name="dosguard"></a>DoSGuard
Het aantal DoS-aanvallen (Denial of service) wordt teruggebracht door een SQL Database-gatewayservice met de naam DoSGuard. DoSGuard traceert actief mislukte aanmeldingen van IP-adressen. Als er binnen een bepaalde periode meerdere mislukte aanmeldingen vanaf een specifiek IP-adres zijn, heeft het IP-adres geen toegang tot resources in de service voor een vooraf gedefinieerde tijds periode.

Daarnaast voert de Azure SQL Database-gateway de volgende handelingen uit:

- Mogelijkheden voor het beveiligen van kanalen voor het implementeren van met TDS FIPS 140-2 gevalideerde versleutelde verbindingen wanneer verbinding wordt gemaakt met de database servers.
- Stateful TDS-pakket inspectie terwijl het verbindingen van clients accepteert. De gateway valideert de verbindings gegevens en geeft de TDS-pakketten door aan de juiste fysieke server op basis van de database naam die is opgegeven in de connection string.

Het overkoepelend principe voor netwerkbeveiliging van de Azure SQL Database-service is dat alleen de verbinding en communicatie die nodig zijn om de service uit te voeren, worden toegestaan. Alle andere poorten, protocollen en verbindingen worden standaard geblokkeerd. Virtual Local Area Networks (VLAN'S) en Acl's worden gebruikt om netwerk communicatie te beperken op basis van de bron-en doel netwerken, protocollen en poort nummers.

Mechanismen die zijn goedgekeurd voor het implementeren van op het netwerk gebaseerde Acl's, bevatten Acl's op routers en load balancers. Deze mechanismen worden beheerd door Azure-netwerken, firewalls voor gast-VM'S en Azure SQL Database gateway-firewall regels, die door de klant worden geconfigureerd.

## <a name="data-segregation-and-customer-isolation"></a>Schei ding van gegevens en gebruikers isolatie
Het productie netwerk van Azure is zodanig gestructureerd dat openbaar toegankelijke systeem onderdelen worden gescheiden van interne bronnen. Er bestaan fysieke en logische grenzen tussen webservers die toegang bieden tot de open bare Azure Portal en de onderliggende Azure-infra structuur, waarbij exemplaren van klant toepassingen en klant gegevens zich bevinden.

Alle openbaar toegankelijke informatie wordt beheerd in het productie netwerk van Azure. Het productie netwerk is onderhevig aan twee ledige verificatie-en grens beveiligings mechanismen, maakt gebruik van de functieset firewall en beveiliging die wordt beschreven in de vorige sectie, en maakt gebruik van gegevens isolatie functies zoals vermeld in de volgende secties.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Niet-geautoriseerde systemen en isolatie van de FC
Omdat de infrastructuur controller (FC) de centrale orchestrator van de Azure-infra structuur is, zijn er belang rijke controles aanwezig om bedreigingen te beperken, met name van mogelijk geknoeide FAs binnen de klant toepassingen. De FC herkent geen hardware waarvan de apparaatgegevens (bijvoorbeeld MAC-adres) niet vooraf zijn geladen in de FC. De DHCP-servers op de FC hebben lijsten geconfigureerd van MAC-adressen van de knoop punten die ze bereid zijn om op te starten. Zelfs als niet-geautoriseerde systemen zijn verbonden, zijn ze niet opgenomen in de inventaris van de infra structuur en zijn ze dus niet verbonden of geautoriseerd om te communiceren met een systeem binnen de inventaris van de infra structuur. Dit vermindert het risico van communicatie van niet-geautoriseerde systemen met de FC en krijgt toegang tot het VLAN en Azure.

### <a name="vlan-isolation"></a>VLAN-isolatie
Het productie netwerk van Azure wordt logisch gescheiden in drie primaire VLAN'S:

- Het hoofd-VLAN: verbindt niet-vertrouwde klant knooppunten.
- Het FC-VLAN: bevat vertrouwde FCs en ondersteunende systemen.
- Het VLAN van het apparaat: bevat vertrouwde netwerk-en andere infrastructuur apparaten.

### <a name="packet-filtering"></a>Pakket filtering
De IPFilter en de software firewalls die zijn geïmplementeerd in het basis besturingssysteem en gast besturingssysteem van de knoop punten, leggen connectiviteits beperkingen af en voor komen niet-geautoriseerd verkeer tussen Vm's.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hyper Visor, basis besturingssysteem en gast-Vm's
De isolatie van het basis besturingssysteem van de virtuele gast-Vm's en de gast-Vm's van elkaar worden beheerd door de Hyper Visor en het basis besturingssysteem.

### <a name="types-of-rules-on-firewalls"></a>Typen regels op firewalls
Een regel wordt gedefinieerd als:

{Src IP, src-poort, doel-IP, doel poort, doel protocol, in/uit, stateful/stateless, stateful flow time-out}.

Synchrone SYN-pakketten (inactief teken) zijn alleen toegestaan in of uit als een van de regels dit toestaat. Voor TCP maakt Azure gebruik van stateless regels waarbij het principe alleen alle niet-SYN-pakketten naar of van de virtuele machine toestaat. De Security-locatie is dat elke host-stack kan leiden tot het negeren van een niet-SYN als er eerder geen SYN-pakket is gedetecteerd. Het TCP-protocol zelf is stateful en in combi natie met de op stateless SYN gebaseerde regel wordt een algemeen gedrag van een stateful implementatie gerealiseerd.

Voor UDP (User Data gram Protocol) maakt Azure gebruik van een stateful regel. Telkens wanneer een UDP-pakket overeenkomt met een regel, wordt een omgekeerde stroom in de andere richting gemaakt. Deze stroom heeft een ingebouwde time-out.

Klanten zijn verantwoordelijk voor het instellen van hun eigen firewalls boven op wat Azure biedt. Hier kunnen klanten de regels voor binnenkomend en uitgaand verkeer definiëren.

### <a name="production-configuration-management"></a>Productie configuratie beheer
Standaard beveiligde configuraties worden onderhouden door de respectieve Operations-teams in Azure en Azure SQL Database. Alle configuratie wijzigingen in productie systemen worden gedocumenteerd en bijgehouden door middel van een centraal traceer systeem. Wijzigingen in de software en hardware worden bijgehouden via het centrale traceer systeem. Netwerk wijzigingen die betrekking hebben op de toegangs beheer lijst worden bijgehouden met behulp van een ACL Management-service.

Alle configuratie wijzigingen in Azure worden ontwikkeld en getest in de faserings omgeving en ze worden vervolgens geïmplementeerd in de productie omgeving. Software builds worden gecontroleerd als onderdeel van de tests. Beveiligings-en privacy-controles worden gecontroleerd als onderdeel van de criteria voor de invoer controlelijst. Wijzigingen worden op geplande intervallen geïmplementeerd door het respectieve implementatie team. Releases worden beoordeeld en afgemeld door het respectieve personeel van het implementatie team voordat ze worden geïmplementeerd in de productie omgeving.

Wijzigingen worden gecontroleerd op geslaagde pogingen. Als er een fout optreedt, wordt de wijziging teruggedraaid naar de vorige staat of wordt er een hotfix geïmplementeerd om het probleem met de goed keuring van de aangewezen mede werkers te verhelpen. Source depot, Git, TFS, Master Data Services (MDS), lopers, Azure-beveiligings bewaking, de FC en het WinFabric-platform worden gebruikt om de configuratie-instellingen in de virtuele Azure-omgeving centraal te beheren, toe te passen en te controleren.

Op dezelfde manier hebben de wijzigingen in de hardware en het netwerk tot stand gebracht om de naleving van de build-vereisten te evalueren. De releases worden beoordeeld en geautoriseerd via een gecoördineerde wijzigings advies bord (CAB) van de respectieve groepen in de stack.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van micro soft voor het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)
