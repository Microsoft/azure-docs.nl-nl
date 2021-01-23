---
title: Referentie architecturen voor Oracle-data bases op Azure | Microsoft Docs
description: Verwijst naar architecturen voor het uitvoeren van Oracle Database Enterprise Edition-data bases op Microsoft Azure Virtual Machines.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: bbaf34c977546891c6ac05fbd4b5feb15f333e04
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737809"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referentie architecturen voor Oracle Database Enterprise Edition op Azure

Deze hand leiding bevat informatie over het implementeren van een Maxi maal beschik bare Oracle-data base in Azure. Daarnaast is deze hand leiding Dives in geval van nood herstel. Deze architecturen zijn gemaakt op basis van klant implementaties. Deze hand leiding is alleen van toepassing op Oracle Database Enterprise Edition.

Zie [architect a Oracle DB](oracle-design.md)als u meer wilt weten over het maximaliseren van de prestaties van uw Oracle-data base.

## <a name="assumptions"></a>Aannames

- U hebt een goed idee van de verschillende concepten van Azure, zoals [beschikbaarheids zones](../../../availability-zones/az-overview.md)
- U werkt Oracle Database Enterprise Edition 12c of hoger
- U bent op de hoogte en erkent de implicaties van de licenties bij het gebruik van de oplossingen in dit artikel

## <a name="high-availability-for-oracle-databases"></a>Hoge Beschik baarheid voor Oracle-data bases

