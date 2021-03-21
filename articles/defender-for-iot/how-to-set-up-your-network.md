---
title: Uw netwerk instellen
description: Meer informatie over de architectuur van de oplossing, netwerk voorbereiding, vereisten en andere gegevens die nodig zijn om ervoor te zorgen dat uw netwerk is ingesteld voor gebruik met Azure Defender voor IoT-apparaten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0f85eebbfa8fcdfd9ad6e31a564f27b5d9bfbdfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733241"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Over de installatie van het Azure Defender for IoT-netwerk

Azure Defender voor IoT biedt voortdurende bewaking van Internet bedreigingen en detectie van apparaten. Het platform bevat de volgende onderdelen:

**Defender voor IOT Sens oren:** Sens oren verzamelen ICS-netwerk verkeer met behulp van passieve bewaking (zonder agent). Passieve en niet-werkende Sens oren hebben geen invloed op de prestaties van het OT en de netwerken en apparaten op IoT. De sensor maakt verbinding met een SPANNe-poort of netwerk Tik en begint direct met het controleren van uw netwerk. Detecties worden weer gegeven in de sensor console. Daar kunt u ze bekijken, onderzoeken en analyseren in een netwerk kaart, een inventarisatie van apparaten en een uitgebreid scala aan rapporten. Voor beelden zijn rapporten over risico analyse, query's voor gegevens analyse en aanvals vectoren. 

**Defender voor IOT on-premises beheer console**: de on-premises beheer console bevat een geconsolideerde weer gave van alle netwerk apparaten. Het biedt een real-time weer gave van Key OT-en IoT-risico indicatoren en waarschuwingen over al uw faciliteiten. Het is nauw geïntegreerd met uw SOC-werk stromen en playbooks en maakt het eenvoudig om de prioriteit van de oplossings activiteiten en de correlatie van bedreigingen op meerdere locaties te verbeteren. 

**Defender voor IOT-portal:** De app Defender voor IoT kan u helpen bij het kopen van oplossings apparaten, het installeren en bijwerken van software en het bijwerken van TI-pakketten. 

Dit artikel bevat informatie over de oplossings architectuur, netwerk voorbereiding, vereisten en meer om u te helpen uw netwerk zo in te stellen dat het werkt met Defender voor IoT-apparaten. Lezers die werken met de informatie in dit artikel, moeten ervaring hebben met het gebruik en beheer van netwerken van OT en IoT. Voor beelden zijn onder andere automatiserings engineers, plant aardige managers, OT met netwerk infrastructuur service providers, Cyber beveiliging teams, CISOs of Cio's.

Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)voor hulp of ondersteuning.

## <a name="on-site-deployment-tasks"></a>Implementatie taken op site

Site-implementatie taken zijn onder andere:

