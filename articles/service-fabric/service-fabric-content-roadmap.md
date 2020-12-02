---
title: Meer informatie over Azure Service Fabric
description: Meer informatie over de belangrijkste concepten en belang rijke gebieden van Azure Service Fabric. Biedt een uitgebreid overzicht van Service Fabric en hoe u micro Services maakt.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 36215dd3419050cf498a749b5caf927c3c4e275a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485447"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Wilt u meer informatie over Service Fabric?
Azure Service Fabric is een gedistribueerde systemen platform waarmee u gemakkelijk pakket, implementeren en beheren van schaalbare en betrouwbare microservices.  Service Fabric heeft echter een grote surface area, maar er is veel te leren.  In dit artikel vindt u een samen vatting van Service Fabric en worden de belangrijkste concepten, programmeer modellen, toepassings levenscyclus, testen, clusters en status controle beschreven. Lees het [overzicht](service-fabric-overview.md) en [Wat zijn micro Services?](service-fabric-overview-microservices.md) voor een inleiding en hoe service Fabric kan worden gebruikt om micro services te maken. Dit artikel bevat geen uitgebreide inhouds lijst, maar bevat een koppeling naar overzicht en aan de slag-artikelen voor elk gebied van Service Fabric. 

## <a name="core-concepts"></a>Basisconcepten
[Service Fabric terminologie](service-fabric-technical-overview.md), [toepassings model](service-fabric-application-model.md)en [ondersteunde programmeer modellen](service-fabric-choose-framework.md) bieden meer concepten en beschrijvingen, maar dit zijn de basis principes.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Ontwerp tijd: Service type, service pakket en manifest, toepassings type, toepassings pakket en manifest
Een service type is de naam/versie die is toegewezen aan de code pakketten, gegevens pakketten en configuratie pakketten van een service. Dit wordt gedefinieerd in een ServiceManifest.xml-bestand. Het Service type bestaat uit uitvoer bare code en service configuratie-instellingen, die tijdens de uitvoering worden geladen en statische gegevens die door de service worden gebruikt.

Een service pakket is een schijf Directory met het ServiceManifest.xml-bestand van het Service type, dat verwijst naar de code, statische gegevens en configuratie pakketten voor het Service type. Een service pakket kan bijvoorbeeld verwijzen naar de code, statische gegevens en configuratie pakketten die een database service vormen.

Een toepassings type is de naam/versie die is toegewezen aan een verzameling service typen. Dit wordt gedefinieerd in een ApplicationManifest.xml-bestand.

![Service Fabric toepassings typen en-service typen][cluster-imagestore-apptypes]

Het toepassings pakket is een schijf directory die het ApplicationManifest.xml bestand van het toepassings type bevat, dat verwijst naar de service pakketten voor elk Service type dat het toepassings type vormt. Een toepassings pakket voor een e-mail toepassings type kan bijvoorbeeld verwijzingen bevatten naar een wachtrij service pakket, een frontend-service pakket en een database service pakket.  

