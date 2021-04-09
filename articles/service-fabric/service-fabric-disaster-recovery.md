---
title: Herstel na nood gevallen voor Azure Service Fabric
description: Azure Service Fabric biedt mogelijkheden voor het afhandelen van rampen. In dit artikel worden de soorten rampen beschreven die zich kunnen voordoen en hoe u deze kunt verwerken.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791152"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Herstel na nood geval in azure Service Fabric
Een essentieel onderdeel van het leveren van hoge Beschik baarheid zorgt ervoor dat Services alle verschillende soorten fouten kunnen overlaten. Dit is vooral belang rijk voor storingen die ongepland zijn en buiten uw besturings element vallen. 

In dit artikel worden enkele veelvoorkomende fout modi beschreven die nood herstel kunnen zijn als ze niet correct worden gemodelleerd en beheerd. Er worden ook oplossingen en acties beschreven die moeten worden uitgevoerd als er toch een nood geval is. Het doel is om het risico van uitval tijd of gegevens verlies te beperken of te elimineren wanneer er fouten optreden, gepland of anderszins.

## <a name="avoiding-disaster"></a>Nood gevallen voor komen
Het belangrijkste doel van Azure Service Fabric is om u te helpen uw omgeving en uw services zo te model leren dat veelvoorkomende fout typen geen rampen zijn. 

Over het algemeen zijn er twee soorten scenario's voor rampen/mislukken:
- Hardware-en software fouten
- Operationele fouten

### <a name="hardware-and-software-faults"></a>Hardware-en software fouten
Hardware-en software fouten zijn onvoorspelbaar. De eenvoudigste manier om fouten te overleven, is dat er meer exemplaren van de service worden uitgevoerd op de grenzen van hardware-of software fouten. 

Als uw service bijvoorbeeld op slechts één computer wordt uitgevoerd, is het mislukken van de ene computer een nood geval voor die service. De eenvoudige manier om deze nood geval te vermijden, is om ervoor te zorgen dat de service wordt uitgevoerd op meerdere computers. Testen is ook nodig om ervoor te zorgen dat de uitvoering van de actieve service niet wordt verstoord door de ene computer. Capaciteits planning zorgt ervoor dat een vervangend exemplaar op een andere locatie kan worden gemaakt en dat de resterende Services niet overbelasten. 

Hetzelfde patroon is afhankelijk van wat u wilt voor komen dat de fout optreedt. Als u zich bijvoorbeeld zorgen maakt over het mislukken van een SAN, voert u uit op meerdere San's. Als u zich zorgen maakt over het verlies van een rek met servers, voert u uit op meerdere racks. Als u zich zorgen maakt over het verlies van data centers, moet uw service worden uitgevoerd in meerdere Azure-regio's, op meerdere Azure-beschikbaarheidszones, of in uw eigen data centers. 

