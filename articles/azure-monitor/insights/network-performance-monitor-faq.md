---
title: Veelgestelde vragen-Netwerkprestatiemeter oplossing in azure | Microsoft Docs
description: In dit artikel worden de veelgestelde vragen over Netwerkprestatiemeter in azure vastgelegd. Met Netwerkprestatiemeter (NPM) kunt u de prestaties van uw netwerken nagenoeg in realtime bewaken en netwerk prestatie knelpunten detecteren en vinden.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 8e26a4d996e4c1767856b3dd98ae318d8992a89b
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562689"
---
# <a name="network-performance-monitor-solution-faq"></a>Veelgestelde vragen over Netwerkprestatiemeter oplossingen

![Netwerkprestatiemeter-symbool](media/network-performance-monitor-faq/npm-symbol.png)

In dit artikel worden de veelgestelde vragen over Netwerkprestatiemeter (NPM) in azure vastgelegd

[Netwerkprestatiemeter](../../networking/network-monitoring-overview.md) is een op de cloud gebaseerde oplossing voor [hybride netwerk bewaking](./network-performance-monitor-performance-monitor.md) waarmee u de netwerk prestaties tussen verschillende punten in uw netwerk infrastructuur kunt bewaken. Daarnaast kunt u hiermee de netwerk connectiviteit van [service-en toepassings eindpunten](./network-performance-monitor-service-connectivity.md) bewaken en [de prestaties van Azure ExpressRoute bewaken](./network-performance-monitor-expressroute.md). 

Netwerkprestatiemeter detecteert netwerk problemen zoals verkeer blackholing, routerings fouten en problemen die niet kunnen worden gedetecteerd door conventionele methoden voor netwerk bewaking. De oplossing genereert waarschuwingen en waarschuwt u als een drempelwaarde voor een netwerkverbinding wordt overschreden. Bovendien worden problemen met de netwerkprestaties tijdig gedetecteerd en wordt de oorzaak van het probleem op een bepaald netwerksegment of apparaat opgespoord. 

Meer informatie over de verschillende mogelijkheden die door [Netwerkprestatiemeter](../../networking/network-monitoring-overview.md) worden ondersteund, is online beschikbaar.

## <a name="set-up-and-configure-agents"></a>Agents instellen en configureren

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Wat zijn de platform vereisten voor de knoop punten die door NPM worden gebruikt voor bewaking?
Hieronder vindt u de platform vereisten voor de verschillende mogelijkheden van NPM:

- De prestatie meter van de NPM en de functies van de service connectiviteits monitor ondersteunen zowel Windows Server-als Windows-Desk tops/-client besturingssystemen. Ondersteunde versies van Windows Server-besturings systemen zijn 2008 SP1 of hoger. Ondersteunde versies van Windows-Desk tops/-clients zijn Windows 10, Windows 8,1, Windows 8 en Windows 7. 
- De ExpressRoute-monitor functie van NPM ondersteunt alleen het besturings systeem Windows Server (2008 SP1 of hoger).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Kan ik Linux-machines gebruiken als bewakings knooppunten in NPM?
De mogelijkheid om netwerken met Linux-knoop punten te bewaken, is nu algemeen beschikbaar. Zien [hier](../../virtual-machines/extensions/oms-linux.md)de agent. Linux-agents bieden alleen bewakings mogelijkheden voor de prestaties van de prestatie meter van de NPM en zijn niet beschikbaar voor de monitor mogelijkheden voor service connectiviteit en ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Wat zijn de grootte vereisten van de knoop punten die moeten worden gebruikt voor bewaking door NPM?
Voor het uitvoeren van de NPM-oplossing op knoop punt-Vm's voor het bewaken van netwerken, moeten de knoop punten ten minste 500 MB geheugen en één kern hebben. U hoeft geen afzonderlijke knoop punten te gebruiken voor het uitvoeren van NPM. De oplossing kan worden uitgevoerd op knoop punten waarop andere workloads worden uitgevoerd. De oplossing biedt de mogelijkheid om het bewakings proces te stoppen als er meer dan 5% CPU wordt gebruikt.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Als u NPM wilt gebruiken, moet ik mijn knoop punten verbinden als een directe agent of via System Center Operations Manager?
Zowel de prestatie meter als de service connectiviteits monitor biedt ondersteuning voor knoop punten [die zijn verbonden als directe agents](../platform/agent-windows.md) en [verbonden via Operations Manager](../platform/om-agents.md).