De bestanden in de map van het toepassings pakket worden gekopieerd naar de installatie kopie opslag van het Service Fabric cluster. U kunt vervolgens een benoemde toepassing maken op basis van dit toepassings type, dat vervolgens binnen het cluster wordt uitgevoerd. Nadat u een benoemde toepassing hebt gemaakt, kunt u een benoemde service maken van een van de service typen van het toepassings type. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Uitvoerings tijd: clusters en knoop punten, benoemde toepassingen, benoemde services, partities en replica's
Een [Service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Clusters kunnen naar duizenden machines worden geschaald.

Een machine of VM die onderdeel uitmaakt van een cluster, heet een knooppunt. Aan elk knooppunt wordt een knooppuntnaam toegewezen (een tekenreeks). Knooppunten hebben kenmerken zoals plaatsingseigenschappen. Elke machine of VM heeft een Windows-service die automatisch wordt gestart, en `FabricHost.exe` die start op het moment dat de computer wordt opgestart en twee uitvoer bare bestanden worden gestart: `Fabric.exe` en `FabricGateway.exe` . Deze twee uitvoer bare bestanden vormen het knoop punt. Voor ontwikkelings-en test scenario's kunt u meerdere knoop punten op één computer of virtuele machine hosten door meerdere exemplaren van en uit te voeren `Fabric.exe` `FabricGateway.exe` .

Een benoemde toepassing is een verzameling benoemde services die een bepaalde functie of functies uitvoert. Een service voert een volledige en zelfstandige functie uit (deze kan onafhankelijk van andere services worden gestart en uitgevoerd) en bestaat uit code, configuratie en gegevens. Nadat een toepassings pakket is gekopieerd naar het archief met installatie kopieën, maakt u een exemplaar van de toepassing in het cluster door het toepassings type van het toepassings pakket op te geven (met de naam/versie). Elk exemplaar van het toepassings type krijgt een URI-naam die lijkt op *Fabric:/MyNamedApp*. Binnen een cluster kunt u meerdere benoemde toepassingen maken op basis van één toepassings type. U kunt ook benoemde toepassingen maken op basis van verschillende toepassings typen. Elke benoemde toepassing wordt afzonderlijk beheerd en gemaakt.

Nadat u een benoemde toepassing hebt gemaakt, kunt u een exemplaar van een van de service typen (een named service) in het cluster maken door het Service type (met de naam/versie) op te geven. Aan elk Service type-exemplaar is een URI-naam toegewezen onder de URI van de benoemde toepassing. Als u bijvoorbeeld een ' MyDatabase-service met de naam ' in een ' MyNamedApp '-toepassing maakt, ziet de URI er als volgt uit: *Fabric:/MyNamedApp/MyDatabase*. Binnen een benoemde toepassing kunt u een of meer benoemde services maken. Elke benoemde service kan een eigen partitie schema en aantal exemplaren/replica's hebben. 

Er zijn twee soorten services: stateless en stateful. Stateless Services slaan geen status op in de service. Stateless Services hebben helemaal geen permanente opslag of slaan een permanente status op in een externe opslag service, zoals Azure Storage, Azure SQL Database of Azure Cosmos DB. Een stateful service de status van de service opslaat en gebruikmaakt van betrouw bare verzamelingen of Reliable Actors programmeer modellen om de status te beheren. 

Bij het maken van een benoemde service geeft u een partitie schema op. Services met grote hoeveel heden status splitsen de gegevens over partities. Elke partitie is verantwoordelijk voor een deel van de volledige status van de service, die wordt verspreid over de knoop punten van het cluster.  

In het volgende diagram ziet u de relatie tussen toepassingen en service-exemplaren, partities en replica's.

![Partities en replica's in een service][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partitioneren, schalen en beschik baarheid
[Partitioneren](service-fabric-concepts-partitioning.md) is niet uniek voor service Fabric. Een bekende vorm van partitionering is het partitioneren van gegevens, of sharding. Stateful Services met grote hoeveel heden status splitsen de gegevens over partities. Elke partitie is verantwoordelijk voor een deel van de volledige status van de service. 

De replica's van elke partitie worden verdeeld over de knoop punten van het cluster, waardoor de status van de naam van de service kan worden [geschaald](service-fabric-concepts-scalability.md). Naarmate de behoeften van de gegevens groeien, worden de partities verg root en Service Fabric worden partities op verschillende knoop punten opnieuw gebalanceerd om efficiënt gebruik te maken van hardwarebronnen. Als u nieuwe knoop punten aan het cluster toevoegt, worden de partitie replica's door Service Fabric opnieuw gebalanceerd over het verhoogde aantal knoop punten. De algehele prestaties van toepassingen verbeteren en conflicten voor toegang tot het geheugen neemt af. Als de knoop punten in het cluster niet efficiënt worden gebruikt, kunt u het aantal knoop punten in het cluster verlagen. Service Fabric opnieuw, worden de partitie replica's op het verkleinde aantal knoop punten opnieuw verdeeld om het gebruik van de hardware op elk knoop punt beter te maken.

Binnen een partitie hebben stateless benoemde services instanties terwijl stateful benoemde services replica's hebben. Normaal gesp roken hebben stateless benoemde services slechts één partitie, omdat ze geen interne status hebben, hoewel [er uitzonde ringen zijn](./service-fabric-concepts-partitioning.md#partition-service-fabric-stateless-services). De partitie-exemplaren bieden [Beschik baarheid](service-fabric-availability-services.md). Als één exemplaar mislukt, blijven andere instanties normaal functioneren en maakt Service Fabric vervolgens een nieuw exemplaar. Stateful benoemde services behouden hun status in replica's en elke partitie heeft een eigen replicaset. Lees-en schrijf bewerkingen worden uitgevoerd op één replica (de primaire). Wijzigingen in de status van schrijf bewerkingen worden gerepliceerd naar meerdere andere replica's (actieve secundaire zones genoemd). Als een replica mislukt, Service Fabric een nieuwe replica bouwt van de bestaande replica's.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Staatloze en stateful microservices voor Service Fabric
Met Service Fabric kunt u toepassingen bouwen die uit microservices of containers bestaan. Staatloze microservices (zoals protocolgateways en webproxy's) handhaven buiten een aanvraag en de reactie erop van de service geen veranderlijke status. Azure Cloud Services-werkrollen zijn een voorbeeld van een staatloze service. Stateful microservices (zoals gebruikersaccounts, databases, apparaten, winkelwagentjes en wachtrijen) handhaven een veranderlijke, gezaghebbende status, buiten de aanvraag en de reactie erop. De huidige toepassingen op internetschaal bestaan uit een combinatie van staatloze en stateful microservices. 

Een belang rijke differentiatie met Service Fabric is de sterke focus op het bouwen van stateful Services, hetzij met de [ingebouwde programmeer modellen](service-fabric-choose-framework.md) , hetzij met stateful Services in de container. De [toepassingsscenario's](service-fabric-application-scenarios.md) beschrijven de scenario's waarin stateful services worden gebruikt.

Waarom hebben stateful micro Services en stateless? De twee belangrijkste redenen zijn:

* U kunt fout tolerante Services voor de verwerking van hoge door Voer, lage latentie en OLTP (online Trans Action processing) maken door de code en gegevens op dezelfde computer sluiten. Enkele voor beelden zijn interactieve werk systemen, Zoek-, Internet of Things-, IoT-,-, verkoop-, creditcard verwerking-en fraude detectie systemen en persoonlijk record beheer.
* U kunt het toepassings ontwerp vereenvoudigen. Stateful micro Services verwijdert de nood zaak van extra wacht rijen en caches, die traditioneel zijn vereist om de vereisten voor Beschik baarheid en latentie van een louter stateless toepassing te verhelpen. Stateful Services zijn natuurlijke hoge Beschik baarheid en lage latentie, waardoor het aantal te verplaatsen onderdelen in uw toepassing als geheel wordt beperkt.

## <a name="supported-programming-models"></a>Ondersteunde programmeermodellen
Service Fabric biedt meerdere manieren om uw services te schrijven en te beheren. Services kunnen de Service Fabric-Api's gebruiken om optimaal te profiteren van de functies en toepassings raamwerken van het platform. Services kunnen ook alle gecompileerde uitvoer bare Program ma's zijn die zijn geschreven in elke taal en worden gehost op een Service Fabric cluster. Zie [ondersteunde programmeer modellen](service-fabric-choose-framework.md)voor meer informatie.

### <a name="containers"></a>Containers
Service Fabric implementeert en activeert standaard services als processen. Service Fabric kunnen ook services in [containers](service-fabric-containers-overview.md)implementeren. Belang rijk: u kunt Services in processen en services combi neren in containers in dezelfde toepassing. Service Fabric ondersteunt de implementatie van Linux-containers en Windows-containers in Windows Server 2016. U kunt bestaande toepassingen, stateless Services of stateful Services in containers implementeren. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) is een licht gewicht Framework voor het schrijven van services die zijn geïntegreerd met het service Fabric platform en profiteren van de volledige set platform functies. Reliable Services kan stateless zijn (vergelijkbaar met de meeste service platforms, zoals webservers of werk rollen in azure Cloud Services), waarbij de status persistent is in een externe oplossing, zoals Azure DB of Azure Table Storage. Reliable Services kan ook stateful zijn, waarbij de status direct in de service zelf wordt gehandhaafd met behulp van betrouw bare verzamelingen. De status wordt [Maxi maal beschikbaar](service-fabric-availability-services.md) gemaakt via replicatie en gedistribueerd via [partitioneren](service-fabric-concepts-partitioning.md), die allemaal automatisch worden beheerd door service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Het [reliable actor](service-fabric-reliable-actors-introduction.md) Framework is gebaseerd op reliable Services en is een toepassings raamwerk dat het virtuele actor-patroon implementeert op basis van het ontwerp patroon actor. Het reliable actor-Framework maakt gebruik van onafhankelijke eenheden van Compute en State met uitvoering met één thread en ' Actors '. Het reliable actor-Framework biedt ingebouwde communicatie voor actors en vooraf ingestelde persistentie en scale-out configuraties.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric integreert met [ASP.net core](service-fabric-reliable-services-communication-aspnetcore.md) als een eerste programmeer model voor klassen voor het bouwen van web-en API-toepassingen.  ASP.NET Core kan op twee verschillende manieren in Service Fabric worden gebruikt:

- Gehost als een gast-uitvoerbaar bestand. Dit wordt voornamelijk gebruikt om bestaande ASP.NET Core-toepassingen uit te voeren op Service Fabric zonder code wijzigingen.
- Binnen een betrouw bare service worden uitgevoerd. Hierdoor is een betere integratie met de Service Fabric runtime mogelijk en kunnen stateful ASP.NET Core Services worden uitgevoerd.

### <a name="guest-executables"></a>Uitvoerbare gastbestanden
Een [uitvoerbaar gast bestand](service-fabric-guest-executables-introduction.md) is een bestaand, wille keurig uitvoerbaar bestand (geschreven in een taal) dat wordt gehost op een service Fabric cluster naast andere services. Uitvoer bare bestanden voor gasten kunnen niet rechtstreeks worden geïntegreerd met Service Fabric-Api's. Ze profiteren echter nog steeds van de functies van het platform, zoals aangepaste status en belasting rapportage en service detectie door het aanroepen van REST-Api's. Ze hebben ook volledige ondersteuning voor toepassings levenscyclus. 

## <a name="application-lifecycle"></a>Toepassingslevenscyclus
Net als bij andere platforms gaat een toepassing op Service Fabric doorgaans door de volgende fasen: ontwerpen, ontwikkelen, testen, implementeren, bijwerken, onderhoud en verwijderen. Service Fabric biedt eersteklas ondersteuning voor de volledige levens cyclus van toepassingen van Cloud toepassingen, van ontwikkeling tot implementatie tot en met het dagelijks beheer en het onderhoud om te voor komen dat ze buiten gebruik worden gesteld. Het service model maakt het mogelijk dat verschillende rollen onafhankelijk deel nemen in de levens cyclus van de toepassing. [Service Fabric levens cyclus van toepassingen](service-fabric-application-lifecycle.md) biedt een overzicht van de api's en hoe deze worden gebruikt door de verschillende rollen in de fasen van de levens cyclus van de service Fabric-toepassing. 

De volledige levens cyclus van de app kan worden beheerd met behulp van [Power shell-cmdlets](/powershell/module/ServiceFabric/), [cli-opdrachten](service-fabric-sfctl.md), [C#-api's](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java-api's](/java/api/overview/azure/servicefabric)en [rest api's](/rest/api/servicefabric/). U kunt ook doorlopende integratie/doorlopende implementatie pijplijnen instellen met behulp van hulpprogram ma's als [Azure-pijp lijnen](./service-fabric-tutorial-deploy-app-with-cicd-vsts.md) of [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster).

## <a name="test-applications-and-services"></a>Testtoepassingen en -services
Om echt Services voor de Cloud schaal te maken, is het belang rijk om te controleren of uw toepassingen en services zich in de praktijk kunnen voordoen. De fout analyse service is ontworpen voor het testen van services die zijn gebouwd op Service Fabric. Met de [Fout analyse service](service-fabric-testability-overview.md)kunt u zinvolle fouten tegen komen en volledige test scenario's uitvoeren voor uw toepassingen. Deze fouten en scenario's oefenen en valideren de talrijke statussen en overgangen die gedurende de levens duur van een service worden ervaren, op een gecontroleerde, veilige en consistente manier.

[Acties](service-fabric-testability-actions.md) richten een service op die met afzonderlijke fouten kan worden getest. Een service ontwikkelaar kan deze gebruiken als bouw stenen voor het schrijven van gecompliceerde scenario's. Voor beelden van gesimuleerde fouten zijn:

* Start een knoop punt opnieuw voor het simuleren van een wille keurig aantal situaties waarbij een machine of VM opnieuw wordt opgestart.
* Verplaats een replica van uw stateful service om taak verdeling, failover of toepassings upgrade te simuleren.
* Aanroepen van quorum verlies op een stateful service voor het maken van een situatie waarin schrijf bewerkingen niet kunnen worden voortgezet omdat er onvoldoende ' back-up '-of ' secundaire ' replica's zijn om nieuwe gegevens te accepteren.
* Gegevens verlies op een stateful service aanroepen om een situatie te maken waarin de status van alle in-Memory volledig is gewist.

[Scenario's](service-fabric-testability-scenarios.md) zijn complexe bewerkingen die bestaan uit een of meer acties. De fout analyse service biedt twee ingebouwde volledige scenario's:

* [Chaos-scenario](service-fabric-controlled-chaos.md): simuleert doorlopende, Interleaved fouten (zowel zonder problemen als een uitstel) in het hele cluster gedurende een langere periode.
* [Failover-scenario](service-fabric-testability-scenarios.md#failover-test): een versie van het chaos-test scenario dat gericht is op een specifieke service partitie, terwijl andere services ongewijzigd blijven.

## <a name="clusters"></a>Clusters
Een [Service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Clusters kunnen naar duizenden machines worden geschaald. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een cluster knooppunt genoemd. Aan elk knooppunt wordt een knooppuntnaam toegewezen (een tekenreeks). Knooppunten hebben kenmerken zoals plaatsingseigenschappen. Elke machine of VM heeft een service die automatisch wordt gestart `FabricHost.exe` en die wordt gestart wanneer de computer wordt opgestart en twee uitvoer bare bestanden worden gestart: Fabric.exe en FabricGateway.exe. Deze twee uitvoer bare bestanden vormen het knoop punt. Voor het testen van scenario's kunt u meerdere knoop punten op één computer of virtuele machine hosten door meerdere exemplaren van en uit te voeren `Fabric.exe` `FabricGateway.exe` .

Service Fabric clusters kunnen worden gemaakt op virtuele of fysieke machines met Windows Server of Linux. U kunt Service Fabric-toepassingen implementeren en uitvoeren in elke omgeving waar u een set Windows Server-of Linux-computers hebt die zijn verbonden: on-premises, op Microsoft Azure of op een Cloud provider.

### <a name="clusters-on-azure"></a>Clusters op Azure
Het uitvoeren van Service Fabric clusters in Azure biedt integratie met andere Azure-functies en-services, waardoor bewerkingen en beheer van het cluster eenvoudiger en betrouwbaarder kunnen worden. Een cluster is een Azure Resource Manager bron, zodat u clusters zoals andere resources in azure kunt model leren. Resource Manager biedt ook eenvoudig beheer van alle resources die worden gebruikt door het cluster als één eenheid. Clusters op Azure zijn geïntegreerd met Azure Diagnostics en Azure Monitor logs. Cluster knooppunt typen zijn [virtuele-machine schaal sets](../virtual-machine-scale-sets/index.yml), waardoor de functionaliteit voor automatisch schalen is ingebouwd.

U kunt een cluster op Azure maken via de [Azure Portal](service-fabric-cluster-creation-via-portal.md), vanuit een [sjabloon](service-fabric-cluster-creation-via-arm.md)of vanuit [Visual Studio](./service-fabric-cluster-creation-via-arm.md).

Met Service Fabric op Linux kunt u Maxi maal beschik bare, zeer schaal bare toepassingen op Linux bouwen, implementeren en beheren, net zoals u zou doen met Windows. De Service Fabric Frameworks (Reliable Services en Reliable Actors) zijn beschikbaar in Java onder Linux, naast C# (.NET core). U kunt ook [uitvoer bare gast Services](service-fabric-guest-executables-introduction.md) bouwen met elke taal of elk Framework. Orchestrator docker-containers worden ook ondersteund. Docker-containers kunnen uitvoer bare gast bestanden of systeem eigen Service Fabric Services uitvoeren die gebruikmaken van de Service Fabric frameworks. Lees voor meer informatie over [service fabric in Linux](service-fabric-deploy-anywhere.md).

Er zijn enkele functies die worden ondersteund in Windows, maar niet in Linux. Lees [verschillen tussen service Fabric op Linux en Windows](service-fabric-linux-windows-differences.md)voor meer informatie.

### <a name="standalone-clusters"></a>Zelfstandige clusters
Service Fabric biedt een installatie pakket waarmee u zelfstandige Service Fabric clusters on-premises of op een Cloud provider kunt maken. Zelfstandige clusters bieden u de vrijheid om een cluster op elke gewenste locatie te hosten. Als uw gegevens onderhevig zijn aan nalevings-of reglementaire beperkingen of als u uw gegevens lokaal wilt houden, kunt u uw eigen cluster en toepassingen hosten. Service Fabric toepassingen kunnen worden uitgevoerd in meerdere hosting omgevingen zonder wijzigingen, zodat uw kennis van het bouwen van toepassingen van de ene hostomgeving naar de andere wordt overgedragen. 

[Uw eerste zelfstandige Service Fabric-cluster maken](service-fabric-cluster-creation-for-windows-server.md)

Zelfstandige Linux-clusters worden nog niet ondersteund.

### <a name="cluster-security"></a>Clusterbeveiliging
Clusters moeten worden beveiligd om te voor komen dat onbevoegde gebruikers verbinding maken met uw cluster, met name wanneer er productie werkbelastingen worden uitgevoerd. Hoewel het mogelijk is om een niet-beveiligd cluster te maken, kunnen anonieme gebruikers hiermee verbinding maken als er beheer eindpunten beschikbaar zijn op het open bare Internet. Het is niet mogelijk om later beveiliging in te scha kelen op een niet-beveiligd cluster: cluster beveiliging is ingeschakeld op het moment van maken van het cluster.

De cluster beveiligings scenario's zijn:
* Beveiliging van knoop punt naar knoop punt
* Beveiliging van client naar knoop punt
* Service Fabric op rollen gebaseerd toegangs beheer

Lees [een cluster beveiligen](service-fabric-cluster-security.md)voor meer informatie.

### <a name="scaling"></a>Schalen
Als u nieuwe knoop punten aan het cluster toevoegt, worden in Service Fabric de partitie replica's en instanties over het verhoogde aantal knoop punten gebalanceerd. De algehele prestaties van toepassingen verbeteren en conflicten voor toegang tot het geheugen neemt af. Als de knoop punten in het cluster niet efficiënt worden gebruikt, kunt u het aantal knoop punten in het cluster verlagen. Service Fabric opnieuw, worden de partitie replica's en instanties over het aantal knoop punten verkleind om beter gebruik te maken van de hardware op elk knoop punt. U kunt clusters [hand matig](service-fabric-cluster-scale-in-out.md) of [programmatisch](service-fabric-cluster-programmatic-scaling.md)schalen op Azure. Zelfstandige clusters kunnen [hand matig](service-fabric-cluster-windows-server-add-remove-nodes.md)worden geschaald.

### <a name="cluster-upgrades"></a>Cluster upgrades
Er worden regel matig nieuwe versies van de Service Fabric runtime uitgebracht. Voer runtime, of Fabric, upgrades uit op het cluster, zodat u altijd een [ondersteunde versie](service-fabric-support.md)uitvoert. Naast infrastructuur upgrades kunt u ook de cluster configuratie, zoals certificaten of toepassings poorten, bijwerken.

Een Service Fabric cluster is een bron waarvan u de eigenaar bent, maar die gedeeltelijk wordt beheerd door micro soft. Micro soft is verantwoordelijk voor het patchen van het onderliggende besturings systeem en het uitvoeren van infrastructuur upgrades voor uw cluster. U kunt uw cluster instellen voor het ontvangen van automatische infrastructuur upgrades, wanneer micro soft een nieuwe versie uitgeeft of een ondersteunde infrastructuur versie selecteert die u wilt. Infra structuur-en configuratie-upgrades kunnen worden ingesteld via de Azure Portal of via Resource Manager. Lees [Upgrade a Service Fabric cluster](service-fabric-cluster-upgrade.md)voor meer informatie. 

Een zelfstandig cluster is een bron die u volledig bezit. U bent zelf verantwoordelijk voor het patchen van het onderliggende besturings systeem en het initiëren van infrastructuur upgrades. Als uw cluster verbinding kan maken met [https://www.microsoft.com/download](https://www.microsoft.com/download) , kunt u uw cluster zo instellen dat het nieuwe service Fabric runtime-pakket automatisch wordt gedownload en ingericht. Vervolgens initieert u de upgrade. Als uw cluster geen toegang heeft [https://www.microsoft.com/download](https://www.microsoft.com/download) , kunt u het nieuwe runtime pakket hand matig downloaden van een computer met Internet verbinding en vervolgens de upgrade starten. Lees voor meer informatie [een zelfstandige service Fabric cluster bijwerken](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Statuscontrole
Service Fabric introduceert een [status model](service-fabric-health-introduction.md) dat is ontworpen voor het markeren van slechte cluster-en toepassings voorwaarden voor specifieke entiteiten (zoals cluster knooppunten en service replica's). Het status model gebruikt status rapporten (systeem onderdelen en watchdog). Het doel is eenvoudig en snel te diagnosticeren en te herstellen. Service schrijvers moeten vooraf denken over de status en het ontwerpen van [status rapportage](service-fabric-report-health.md#design-health-reporting). Elke voor waarde die de status van invloed kan hebben, moet worden gerapporteerd op, met name als het kan helpen om problemen in de buurt van de hoofdmap te markeren. De status informatie kan tijd en moeite besparen bij het opsporen van fouten en onderzoek wanneer de service op schaal wordt uitgevoerd in de productie omgeving.

De Service Fabric rapporters bewaken de geïdentificeerde voor waarden. Ze rapporteren over deze voor waarden op basis van hun lokale weer gave. De [Health Store](service-fabric-health-introduction.md#health-store) verzamelt status gegevens die door alle rapporten worden verzonden om te bepalen of entiteiten wereld wijd in orde zijn. Het model is bedoeld als rijk, flexibel en eenvoudig te gebruiken. De kwaliteit van de status rapporten bepaalt de nauw keurigheid van de status weergave van het cluster. Fout-positieven die onjuiste slechte problemen weer geven, kunnen een negatieve invloed hebben op upgrades of andere services die gebruikmaken van status gegevens. Voor beelden van dergelijke services zijn Reparatie Services en waarschuwings mechanismen. Daarom is er een aantal Denkingen nodig om rapporten te bieden waarin de voor waarden van belang op de beste manier worden vastgelegd.

Rapportage kan worden uitgevoerd vanaf:
* De bewaakte Service Fabric service replica of-instantie.
* Interne watchdog worden geïmplementeerd als een Service Fabric-service (bijvoorbeeld een Service Fabric stateless service waarmee voor waarden en rapporten van problemen worden gecontroleerd). De watchdog kunnen op alle knoop punten worden geïmplementeerd of kunnen worden stelt teamlid aan de bewaakte service.
* Interne watchdog die worden uitgevoerd op de Service Fabric knoop punten, maar die niet zijn geïmplementeerd als Service Fabric Services.
* Externe watchdog die de bron van buiten het Service Fabric cluster testen (bijvoorbeeld bewakings service zoals Gomez).

Service Fabric onderdelen rapporteren de status van alle entiteiten in het cluster. [Systeem status rapporten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) bieden inzicht in de functionaliteit van het cluster en de toepassing en markeren problemen via de status. Voor toepassingen en services controleert de systeem status rapporten of de entiteiten zijn geïmplementeerd en goed werken vanuit het perspectief van de Service Fabric runtime. De rapporten bieden geen status controle van de bedrijfs logica van de service of detecteert processen die niet meer reageren. Als u informatie over de status wilt toevoegen die specifiek is voor de logica van uw service, [implementeert u aangepaste status rapportage](service-fabric-report-health.md) in uw services.

Service Fabric biedt meerdere manieren om [status rapporten weer te geven](service-fabric-view-entities-aggregated-health.md) die zijn samengevoegd in de Health Store:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) of andere hulpprogram ma's voor visualisaties.
* Status query's (via [Power shell](/powershell/module/ServiceFabric/), [cli](service-fabric-sfctl.md), de [C# FabricClient Api's](/dotnet/api/system.fabric.fabricclient.healthclient) en [Java FabricClient-api's](/java/api/system.fabric)of [rest-api's](/rest/api/servicefabric)).
* Algemene query's die een lijst met entiteiten retour neren die een status hebben als een van de eigenschappen (via Power shell, CLI, de Api's of REST).

## <a name="monitoring-and-diagnostics"></a>Controle en diagnose
[Bewaking en diagnose](service-fabric-diagnostics-overview.md) zijn essentieel voor het ontwikkelen, testen en implementeren van toepassingen en services in elke omgeving. Service Fabric oplossingen werken het beste wanneer u bewaking en diagnostische gegevens plant en implementeert waarmee u ervoor kunt zorgen dat toepassingen en services naar behoren werken in een lokale ontwikkel omgeving of in productie.

De belangrijkste doel stellingen van bewaking en diagnose zijn:

- Hardware-en infrastructuur problemen detecteren en vaststellen
- Software-en app-problemen detecteren, uitval tijd van services verminderen
- Resource verbruik begrijpen en beslissingen over het oplossen van bewerkingen
- Prestaties van toepassingen, services en infra structuur optimaliseren
- Business Insights genereren en gebieden van verbetering identificeren
 
De algemene werk stroom voor bewaking en diagnostiek bestaat uit drie stappen:

1. Gebeurtenis generatie: Dit omvat gebeurtenissen (logboeken, traceringen, aangepaste gebeurtenissen) op de infra structuur (cluster), platform en toepassings/service niveau
2. Gebeurtenis aggregatie: gegenereerde gebeurtenissen moeten worden verzameld en geaggregeerd voordat ze kunnen worden weer gegeven
3. Analyse: gebeurtenissen moeten in een van de indelingen worden gevisualiseerd en toegankelijk om te zorgen voor analyse en weer gave als dat nodig is

Er zijn meerdere producten beschikbaar die deze drie gebieden beslaan, en u kunt voor elk product verschillende technologieën kiezen. Lees voor meer informatie [bewaking en diagnostische gegevens voor Azure service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Volgende stappen
* Leer hoe u een [cluster maakt in Azure](service-fabric-cluster-creation-via-portal.md) of hoe u een [zelfstandig cluster maakt in Windows](service-fabric-cluster-creation-for-windows-server.md).
* Maak een service met het programmeermodel [Reliable Services](service-fabric-reliable-services-quick-start.md) of [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Meer informatie over het [migreren van Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Meer informatie over het [bewaken en diagnosticeren van services](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Meer informatie over [het testen van uw apps en services](service-fabric-testability-overview.md).
* Meer informatie over het [beheren en organiseren van cluster resources](service-fabric-cluster-resource-manager-introduction.md).
* Bekijk de Service Fabric-voor [beelden](/samples/browse/?products=azure).
* Meer informatie over [service Fabric ondersteunings opties](service-fabric-support.md).
* Lees het [Team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) voor artikelen en aankondigingen.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png