Het bereiken van hoge Beschik baarheid in de Cloud is een belang rijk onderdeel van de planning en het ontwerp van elke organisatie. Microsoft Azure biedt [beschikbaarheids zones](../../../availability-zones/az-overview.md) en beschikbaarheids sets (worden gebruikt in regio's waar beschikbaarheids zones niet beschikbaar zijn). Lees meer over het [beheren van de beschik baarheid van uw virtuele machines](../../manage-availability.md) om te ontwerpen voor de Cloud.

Naast de Cloud-systeem eigen hulpprogram ma's en aanbiedingen biedt Oracle oplossingen voor hoge Beschik baarheid, zoals [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [Data Guard met FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)en [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) die op Azure kunnen worden ingesteld. Deze hand leiding bevat referentie architecturen voor elk van deze oplossingen.

Ten slotte is het belang rijk dat u bij het migreren of maken van toepassingen voor de Cloud de code van uw toepassing verfijnt om Cloud-eigen patronen toe te voegen, zoals het [patroon voor opnieuw proberen](/azure/architecture/patterns/retry) en de [circuit onderbreker](/azure/architecture/patterns/circuit-breaker). Aanvullende patronen die zijn gedefinieerd in de [hand leiding voor het ontwerpen van Clouds](/azure/architecture/patterns/) , kunnen u helpen bij het verbeteren van de toepassing.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC in de Cloud

Oracle Real Application Cluster (RAC) is een oplossing van Oracle waarmee klanten hoge door Voer kunnen bereiken door veel instanties die toegang hebben tot één database opslag (gedeeld patroon van alle architectuur). Hoewel Oracle RAC ook on-premises kan worden gebruikt voor hoge Beschik baarheid, kan Oracle RAC alleen worden gebruikt voor hoge Beschik baarheid in de Cloud omdat het alleen bescherming biedt tegen storingen op exemplaar niveau en niet tegen storingen op het niveau van het rek of het Data Center. Daarom raadt Oracle aan Oracle Data Guard te gebruiken met uw data base (of één exemplaar of RAC) voor hoge Beschik baarheid. Klanten hebben doorgaans een hoge SLA nodig voor het uitvoeren van hun essentiële toepassingen. Oracle RAC is momenteel niet gecertificeerd of wordt niet ondersteund door Oracle in Azure. Azure biedt echter functies als Azure biedt Beschikbaarheidszones en geplande onderhouds Vensters om te helpen beschermen tegen storingen op exemplaar niveau. Klanten kunnen daarnaast gebruikmaken van technologieën als Oracle Data Guard, Oracle Golden Gate en Oracle sharding voor hoge prestaties en meer flexibiliteit door hun data bases te beschermen tegen rack niveau en op datacenter niveau en geo-politieke fouten.

Bij het uitvoeren van Oracle-data bases in meerdere [beschikbaarheids zones](../../../availability-zones/az-overview.md) in combi natie met Oracle Data Guard of Golden Gate, kunnen klanten een sla voor de uptime van 99,99% ophalen. In azure-regio's waar beschikbaarheids zones nog niet aanwezig zijn, kunnen klanten [beschikbaarheids sets](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) gebruiken en een sla voor de uptime van 99,95% verzorgen.

>Opmerking: u kunt een doel voor de uptime hebben die veel hoger is dan de SLA voor de uptime van micro soft.

## <a name="disaster-recovery-for-oracle-databases"></a>Herstel na nood geval voor Oracle-data bases

Wanneer u uw essentiële toepassingen in de Cloud host, is het belang rijk om te ontwerpen voor hoge Beschik baarheid en herstel na nood gevallen.

Voor Oracle Database Enterprise Edition is Oracle Data Guard een handige functie voor herstel na nood gevallen. U kunt een standby-data base-exemplaar instellen in een [gekoppelde Azure-regio](../../../best-practices-availability-paired-regions.md) en failover voor gegevens beveiliging instellen voor herstel na nood gevallen. Als er geen gegevens verloren gaan, is het raadzaam om een Oracle Data Guard Far Sync-exemplaar te implementeren naast actieve Data Guard. 

U kunt overwegen om het Data Guard Far Sync-exemplaar in te stellen in een andere beschikbaarheids zone dan uw primaire Oracle-Data Base als uw toepassing de latentie toestaat (grondige tests zijn vereist). Gebruik een **maximale beschikbaarheids** modus voor het instellen van het synchrone Trans Port van uw opnieuw uitgevoerde bestanden naar de meest linkse Sync-instantie. Deze bestanden worden vervolgens asynchroon overgedragen naar de standby-data base. 

Als uw toepassing het prestatie verlies niet toestaat bij het instellen van een far Sync-exemplaar in een andere beschikbaarheids zone in de **maximale beschikbaarheids** modus (synchroon), kunt u een uiterst Sync-exemplaar instellen in dezelfde beschikbaarheids zone als uw primaire data base. Voor extra Beschik baarheid kunt u instellen dat er meerdere Sync-exemplaren worden gesloten die dicht bij de primaire data base liggen en ten minste één instantie dicht bij uw standby-data base (als de functie wordt overgezet). Meer informatie over de synchronisatie van Oracle Data Guard uiterst in deze [Oracle Active Data Guard Far Sync-technisch](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)document.

Wanneer u Oracle Standard Edition-data bases gebruikt, zijn er ISV-oplossingen, zoals DBVisit standby, waarmee u hoge Beschik baarheid en herstel na nood gevallen kunt instellen.

## <a name="reference-architectures"></a>Referentiearchitecturen

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle Data Guard zorgt voor hoge Beschik baarheid, gegevens bescherming en herstel na nood geval voor bedrijfs gegevens. Data Guard houdt stand-by-data bases als transactioneel consistente kopieën van de primaire data base. Afhankelijk van de afstand tussen de primaire en secundaire data bases en de toepassings tolerantie voor latentie, kunt u synchrone of asynchrone replicatie instellen. Als de primaire data base niet beschikbaar is vanwege een geplande of niet-geplande onderbreking, kan met Data Guard elke stand-by-Data Base worden overgeschakeld naar de primaire rol, waarbij de downtime wordt geminimaliseerd. 

Wanneer u Oracle Data Guard gebruikt, kunt u de secundaire data base ook openen voor alleen-lezen. Deze configuratie heet actieve Data Guard. Oracle Database 12c heeft een functie geïntroduceerd met de naam Data Guard uiterst Sync-exemplaar. Met dit exemplaar kunt u een configuratie met een gegevens verlies van nul instellen voor uw Oracle-data base zonder dat dit van invloed is op de prestaties.

> [!NOTE]
> Actieve Data Guard vereist extra licenties. Deze licentie is ook vereist voor het gebruik van de Far Sync-functie. Neem contact op met uw Oracle-vertegenwoordiger om de implicaties van de licenties te bespreken.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard met FSFO
Oracle Data Guard met Fast-Start failover (FSFO) kan extra flexibiliteit bieden door de broker in te stellen op een afzonderlijke machine. De Data Guard Broker en de secundaire data base voeren de waarnemer uit en bekijken de primaire Data Base voor uitval tijd. Hierdoor kunt u ook de installatie van uw Data Guard observeren. 

Met Oracle Database versie 12,2 en hoger is het ook mogelijk meerdere waarnemers te configureren met één Oracle Data Guard Broker-configuratie. Deze installatie biedt extra Beschik baarheid, in het geval van één waarnemer en de secundaire data base Ervaar tijd. Data Guard Broker is licht gewicht en kan worden gehost op een relatief kleine virtuele machine. Raadpleeg de [Oracle-documentatie](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) in dit onderwerp voor meer informatie over Data Guard Broker en de voor delen ervan.

Het volgende diagram is een aanbevolen architectuur voor het gebruik van Oracle Data Guard op Azure met beschikbaarheids zones. Met deze architectuur kunt u een SLA voor de VM-uptime van 99,99% ophalen.

![Diagram waarin een aanbevolen architectuur wordt weer gegeven voor het gebruik van Oracle Data Guard op Azure met beschikbaarheids zones.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

In het voor gaande diagram opent het client systeem een aangepaste toepassing met Oracle-back-end via het web. De web-front-end is geconfigureerd in een load balancer. Met de web-frontend wordt een aanroep uitgevoerd naar de juiste toepassings server om het werk af te handelen. De toepassings server voert een query uit op de primaire Oracle-data base. De Oracle-data base is geconfigureerd met een [virtuele machine](../../sizes-memory.md) met hyperthreaded geoptimaliseerd voor geheugen met [beperkte kern vcpu's](../../../virtual-machines/constrained-vcpu.md) om de licentie kosten op te slaan en de prestaties te maximaliseren. Er worden meerdere Premium-of Ultra schijven (Managed Disks) gebruikt voor prestaties en hoge Beschik baarheid.

De Oracle-data bases worden in meerdere beschikbaarheids zones geplaatst voor hoge Beschik baarheid. Elke zone bestaat uit een of meer data centers die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Ten minste drie afzonderlijke zones worden in alle ingeschakelde regio's ingesteld om tolerantie te garanderen. De fysieke schei ding van beschikbaarheids zones binnen een regio beveiligt de gegevens van fouten in data centers. Daarnaast zijn twee FSFO-waarnemers in twee beschikbaarheids zones ingesteld voor het initiëren en failoveren van de Data Base naar de secundaire als er een storing optreedt. 

U kunt extra waarnemers-en/of standby-data bases in een andere beschikbaarheids zone (AZ 1, in dit geval) instellen dan de zone die in de voor gaande architectuur wordt weer gegeven. Ten slotte worden Oracle-data bases bewaakt voor uptime en prestaties door Oracle Enter prise Manager (OEM). Met OEM kunt u ook verschillende prestatie-en gebruiks rapporten genereren.

In regio's waar beschikbaarheids zones niet worden ondersteund, kunt u beschikbaarheids sets gebruiken om uw Oracle Database op een Maxi maal beschik bare manier te implementeren. Met beschikbaarheids sets kunt u de uptime van een VM van 99,95% verzorgen. Het volgende diagram is een referentie architectuur van dit gebruik:

![Oracle Database beschikbaarheids sets gebruiken met Data Guard Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * De Oracle Enter prise Manager-VM hoeft niet in een beschikbaarheidsset te worden geplaatst, omdat er maar één exemplaar van de OEM wordt geïmplementeerd.
> * Ultra disks wordt momenteel niet ondersteund in een configuratie van een beschikbaarheidsset.

#### <a name="oracle-data-guard-far-sync"></a>Uiterst synchronisatie van Oracle Data Guard

De meest synchronisatie van Oracle Data Guard biedt een beschermings functie voor gegevens verlies voor Oracle-data bases. Met deze mogelijkheid kunt u zich beschermen tegen gegevens verlies in als uw database machine uitvalt. De meest synchronisatie van Oracle Data Guard moet worden geïnstalleerd op een afzonderlijke virtuele machine. Far Sync is een licht gewicht Oracle-exemplaar dat alleen een besturings bestand, een wachtwoord bestand, SPFile en stand-by-logboeken heeft. Er zijn geen gegevens bestanden of Rego-logboek bestanden. 

Voor de beveiliging van gegevens verlies moet er sprake zijn van synchrone communicatie tussen uw primaire data base en het Far Sync-exemplaar. Het Sync-exemplaar dat het meest synchroon wordt uitgevoerd, wordt op synchrone wijze opnieuw van de primaire en doorgestuurd naar alle data bases met de stand-by-modus op asynchrone wijze. Deze instelling vermindert ook de overhead van de primaire data base, omdat alleen de bewerking opnieuw moet worden verzonden naar het Far Sync-exemplaar in plaats van alle data bases met de stand-by. Als er sprake is van een far Sync-exemplaar, gebruikt Data Guard automatisch een asynchroon Trans Port naar de secundaire data base van de primaire data base om bijna geen gegevens verlies beveiliging te onderhouden. Voor extra tolerantie kunnen klanten meerdere Far Sync-instanties implementeren per data base-exemplaar (primaire en secundaire).

Het volgende diagram is een architectuur met hoge Beschik baarheid met een ver-synchronisatie van Oracle Data Guard:

![Oracle data base using Availability zones with Data Guard ver Sync & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

In de voor gaande architectuur is er een aanzienlijk synchronisatie-exemplaar geïmplementeerd in dezelfde beschikbaarheids zone als de data base-instantie om de latentie tussen de twee te verminderen. In gevallen waarin de toepassing latentie gevoelig is, kunt u overwegen om uw data base en de meest gesynchroniseerde instantie of instanties te implementeren in een [proximity-plaatsings groep](../../../virtual-machines/linux/proximity-placement-groups.md).

Het volgende diagram is een architectuur die gebruikmaakt van Oracle Data Guard FSFO en ver Sync om hoge Beschik baarheid en herstel na nood gevallen te garanderen:

![Oracle Database beschikbaarheids zones gebruiken voor herstel na nood gevallen met een far-synchronisatie van gegevens & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

Golden Gate maakt het mogelijk om gegevens op transactie niveau uit te wisselen en te bewerken via meerdere heterogene platformen in de hele onderneming. Er worden doorgevoerde trans acties met trans actie-integriteit en minimale overhead op uw bestaande infra structuur verplaatst. Dankzij de modulaire architectuur hebt u de flexibiliteit om geselecteerde gegevens records, transactionele wijzigingen en wijzigingen in DDL (Data Definition Language) uit verschillende topologieën op te halen en te repliceren.

Met Oracle Golden Gate kunt u uw data base configureren voor maximale Beschik baarheid door bidirectionele replicatie te bieden. Hiermee kunt u een configuratie met **meerdere masters** of **actief-actief** instellen. Het volgende diagram is een aanbevolen architectuur voor Oracle Golden Gate Active-Active-Setup op Azure. In de volgende architectuur is de Oracle-data base geconfigureerd met behulp van een [virtuele machine](../../sizes-memory.md) met hyperthreaded geoptimaliseerd voor geheugen met [beperkte kern vcpu's](../../../virtual-machines/constrained-vcpu.md) om de licentie kosten op te slaan en de prestaties te maximaliseren. Er worden meerdere Premium-of Ultra schijven (Managed disks) gebruikt voor prestaties en beschik baarheid.

![Oracle Database beschikbaarheids zones gebruiken met Data Guard Broker-FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Een vergelijk bare architectuur kan worden ingesteld met behulp van beschikbaarheids sets in regio's waar beschikbaarheids zones momenteel niet beschikbaar zijn.

Oracle Golden Gate heeft processen, zoals extract, pomp en replicatie, waarmee u uw gegevens asynchroon kunt repliceren van de ene Oracle-database server naar een andere. Met deze processen kunt u een bidirectionele replicatie instellen om te zorgen voor een hoge Beschik baarheid van uw data base als er sprake is van downtime van beschikbaarheids zone. In het voor gaande diagram wordt het uitpak proces uitgevoerd op dezelfde server als uw Oracle-data base, terwijl de gegevens pomp en de replicatie processen worden uitgevoerd op een afzonderlijke server in dezelfde beschikbaarheids zone. Het replicatie proces wordt gebruikt om gegevens van de data base in de andere beschikbaarheids zone te ontvangen en de gegevens op te slaan in de Oracle-data base in de beschikbaarheids zone. Op dezelfde manier verzendt het gegevens pomp proces gegevens die door het uitpak proces zijn geëxtraheerd naar het replicatie proces in de andere beschikbaarheids zone. 

Hoewel in het voor gaande architectuur diagram het proces voor gegevens pomp en replicatie wordt weer gegeven dat op een afzonderlijke server is geconfigureerd, kunt u alle Oracle Golden Gate-processen op dezelfde server instellen, op basis van de capaciteit en het gebruik van uw server. Raadpleeg altijd uw AWR-rapport en de metrische gegevens in azure om inzicht te krijgen in het gebruiks patroon van uw server.

Bij het instellen van Oracle Golden Gate bidirectionele replicatie in verschillende beschikbaarheids zones of verschillende regio's, is het belang rijk om ervoor te zorgen dat de latentie tussen de verschillende onderdelen acceptabel is voor uw toepassing. De latentie tussen beschikbaarheids zones en regio's kan variëren en is afhankelijk van meerdere factoren. Het is raadzaam om prestatie tests tussen uw toepassingslaag en uw database laag in verschillende beschikbaarheids zones en/of regio's in te stellen om te bevestigen dat deze voldoen aan de prestatie vereisten van uw toepassing.

De toepassingslaag kan worden ingesteld in een eigen subnet en de gegevenslaag kan worden gescheiden in een eigen subnet. Overweeg, indien mogelijk, om [Azure-toepassing gateway](../../../application-gateway/overview.md) te gebruiken om verkeer tussen uw toepassings servers te verdelen. Azure-toepassing gateway is een robuust webverkeer load balancer. Het biedt sessie affiniteit op basis van cookies die een gebruikers sessie op dezelfde server houdt, waardoor de conflicten voor de Data Base worden geminimaliseerd. Alternatieven voor Application Gateway zijn [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) en [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Oracle-sharding

Sharding is een gegevenslaag patroon dat is geïntroduceerd in Oracle 12,2. Zo kunt u uw gegevens Horizon taal partitioneren en schalen op onafhankelijke data bases. Het is een deel-niets-architectuur waarbij elke Data Base wordt gehost op een specifieke virtuele machine, waardoor een hoge lees-en schrijf doorvoer mogelijk is naast tolerantie en verhoogde Beschik baarheid. Met dit patroon worden individuele storings punten geëlimineerd, wordt fout isolatie geboden en worden rolling upgrades zonder uitval tijd ingeschakeld. De downtime van één Shard of een fout op het niveau van een Data Center heeft geen invloed op de prestaties of Beschik baarheid van de andere Shards in andere data centers. 

Sharding is geschikt voor OLTP-toepassingen met een hoge door Voer die geen uitval tijd kunnen veroorloven. Alle rijen met dezelfde sharding-sleutel zijn altijd gegarandeerd op dezelfde Shard, waardoor de prestaties worden verbeterd door de hoge consistentie te bieden. Toepassingen die gebruikmaken van sharding, moeten beschikken over een goed gedefinieerd gegevens model en een strategie voor gegevens distributie (consistente hash, bereik, lijst of samengestelde waarde) die primair toegang heeft tot gegevens met behulp van een sharding-sleutel (bijvoorbeeld *KlantId* of *accountNum*). Sharding biedt u ook de mogelijkheid om bepaalde gegevens sets dichter bij de eind gebruikers op te slaan, zodat u kunt voldoen aan de vereisten voor prestaties en naleving.

U wordt aangeraden uw Shards te repliceren voor hoge Beschik baarheid en herstel na nood gevallen. Deze installatie kan worden uitgevoerd met behulp van Oracle-technologieën zoals Oracle Data Guard of Oracle Golden Gate. Een replicatie-eenheid kan een Shard, een deel van een Shard of een groep Shards zijn. De beschik baarheid van een Shard-data base wordt niet beïnvloed door een storing of vertraging van een of meer Shards. Voor maximale Beschik baarheid kan de stand-Shards in dezelfde beschikbaarheids zone worden geplaatst waar de primaire Shards worden geplaatst. Voor herstel na nood gevallen kan de stand-Shards in een andere regio worden gevonden. U kunt ook Shards implementeren in meerdere regio's om verkeer in die regio's te verwerken. Lees meer over het configureren van hoge Beschik baarheid en replicatie van uw Shard-data base in [Oracle sharding-documentatie](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle sharding bestaat voornamelijk uit de volgende onderdelen. Meer informatie over deze onderdelen vindt u in de [documentatie van Oracle sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Shard Catalog** : de Oracle-Data Base voor speciale doel einden die een permanente opslag is voor alle configuratie gegevens van de Shard-data base. Alle configuratie wijzigingen, zoals het toevoegen of verwijderen van Shards, het toewijzen van de gegevens en het DDLs in een Shard-data base, worden gestart op de Shard catalogus. De Shard-catalogus bevat ook de hoofd kopie van alle gedupliceerde tabellen in een SDB. 

  De catalogus Shard maakt gebruik van gerealiseerde weer gaven voor het automatisch repliceren van wijzigingen in gedupliceerde tabellen in alle Shards. De catalogus database Shard fungeert ook als een query coördinator die wordt gebruikt voor het verwerken van query's en query's met meerdere Shard die geen sharding-sleutel opgeven. 
  
  Het gebruik van Oracle Data Guard in combi natie met beschikbaarheids zones of beschikbaarheids sets voor Shard-catalogus hoge Beschik baarheid is een aanbevolen best practice. De beschik baarheid van de Shard-catalogus heeft geen invloed op de beschik baarheid van de Shard-data base. Een downtime in de Shard-catalogus heeft alleen invloed op onderhouds bewerkingen en multishard query's gedurende de korte periode dat de Data Guard-failover is voltooid. Online transacties worden door de SDB gerouteerd en uitgevoerd en worden niet beïnvloed door een storing in de catalogus.

- **Shard-directeurs** -Lightweight services die moeten worden geïmplementeerd in elke regio/beschikbaarheids zone waarin uw Shards zich bevinden. Shard-Directors zijn wereld wijde service managers die zijn geïmplementeerd in de context van Oracle sharding. Voor maximale Beschik baarheid kunt u het beste ten minste één Shard-Director implementeren in elke beschikbaarheids zone waarin uw Shards zich bevinden. 

  Wanneer u in eerste instantie verbinding maakt met de data base, worden de routerings gegevens ingesteld door een Shard-Director en worden deze in de cache opgeslagen voor volgende aanvragen, waarbij de Shard Director wordt overgeslagen. Zodra de sessie tot stand is gebracht met een Shard, worden alle SQL-query's en Dml's ondersteund en uitgevoerd binnen het bereik van de opgegeven Shard. Deze route ring is snel en wordt gebruikt voor alle OLTP-workloads die trans acties binnen de Shard uitvoeren. Het is raadzaam om direct route ring te gebruiken voor alle OLTP-workloads die de hoogste prestaties en beschik baarheid vereisen. De routerings cache wordt automatisch vernieuwd wanneer een Shard niet meer beschikbaar is of wanneer er wijzigingen optreden in de sharding-topologie. 
  
  Voor hoogwaardige gegevensgestuurde route ring, raadt Oracle aan een verbindings groep te gebruiken bij het openen van gegevens in de Shard-data base. Oracle-verbindings Pools, taalspecifieke bibliotheken en stuur Programma's ondersteunen Oracle sharding. Raadpleeg de [documentatie van Oracle sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) voor meer informatie.

- **Globale service** : de wereld wijde service is vergelijkbaar met de normale database service. Naast alle eigenschappen van een database service heeft een globale service eigenschappen voor Shard-data bases, zoals regio affiniteit tussen clients en Shard en tolerantie voor replicatie vertraging. Er moet slechts één globale service worden gemaakt om gegevens van/naar een Shard-data base te lezen/schrijven. Bij het gebruik van Active Data Guard en het instellen van alleen-lezen replica's van de Shards, kunt u een andere gGobal-service maken voor alleen-lezen workloads. De client kan deze globale Services gebruiken om verbinding te maken met de data base.

- **Shard-data** bases-Shard-data bases zijn uw Oracle-data bases. Elke Data Base wordt met behulp van Oracle Data Guard gerepliceerd in een Broker-configuratie waarvoor Fast-Start failover (FSFO) is ingeschakeld. U hoeft geen Data Guard-failover en replicatie in te stellen op elke Shard. Dit wordt automatisch geconfigureerd en geïmplementeerd wanneer de gedeelde data base wordt gemaakt. Als een bepaalde Shard mislukt, wordt het delen van Oracle automatisch uitgevoerd via database verbindingen van de primaire naar de stand-by.

U kunt Oracle Shard-data bases met twee interfaces implementeren en beheren: Oracle Enter prise Manager-interface voor Cloud beheer en/of het `GDSCTL` opdracht regel programma. U kunt de verschillende Shards zelfs controleren op Beschik baarheid en prestaties met behulp van Cloud beheer. `GDSCTL DEPLOY`Met deze opdracht worden automatisch de Shards en hun respectieve listeners gemaakt. Bovendien implementeert deze opdracht automatisch de replicatie Configuratie die wordt gebruikt voor Shard hoge Beschik baarheid die is opgegeven door de beheerder.

Er zijn verschillende manieren om een Data Base te Shard:

* Door het systeem beheerde sharding: distribueert automatisch over Shards met behulp van partitionering
* Door de gebruiker gedefinieerde sharding: Hiermee kunt u de toewijzing van de gegevens opgeven aan de Shards, wat goed werkt wanneer er sprake is van regelgevende of gegevensgestuurde vereisten)
* Samengestelde sharding: een combi natie van door het systeem beheerde en door de gebruiker gedefinieerde sharding voor verschillende _shardspaces_
* Tabel subpartitionen: vergelijkbaar met een reguliere gepartitioneerde tabel.

Meer informatie over de verschillende [sharding-methoden](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) vindt u in de documentatie van Oracle.

Hoewel een Shard-data base eruit kan zien als één Data Base voor toepassingen en ontwikkel aars, moet er zorgvuldig worden gepland om te bepalen welke tabellen worden gedupliceerd versus Shard. 

Gedupliceerde tabellen worden opgeslagen op alle Shards, terwijl Shard-tabellen worden gedistribueerd over verschillende Shards. De aanbeveling is om kleine en dimensionale tabellen te dupliceren en de feiten tabellen te distribueren/Shard. Gegevens kunnen in uw Shard-Data Base worden geladen met behulp van de Shard-catalogus als de centrale coördinator of door de gegevens pomp op elke Shard uit te voeren. Lees meer informatie over het [migreren van gegevens naar een Shard-data base](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) in de documentatie van Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle-sharding met Data Guard

Oracle Data Guard kan worden gebruikt voor sharding met door het systeem beheerde, door de gebruiker gedefinieerde en samengestelde sharding-methoden.

Het volgende diagram is een referentie architectuur voor Oracle-sharding met Oracle-gegevens beveiliging die wordt gebruikt voor hoge Beschik baarheid van elke Shard. Het architectuur diagram toont een _samengestelde sharding-methode_. Het architectuur diagram wijkt waarschijnlijk af van toepassingen met verschillende vereisten voor gegevens locatie, taak verdeling, hoge Beschik baarheid, herstel na nood gevallen enzovoort en kan gebruikmaken van verschillende methoden voor sharding. Oracle sharding biedt u de mogelijkheid om aan deze vereisten te voldoen en horizon taal en efficiënt te schalen door deze opties te bieden. Een vergelijk bare architectuur kan zelfs worden geïmplementeerd met Oracle Golden Gate.

![Oracle Database sharding met beschik baarheids zones met Data Guard Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Een door het systeem beheerde sharding is het eenvoudigst te configureren en te beheren, door de gebruiker gedefinieerde sharding of samengestelde sharding is heel geschikt voor scenario's waarbij uw gegevens en toepassing geografisch worden gedistribueerd of in scenario's waarin u de controle over de replicatie van elke Shard moet hebben. 

In de voor gaande architectuur wordt samengestelde sharding gebruikt voor het geo-distribueren van de gegevens en het Horizon taal schalen van uw toepassings lagen. Samengestelde sharding is een combi natie van door het systeem beheerde en door de gebruiker gedefinieerde sharding en biedt daarom het voor deel van beide methoden. In het voor gaande scenario worden gegevens eerst Shard in meerdere shardspaces gescheiden door de regio. Vervolgens worden de gegevens verder gepartitioneerd door een consistente hash over meerdere Shards in de shardspace. Elk shardspace bevat meerdere shardgroups. Elke shardgroup heeft meerdere Shards en is in dit geval een ' eenheid ' van replicatie. Elk shardgroup bevat alle gegevens in de shardspace. Shardgroups a1 en B1 zijn primaire Shardgroups, terwijl Shardgroups a2 en B2 stand-by zijn. U kunt ervoor kiezen om afzonderlijke Shards te gebruiken als replicatie-eenheid in plaats van een shardgroup.

In de voor gaande architectuur wordt een GSM-Shard-Director geïmplementeerd in elke beschikbaarheids zone voor hoge Beschik baarheid. De aanbeveling is om ten minste één GSM/Shard Director per Data Center/regio te implementeren. Daarnaast wordt een exemplaar van de toepassings server geïmplementeerd in elke beschikbaarheids zone die een shardgroup bevat. Met deze instelling kan de toepassing de latentie tussen de toepassings server en de data base-shardgroup laag blijven. Als een Data Base mislukt, kan de toepassings server in dezelfde zone als de stand-by-data base aanvragen afhandelen nadat de databaserol is overgezet. Azure-toepassing gateway en de Shard-Director houden de aanvraag-en antwoord latentie bij en router aanvragen dienovereenkomstig.

Vanuit het oogpunt van een toepassing maakt het client systeem een aanvraag voor de Azure-toepassing-gateway (of andere technologieën voor taak verdeling in Azure) waarmee de aanvraag wordt omgeleid naar de regio die het dichtst bij de client is. Azure-toepassing gateway biedt ook ondersteuning voor plak sessies, zodat aanvragen die afkomstig zijn van dezelfde client naar dezelfde toepassings server worden doorgestuurd. De toepassings server maakt gebruik van groepsgewijze verbindingen in Data Access-Stuur Programma's. Deze functie is beschikbaar in Stuur Programma's zoals JDBC, ODP.NET, OCI, enzovoort. De Stuur Programma's kunnen sharding-sleutels herkennen die zijn opgegeven als onderdeel van de aanvraag. [Oracle universele verbindings groep (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) voor JDBC-clients kan niet-Oracle Application clients zoals Apache Tomcat en IIS inschakelen voor gebruik met Oracle sharding. 

Tijdens de eerste aanvraag verbindt de toepassings server met de Shard Director in de regio om routerings informatie te verkrijgen voor de Shard waarnaar de aanvraag moet worden doorgestuurd. Op basis van de door gegeven sharding-sleutel stuurt de Director de toepassings server door naar de respectieve Shard. De toepassings server slaat deze informatie op in het cache geheugen door een kaart te maken, en voor volgende aanvragen, wordt de Shard Director omzeild en worden aanvragen direct naar de Shard geleid.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle-sharding met Golden Gate

Het volgende diagram is een referentie architectuur voor Oracle sharding met Oracle Golden Gate voor de maximale Beschik baarheid van elke Shard in de regio. In plaats van de voor gaande architectuur heeft deze architectuur alleen portrays hoge Beschik baarheid binnen één Azure-regio (meerdere beschikbaarheids zones). Eén kan een Shard-data base met hoge Beschik baarheid van meerdere regio's implementeren (vergelijkbaar met het voor gaande voor beeld) met behulp van Oracle Golden Gate.

![Sharding Oracle Database met behulp van beschikbaarheids zones met Golden Gate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

De voor gaande referentie architectuur maakt gebruik van de door het _systeem beheerde_ sharding-methode voor het Shard van de gegevens. Omdat Oracle Golden Gate-replicatie op segment niveau wordt uitgevoerd, kunnen de helft van de gegevens die naar één Shard worden gerepliceerd, worden gerepliceerd naar een andere Shard. De andere helft kan worden gerepliceerd naar een andere Shard. 

De manier waarop de gegevens worden gerepliceerd, is afhankelijk van de replicatie factor. Met de replicatie factor 2 hebt u twee kopieën van elk gegevens segment in uw drie Shards in de shardgroup. Op dezelfde manier, met een replicatie factor van 3 en drie Shards in uw shardgroup, worden alle gegevens in elke Shard gerepliceerd naar elke andere Shard in de shardgroup. Elke Shard in de shardgroup kan een andere replicatie factor hebben. Met deze instelling kunt u het ontwerp voor hoge Beschik baarheid en herstel na nood gevallen op efficiënte wijze in een shardgroup en in meerdere shardgroups definiëren.

In de voor gaande architectuur bevatten shardgroup A en shardgroup B dezelfde gegevens, maar deze bevinden zich in verschillende beschikbaarheids zones. Als zowel shardgroup A als shardgroup B dezelfde replicatie factor van 3 hebben, wordt elke rij/segment van de Shard-tabel zes keer gerepliceerd over de twee shardgroups. Als shardgroup A een replicatie factor van 3 en shardgroup B met de replicatie factor 2 heeft, wordt elke rij/segment vijf keer gerepliceerd over de twee shardgroups.

Deze installatie voor komt gegevens verlies als er een fout optreedt op instantie niveau of op een niveau van een beschikbaarheids zone. De toepassingslaag kan lezen uit en schrijven naar elke Shard. Oracle sharding geeft voor elk bereik van hash-waarden een ' hoofd segment ' op om conflicten te minimaliseren. Deze functie zorgt ervoor dat schrijf aanvragen voor een bepaald segment worden omgeleid naar het bijbehorende segment. Daarnaast biedt Oracle Golden Gate automatische detectie van conflicten en oplossingen voor het afhandelen van conflicten die zich kunnen voordoen. Raadpleeg de documentatie van Oracle over het gebruik van [Oracle Golden Gate met een Shard-data base](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)voor meer informatie en beperkingen voor het implementeren van Golden Gate met Oracle sharding.

In de voor gaande architectuur wordt een GSM-Shard-Director geïmplementeerd in elke beschikbaarheids zone voor hoge Beschik baarheid. De aanbeveling is om ten minste één GSM/Shard Director per Data Center of regio te implementeren. Daarnaast wordt een exemplaar van de toepassings server geïmplementeerd in elke beschikbaarheids zone die een shardgroup bevat. Met deze instelling kan de toepassing de latentie tussen de toepassings server en de data base-shardgroup laag blijven. Als een Data Base mislukt, kan de toepassings server in dezelfde zone als de stand-by-data base aanvragen afhandelen nadat de database functie is overgegaan. Azure-toepassing gateway en de Shard-Director houden de aanvraag-en antwoord latentie bij en router aanvragen dienovereenkomstig.

Vanuit het oogpunt van een toepassing maakt het client systeem een aanvraag voor de Azure-toepassing-gateway (of andere technologieën voor taak verdeling in Azure) waarmee de aanvraag wordt omgeleid naar de regio die het dichtst bij de client is. Azure-toepassing gateway biedt ook ondersteuning voor plak sessies, zodat aanvragen die afkomstig zijn van dezelfde client naar dezelfde toepassings server worden doorgestuurd. De toepassings server maakt gebruik van groepsgewijze verbindingen in Data Access-Stuur Programma's. Deze functie is beschikbaar in Stuur Programma's zoals JDBC, ODP.NET, OCI, enzovoort. De Stuur Programma's kunnen sharding-sleutels herkennen die zijn opgegeven als onderdeel van de aanvraag. [Oracle universele verbindings groep (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) voor JDBC-clients kan niet-Oracle Application clients zoals Apache Tomcat en IIS inschakelen voor gebruik met Oracle sharding. 

Tijdens de eerste aanvraag verbindt de toepassings server met de Shard Director in de regio om routerings informatie te verkrijgen voor de Shard waarnaar de aanvraag moet worden doorgestuurd. Op basis van de door gegeven sharding-sleutel stuurt de Director de toepassings server door naar de respectieve Shard. De toepassings server slaat deze informatie op in het cache geheugen door een kaart te maken, en voor volgende aanvragen, wordt de Shard Director omzeild en worden aanvragen direct naar de Shard geleid.

## <a name="patching-and-maintenance"></a>Patches en onderhoud

Wanneer u uw Oracle-workloads op Azure implementeert, zorgt micro soft voor alle patches op het niveau van de host-OS. Elk gepland onderhoud op besturingssysteem niveau wordt vooraf aan klanten meegedeeld om de klant in staat te stellen voor dit geplande onderhoud. Twee servers van twee verschillende Beschikbaarheidszones worden nooit tegelijkertijd patches uitgevoerd. Zie [de beschik baarheid van virtuele machines beheren](../../manage-availability.md) voor meer informatie over het onderhoud en de reparatie van de VM. 

Het patchen van het besturings systeem van de virtuele machine kan worden geautomatiseerd met behulp van [Azure Automation updatebeheer](../../../automation/update-management/overview.md). Het patchen en onderhouden van uw Oracle-data base kan worden geautomatiseerd en gepland met behulp van [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines) of [Azure Automation updatebeheer](../../../automation/update-management/overview.md) om de downtime te minimaliseren. Zie [continue levering en Blue/groen-implementaties](/azure/devops/learn/what-is-continuous-delivery) om te begrijpen hoe deze kunnen worden gebruikt in de context van uw Oracle-data bases.

## <a name="architecture-and-design-considerations"></a>Architectuur-en ontwerp overwegingen

- Overweeg het gebruik van hyperthreaded [geheugen geoptimaliseerde virtuele machine](../../sizes-memory.md) met [beperkte kern vcpu's](../../../virtual-machines/constrained-vcpu.md) voor uw Oracle database-VM om de licentie kosten op te slaan en de prestaties te maximaliseren. Gebruik meerdere Premium-of Ultra-schijven (Managed disks) voor prestaties en beschik baarheid.
- Wanneer u beheerde schijven gebruikt, kan de naam van de schijf/apparaat worden gewijzigd bij het opnieuw opstarten. Het is raadzaam om de UUID van het apparaat te gebruiken in plaats van de naam om ervoor te zorgen dat uw koppelingen behouden blijven tijdens het opnieuw opstarten. Meer informatie vindt u [hier](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab).
- Gebruik beschikbaarheids zones voor maximale Beschik baarheid in-regio.
- Overweeg het gebruik van ultra schijven (indien beschikbaar) of Premium-schijven voor uw Oracle-data base.
- Overweeg een stand-by Oracle-data base in een andere Azure-regio in te stellen met behulp van Oracle Data Guard.
- Overweeg het gebruik van [proximity placement groups](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) om de latentie tussen uw toepassing en database laag te verminderen.
- [Oracle Enter prise Manager](https://docs.oracle.com/en/enterprise-manager/) instellen voor beheer, bewaking en logboek registratie.
- Overweeg het gebruik van Oracle Automatic Storage Management (ASM) voor gestroomlijnd opslag beheer voor uw data base.
- Gebruik [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines) voor het beheren van patches en updates voor uw data base zonder uitval tijd.
- Pas de code van uw toepassing aan om Cloud-systeem eigen patronen toe te voegen, zoals het [patroon voor opnieuw proberen](/azure/architecture/patterns/retry), het [patroon circuit onderbreker](/azure/architecture/patterns/circuit-breaker)en andere patronen die zijn gedefinieerd in de [hand leiding voor het ontwerpen van Clouds](/azure/architecture/patterns/) , waardoor uw toepassing flexibeler kan zijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende Oracle-referentie artikelen die van toepassing zijn op uw scenario.

- [Inleiding tot Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Concepten van Oracle Data Guard Broker](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Oracle Golden Gate configureren voor Active-Active hoge Beschik baarheid](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Overzicht van Oracle sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Far Sync van Oracle Active Data Guard heeft geen gegevens verlies op elke afstand](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