Voor de functionaliteit van de ExpressRoute-monitor moeten de Azure-knoop punten alleen als directe agents zijn verbonden. Azure-knoop punten, die zijn verbonden via Operations Manager worden niet ondersteund. Voor on-premises knoop punten worden de knoop punten die zijn verbonden als directe agents en via Operations Manager ondersteund voor het bewaken van een ExpressRoute-circuit.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Welk protocol onder TCP en ICMP moet worden gekozen voor bewaking?
Als u uw netwerk bewaakt met behulp van knoop punten op basis van Windows Server, raden we u aan TCP als controle protocol te gebruiken, omdat dit een betere nauw keurigheid biedt. 

ICMP wordt aanbevolen voor Windows-Desk tops/client besturingssystemen op basis van het besturings systeem. Dit platform staat niet toe dat TCP-gegevens worden verzonden via onbewerkte sockets, die door NPM worden gebruikt om de netwerk topologie te detecteren.

U kunt [hier](./network-performance-monitor-performance-monitor.md#choose-the-protocol)meer informatie vinden over de relatieve voor delen van elk protocol.

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hoe kan ik een knoop punt configureren voor de ondersteuning van bewaking met behulp van het TCP-protocol?
Voor het knoop punt voor de ondersteuning van bewaking met het TCP-protocol: 
* Zorg ervoor dat het knoop punt platform Windows Server is (2008 SP1 of hoger).
* Voer [EnableRules.ps1](https://aka.ms/npmpowershellscript) Power shell-script uit op het knoop punt. Zie de [instructies](./network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) voor meer informatie.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hoe kan ik de TCP-poort die wordt gebruikt door NPM wijzigen voor bewaking?
U kunt de TCP-poort die door NPM wordt gebruikt voor bewaking wijzigen door het [EnableRules.ps1](https://aka.ms/npmpowershellscript) script uit te voeren. U moet het poort nummer invoeren dat u wilt gebruiken als para meter. Als u bijvoorbeeld TCP wilt inschakelen op poort 8060, voert u uit `EnableRules.ps1 8060` . Zorg ervoor dat u dezelfde TCP-poort gebruikt op alle knoop punten die worden gebruikt voor de bewaking.

Het script configureert alleen Windows Firewall lokaal. Als u een netwerk firewall of NSG-regels (netwerk beveiligings groep) hebt, moet u ervoor zorgen dat het verkeer dat bestemd is voor de TCP-poort die wordt gebruikt door NPM wordt toegestaan.

### <a name="how-many-agents-should-i-use"></a>Hoeveel agents moet ik gebruiken?
U moet ten minste één agent gebruiken voor elk subnet dat u wilt bewaken.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Wat is het maximum aantal agents dat ik kan gebruiken of zie fout '... ' u hebt de configuratie limiet bereikt "?
NPM beperkt het aantal Ip's tot 5000 Ip's per werk ruimte. Als een knoop punt zowel IPv4-als IPv6-adressen heeft, worden de twee Ip's voor dat knoop punt geteld. Deze limiet van 5000 Ip's zou daarom de bovengrens bepalen van het aantal agents. U kunt het tabblad inactieve agents verwijderen van knoop punten in NPM >> configureren. NPM houdt ook geschiedenis bij van alle IP-adressen die ooit zijn toegewezen aan de virtuele machine die als host fungeert voor de agent en die elke worden beschouwd als een afzonderlijk IP-adres dat bijdragen aan de maximum limiet van 5000 Ip's. Om IP-adressen voor uw werk ruimte vrij te maken, kunt u de pagina knoop punten gebruiken om de IP-adressen te verwijderen die niet in gebruik zijn.

## <a name="monitoring"></a>Bewaking

### <a name="how-are-loss-and-latency-calculated"></a>Hoe worden verlies en latentie berekend?
Bron agenten verzenden TCP-SYN-aanvragen (als TCP is gekozen als protocol voor bewaking) of ICMP-ECHO aanvragen (als ICMP is gekozen als het protocol voor bewaking) tot doel-IP met regel matige tussen pozen om ervoor te zorgen dat alle paden tussen de IP-combi natie van de bron doel worden gedekt. Het percentage ontvangen pakketten en de round-trip tijd van het pakket wordt gemeten om het verlies en de latentie van elk pad te berekenen. Deze gegevens worden geaggregeerd over het polling-interval en over alle paden om de geaggregeerde waarden van verlies en latentie voor de IP-combi natie voor het betreffende polling-interval op te halen.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Met welke frequentie verzendt de bron agent pakketten naar de bestemming voor bewaking?
Voor de monitor mogelijkheden prestatie meter en ExpressRoute wordt de bron pakketten elke vijf seconden verzonden en worden de netwerk metingen vastgelegd. Deze gegevens worden samengevoegd over een polling-interval van drie minuten om de gemiddelde en piek waarden van verlies en latentie te berekenen. Voor de functionaliteit van de service connectiviteits monitor wordt de frequentie van het verzenden van de pakketten voor netwerk meting bepaald door de frequentie die de gebruiker voor de specifieke test heeft opgegeven tijdens het configureren van de test.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hoeveel pakketten worden er voor bewaking verzonden?
Het aantal pakketten dat door de bron agent naar het doel in een polling is verzonden, is adaptief en wordt bepaald door ons eigen algoritme, dat kan verschillen voor verschillende netwerk topologieën. Meer het aantal netwerk paden tussen de combi natie van de bron doel-IP, meer is het aantal pakketten dat wordt verzonden. Het systeem zorgt ervoor dat alle paden tussen de combi natie van de bron-IP-doel combinatie worden gedekt.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hoe detecteert NPM de netwerk topologie tussen de bron en de bestemming?
NPM maakt gebruik van een eigen algoritme op basis van traceroute om alle paden en hops tussen de bron en de bestemming te detecteren.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Biedt NPM informatie over route ring en scha kelen 
Hoewel NPM alle mogelijke routes tussen de bron agent en de bestemming detecteert, biedt deze geen inzicht in welke route werd gebruikt door de pakketten die worden verzonden door uw specifieke workloads. De oplossing kan u helpen de paden en onderliggende netwerk-hops te identificeren, waardoor er meer latentie wordt toegevoegd dan u verwacht.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Waarom zijn sommige paden beschadigd?
Verschillende netwerk paden kunnen bestaan tussen de bron-en doel-IP-adressen en elk pad kan een andere waarde voor verlies en latentie hebben. NPM markeert deze paden als slecht (aangeduid met een rode kleur) waarvoor de waarden van verlies en/of latentie groter zijn dan de respectieve drempel waarde die is ingesteld in de controle configuratie.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Wat geeft een sprong in kleur rood aan in de netwerk topologie kaart?
Als een hop rood is, betekent dit dat deze deel uitmaakt van ten minste één beschadigd pad. NPM markeert alleen de paden als beschadigd, de status van elk pad wordt niet gescheiden. Als u de lastige-hops wilt identificeren, kunt u de latentie van hop-per-hop bekijken en de waarden die meer dan de verwachte latentie toevoegen door elkaar scheiden.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hoe werkt fout lokalisatie in prestatie meter?
NPM maakt gebruik van een Probabilistic-mechanisme om fout kansen toe te wijzen aan elk netwerkpad, netwerk segment en de samenstellende netwerk hops op basis van het aantal beschadigde paden waarvan ze deel uitmaken. Naarmate de netwerk segmenten en-hops deel uitmaken van meer aantal beschadigde paden, nemen de fout waarschijnlijke fouten toe. Dit algoritme werkt het beste wanneer u een groot aantal knoop punten met NPM-agent hebt verbonden, omdat hierdoor de gegevens punten voor het berekenen van de fout kansen worden verhoogd.

### <a name="how-can-i-create-alerts-in-npm"></a>Hoe kan ik waarschuwingen in NPM maken?
Op dit moment wordt het maken van waarschuwingen van de NPM-gebruikers interface mislukt als gevolg van een bekend probleem. [Maak waarschuwingen hand matig](../platform/alerts-log.md).

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Wat zijn de standaard Log Analytics query's voor waarschuwingen
Query prestatie meter

```kusto
NetworkMonitoring
 | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
```

Query voor service connectiviteits controle

```kusto
NetworkMonitoring
 | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
```

ExpressRoute-controle query's: circuits query

```kusto
NetworkMonitoring
 | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"
```

Persoonlijke peering

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"
```

Microsoft-peering

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"
```

Algemene query

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy")
```

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Kunnen NPM-routers en-servers als afzonderlijke apparaten worden bewaakt?
NPM identificeert alleen de IP-en hostnaam van de onderliggende netwerk-hops (switches, routers, servers, enzovoort) tussen de bron-en doel-IP-adressen. Ook wordt de latentie tussen deze geïdentificeerde hops geïdentificeerd. Deze onderliggende hops worden niet afzonderlijk bewaakt.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Kan NPM worden gebruikt voor het bewaken van de netwerk verbinding tussen Azure en AWS?
Ja. Raadpleeg het artikel [Azure, AWS en on-premises netwerken bewaken met behulp van NPM](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor) voor meer informatie.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Is het ExpressRoute-bandbreedte gebruik binnenkomend of uitgaand?
Bandbreedte gebruik is het totale aantal binnenkomende en uitgaande band breedte. De waarde wordt uitgedrukt in bits per seconde.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Kan er informatie over binnenkomende en uitgaande band breedte worden ontvangen voor de ExpressRoute?
Binnenkomende en uitgaande waarden voor zowel de primaire als de secundaire band breedte kunnen worden vastgelegd.

Voor informatie over het niveau van MS-peering gebruikt u de onderstaande query in zoeken in Logboeken

```kusto
NetworkMonitoring
 | where SubType == "ERMSPeeringUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond 
```

Voor informatie over privé-peering kunt u de onderstaande query gebruiken in zoeken in Logboeken

```kusto
NetworkMonitoring
 | where SubType == "ERVNetConnectionUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond
```

Voor informatie over het circuit niveau gebruikt u de onderstaande query in zoeken in Logboeken

```kusto
NetworkMonitoring
 | where SubType == "ERCircuitTotalUtilization"
 | project CircuitName, BitsInPerSecond, BitsOutPerSecond
```

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Welke regio's worden ondersteund voor de prestatie meter van de NPM?
NPM kan de connectiviteit van netwerken in een deel van de wereld bewaken, van een werk ruimte die wordt gehost in een van de [ondersteunde regio's](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Welke regio's worden ondersteund voor de service connectiviteits monitor van NPM?
NPM kan de connectiviteit van services in elk deel van de wereld bewaken, van een werk ruimte die wordt gehost in een van de [ondersteunde regio's](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Welke regio's worden ondersteund voor de ExpressRoute-monitor van NPM?
Met NPM kunt u uw ExpressRoute-circuits bewaken die zich in een Azure-regio bevinden. Voor een onboarding naar NPM hebt u een Log Analytics-werk ruimte nodig die moet worden gehost in een van de [ondersteunde regio's](../../expressroute/how-to-npm.md)

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Waarom zijn sommige hops gemarkeerd als niet-geïdentificeerd in de weer gave netwerk topologie?
NPM maakt gebruik van een gewijzigde versie van traceroute om de topologie van de bron agent naar de bestemming te ontdekken. Een niet-geïdentificeerde hop geeft aan dat de netwerk-hop niet heeft gereageerd op de traceroute-aanvraag van de bron agent. Als drie opeenvolgende netwerk hops niet reageren op de traceroute van de agent, markeert de oplossing de niet-reagerende hops als niet-geïdentificeerd en wordt er niet meer hops gedetecteerd.

Een hop reageert mogelijk niet op een traceroute in een of meer van de onderstaande scenario's:

* De routers zijn zodanig geconfigureerd dat hun identiteit niet wordt onthuld.
* De netwerk apparaten mogen geen ICMP_TTL_EXCEEDED verkeer toestaan.
* De ICMP_TTL_EXCEEDED reactie van het netwerk apparaat wordt geblokkeerd door een firewall.

Wanneer een van de eind punten zich in azure bevindt, geeft traceroute niet-geïdentificeerde hops weer als Azure-infra structuur de identiteit niet onthult naar traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Er worden waarschuwingen weer geven voor slechte testen, maar de hoge waarden in de grafiek met verlies en latentie van NPM zijn niet zichtbaar. Hoe kan ik controleren wat is beschadigd?
NPM treedt een waarschuwing als de eind-en eind latentie tussen de bron en het doel de drempel waarde voor een wille keurig pad ertussen overschrijdt. Sommige netwerken hebben meerdere paden waarmee dezelfde bron en bestemming worden verbonden. NPM genereert een waarschuwing dat een pad een onjuiste status heeft. Het verlies en de latentie die in de grafieken worden weer gegeven, is de gemiddelde waarde voor alle paden. Daarom kan de waarde van één pad niet exact worden weer gegeven. Zoek naar de kolom subtype in de waarschuwing om te begrijpen waar de drempel is geschonden. Als het probleem wordt veroorzaakt door een pad, wordt de waarde van subtype NetworkPath (voor test tests van prestaties), EndpointPath (voor de tests van de verbindings monitor van de service) en ExpressRoutePath (voor ExpressRotue-monitor tests). 

De voorbeeld query die u wilt zoeken, is beschadigd:

```kusto
NetworkMonitoring
 | where ( SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
 | project SubType, LossHealthState, LatencyHealthState, MedianLatency
```

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Waarom laat mijn test beschadigd, maar de topologie niet 
NPM bewaakt end-to-end-verlies, latentie en topologie met verschillende intervallen. Verlies en latentie worden elke vijf seconden gemeten en om de drie minuten geaggregeerd (voor prestatie meter en Express route monitor) terwijl de topologie elke 10 minuten wordt berekend met traceroute. Bijvoorbeeld tussen 3:44 en 4:04 kan de topologie drie keer worden bijgewerkt (3:44, 3:54, 4:04), maar verlies en latentie worden bijgewerkt ongeveer zeven keer (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). De topologie die wordt gegenereerd bij 3:54 wordt weer gegeven voor het verlies en de latentie die worden berekend op 3:56, 3:59 en 4:02. Stel dat u een waarschuwing krijgt dat het circuit van de nood herstel is beschadigd op 3:59. U meldt zich aan bij NPM en probeert de topologie tijd in te stellen op 3:59. NPM geeft de topologie weer die is gegenereerd op 3:54. Als u inzicht wilt krijgen in de laatste bekende topologie van uw netwerk, vergelijkt u de velden TimeProcessed (tijd waarop verlies en latentie is berekend) en TracerouteCompletedTime (tijd waarop de topologie is berekend). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Wat is het verschil tussen de velden E2EMedianLatency en AvgHopLatencyList in de tabel NetworkMonitoring
E2EMedianLatency is de latentie dat elke drie minuten na het samen voegen van de resultaten van TCP-ping-tests is bijgewerkt, terwijl AvgHopLatencyList elke 10 minuten wordt bijgewerkt op basis van traceroute. Gebruik het veld TimeProcessed om inzicht te krijgen in de exacte tijd waarop E2EMedianLatency is berekend. Gebruik het veld TracerouteCompletedTime om inzicht te krijgen in de exacte tijd waarop traceroute is voltooid en bijgewerkt AvgHopLatencyList.

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Waarom verschilt de latentie van hop-by-hop van HopLatencyValues 
HopLatencyValues zijn bron naar eind punt.
Bijvoorbeeld: hops-A, B, C. AvgHopLatency-10, 15, 20. Dit betekent bron tot een latentie = 10, bron naar B latentie = 15 en de bron-naar C-latentie is 20. In de gebruikers interface wordt een hop-z-latentie berekend als 5 in de topologie

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>De oplossing toont 100% verlies, maar er is een verbinding tussen de bron en het doel
Dit kan gebeuren als de host-firewall of de tussenliggende firewall (netwerk firewall of Azure NSG) de communicatie tussen de bron agent en de bestemming blokkeert via de poort die wordt gebruikt voor bewaking door NPM (de poort is standaard 8084, tenzij deze door de klant is gewijzigd).

* Als u wilt controleren of de host-firewall de communicatie op de vereiste poort niet blokkeert, bekijkt u de status van de bron-en doel knooppunten in de volgende weer gave: Netwerkprestatiemeter-> configuratie-> knoop punten. 
  Als ze niet in orde zijn, raadpleegt u de instructies en neemt u corrigerende maat regelen. Als de knoop punten in orde zijn, gaat u naar stap b. hieronder.
* Als u wilt controleren of een tussenliggende netwerk firewall of Azure NSG de communicatie op de vereiste poort niet blokkeert, gebruikt u het PsPing-hulp programma van derden met behulp van de onderstaande instructies:
  * het hulp programma psping kan [hier](/sysinternals/downloads/psping) worden gedownload 
  * Voer de volgende opdracht uit vanaf het bron knooppunt.
    * psping-n 15 \<destination node IPAddress\> :P ortnumber gebruikt standaard NPM 8084-poort. Als u dit expliciet hebt gewijzigd met behulp van het EnableRules.ps1 script, voert u het aangepaste poort nummer in dat u gebruikt. Dit is een ping van Azure machine naar on-premises
* Controleer of de pings zijn geslaagd. Als dat niet het geval is, duidt dit erop dat een tussenliggende netwerk firewall of Azure NSG het verkeer op deze poort blokkeert.
* Voer nu de opdracht uit vanaf het doel knooppunt naar het IP-adres van het bron knooppunt.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Er is geen verlies van knoop punt A naar B, maar niet van knoop punt B naar A. waarom?
Aangezien de netwerk paden tussen A en B kunnen verschillen van de netwerk paden tussen B en A, kunnen verschillende waarden voor verlies en latentie worden waargenomen.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Waarom worden al mijn ExpressRoute-circuits en peering-verbindingen niet gedetecteerd?
NPM detecteert nu ExpressRoute-circuits en peering verbindingen in alle abonnementen waartoe de gebruiker toegang heeft. Kies alle abonnementen waar uw Express-route resources zijn gekoppeld en schakel bewaking in voor elke gedetecteerde resource. NPM zoekt naar verbindings objecten bij het detecteren van een persoonlijke peering, dus controleer of er een VNET is gekoppeld aan uw peering. NPM detecteert geen circuits en peering die zich in een andere Tenant bevinden vanuit de werk ruimte Log Analytics.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>De functie voor het controleren van de monitor heeft een diagnostisch bericht ' verkeer wordt niet door gegeven via een circuit '. Wat betekent dat?

Er kan een scenario zijn met een goede verbinding tussen de on-premises en Azure-knoop punten, maar het verkeer gaat niet over het ExpressRoute-circuit dat is geconfigureerd om te worden bewaakt door NPM. 

Dit kan gebeuren als:

* Het circuit is niet beschikbaar.
* De route filters worden zo geconfigureerd dat ze prioriteit geven aan andere routes (zoals een VPN-verbinding of een ander ExpressRoute-circuit) over het beoogde ExpressRoute-circuit. 
* De on-premises en Azure-knoop punten die zijn gekozen voor het bewaken van het ExpressRoute-circuit in de bewakings configuratie, hebben geen verbinding met elkaar via het beoogde ExpressRoute-circuit. Zorg ervoor dat u de juiste knoop punten hebt gekozen die met elkaar zijn verbonden via het ExpressRoute-circuit dat u wilt bewaken.

### <a name="why-does-expressroute-monitor-report-my-circuitpeering-as-unhealthy-when-it-is-available-and-passing-data"></a>Waarom controleert ExpressRoute mijn circuit/peering als beschadigd wanneer deze beschikbaar zijn en gegevens door gegeven.
De monitor ExpressRoute vergelijkt de netwerk prestatie waarden (verlies, latentie en bandbreedte gebruik) die worden gerapporteerd door de agents/service met de drempels die tijdens de configuratie zijn ingesteld. Als de gerapporteerde bandbreedte bezetting groter is dan de drempel waarde die is ingesteld in de configuratie, wordt het circuit gemarkeerd als beschadigd. Als het verlies, de latentie of de gebruikte bandbreedte benutting groter is dan de drempel waarde die in de configuratie is ingesteld, wordt de peering als beschadigd gemarkeerd. NPM maakt geen gebruik van metrische gegevens of enige andere vorm van data voor deicde-status.

### <a name="why-does-expressroute-monitorbandwidth-utilisation-report-a-value-differrent-from-metrics-bits-inout"></a>Waarom rapporteert ExpressRoute Monitor'bandwidth-gebruik een waarde andere van metrische bits in/uit
Voor ExpressRoute monitor is band breedte utiliation het gemiddelde van de inkomende en uitgaande band breedte in de afgelopen 20 minuten. deze worden uitgedrukt in bits per seconde. Voor metrische gegevens van snelle route zijn bit-in-en uitgangs punten per minuut. De gegevensset die voor beide wordt gebruikt, is niet alleen hetzelfde, maar de aggregatie valies tussen NPM en de metrische gegevens. Voor gedetailleerde, minuut per minuut controleren en snelle waarschuwingen wordt u aangeraden waarschuwingen rechtstreeks in te stellen op basis van de maat eenheden.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Tijdens het configureren van de bewaking van mijn ExpressRoute-circuit worden de Azure-knoop punten niet gedetecteerd.
Dit kan gebeuren als de Azure-knoop punten zijn verbonden via Operations Manager. De ExpressRoute-monitor mogelijkheid biedt alleen ondersteuning voor Azure-knoop punten die zijn verbonden als rechtstreekse agents.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Ik kan geen ExpressRoute-circuits detecteren in de OMS-Portal
Hoewel NPM zowel vanuit de Azure Portal als met de OMS-Portal kan worden gebruikt, werkt de circuit detectie in de ExpressRoute-monitor mogelijk alleen via de Azure Portal. Zodra de circuits zijn gedetecteerd via de Azure Portal, kunt u de mogelijkheid gebruiken in een van de twee portals. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>In de functie voor het controleren van de service connectiviteit worden de reactie tijd van de service, het netwerk verlies en de latentie weer gegeven als N.V.T.
Dit kan gebeuren als er sprake is van een of meer waar is:

* De service is niet beschikbaar.
* Het knoop punt dat wordt gebruikt voor het controleren van de netwerk verbinding met de service is niet beschikbaar.
* Het doel dat is ingevoerd in de test configuratie is onjuist.
* Het knoop punt heeft geen netwerk verbinding.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>In de monitor van de service connectiviteit wordt een geldige reactie tijd van de service weer gegeven, maar netwerk verlies en latentie worden weer gegeven als N.V.T.
 Dit kan gebeuren als er sprake is van een of meer waar is:

* Als het knoop punt dat wordt gebruikt om de netwerk verbinding met de service te controleren, een Windows-client computer is, blokkeert de doel service ICMP-aanvragen of een netwerk firewall blokkeert ICMP-aanvragen die afkomstig zijn van het knoop punt.
* Het selectie vakje netwerk metingen uitvoeren is leeg in de test configuratie.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>In de monitor van de service connectiviteit is de reactie tijd van de service NA, maar netwerk verlies en latentie geldig
Dit kan gebeuren als de doel service geen webtoepassing is, maar de test is geconfigureerd als een Webtest. Bewerk de configuratie van de test en kies het test type netwerk in plaats van het web.

## <a name="miscellaneous"></a>Diversen

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Is er sprake van invloed op de prestaties van het knoop punt dat wordt gebruikt voor bewaking?
Het NPM-proces is zo geconfigureerd dat er wordt gestopt als er meer dan 5% van de CPU-bronnen van de host worden gebruikt. Dit is om ervoor te zorgen dat u de knoop punten voor hun gebruikelijke werk belastingen kunt blijven gebruiken zonder dat dit invloed heeft op de prestaties.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>NPM de firewall regels voor bewaking bewerken?
NPM maakt alleen een lokale Windows Firewall regel voor de knoop punten waarop het EnableRules.ps1 Power shell-script wordt uitgevoerd, zodat de agents TCP-verbindingen met elkaar kunnen maken op de opgegeven poort. De oplossing wijzigt geen NSG-regels (netwerk firewall of netwerk beveiligings groep).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hoe kan ik de status van de knoop punten controleren die worden gebruikt voor bewaking?
U kunt de status van de knoop punten die worden gebruikt voor bewaking, bekijken in de volgende weer gave: Netwerkprestatiemeter-> configuratie-> knoop punten. Als een knoop punt een slechte status heeft, kunt u de fout Details bekijken en de voorgestelde actie uitvoeren.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Kunnen latentie nummers in micro seconden NPMen?
NPM rondt de latentie nummers af in de gebruikers interface en in milliseconden. Dezelfde gegevens worden op een grotere granulariteit opgeslagen (soms Maxi maal vier decimalen).

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [Netwerkprestatiemeter-oplossing in azure](./network-performance-monitor.md)voor meer informatie over Netwerkprestatiemeter.