- [Site gegevens verzamelen](#collect-site-information)

- [Een configuratie werkstation voorbereiden](#prepare-a-configuration-workstation)

- [Rack installatie plannen](#planning-rack-installation)

### <a name="collect-site-information"></a>Site gegevens verzamelen

Site-informatie vastleggen, zoals:

- Informatie over sensor Management-netwerk.

- Site netwerk architectuur.

- Fysieke omgeving.

- Systeem integraties.

- Geplande gebruikers referenties.

- Configuratie werkstation.

- SSL-certificaten (optioneel, maar aanbevolen).

- SMTP-verificatie (optioneel). Als u de SMTP-server met verificatie wilt gebruiken, moet u de referenties voorbereiden die vereist zijn voor uw server.

- DNS-servers (optioneel). Bereid het IP-adres en de hostnaam van uw DNS-server voor.

Voor een gedetailleerde lijst en beschrijving van belang rijke informatie over de site raadpleegt u [voor beeld van site Book](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Geslaagde controle richtlijnen

U kunt het beste de volgende procedure volgen om de optimale plaats te vinden voor het aansluiten van het apparaat in elk van uw productie netwerken:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Diagram voorbeeld van de installatie van het productie netwerk.":::

### <a name="prepare-a-configuration-workstation"></a>Een configuratie werkstation voorbereiden

Bereid een Windows-werk station voor, met inbegrip van het volgende:

- Connectiviteit met de sensor beheer interface.

- Een ondersteunde browser

- Terminal software, zoals PuTTy.

Controleer of de vereiste firewall regels zijn geopend op het werk station. Zie [vereisten voor netwerk toegang](#network-access-requirements) voor meer informatie.

#### <a name="supported-browsers"></a>Ondersteunde browsers

De volgende browsers worden ondersteund voor de-webtoepassingen Sens oren en on-premises beheer console:

- Chrome 32 +

- Micro soft Edge 86 +

- Internet Explorer 10 +

### <a name="network-access-requirements"></a>Netwerk toegangs vereisten

Controleer of het beveiligings beleid van uw organisatie toegang biedt tot het volgende:

| Protocol | Transport | In/Out | Poort | Gebruikt | Doel | Bron | Doel |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | IN/UIT | 443 | De webconsole van de sensor en on-premises beheer console | Toegang tot de webconsole | Client | Sensor-en on-premises beheer console |
| SSH | TCP | IN/UIT | 22 | CLI | Toegang tot de CLI | Client | Sensor-en on-premises beheer console |
| SSL | TCP | IN/UIT | 443 | Sensor-en on-premises beheer console | Verbinding tussen Cyberx-platform en het centrale beheer platform | sensor | On-premises beheer console |
| NTP | UDP | IN | 123 | Tijd synchronisatie | On-premises beheer console gebruiken als NTP voor sensor | sensor | on-premises beheer console |
| NTP | UDP | IN/UIT | 123 | Tijd synchronisatie | Sensor verbonden met externe NTP-server, wanneer er geen on-premises beheer console is geïnstalleerd | sensor | NTP |
| SMTP | TCP | AF | 25 | E-mail | De verbinding tussen het Cyber-platform en het beheer platform en de e-mail server | Sensor-en on-premises beheer console | E-mail server |
| Syslog | UDP | AF | 514 | LEEF | Logboeken die vanuit de on-premises beheer console naar syslog-server worden verzonden | On-premises beheer console en sensor | Syslog-server |
| DNS |  | IN/UIT | 53 | DNS | DNS-server poort | On-premises beheer console en sensor | DNS-server |
| LDAP | TCP | IN/UIT | 389 | Active Directory | De verbinding tussen het Cyberx-platform en het beheer platform voor de Active Directory | On-premises beheer console en sensor | LDAP-server |
| LDAPS | TCP | IN/UIT | 636 | Active Directory | De verbinding tussen het Cyberx-platform en het beheer platform voor de Active Directory | On-premises beheer console en sensor | LDAPS-server |
| SNMP | UDP | AF | 161 | Bewaking | Externe SNMP-verzamelaars. | On-premises beheer console en sensor | SNMP-server |
| WMI | UDP | AF | 135 | bewaking | Controle van Windows-eind punten | Sensoren | Relevant netwerk element |
| Tunneling | TCP | IN | 9000 <br /><br />-boven op poort 443 <br /><br />Van de eind gebruiker naar de on-premises beheer console. <br /><br />-Poort 22 van sensor naar de on-premises beheer console  | bewaking | Tunneling | Sensoren | On-premises beheer console |

### <a name="planning-rack-installation"></a>Rack installatie plannen

Uw rack installatie plannen:

1. Bereid een monitor en een toetsen bord voor uw toestel netwerk instellingen voor.

1. Wijs de rack ruimte voor het apparaat toe.

1. Netstroom beschikbaar voor het apparaat.
1. Bereid de LAN-kabel voor om het beheer te verbinden met de netwerk switch.
1. Bereid de LAN-kabels voor op het aansluiten van Switch SPAN-poorten (mirror) en of netwerk kranen naar het apparaat Defender voor IoT. 
1. In de gespiegelde switches configureren, verbinding maken en valideren van SPANNe poorten zoals beschreven in de architectuur controle sessie.
1. Verbind de geconfigureerde bereik poort met een computer met wireshark en controleer of de poort correct is geconfigureerd.
1. Open alle relevante firewall poorten.

## <a name="about-passive-network-monitoring"></a>Over passieve netwerk bewaking

Het apparaat ontvangt verkeer van meerdere bronnen, door gespiegelde poorten (bereik poorten) of door netwerk kranen te wisselen. De beheer poort is verbonden met het bedrijfs-, bedrijfs-of sensor beheer netwerk met connectiviteit met een on-premises beheer console of de Defender voor IoT-Portal.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagram van een beheerde switch met poort spiegeling.":::

### <a name="purdue-model"></a>Purdue-model

In de volgende secties worden Purdue-niveaus beschreven.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagram van het Purdue-model.":::

####  <a name="level-0-cell-and-area"></a>Niveau 0: cel en gebied  

Niveau 0 bestaat uit een groot aantal Sens oren, actuators en apparaten die bij het basis productie proces betrokken zijn. Deze apparaten voeren de basis functies van het industrieel automatiserings-en controle systeem uit, zoals:

- Een motor rijden.

- Meet variabelen.
- Een uitvoer instellen.
- Het uitvoeren van belang rijke functies, zoals schilderen, lassen en buigen.

#### <a name="level-1-process-control"></a>Niveau 1: proces beheer

Niveau 1 bestaat uit Inge sloten controllers die het productie proces beheren en manipuleren waarvan de sleutel functie moet communiceren met het niveau 0-apparaten. In discrete productie zijn deze apparaten Programmeer bare logic controllers (PLCs) of externe telemetrie-eenheden (RTUs). In proces productie wordt de basis controller een gedistribueerd besturings systeem (DCS) genoemd.

#### <a name="level-2-supervisory"></a>Niveau 2: toezicht

Niveau 2 vertegenwoordigt de systemen en functies die zijn gekoppeld aan het runtime-toezicht en de werking van een gebied van een productie-faciliteit. Dit zijn doorgaans de volgende: 

- Operator interfaces of HMIs

- Alarm-of waarschuwings systemen

- Historian-en batch beheersystemen verwerken

- Control Room-werk stations

Deze systemen communiceren met de PLCs en RTUs in niveau 1. In sommige gevallen communiceren of delen gegevens met de site-of ENTER prise-systemen en-toepassingen (niveau 4 en niveau 5). Deze systemen zijn voornamelijk gebaseerd op standaard computing Equipment en besturings systemen (UNIX of micro soft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Niveau 3 en 3,5: site niveau en industrieel perimeter netwerk

Het site niveau vertegenwoordigt het hoogste niveau van industriële automatiserings-en besturings systemen. De systemen en toepassingen die op dit niveau bestaan, beheren industriële automatiserings-en beheer functies op de hele site. Niveaus 0 tot en met 3 worden beschouwd als kritiek voor site bewerkingen. De systemen en functies die op dit niveau bestaan, kunnen het volgende omvatten:

- Productie-rapporten (bijvoorbeeld cyclus tijden, kwaliteits index, voor speld onderhoud)

- Plant historian

- Gedetailleerde productie planning

- Beheer van bewerkingen op site niveau

- Beheer van apparaten en materialen

- Patch starten server

- Bestandsserver

- Industrieel domein, Active Directory, Terminal Server

Deze systemen communiceren met de productie zone en delen gegevens met de Enter prise-systemen en-toepassingen (Level 4 en niveau 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Niveau 4 en 5: bedrijfs-en bedrijfs netwerken

Niveau 4 en niveau 5 vertegenwoordigen het site-of bedrijfs netwerk waar de gecentraliseerde IT-systemen en-functies bestaan. De IT-afdeling beheert de services, systemen en toepassingen rechtstreeks op deze niveaus.

## <a name="planning-for-network-monitoring"></a>Planning voor netwerk bewaking

In de volgende voor beelden ziet u verschillende typen topologieën voor industriële beheer netwerken, samen met overwegingen voor optimale bewaking en plaatsing van Sens oren.

### <a name="what-should-be-monitored"></a>Wat moet er worden bewaakt?

Verkeer dat door lagen 1 en 2 gaat, moet worden bewaakt.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>Hoe moet de Defender voor IoT-apparaat verbinding maken?

Het apparaat Defender voor IoT moet verbinding maken met de beheerde switches die de industriële communicatie tussen lagen 1 en 2 zien (in sommige gevallen ook laag 3).

Het volgende diagram is een algemene abstractie van een multilaag, multi tenant netwerk, met een expansieve, Cyber beveiliging-ecosysteem dat doorgaans wordt geëxploiteerd door een SOC en MSSP.

Normaal gesp roken worden NTA Sens oren geïmplementeerd in lagen van 0 tot 3 van het OSI-model.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagram van het OSI-model.":::

#### <a name="example-ring-topology"></a>Voor beeld: ring topologie

Het Ring netwerk is een topologie waarin elke switch of elk knoop punt verbinding maakt met precies twee andere switches, waardoor één doorlopende route voor het verkeer wordt gevormd.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagram van de ring topologie.":::

#### <a name="example-linear-bus-and-star-topology"></a>Voor beeld: lineaire bus en ster topologie

In een Star netwerk is elke host verbonden met een centrale hub. In de meest eenvoudige vorm fungeert één centrale hub als een kanaal om berichten te verzenden. In het volgende voor beeld worden lagere switches niet bewaakt en blijven verkeer dat lokaal is voor deze switches niet zichtbaar. Apparaten kunnen worden geïdentificeerd op basis van ARP-berichten, maar er ontbreken verbindings gegevens.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagram van de lineaire bus en ster topologie.":::

#### <a name="multisensor-deployment"></a>Implementatie met meer Sens oren

Hier volgen enkele aanbevelingen voor het implementeren van meerdere Sens oren:

| **Number** | **Meet** | **Afhankelijkheid** | **Aantal Sens oren** |
|--|--|--|--|
| De maximale afstand tussen switches | 80 meters | Voor bereide Ethernet-kabel | Meer dan 1 |
| Aantal netwerken | Meer dan 1 | Geen fysieke verbinding | Meer dan 1 |
| Aantal switches | Kan RSPAN-configuratie gebruiken | Maxi maal acht switches met de lokale periode dicht bij de sensor door de afstand tussen kabels | Meer dan 1 |

#### <a name="traffic-mirroring"></a>Mirroring van verkeer  

Als u alleen relevante informatie voor verkeers analyse wilt zien, moet u het Defender voor IoT-platform verbinden met een mirroring poort op een switch of een tik die alleen industriële ICS en SCADA-verkeer bevat. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Gebruik deze optie voor uw installatie.":::

U kunt switch verkeer bewaken met behulp van de volgende methoden:

- [Switch-poort](#switch-span-port)

- [Externe periode (RSPAN)](#remote-span-rspan)

- [Tikken op actieve en passieve aggregatie](#active-and-passive-aggregation-tap)

SPAN en RSPAN zijn Cisco-terminologie. Andere merken van switches hebben vergelijk bare functionaliteit, maar kunnen gebruikmaken van verschillende terminologie.

#### <a name="switch-span-port"></a>Switch-poort

Een switch-poort analyse spiegelt lokaal verkeer van interfaces op de switch naar interface op dezelfde switch. Hier volgen enkele overwegingen:

- Controleer of de relevante switch ondersteuning biedt voor de functie poort spie gelen.  

- De optie mirroring is standaard uitgeschakeld.

- We raden u aan alle poorten van de switch te configureren, zelfs als er geen gegevens aan zijn gekoppeld. Als dat niet het geval is, kan een Rogue-apparaat zijn verbonden met een niet-bewaakte poort en wordt er niet op de sensor gewaarschuwd.

- In netwerken die gebruikmaken van broadcast-of multi cast-berichten, configureert u de switch zodanig dat er alleen maar RX (receive)-verzen dingen zijn. Anders worden multi cast-berichten herhaald voor evenveel actieve poorten en wordt de band breedte vermenigvuldigd.

De volgende configuratie voorbeelden zijn alleen ter referentie en zijn gebaseerd op een Cisco 2960-switch (24 poorten) met IOS. Dit zijn typische voor beelden, dus gebruik deze niet als instructies. Mirror-poorten op andere Cisco-besturings systemen en andere merken van switches zijn anders geconfigureerd.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagram van een configuratie terminal voor een bereik poort.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagram van de configuratie modus voor de bereik poort.":::

##### <a name="monitoring-multiple-vlans"></a>Meerdere VLAN'S bewaken

Met Defender voor IoT kunnen bewaakte VLAN'S in uw netwerk worden geconfigureerd. Er is geen configuratie van het Defender voor IoT-systeem vereist. De gebruiker moet ervoor zorgen dat de switch in uw netwerk is geconfigureerd voor het verzenden van VLAN-Tags naar Defender voor IoT.

In het volgende voor beeld ziet u de vereiste opdrachten die moeten worden geconfigureerd op de Cisco-switch om het controleren van VLAN'S in Defender voor IoT in te scha kelen:

**Sessie bewaken**: deze opdracht is verantwoordelijk voor het proces van het verzenden van vlan's naar de bereik poort.

- sessie 1 bewaken bron interface Gi1/2

- monitor sessie 1 filter pakket type goede RX

- Bewaak sessie 1 bestemmings interface fastEthernet1/1 encapsulation dot1q

De **trunk-poort F.E. Gi1/1 bewaken**: er worden vlan's geconfigureerd op de trunk-poort.

- Interface GigabitEthernet1/1

- switchport trunk encapsulation dot1q

- trunk van switchport-modus

#### <a name="remote-span-rspan"></a>Externe periode (RSPAN)

De sessie van de externe periode spiegelt verkeer van meerdere gedistribueerde bron poorten naar een toegewezen extern VLAN.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagram van de externe periode.":::

De gegevens in het VLAN worden vervolgens via getrunkde poorten over meerdere switches geleverd naar een specifieke switch die de fysieke doel poort bevat. Deze poort maakt verbinding met het Defender voor IoT-platform.  

##### <a name="more-about-rspan"></a>Meer informatie over RSPAN

- RSPAN is een geavanceerde functie die een speciaal VLAN nodig heeft om het verkeer tussen switches te laten lopen. RSPAN wordt niet ondersteund voor alle switches. Controleer of de switch ondersteuning biedt voor de functie RSPAN.

- De optie mirroring is standaard uitgeschakeld.

- Het externe VLAN moet worden toegestaan op de getrunkde poort tussen de bron-en doel switches.

- Alle switches die verbinding maken met dezelfde RSPAN-sessie, moeten van dezelfde leverancier zijn.

> [!NOTE]
> Zorg ervoor dat de trunk-poort die het externe VLAN deelt tussen de switches, niet is gedefinieerd als bron poort voor een mirror-sessie.
>
> Het externe VLAN verhoogt de band breedte op de getrunkde poort door de grootte van de band breedte van de gespiegelde sessie. Controleer of de trunk-poort van de switch dit ondersteunt.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagram van het externe VLAN.":::

#### <a name="rspan-configuration-examples"></a>Voor beelden van RSPAN-configuratie

RSPAN: gebaseerd op Cisco Catalyst 2960 (24 poorten).

**Configuratie van bron switch-voor beeld:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Scherm opname van RSPAN-configuratie.":::

1. Voer de globale configuratie modus in.

1. Maak een toegewezen VLAN.

1. Identificeer het VLAN als het RSPAN-VLAN.

1. Ga terug naar de modus ' Terminal configureren '.

1. Configureer alle 24 poorten als sessie bronnen.

1. Configureer het RSPAN-VLAN als de sessie bestemming.

1. Ga terug naar de modus privileged EXEC.

1. Controleer de configuratie van de poort spiegeling.

**Configuratie van doel switch-voor beeld:**

1. Voer de globale configuratie modus in.

1. Configureer het RSPAN-VLAN als de sessie bron.

1. Configureer fysieke poort 24 als de sessie bestemming.

1. Ga terug naar de modus privileged EXEC.

1. Controleer de configuratie van de poort spiegeling.

1. Sla de configuratie op.

#### <a name="active-and-passive-aggregation-tap"></a>Tikken op actieve en passieve aggregatie

Een actieve of passieve aggregatie Tik is geïnstalleerd inline naar de netwerk kabel. Het dupliceert zowel RX als TX naar de bewakings sensor.

Het Terminal toegangs punt (tik) is een hardwareapparaat waarmee netwerk verkeer van poort A naar poort B en van poort B naar poort A kan worden getransporteerd, zonder onderbreking. Hiermee maakt u een exacte kopie van beide zijden van de verkeers stroom, zonder dat dit de netwerk integriteit in gevaar brengt. Sommige kranen verzamelen en ontvangen verkeer met behulp van switch instellingen, indien gewenst. Als aggregatie niet wordt ondersteund, gebruikt elke tik twee sensor poorten voor het bewaken van het verzenden en ontvangen van verkeer.

Kranen kunnen om verschillende redenen worden benut. Ze zijn gebaseerd op hardware en kunnen niet worden aangetast. Ze slagen alle verkeer, zelfs beschadigde berichten, waardoor de switches vaak wegvallen. Ze zijn geen processor gevoelig, dus pakket timing is exact waar switches de spiegel functie als een taak met lage prioriteit afhandelen die van invloed kunnen zijn op de timing van de gespiegelde pakketten. Een tik is het beste apparaat voor forensische-doel einden.

Tik op aggregaties kan ook worden gebruikt voor poort bewaking. Deze apparaten zijn gebaseerd op processors en zijn niet zo intrinsiek beveiligd als hardware-tikken. Deze kunnen niet overeenkomen met de exacte pakket timing.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagram van actieve en passieve kranen.":::

##### <a name="common-tap-models"></a>Gemeen schappelijke TAP-modellen

Deze modellen zijn getest op compatibiliteit. Andere leveranciers en modellen zijn mogelijk ook compatibel.

| Installatiekopie | Model |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Scherm opname van Garland P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Scherm opname van IXIA TPA2-CU3."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Scherm afbeelding van US Robotics USR 4503."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Speciale TAP-configuratie

| Garland Tik | US Robotics tikken |
| ----------- | --------------- |
| Zorg ervoor dat jumpers als volgt zijn ingesteld:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Scherm opname van US Robotics-switch.":::| Zorg ervoor dat de aggregatie modus actief is. |

## <a name="deployment-validation"></a>Implementatie validatie

### <a name="engineering-self-review"></a>Zelf studie voor engineering  

Het weer geven van het netwerk diagram binnenkomend en ICS is de meest efficiënte manier om de beste plaats te definiëren waarmee verbinding moet worden gemaakt, waar u het meest relevante verkeer voor bewaking kunt ophalen.

De site-engineers weten hoe hun netwerk eruitziet. Als u een beoordelings sessie met het lokale netwerk hebt en operationele teams, worden doorgaans de verwachtingen verduidelijkt en wordt de beste plaats voor het aansluiten van het apparaat gedefinieerd.

Relevante informatie:

- Lijst met bekende apparaten (werk blad)  

- Geschat aantal apparaten  

- Leveranciers en industriële protocollen

- Model van switches om te controleren of de optie poort spie gelen beschikbaar is

- Informatie over wie de switches beheert (bijvoorbeeld IT) en of ze externe resources zijn

- Lijst met netwerken op de site

#### <a name="common-questions"></a>Veelgestelde vragen

- Wat zijn de algemene doelen van de implementatie? Is een volledige inventarisatie en nauw keurige netwerk kaart belang rijk?

- Zijn er meerdere of redundante netwerken in de ICS-hostcomputer? Worden alle netwerken bewaakt?

- Is er sprake van een communicatie tussen de ICS-en het Enter prise (bedrijfs) netwerk? Worden deze communicaties bewaakt?

- Zijn er VLAN'S geconfigureerd in het netwerk ontwerp?

- Hoe wordt het onderhoud van de ICS uitgevoerd, met vaste of tijdelijke apparaten?

- Waar worden firewalls in de bewaakte netwerken geïnstalleerd?

- Is er een route ring in de bewaakte netwerken?

- Welke protocollen zijn actief op de bewaakte netwerken?

- Als er verbinding wordt gemaakt met deze switch, zien we de communicatie tussen de HMI en de PLCs?

- Wat is de fysieke afstand tussen de ICS-switches en de firewall van de onderneming? 

- Kunnen onbeheerde switches worden vervangen door beheerde switches of is het gebruik van netwerk aftakkingen een optie?

- Is er een seriële communicatie in het netwerk? Zo ja, wordt het weer gegeven in het netwerk diagram.

- Als het apparaat Defender voor IoT moet worden aangesloten op die switch, is er dan fysieke beschik bare rack ruimte in dat cabinet?

#### <a name="other-considerations"></a>Andere overwegingen

Het doel van het apparaat Defender voor IoT is het bewaken van verkeer van lagen 1 en 2.

Voor sommige architecturen wordt in het Defender voor IoT-apparaat ook laag 3 bewaakt als er geen verkeer is op deze laag. Wanneer u de site-architectuur bekijkt en beslist of u een switch wilt bewaken, moet u rekening houden met de volgende variabelen:

- Wat is de kosten/vergoeding versus het belang van het bewaken van deze switch?

- Als een switch niet-beheerd is, is het mogelijk het verkeer van een switch op een hoger niveau te bewaken?

  Als de ICS-architectuur een ring topologie is, moet slechts één switch in deze ring worden bewaakt.

- Wat is het beveiligings-of operationeel risico in dit netwerk?

- Is het mogelijk om het VLAN van de switch te controleren? Is die VLAN zichtbaar in een andere switch die kan worden bewaakt?

#### <a name="technical-validation"></a>Technische validatie

Het ontvangen van een voor beeld van een opgenomen verkeer (PCAP-bestand) van de switch SPAN-of mirror-poort kan u helpen bij het volgende:

- Controleer of de switch juist is geconfigureerd.

- Controleer of het verkeer dat door de switch loopt, relevant is voor bewaking (OT).

- Bepaal de band breedte en het geschatte aantal apparaten in deze switch.

U kunt een voor beeld van een PCAP-bestand (enkele minuten) vastleggen door een laptop te verbinden met een al geconfigureerde bereik poort via de wireshark-toepassing.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Scherm afbeelding van een laptop die is verbonden met een bereik poort.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Scherm opname van de opname van een voor beeld van een PCAP-bestand.":::

#### <a name="wireshark-validation"></a>Wireshark validatie

- Controleer of de *unicast-pakketten* aanwezig zijn in het opname verkeer. Unicast is van het ene naar het andere adres. Als het grootste deel van het verkeer ARP-berichten is, is de installatie van de switch onjuist.

- Ga naar **Statistieken**  >  **protocol hiërarchie**. Controleer of de industriële protocollen aanwezig zijn.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Scherm opname van wireshark-validatie.":::

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik deze secties voor het oplossen van problemen:

- [Kan geen verbinding maken met behulp van een webinterface](#cant-connect-by-using-a-web-interface)

- [Apparaat reageert niet](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Kan geen verbinding maken met behulp van een webinterface

1. Controleer of de computer waarmee u verbinding probeert te maken, zich op hetzelfde netwerk bevindt als het apparaat.

2. Controleer of het GUI-netwerk is verbonden met de beheer poort op de sensor.

3. Ping het IP-adres van het apparaat. Als er geen reactie is op ping:

    1. Een monitor en een toetsen bord aansluiten op het apparaat.

    1. Gebruik de **ondersteunings** gebruiker en het wacht woord om u aan te melden.

    1. Gebruik de **lijst met netwerk** opdrachten om het huidige IP-adres weer te geven.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Scherm opname van de netwerk lijst opdracht.":::

4. Als de netwerk parameters onjuist zijn geconfigureerd, gebruikt u de volgende procedure om het te wijzigen:

    1. Gebruik de opdracht **netwerk instellingen**.

    1. Selecteer **Y** als u het IP-adres van het beheer netwerk wilt wijzigen.

    1. Selecteer **Y** om het subnetmasker te wijzigen.

    1. Selecteer **Y** om de DNS te wijzigen.

    1. Als u het standaard gateway-IP-adres wilt wijzigen, selecteert u **Y**.

    1. Selecteer **Y** voor de wijziging in de invoer interface (alleen voor sensors).

    1. Selecteer voor de bridge-interface **N**.

    1. Selecteer **Y** om de instellingen toe te passen.

5. Nadat u opnieuw hebt opgestart, maakt u verbinding met de gebruikers ondersteuning en gebruikt u de opdracht **netwerk lijst** om te controleren of de para meters zijn gewijzigd.

6. Probeer opnieuw te pingen en maak verbinding met de gebruikers interface.

### <a name="appliance-is-not-responding"></a>Apparaat reageert niet

1. Maak verbinding met een monitor en toetsen bord op het apparaat of gebruik PuTTy om extern verbinding te maken met de CLI.

2. Gebruik de ondersteunings referenties om u aan te melden.

3. Gebruik de opdracht **systeem Sanity** en controleer of alle processen worden uitgevoerd.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Scherm afbeelding van de opdracht systeem Sanity.":::

Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)voor eventuele andere problemen.

## <a name="example-site-book"></a>Voorbeeld site boek

Gebruik het voorbeeld site boek om belang rijke informatie die u nodig hebt voor het instellen van het netwerk op te halen en te controleren.

### <a name="site-checklist"></a>Controle lijst voor sites

Bekijk deze lijst vóór de site-implementatie:

| **#** | **Taak of activiteit** | **Status** | **Opmerkingen** |
|--|--|--|--|
| 1 | Order apparaten. | ☐ |  |
| 2 | Een lijst met subnetten voorbereiden in het netwerk. | ☐ |  |
| 3 | Geef een VLAN-lijst van de productie netwerken op. | ☐ |  |
| 4 | Geef een lijst van switch modellen op in het netwerk. | ☐ |  |
| 5 | Geef een lijst met leveranciers en protocollen van de industriële apparatuur op. | ☐ |  |
| 6 | Geef netwerk gegevens op voor Sens oren (IP-adres, subnet, D-GW, DNS). | ☐ |  |
| 7 | Maak de benodigde firewall regels en de toegangs lijst. | ☐ |  |
| 8 | Maak spanned poorten op switches voor poort bewaking of configureer de netwerk kranen naar wens. | ☐ |  |
| 9 | Rack ruimte voorbereiden voor sensor apparaten. | ☐ |  |
| 10 | Een werk station voorbereiden voor personeel. | ☐ |  |
| 11 | Geef een toetsen bord, monitor en muis op voor de Defender voor IoT-rack apparaten. | ☐ |  |
| 12 | De apparaten racken en aansluiten. | ☐ |  |
| 13 | Wijs site bronnen toe om de implementatie te ondersteunen. | ☐ |  |
| 14 | Maak Active Directory groepen of lokale gebruikers. | ☐ |  |
| 15 | Training instellen (zelf studie). | ☐ |  |
| 16 | Go of no-go. | ☐ |  |
| 17 | De implementatie datum plannen. | ☐ |  |


| **Datum** | **Opmerking** | **Implementatie datum** | **Opmerking** |
|--|--|--|--|
| Defender for IoT |  | Site naam * |  |
| Name |  | Name |  |
| Positie |  | Positie |  |

#### <a name="architecture-review"></a>Architectuur beoordeling

Een overzicht van het diagram van een industrieel netwerk stelt u in staat om de juiste locatie te definiëren voor de Defender voor IoT-apparatuur.

1.  Een wereld wijd netwerk diagram weer geven van de industriële OT-omgeving. Bijvoorbeeld:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagram van de industriële OT-omgeving voor het wereld wijde netwerk.":::

    > [!NOTE]
    > Het apparaat Defender voor IoT moet zijn verbonden met een switch op een lager niveau dat het verkeer tussen de poorten op de switch ziet.  

2. Geef het geschatte aantal netwerk apparaten op dat wordt bewaakt. U hebt deze informatie nodig wanneer u uw abonnement op de Azure Defender voor IoT-Portal wilt voorbereiden. Tijdens het voorbereidings proces wordt u gevraagd om het aantal apparaten in stappen van 1000 in te voeren.

3. Geef een lijst met subnetten voor de productie netwerken en een beschrijving (optioneel). 

    |  **#**  | **Subnetnaam** | **Beschrijving** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Geef een VLAN-lijst van de productie netwerken op.

    | **#** | **VLAN-naam** | **Beschrijving** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Als u wilt controleren of de switches mogelijkheden voor poort spiegeling hebben, geeft u de switch model nummers op waarmee de Defender voor IoT-platform verbinding moet maken:

    | **#** | **/Tijdnotatie** | **Model** | **Ondersteuning voor mirroring van verkeer (SPAN, RSPAN of none)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Beheert een derde partij de switches. J of N 

    Zo ja, wie? __________________________________ 

    Wat is het beleid? __________________________________ 

    Bijvoorbeeld:

    - Siemens

    - Rock well Automation-Ethernet of IP

    - Emerson – DeltaV, Ovation
    
6. Zijn er apparaten die communiceren via een seriële verbinding in het netwerk? Ja of nee 

    Zo ja, geeft u op welk serie communicatie protocol: ________________ 

    Zo ja, op het netwerk diagram markeren welke apparaten communiceren met seriële protocollen en waar ze zijn: 
 
    <Add your network diagram with marked serial connection> 

7. Voor QoS is de standaard instelling van de sensor 1,5 Mbps. Geef op of u de wijziging wilt aanbrengen: ________________ 

   Business Unit (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>Specificaties voor site-uitrusting

#### <a name="network"></a>Netwerk  

Het sensor apparaat is verbonden met switch-poort via een netwerk adapter. Deze is verbonden met het bedrijfs netwerk van de klant voor beheer via een andere specifieke netwerk adapter.

Geef adres gegevens op voor de sensor-NIC die wordt verbonden met het bedrijfs netwerk: 

|  Item               | Apparaat 1 | Apparaat 2 | Apparaat 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP-adres van apparaat    |               |               |               |
| Subnet          |               |               |               |
| Standaardgateway |               |               |               |
| DNS             |               |               |               |
| Hostnaam       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/server beheer

|       Item          | Apparaat 1 | Apparaat 2 | Apparaat 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP-adres van apparaat     |               |               |               |
| Subnet          |               |               |               |
| Standaardgateway |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>On-premises beheer console  

|       Item          | Actief | Passief (bij gebruik van HA) |
| --------------- | ------ | ----------------------- |
| IP-adres             |        |                         |
| Subnet          |        |                         |
| Standaardgateway |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Item              | Details |
| --------------- | ------ |
| IP              |        |
| IP-adres | |
| Gebruikersnaam | |
| Wachtwoord | |
| Verificatietype | MD5 of SHA |
| Versleuteling | DES of AES |
| Geheime sleutel | |
| SNMP v2-Community-teken reeks |

### <a name="on-premises-management-console-ssl-certificate"></a>SSL-certificaat van on-premises beheer console

Bent u van plan om een SSL-certificaat te gebruiken? Ja of nee

Zo ja, welke service gebruikt u om deze te genereren? Welke kenmerken neemt u op in het certificaat (bijvoorbeeld domein of IP-adres)?

### <a name="smtp-authentication"></a>SMTP-verificatie

Bent u van plan om SMTP te gebruiken om waarschuwingen door te sturen naar een e-mail server? Ja of nee

Zo ja, welke verificatie methode u dan ook gebruiken?  

### <a name="active-directory-or-local-users"></a>Active Directory of lokale gebruikers

Neem contact op met een Active Directory beheerder om een Active Directory site-gebruikers groep te maken of lokale gebruikers te maken. Zorg ervoor dat uw gebruikers klaar zijn voor de dag van de implementatie. 

### <a name="iot-device-types-in-the-network"></a>IoT-apparaattypen in het netwerk

| Apparaattype | Aantal apparaten in het netwerk | Gemiddelde band breedte |
| --------------- | ------ | ----------------------- |
| Camera | |
| Röntgen computer | |

## <a name="see-also"></a>Zie ook

[Over de installatie van Defender voor IoT](how-to-install-software.md)