Wanneer een service wordt verdeeld over meerdere fysieke instanties (machines, rekken, data centers, regio's), bent u nog steeds van toepassing op enkele typen gelijktijdige storingen. Maar eenmalige en zelfs meerdere storingen van een bepaald type (bijvoorbeeld een storing in één virtuele machine of netwerk koppeling) worden automatisch verwerkt en zijn dus niet langer een ' ramp '. 

Service Fabric voorziet in mechanismen voor het uitbreiden van het cluster en het is niet meer gelukt om mislukte knoop punten en services terug te brengen. Service Fabric kunt ook veel exemplaren van uw services uitvoeren om ongeplande storingen te voor komen bij het omzetten in echte rampen.

Er kunnen redenen zijn waarom het uitvoeren van een implementatie die groot genoeg is voor uitval, niet haalbaar is. Het is bijvoorbeeld mogelijk dat er meer hardwarebronnen worden gebruikt dan u wilt betalen ten opzichte van de kans op fouten. Wanneer u met gedistribueerde toepassingen communiceert, kunnen er voor extra communicatie-of status replicatie kosten over geografische afstand een onaanvaardbaar latentie optreden. Wanneer deze regel wordt getekend, verschilt voor elke toepassing. 

Voor software fouten is het mogelijk dat de fout zich in de service bevindt die u probeert te schalen. In dit geval is het niet mogelijk om de nood herstel bewerking te voor komen, omdat de fout voorwaarde is afgestemd op alle exemplaren.

### <a name="operational-faults"></a>Operationele fouten
Zelfs als uw service wordt verspreid over de hele wereld met veel redundantie, kan deze nog steeds disastrous-gebeurtenissen ervaren. Iemand kan bijvoorbeeld per ongeluk de DNS-naam voor de service opnieuw configureren of het verwijderen. 

Stel dat u een stateful Service Fabric-service hebt en iemand die service per ongeluk hebt verwijderd. Tenzij er sprake is van een andere beperking, wordt die service en alle statussen die het nu had, verwijderd. Voor deze typen operationele rampen (' al') zijn verschillende oplossingen vereist en de herstel stappen dan bij normale, niet-geplande storingen. 

De beste manieren om deze typen operationele fouten te vermijden, zijn:
- Beperk operationele toegang tot de omgeving.
- Beoordeel gevaarlijke bewerkingen strikt.
- Pas automatisering toe, voorkom hand matige of buiten-band wijzigingen en valideer specifieke wijzigingen in de omgeving voordat u ze toebrengt.
- Zorg ervoor dat destructieve bewerkingen ' zacht ' zijn. Tijdelijke bewerkingen worden niet direct van kracht of kunnen ongedaan worden gemaakt binnen een tijd venster.

Service Fabric biedt mechanismen om operationele fouten te voor komen, zoals het bieden van toegangs beheer op [basis van rollen](service-fabric-cluster-security-roles.md) voor cluster bewerkingen. Voor de meeste van deze operationele fouten zijn echter bedrijfs activiteiten en andere systemen vereist. Service Fabric biedt mechanismen voor het naleven van operationele fouten, met name [back-up en herstel voor stateful Services](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Fouten beheren
Het doel van Service Fabric is het automatisch beheer van fouten. Voor het verwerken van bepaalde soorten fouten, moeten services echter extra code hebben. Andere soorten fouten mogen _niet_ automatisch worden aangepakt om redenen van veiligheid en bedrijfs continuïteit. 

### <a name="handling-single-failures"></a>Verwerking van enkele fouten
Eén machine kan om verschillende redenen mislukken. Soms zijn er hardware-oorzaken, zoals voedingen en netwerkhardware. Andere fouten bevinden zich in software. Dit zijn onder andere fouten van het besturings systeem en de service zelf. Service Fabric detecteert deze typen fouten automatisch, met inbegrip van gevallen waarin de computer wordt geïsoleerd van andere computers vanwege netwerk problemen.

Ongeacht het type van de service, leidt het uitvoeren van één exemplaar tot uitval tijd voor die service als één exemplaar van de code om een of andere reden mislukt. 

Als u één storing wilt verwerken, kunt u het eenvoudigste doen door ervoor te zorgen dat uw services standaard worden uitgevoerd op meer dan één knoop punt. Zorg ervoor dat er `InstanceCount` meer dan 1 is voor stateless Services. Voor stateful Services is de minimale aanbeveling `TargetReplicaSetSize` `MinReplicaSetSize` ingesteld op 3. Wanneer u meer exemplaren van uw service code uitvoert, zorgt u ervoor dat de service een enkele fout automatisch kan verwerken. 

### <a name="handling-coordinated-failures"></a>Gecoördineerde fouten verwerken
Gecoördineerde fouten in een cluster kunnen worden veroorzaakt door geplande of ongeplande infrastructuur fouten en wijzigingen, of geplande software wijzigingen. Service Fabric modellen infrastructuur zones die gecoördineerde fouten als *fout domeinen* ondervinden. Gebieden die gecoördineerde software wijzigingen zullen ondervinden, worden als *upgrade domeinen* gemodelleerd. Zie [een service Fabric cluster beschrijven met cluster resource manager](service-fabric-cluster-resource-manager-cluster-description.md)voor meer informatie over fout domeinen, upgrade domeinen en cluster topologie.

Service Fabric houdt standaard rekening met de fout-en upgrade domeinen bij het plannen waar uw services moeten worden uitgevoerd. Service Fabric probeert standaard om ervoor te zorgen dat uw services worden uitgevoerd op verschillende fout-en upgrade domeinen, zodat de services beschikbaar blijven als er geplande of ongeplande wijzigingen plaatsvinden. 

Stel bijvoorbeeld dat een storing van een energie bron ervoor zorgt dat alle machines in een rek gelijktijdig worden uitgevoerd. Als er meerdere exemplaren van de service worden uitgevoerd, wordt het verlies van veel computers in fout domein storing in slechts een ander voor beeld van één storing voor een service. Daarom is het beheer van fout-en upgrade domeinen essentieel om een hoge Beschik baarheid van uw services te garanderen. 

Wanneer u Service Fabric in azure uitvoert, worden fout domeinen en upgrade domeinen automatisch beheerd. In andere omgevingen zijn ze mogelijk niet. Als u uw eigen clusters on-premises bouwt, moet u ervoor zorgen dat u de indeling van het fout domein correct toewijst en plant.

Upgrade domeinen zijn handig voor het maken van een upgrade van de software op hetzelfde moment. Als gevolg hiervan definiëren upgrade domeinen ook vaak de grenzen waarin software wordt uitgevoerd tijdens geplande upgrades. Upgrades van zowel Service Fabric als uw services volgen hetzelfde model. Zie voor meer informatie over rolling upgrades, het upgraden van domeinen en het Service Fabric status model waarmee wordt voor komen dat onbedoelde wijzigingen van invloed zijn op het cluster en uw service.

 - [Toepassingsupgrade](service-fabric-application-upgrade.md)
 - [Zelf studie voor toepassings upgrade](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric status model](service-fabric-health-introduction.md)

U kunt de indeling van uw cluster visualiseren met behulp van de cluster toewijzing in [service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Knoop punten worden verdeeld over fout domeinen in Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Het model leren van fouten, rolling upgrades, het uitvoeren van veel exemplaren van uw service code en-status, plaatsings regels om ervoor te zorgen dat uw services worden uitgevoerd in fout-en upgrade domeinen en ingebouwde status controle is slechts *enkele* van de functies die service Fabric biedt om normale operationele problemen en storingen in nood gevallen te houden. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Gelijktijdige verwerking van hardware-of software fouten
Er zijn enkele fouten opgetreden. Zoals u kunt zien, is het eenvoudig om zowel stateless als stateful services te verwerken door meer kopieën van de code (en status) uit te voeren die worden uitgevoerd op fout-en upgrade domeinen. 

Er kunnen ook meerdere gelijktijdige, wille keurige fouten optreden. Dit is waarschijnlijker om te leiden tot downtime of een echte nood geval.


#### <a name="stateless-services"></a>Stateless Services
Met het aantal exemplaren voor een stateless service wordt het gewenste aantal exemplaren aangegeven dat moet worden uitgevoerd. Wanneer een (of alle) van de instanties mislukt, Service Fabric reageert door automatisch vervangende instanties te maken op andere knoop punten. Service Fabric gaat door met het maken van vervangingen totdat de service weer het aantal gewenste instanties heeft bereikt.

Stel bijvoorbeeld dat de stateless service de `InstanceCount` waarde-1 heeft. Deze waarde betekent dat er één exemplaar moet worden uitgevoerd op elk knoop punt in het cluster. Als sommige van deze instanties mislukken, detecteert Service Fabric dat de service niet de gewenste status heeft en probeert de instanties te maken op de knoop punten waar ze ontbreken.

#### <a name="stateful-services"></a>Stateful Services
Er zijn twee soorten stateful-Services:
- Stateful met persistente status.
- Stateful met niet-persistente status. (Status wordt opgeslagen in het geheugen.)

Herstel van uitval van een stateful service is afhankelijk van het type stateful service, hoeveel replica's de service had en hoeveel replica's er zijn mislukt.

In een stateful service worden inkomende gegevens gerepliceerd tussen replica's (de primaire en eventuele actieve secundairen). Als een meerderheid van de replica's de gegevens ontvangt, worden gegevens beschouwd als *quorum* -vastgelegd. (Voor vijf replica's is drie een quorum.) Dit betekent dat er op elk moment ten minste een quorum van replica's is met de meest recente gegevens. Als replica's mislukken (bijvoorbeeld twee van vijf), kunnen we de quorum waarde gebruiken om te berekenen of we kunnen herstellen. (Omdat de resterende drie van de vijf replica's nog steeds actief zijn, is het gegarandeerd dat ten minste één replica volledige gegevens bevat.)

Wanneer een quorum van replica's mislukt, wordt de partitie gedeclareerd met de status *quorum verlies* . Stel dat een partitie vijf replica's bevat. Dit betekent dat ten minste drie de volledige gegevens zijn gegarandeerd. Als er sprake is van een quorum (drie uitgaand vijf) van replica's, kan Service Fabric niet bepalen of de resterende replica's (twee van de vijf) voldoende gegevens hebben om de partitie te herstellen. In gevallen waarin Service Fabric quorum verlies detecteert, is het standaard gedrag van het voor komen van extra schrijf bewerkingen naar de partitie, het declareren van quorum verlies en het wachten op het herstellen van een quorum van replica's.

Als u wilt bepalen of een nood geval is opgetreden voor een stateful service en u deze vervolgens wilt beheren, volgt u drie fasen:

1. Vaststellen of er sprake is van quorum verlies of niet.
   
   Quorum verlies wordt gedeclareerd wanneer een meerderheid van de replica's van een stateful service tegelijk actief is.
2. Vaststellen of het quorum verlies permanent is of niet.
   
   De meeste tijd zijn fouten tijdelijk. De processen worden opnieuw gestart, de knoop punten opnieuw worden gestart, de virtuele machines opnieuw worden gestart en de netwerk partities worden hersteld. Soms zijn storingen permanent. Of storingen permanent zijn of niet afhankelijk zijn van het feit of de stateful service de status persistent houdt of dat deze alleen in het geheugen wordt bewaard: 
   
   - Voor services zonder persistente status, een fout in een quorum of meer van replica's resulteert _onmiddellijk_ in permanent quorum verlies. Wanneer Service Fabric quorum verlies detecteert in een stateful, niet-persistente service, wordt dit onmiddellijk door gegeven aan stap 3 door het declareren van (potentieel) gegevens verlies. Door gaan met gegevens verlies is zinvol omdat Service Fabric weet dat er geen punt is om te wachten tot de replica's weer worden teruggestuurd. Zelfs als ze worden hersteld, gaan de gegevens verloren vanwege de niet-persistente aard van de service.
   - Als er sprake is van stateful permanente Services, treedt er een fout op in een quorum of meer van replica's, Service Fabric wachten tot de replica's weer worden teruggezet en het quorum herstellen. Dit resulteert in een service onderbreking voor _schrijf bewerkingen_ naar de betrokken partitie (of ' replicaset ') van de service. Lees bewerkingen kunnen echter nog steeds mogelijk zijn met beperkte consistentie garanties. De standaard hoeveelheid tijd die Service Fabric wacht totdat het quorum is hersteld, is *oneindig*, omdat het volgen van een (mogelijke) gegevens verlies gebeurtenis plaatsvindt en andere Risico's ondervindt. Dit betekent dat Service Fabric niet verder gaat met de volgende stap, tenzij een beheerder actie onderneemt om gegevens verlies te declareren.
3. Bepalen of gegevens verloren gaan en herstellen vanaf back-ups.

   Als quorum verlies is gedeclareerd (automatisch of via beheer actie), Service Fabric en de Services verplaatsen op om te bepalen of de gegevens werkelijk verloren zijn gegaan. Op dit Service Fabric punt weet u ook dat de andere replica's niet meer terugkeren. Dit was de beslissing die werd genomen toen werd gewacht tot het quorum verlies vanzelf is opgelost. De beste actie voor de service is meestal om te blok keren en te wachten op specifieke administratieve interventie.
   
   Als Service Fabric de methode aanroept `OnDataLossAsync` , is dit altijd het gevolg van _mogelijk_ gegevens verlies. Service Fabric zorgt ervoor dat deze aanroep wordt afgeleverd bij de _meest_ resterende replica. Dit is de meest voortgang van de replica. 
   
   De reden hiervoor is dat het waarschijnlijk is _dat gegevens verloren_ gaan, omdat de rest van de replica dezelfde status heeft als het primaire exemplaar toen het quorum werd verloren. Zonder deze status te vergelijken met, is er echter geen goede manier voor Service Fabric of Opera tors om zeker te weten.     
   
   Wat doet een typische implementatie van de- `OnDataLossAsync` methode?
   1. De implementatie logboeken die `OnDataLossAsync` zijn geactiveerd en die eventuele nood zakelijke beheerders waarschuwingen worden gestopt.
   1. Normaal gesp roken wordt de implementatie onderbroken en wordt gewacht op verdere beslissingen en hand matige acties die moeten worden ondernomen. Dit komt doordat zelfs als er back-ups beschikbaar zijn, ze mogelijk moeten worden voor bereid. 
   
      Als er bijvoorbeeld twee verschillende Services gegevens coördineren, moeten deze back-ups mogelijk worden gewijzigd om ervoor te zorgen dat na het herstellen de informatie over deze twee services consistent is. 
   1. Vaak is er sprake van een andere telemetrie of een afzuig van de service. Deze meta gegevens kunnen in andere services of in Logboeken zijn opgenomen. Deze informatie kan zo nodig worden gebruikt om te bepalen of er aanroepen zijn ontvangen en verwerkt op de primaire server die niet aanwezig waren in de back-up of die zijn gerepliceerd naar deze specifieke replica. Deze aanroepen moeten mogelijk opnieuw worden afgespeeld of worden toegevoegd aan de back-up voordat de herstel bewerking kan worden uitgevoerd.  
   1. De implementatie vergelijkt de resterende status van de replica naar die in alle beschik bare back-ups is opgenomen. Als u Service Fabric betrouw bare verzamelingen gebruikt, zijn er [hulpprogram ma's en processen](service-fabric-reliable-services-backup-restore.md) beschikbaar waarmee u dit kunt doen. Het doel is om te zien of de status binnen de replica voldoende is en om te zien wat de back-up kan ontbreken.
   1. Nadat de vergelijking is uitgevoerd en de herstel bewerking is voltooid (indien nodig), moet de service code de **waarde True** retour neren als er status wijzigingen zijn aangebracht. Als de replica heeft vastgesteld dat het de beste beschik bare kopie van de status heeft en geen wijzigingen heeft aangebracht, retourneert de code **Onwaar**. 
   
      De waarde **True** geeft aan dat _andere_ Replicas nu mogelijk inconsistent zijn met deze naam. Ze worden verwijderd en opnieuw opgebouwd op basis van deze replica. De waarde **False** geeft aan dat er geen status wijzigingen zijn aangebracht, zodat de andere replica's kunnen blijven wat ze hebben. 

Het is zeer belang rijk dat service ontwerpers mogelijke scenario's voor gegevens verlies en-fouten oefenen voordat Services in productie worden geïmplementeerd. Ter bescherming tegen de mogelijkheid van gegevens verlies is het belang rijk om regel matig een [back-up te maken van de status](service-fabric-reliable-services-backup-restore.md) van uw stateful Services naar een geografisch redundante opslag. 

U moet er ook voor zorgen dat u de mogelijkheid hebt om de status te herstellen. Omdat er op verschillende momenten back-ups van verschillende services worden gemaakt, moet u ervoor zorgen dat uw services na het herstellen een consistente weer gave van elkaar hebben. 

Denk bijvoorbeeld aan een situatie waarbij een service een nummer genereert en opslaat, en deze vervolgens verzendt naar een andere service die deze ook opslaat. Na het herstellen weet u mogelijk dat de tweede service het nummer heeft, maar de eerste niet, omdat deze bewerking niet door de back-up is toegevoegd.

Als u merkt dat de resterende replica's onvoldoende zijn om door te gaan in een scenario met gegevens verlies en u de service status niet opnieuw kunt samen stellen op basis van telemetrie of uitgeput, bepaalt de frequentie van uw back-ups uw best mogelijke Recovery Point Objective (RPO). Service Fabric biedt veel hulpprogram ma's voor het testen van verschillende fout scenario's, waaronder permanent quorum en gegevens verlies waarvoor herstel vanuit een back-up is vereist. Deze scenario's zijn opgenomen als onderdeel van de hulpprogram ma's voor test baarheid in Service Fabric, beheerd door de fout analyse service. Zie [Inleiding tot de fout analyse service](service-fabric-testability-overview.md)voor meer informatie over deze hulpprogram ma's en patronen. 

> [!NOTE]
> Systeem services kunnen ook quorum verlies lijden. De impact is specifiek voor de betreffende service. Quorum verlies in de naamgevings service is bijvoorbeeld van invloed op naam omzetting, terwijl quorum verlies in de Failover Manager-service het maken van nieuwe services en failovers blokkeert. 
> 
> De Service Fabric-systeem services volgen hetzelfde patroon als uw services voor status beheer, maar we raden u niet aan om ze te verplaatsen uit het quorum verlies en naar potentieel gegevens verlies. In plaats daarvan raden we u aan  [ondersteuning](service-fabric-support.md) te zoeken om een oplossing te vinden die is gericht op uw situatie. Normaal gesp roken moet u gewoon wachten tot de down keer dat er replica's worden geretourneerd.
>

#### <a name="troubleshooting-quorum-loss"></a>Problemen met quorum verlies oplossen

Replica's zijn mogelijk tijdelijk niet actief vanwege een tijdelijke fout. Wacht enige tijd als Service Fabric probeert deze te halen. Als replica's langer dan een verwachte duur zijn, volgt u deze probleemoplossings acties:
- Replica's kunnen vastlopen. Controleer status rapporten op replica niveau en uw toepassings Logboeken. Verzamel crash dumps en neem de nodige maat regelen om te herstellen.
- Het replica proces reageert mogelijk niet meer. Controleer uw toepassings Logboeken om dit te controleren. Verzamel proces dumps en Stop vervolgens het niet-reagerende proces. Service Fabric maakt een vervangings proces en wordt geprobeerd de replica terug te brengen.
- Knoop punten die de replica's hosten, zijn mogelijk niet beschikbaar. Start de onderliggende virtuele machine opnieuw op om de knoop punten omhoog te brengen.

Soms is het niet mogelijk om replica's te herstellen. De stations zijn bijvoorbeeld mislukt of de computers reageren niet meer. In deze gevallen moet Service Fabric worden verteld dat er niet moet worden gewacht op replica herstel.

Gebruik deze methoden *niet* als mogelijk gegevens verlies onaanvaardbaar is om de service online te brengen. In dat geval moeten alle inspanningen worden gedaan om fysieke machines te herstellen.

De volgende acties kunnen leiden tot gegevens verlies. Controleer voordat u deze volgt.
   
> [!NOTE]
> Het is _nooit_ veilig om deze methoden te gebruiken, maar niet op een gerichte manier voor specifieke partities. 
>

- Gebruik de `Repair-ServiceFabricPartition -PartitionId` `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API of. Met deze API kunt u de ID van de partitie die uit het quorum verlies of het verlies van gegevens kan optreden, opgeven.
- Als uw cluster veelvuldige storingen detecteert waardoor Services de status van een quorum verlies veroorzaken en mogelijk _gegevens verlies acceptabel is_, kunt u een geschikte [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) -waarde opgeven, zodat uw service automatisch kan worden hersteld. Service Fabric wordt gewacht op de gegeven `QuorumLossWaitDuration` waarde (de standaard instelling is oneindig) voordat het herstel wordt uitgevoerd. Deze methode wordt *niet* aanbevolen omdat dit kan leiden tot onverwachte gegevens verlies.

## <a name="availability-of-the-service-fabric-cluster"></a>Beschik baarheid van het Service Fabric cluster
Over het algemeen is het Service Fabric cluster een zeer gedistribueerde omgeving zonder storings punten. Als er een storing optreedt in een van de knoop punten, worden er geen Beschik baarheid of betrouwbaarheids problemen voor het cluster veroorzaakt, voornamelijk omdat de Service Fabric systeem services dezelfde richt lijnen volgen als hierboven. Dat wil zeggen dat ze altijd worden uitgevoerd met drie of meer replica's standaard, en systeem services die stateless worden uitgevoerd op alle knoop punten. 

De onderliggende Service Fabric netwerk-en fout detectie lagen zijn volledig gedistribueerd. De meeste systeem services kunnen opnieuw worden opgebouwd op basis van meta gegevens in het cluster of u weet hoe u hun status opnieuw moet synchroniseren vanaf andere locaties. De beschik baarheid van het cluster kan worden aangetast als systeem services problemen in quorum verlies voordoen, zoals eerder beschreven. In dergelijke gevallen is het mogelijk dat u bepaalde bewerkingen op het cluster niet kunt uitvoeren (zoals het starten van een upgrade of het implementeren van nieuwe services), maar dat het cluster zelf actief is. 

Services op een actief cluster blijven in deze omstandigheden actief, tenzij ze moeten worden geschreven naar de systeem services om verder te kunnen werken. Als Failover Manager bijvoorbeeld zich in quorum verlies bevindt, blijven alle services worden uitgevoerd. Maar alle services die niet automatisch opnieuw kunnen worden opgestart, omdat hiervoor de betrokkenheid van Failover Manager nodig is. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Storingen in een Data Center of een Azure-regio
In zeldzame gevallen kan een fysiek Data Center tijdelijk niet beschikbaar worden gesteld van stroom uitval of netwerk verbindingen. In deze gevallen is uw Service Fabric-clusters en-services in dat Data Center of Azure-regio niet beschikbaar. _Uw gegevens blijven echter behouden_. 

Voor clusters die in Azure worden uitgevoerd, kunt u updates op de Azure- [status pagina][azure-status-dashboard]bekijken op storingen. In het zeer onwaarschijnlijke geval dat een fysiek Data Center gedeeltelijk of volledig wordt vernietigd, kunnen eventuele Service Fabric clusters die daar worden gehost of de services erin, verloren gaan. Dit verlies omvat alle staten waarvan geen back-up is gemaakt buiten dat Data Center of de regio.

Er zijn twee verschillende strategieën voor het naleven van de permanente of aanhoudende storing van één Data Center of regio: 

- Voer afzonderlijke Service Fabric clusters uit in meerdere dergelijke regio's en gebruik een mechanisme voor failover en failback tussen deze omgevingen. Voor het type van het actieve/actieve of actieve/passieve model van meerdere clusters is meer beheer-en bewerkings code vereist. Dit model vereist ook coördinatie van back-ups van de services in één Data Center of regio, zodat deze beschikbaar zijn in andere data centers of regio's wanneer er een mislukt. 
- Voer één Service Fabric-cluster uit dat meerdere data centers of regio's omvat. De mini maal ondersteunde configuratie voor deze strategie is drie data centers of regio's. Het aanbevolen aantal regio's of data centers is vijf. 
  
  Dit model vereist een complexere cluster topologie. Het voor deel is echter dat het ene Data Center of de regio niet kan worden geconverteerd van een nood geval naar een normale storing. Deze fouten kunnen worden verwerkt door de mechanismen die werken voor clusters binnen één regio. Fout domeinen, upgrade domeinen en Service Fabric plaatsings regels zorgen ervoor dat workloads worden gedistribueerd zodat ze normale storingen verdragen. 
  
  Zie voor meer informatie over beleids regels die kunnen helpen bij het gebruik van services in dit type cluster [plaatsings beleid voor service Fabric Services](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Wille keurige fouten die leiden tot cluster fouten
Service Fabric heeft het concept van *Seed-knoop punten*. Dit zijn knoop punten die de beschik baarheid van het onderliggende cluster behouden. 

Seed-knoop punten helpen ervoor te zorgen dat het cluster actief blijft door leases te maken met andere knoop punten en te fungeren als tiebreakers tijdens bepaalde soorten storingen. Als met wille keurige fouten een meerderheid van de Seed-knoop punten in het cluster wordt verwijderd en deze niet snel worden hersteld, wordt het cluster automatisch afgesloten. Vervolgens mislukt het cluster. 

In azure beheert Service Fabric resource provider Service Fabric cluster configuraties. Standaard worden Seed-knoop punten verdeeld over de fout-en upgrade domeinen voor het *primaire knooppunt type*. Als het primaire knooppunt type is gemarkeerd als zilver of Gold duurzaamheid, probeert het cluster een ander niet-Seed-knoop punt te promo veren van de beschik bare capaciteit van het primaire knooppunt type wanneer u een Seed-knoop punt verwijdert (door de schaal te schalen in het primaire knooppunt type of door het hand matig te verwijderen). Deze poging mislukt als u minder beschik bare capaciteit hebt dan het betrouwbaarheids niveau van het cluster vereist is voor uw primaire knooppunt type.

In zowel zelfstandige Service Fabric clusters en Azure is het primaire knooppunt type de seeding die de zaden uitvoert. Wanneer u een primair knooppunt type definieert, zal Service Fabric automatisch profiteren van het aantal knoop punten dat wordt gegeven door Maxi maal negen Seed-knoop punten te maken en zeven replica's van elke systeem service. Als een reeks wille keurige fouten tegelijkertijd een meerderheid van deze replica's uitmaakt, wordt quorum verlies door de systeem services ingevoerd. Als een meerderheid van de Seed-knoop punten verloren is gegaan, wordt het cluster binnenkort afgesloten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het simuleren van verschillende fouten met behulp van het [Test-Framework](service-fabric-testability-overview.md).
- Lees andere bronnen voor herstel na nood gevallen en hoge Beschik baarheid. Micro soft heeft een groot aantal richt lijnen gepubliceerd op deze onderwerpen. Hoewel sommige van deze resources verwijzen naar specifieke technieken voor gebruik in andere producten, bevatten ze veel algemene aanbevolen procedures die u kunt Toep assen in de Service Fabric context:
  - [Controlelijst voor beschikbaarheid](/azure/architecture/checklist/resiliency-per-service)
  - [Een nood herstel analyse uitvoeren](../azure-sql/database/disaster-recovery-drills.md)
  - [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen][dr-ha-guide]
- Meer informatie over [service Fabric ondersteunings opties](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
