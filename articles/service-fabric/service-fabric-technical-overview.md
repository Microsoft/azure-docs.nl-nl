---
title: Meer informatie over Azure Service Fabric terminologie
description: Meer informatie over de belangrijkste Service Fabric terminologie en concepten die worden gebruikt in de rest van de documentatie.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 2ac4b81a284ed8c38bc9cefccd08db5afa51d600
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575938"
---
# <a name="service-fabric-terminology-overview"></a>Overzicht van Service Fabric terminologie

Azure Service Fabric is een gedistribueerde systemen platform waarmee u gemakkelijk pakket, implementeren en beheren van schaalbare en betrouwbare microservices.  U kunt [service Fabric-clusters overal hosten](service-fabric-deploy-anywhere.md): Azure, in een on-premises Data Center of op een Cloud provider.  Service Fabric is de Orchestrator die [Azure service Fabric net](../service-fabric-mesh/index.yml)aanstuurt. U kunt elk Framework gebruiken om uw services te schrijven en te kiezen waar u de toepassing uitvoert vanuit meerdere omgevings opties. In dit artikel vindt u informatie over de terminologie die wordt gebruikt door Service Fabric om inzicht te krijgen in de voor waarden in de documentatie.

## <a name="infrastructure-concepts"></a>Infrastructuur concepten

**Cluster**: een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw micro services worden geïmplementeerd en beheerd.  Clusters kunnen naar duizenden machines worden geschaald.

**Knoop punt**: een computer of virtuele machine die deel uitmaakt van een cluster, wordt een *knoop punt* genoemd. Elk knoop punt krijgt een knooppunt naam (teken reeks) toegewezen. Knoop punten hebben kenmerken, zoals plaatsings eigenschappen. Elke machine of VM heeft een Windows-service die automatisch wordt gestart, en `FabricHost.exe` die start op het moment dat de computer wordt opgestart en twee uitvoer bare bestanden worden gestart: `Fabric.exe` en `FabricGateway.exe` . Deze twee uitvoer bare bestanden vormen het knoop punt. Voor het testen van scenario's kunt u meerdere knoop punten op één computer of virtuele machine hosten door meerdere exemplaren van en uit te voeren `Fabric.exe` `FabricGateway.exe` .

## <a name="application-and-service-concepts"></a>Toepassings-en service concepten

**Service Fabric mesh-toepassing**: Service Fabric mesh-toepassingen worden beschreven door het resource model (yaml-en JSON-bron bestanden) en kunnen worden geïmplementeerd in elke omgeving waarin Service Fabric wordt uitgevoerd.

**Service Fabric systeem eigen toepassing**: Service Fabric systeem eigen toepassingen worden beschreven door het systeem eigen toepassings model (XML-toepassings-en service manifesten).  Service Fabric systeem eigen toepassingen kunnen niet worden uitgevoerd in Service Fabric net.

### <a name="service-fabric-mesh-application-concepts"></a>Concepten van Service Fabric-mesh-toepassing

**Toepassing**: een toepassing is de implementatie-eenheid, versie beheer en levens duur van een mesh-toepassing. De levens cyclus van elk toepassings exemplaar kan onafhankelijk worden beheerd.  Toepassingen bestaan uit een of meer service code pakketten en instellingen. Een toepassing wordt gedefinieerd met behulp van het schema van het Azure resource model (RM).  Services worden beschreven als eigenschappen van de toepassings bron in een RM-sjabloon.  Voor netwerken en volumes die worden gebruikt door de toepassing wordt verwezen door de toepassing.  Bij het maken van een toepassing worden de toepassing, de service (s), het netwerk en volume (s) gemodelleerd met behulp van het Service Fabric resource model.

**Service**: een service in een toepassing vertegenwoordigt een micro service en voert een volledige en zelfstandige functie uit. Elke service bestaat uit een of meer code pakketten die alles beschrijven wat nodig is voor het uitvoeren van de container installatie kopie die is gekoppeld aan het code pakket.  Het aantal services in een toepassing kan omhoog of omlaag worden geschaald.

**Netwerk**: een netwerk bron maakt een privé netwerk voor uw toepassingen en is onafhankelijk van de toepassingen of services die hiernaar kunnen verwijzen. Meerdere services van verschillende toepassingen kunnen deel uitmaken van hetzelfde netwerk. Netwerken zijn Implementeer bare bronnen waarnaar wordt verwezen door toepassingen.

**Code pakket**: code pakketten beschrijven alles wat nodig is voor het uitvoeren van de container installatie kopie die is gekoppeld aan het code pakket, met inbegrip van het volgende:

* Container naam, versie en REGI ster
* Benodigde CPU-en geheugen resources voor elke container
* Netwerk eindpunten
* Volumes die moeten worden gekoppeld in de container en verwijzen naar een afzonderlijke volume bron.

Alle code pakketten die zijn gedefinieerd als onderdeel van een toepassings bron, worden als een groep geïmplementeerd en geactiveerd.

**Volume**: volumes zijn mappen die in de container instanties worden gekoppeld die u kunt gebruiken om de status te behouden. Het Azure Files-volume stuur programma koppelt een Azure Files-share aan een container en biedt betrouw bare gegevens opslag via elke API die ondersteuning biedt voor bestands opslag. Volumes zijn Implementeer bare resources waarnaar wordt verwezen door toepassingen.

### <a name="service-fabric-native-application-concepts"></a>Service Fabric systeem eigen concepten van toepassingen

**Toepassing**: een toepassing is een verzameling van onderdeel Services die een bepaalde functie of functies uitvoeren. De levens cyclus van elk toepassings exemplaar kan onafhankelijk worden beheerd.

**Service**: een service voert een volledige en zelfstandige functie uit en kan onafhankelijk van andere services worden gestart en uitgevoerd. Een service bestaat uit code, configuratie en gegevens. Voor elke service bestaat code uit de binaire uitvoer bare bestanden, configuratie bestaat uit service-instellingen die tijdens runtime kunnen worden geladen en gegevens bestaan uit wille keurige statische gegevens die door de service worden gebruikt.

**Toepassings type**: de naam/versie die is toegewezen aan een verzameling service typen. Deze is gedefinieerd in een `ApplicationManifest.xml` bestand en is inge sloten in een toepassings pakket Directory. De map wordt vervolgens gekopieerd naar het archief met installatie kopieën van het Service Fabric-cluster. U kunt vervolgens een benoemde toepassing maken op basis van dit toepassings type binnen het cluster.

Lees het artikel over het [toepassings model](service-fabric-application-model.md) voor meer informatie.

**Toepassings pakket**: een schijf Directory met het bestand van het toepassings type `ApplicationManifest.xml` . Verwijst naar de service pakketten voor elk Service type dat het toepassings type vormt. De bestanden in de map van het toepassings pakket worden gekopieerd naar het archief met installatie kopieën van Service Fabric cluster. Een toepassings pakket voor een e-mail toepassings type kan bijvoorbeeld verwijzingen bevatten naar een wachtrij-service pakket, een frontend-service pakket en een Data Base-service pakket.

**Benoemde toepassing**: nadat u een toepassings pakket naar het archief met installatie kopieën hebt gekopieerd, maakt u een exemplaar van de toepassing in het cluster. U maakt een instantie wanneer u het toepassings type van het toepassings pakket opgeeft met behulp van de naam of versie. Aan elk exemplaar van het toepassings type is een URI-naam (Uniform Resource Identifier) toegewezen die er als volgt uitziet: `"fabric:/MyNamedApp"` . Binnen een cluster kunt u meerdere benoemde toepassingen maken op basis van één toepassings type. U kunt ook benoemde toepassingen maken op basis van verschillende toepassings typen. Elke benoemde toepassing wordt afzonderlijk beheerd en gemaakt.

**Service type**: de naam/versie die is toegewezen aan de code pakketten, gegevens pakketten en configuratie pakketten van een service. Het Service type wordt gedefinieerd in het `ServiceManifest.xml` bestand en Inge sloten in een service pakket Directory. Er wordt naar de map van het service pakket verwezen door het bestand van het toepassings pakket `ApplicationManifest.xml` . In het cluster kunt u na het maken van een benoemde toepassing een benoemde service maken van een van de service typen van het toepassings type. In het bestand van het Service type `ServiceManifest.xml` wordt de service beschreven.

Lees het artikel over het [toepassings model](service-fabric-application-model.md) voor meer informatie.

Er zijn twee soorten services:

* **Stateless**: gebruik een stateless service wanneer de permanente status van de service wordt opgeslagen in een externe opslag service, zoals Azure Storage, Azure SQL Database of Azure Cosmos db. Gebruik een stateless service als de service geen permanente opslag heeft. Voor een Calculator service waarbij waarden worden door gegeven aan de service, wordt een berekening uitgevoerd die deze waarden gebruikt, waarna een resultaat wordt geretourneerd.
* **Stateful**: gebruik een stateful service als u wilt dat service Fabric de status van uw service beheert via zijn betrouw bare verzamelingen of reliable actors programmeer modellen. Wanneer u een benoemde service maakt, geeft u op hoeveel partities u uw status wilt spreiden voor schaal baarheid. Geef ook op hoe vaak uw status moet worden gerepliceerd tussen knoop punten, voor betrouw baarheid. Elke benoemde service heeft één primaire replica en meerdere secundaire replica's. U wijzigt de status van de naam van de service wanneer u naar de primaire replica schrijft. Service Fabric repliceert deze status vervolgens naar alle secundaire replica's om uw status synchroon te laten blijven. Service Fabric detecteert automatisch wanneer een primaire replica is mislukt en bevordert een bestaande secundaire replica naar een primaire replica. Service Fabric maakt vervolgens een nieuwe secundaire replica.  

**Replica's of exemplaren** verwijzen naar code (en status voor stateful Services) van een service die is geïmplementeerd en wordt uitgevoerd. Zie [replica's en exemplaren](service-fabric-concepts-replica-lifecycle.md).

**Herconfiguratie** verwijst naar het proces van elke wijziging in de replicaset van een service. Zie [herconfiguratie](service-fabric-concepts-reconfiguration.md).

**Service pakket**: een schijf Directory met het bestand van het Service type `ServiceManifest.xml` . Dit bestand verwijst naar de code, statische gegevens en configuratie pakketten voor het Service type. Naar de bestanden in de map van het service pakket wordt verwezen door het bestand van het toepassings type `ApplicationManifest.xml` . Een service pakket kan bijvoorbeeld verwijzen naar de code, statische gegevens en configuratie pakketten die een database service vormen.

**Named service**: nadat u een benoemde toepassing hebt gemaakt, kunt u een exemplaar van een van de service typen in het cluster maken. U geeft het Service type op met behulp van de naam/versie. Aan elk Service type-exemplaar is een URI-naam toegewezen onder de URI van de benoemde toepassing. Als u bijvoorbeeld een ' MyDatabase-service met de naam ' in een ' MyNamedApp '-toepassing maakt, ziet de URI er als volgt uit: `"fabric:/MyNamedApp/MyDatabase"` . Binnen een benoemde toepassing kunt u meerdere benoemde services maken. Elke benoemde service kan een eigen partitie schema en exemplaar-of replica aantallen hebben.

**Code pakket**: een schijf Directory met de uitvoer bare bestanden van het Service type, doorgaans exe/DLL-bestanden. Er wordt naar de bestanden in de map code pakket verwezen door het bestand van het Service type `ServiceManifest.xml` . Wanneer u een benoemde service maakt, wordt het code pakket gekopieerd naar het knoop punt of de knoop punten die zijn geselecteerd om de genoemde service uit te voeren. Vervolgens wordt de code uitgevoerd. Er zijn twee soorten code pakket uitvoer bare bestanden:

* **Uitvoer bare bestanden voor gasten**: uitvoer bare bestanden die worden uitgevoerd als op het hostbesturingssysteem (Windows of Linux). Deze uitvoer bare bestanden zijn niet gekoppeld aan of verwijzen naar Service Fabric runtime-bestanden en gebruiken daarom geen Service Fabric-programmeer modellen. Deze uitvoer bare bestanden kunnen sommige Service Fabric functies niet gebruiken, zoals de naamgevings service voor eindpunt detectie. Uitvoer bare bestanden voor gasten kunnen geen belasting gegevens rapporteren die specifiek zijn voor elk service-exemplaar.
* **Uitvoer bare bestanden** van de servicehost: uitvoer bare bestanden die gebruikmaken van service Fabric-programmeer modellen door te koppelen aan service Fabric runtime-bestanden, waardoor service Fabric functies worden ingeschakeld. Een benoemd service-exemplaar kan bijvoorbeeld eind punten registreren bij de Naming Service van Service Fabric en kan ook belasting gegevens rapporteren.

**Gegevens pakket**: een schijf Directory met de statische, alleen-lezen gegevens bestanden van het Service type, doorgaans foto-, geluids-en video bestanden. Er wordt naar de bestanden in de map van het gegevens pakket verwezen door het bestand van het Service type `ServiceManifest.xml` . Wanneer u een benoemde service maakt, wordt het gegevens pakket gekopieerd naar het knoop punt of de knoop punten die zijn geselecteerd om de genoemde service uit te voeren. De code wordt uitgevoerd en heeft nu toegang tot de gegevens bestanden.

**Configuratie pakket**: een schijf Directory met de statische, alleen-lezen configuratie bestanden van het Service type, meestal tekst bestanden. Naar de bestanden in de map van het configuratie pakket wordt verwezen door het bestand van het Service type `ServiceManifest.xml` . Wanneer u een benoemde service maakt, worden de bestanden in het configuratie pakket gekopieerd naar een of meer knoop punten die zijn geselecteerd om de genoemde service uit te voeren. Vervolgens wordt de code uitgevoerd en hebt u nu toegang tot de configuratie bestanden.

**Containers**: standaard worden services door service Fabric geïmplementeerd en geactiveerd als processen. Service Fabric kunt ook services implementeren in container installatie kopieën. Containers zijn een Virtualization-technologie die het onderliggende besturings systeem van toepassingen abstractet. Een toepassing en de runtime, afhankelijkheden en systeem bibliotheken worden uitgevoerd binnen een container. De container heeft volledige, persoonlijke toegang tot de eigen geïsoleerde weer gave van de constructie van het besturings systeem in de container. Service Fabric ondersteunt Windows Server-containers en docker-containers in Linux. Lees [service Fabric en containers](service-fabric-containers-overview.md)voor meer informatie.

**Partitie schema**: wanneer u een benoemde service maakt, geeft u een partitie schema op. Services met een aanzienlijke hoeveelheid status splitsen de gegevens over partities, waardoor de status wordt verdeeld over de knoop punten van het cluster. Door de gegevens te splitsen in meerdere partities, kan de status van de named service worden geschaald. Binnen een partitie hebben stateless benoemde services instanties, terwijl stateful benoemde services replica's hebben. Meestal hebben stateless benoemde services slechts één partitie, omdat ze geen interne status hebben. De partitie-exemplaren bieden Beschik baarheid. Als één exemplaar mislukt, blijven andere instanties normaal functioneren en maakt Service Fabric vervolgens een nieuwe instantie. Stateful benoemde services behouden hun status in replica's en elke partitie heeft een eigen replicaset, zodat de status synchroon blijft. Als een replica mislukt, Service Fabric een nieuwe replica bouwt van de bestaande replica's.

Lees de [partitie service Fabric reliable Services](service-fabric-concepts-partitioning.md) -artikel voor meer informatie.

## <a name="system-services"></a>Systeem services

Er zijn systeem services die in elk cluster worden gemaakt en die de platform mogelijkheden van Service Fabric bieden.

**Naming Service**: elk service Fabric cluster heeft een Naming Service, waarmee service namen worden omgezet naar een locatie in het cluster. U beheert de namen en eigenschappen van de service, zoals een Internet Domain Name System (DNS) voor het cluster. Clients communiceren veilig met een wille keurig knoop punt in het cluster door gebruik te maken van de Naming Service om een service naam en de locatie ervan op te lossen. Toepassingen worden binnen het cluster verplaatst. Dit kan bijvoorbeeld het gevolg zijn van fouten, resource verdeling of het wijzigen van het formaat van het cluster. U kunt Services en clients ontwikkelen die de huidige netwerk locatie omzetten. Clients verkrijgen het werkelijke IP-adres en de poort van de computer waarop deze momenteel wordt uitgevoerd.

Lees [communicatie met Services](service-fabric-connect-and-communicate-with-services.md) voor meer informatie over de api's voor client-en service communicatie die met de Naming Service werken.

**Image Store-service**: elke service Fabric cluster heeft een image Store-service waar geïmplementeerde toepassings pakketten met versie worden bewaard. Kopieer een toepassings pakket naar het Image Store en registreer vervolgens het toepassings type in dat toepassings pakket. Nadat het toepassings type is ingericht, maakt u er een benoemde toepassing van. U kunt de registratie van een toepassings type bij de Image Store-service ongedaan maken nadat alle benoemde toepassingen zijn verwijderd.

Lees [de ImageStoreConnectionString-instelling](service-fabric-image-store-connection-string.md) voor meer informatie over de image Store-service.

Lees het artikel [een toepassing implementeren](service-fabric-deploy-remove-applications.md) voor meer informatie over het implementeren van toepassingen voor de image Store-service.

**Failover Manager-service**: elk service Fabric cluster heeft een failover Manager-service die verantwoordelijk is voor de volgende acties:

 - Voert functies uit die betrekking hebben op hoge Beschik baarheid en consistentie van services.
 - Hiermee worden de toepassings-en cluster upgrades georchestrationeerd.
 - Communiceert met andere systeem onderdelen.

**Repair Manager-service**: dit is een optionele systeem service waarmee herstel acties op een cluster op een veilige, geautomatiseerde en transparante manier kunnen worden uitgevoerd. Repair Manager wordt gebruikt in:

   - Onderhoud van Azure wordt uitgevoerd op [Silver en Gold duurzaamheid](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Azure service Fabric-clusters.
   - Herstel acties uitvoeren voor [patch Orchestration-toepassing](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Implementatie-en toepassings modellen

Als u uw services wilt implementeren, moet u beschrijven hoe ze moeten worden uitgevoerd. Service Fabric ondersteunt drie verschillende implementatie modellen:

### <a name="resource-model-preview"></a>Resource model (preview-versie)

Service Fabric resources zijn alles wat afzonderlijk kan worden geïmplementeerd in Service Fabric; inclusief toepassingen, services, netwerken en volumes. Resources worden gedefinieerd met behulp van een JSON-bestand dat kan worden geïmplementeerd in een cluster eindpunt.  Voor Service Fabric net wordt het Azure resource model-schema gebruikt. Een YAML-bestands schema kan ook worden gebruikt om eenvoudig definitie bestanden te schrijven. Resources kunnen overal worden geïmplementeerd Service Fabric worden uitgevoerd. Het resource model is de eenvoudigste manier om uw Service Fabric-toepassingen te beschrijven. De belangrijkste focus is het eenvoudig implementeren en beheren van container Services. Lees [Introduction to the service Fabric resource model](../service-fabric-mesh/service-fabric-mesh-service-fabric-resources.md)voor meer informatie.

### <a name="native-model"></a>Systeem eigen model

Het systeem eigen toepassings model biedt uw toepassingen volledige toegang tot Service Fabric op laag niveau. Toepassingen en services worden gedefinieerd als geregistreerde typen in XML-manifest bestanden.

Het systeem eigen model ondersteunt de Reliable Services-en Reliable Actors frameworks, waarmee toegang wordt geboden tot de Service Fabric runtime-Api's en Cluster beheer-Api's in C# en Java. Het systeem eigen model biedt ook ondersteuning voor wille keurige containers en uitvoer bare bestanden. Het systeem eigen model wordt niet ondersteund in de [service Fabric-omgeving](../service-fabric-mesh/service-fabric-mesh-overview.md).

**Reliable Services**: een API voor het bouwen van stateless en stateful Services. Stateful Services slaan hun status op in betrouw bare verzamelingen, zoals een woorden lijst of een wachtrij. U kunt ook verschillende communicatie stacks, zoals web-API en Windows Communication Foundation (WCF), aansluiten.

**Reliable actors**: een API voor het bouwen van stateless en stateful objecten via het virtuele actor-programmeer model. Dit model is handig als u veel onafhankelijke reken eenheden of status hebt. In dit model wordt gebruikgemaakt van een model voor het maken van een threading. het is daarom het beste om te voor komen dat code wordt aangeroepen voor andere actors of services, omdat een individuele actor andere binnenkomende aanvragen niet kan verwerken totdat alle uitgaande aanvragen zijn voltooid.

U kunt ook uw bestaande toepassingen uitvoeren op Service Fabric:

**Containers**: Service Fabric ondersteunt de implementatie van docker-containers op Linux-en Windows Server-containers in windows server 2016, samen met ondersteuning voor de isolatie modus van Hyper-V. In het Service Fabric [toepassings model](service-fabric-application-model.md)vertegenwoordigt een container een toepassingshost waarin meerdere service replica's worden geplaatst. Service Fabric kunt elke container uitvoeren en het scenario is vergelijkbaar met het uitvoer bare gast scenario, waarbij u een bestaande toepassing in een container inpakt. Daarnaast kunt u ook [service Fabric Services in containers uitvoeren](service-fabric-services-inside-containers.md) .

**Uitvoer bare gast bestanden**: u kunt elk type code uitvoeren, zoals Node.js, Python, Java of C++ in azure service Fabric als een service. Service Fabric verwijst naar deze typen services als uitvoer bare gast bestanden, die worden behandeld als stateless Services. De voor delen van het uitvoeren van een uitvoerbaar gast bestand in een Service Fabric cluster zijn onder andere hoge Beschik baarheid, status controle, beheer van toepassings levenscyclus, hoge densiteit en detectie.

Lees de [een programmeer model kiezen voor uw service](service-fabric-choose-framework.md) -artikel voor meer informatie.

### <a name="docker-compose"></a>Docker opstellen 

[Docker opstellen](https://docs.docker.com/compose/) maakt deel uit van het docker-project. Service Fabric biedt beperkte ondersteuning voor [het implementeren van toepassingen met behulp van het model docker opstellen](service-fabric-docker-compose.md).

## <a name="environments"></a>Omgevingen

Service Fabric is een open-source platform technologie waarmee verschillende services en producten zijn gebaseerd. Micro soft biedt de volgende opties:

 - **Azure service Fabric mesh**: een volledig beheerde service voor het uitvoeren van service Fabric toepassingen in Microsoft Azure.
 - **Azure service Fabric**: het gehoste service Fabric cluster van Azure. Het biedt integratie tussen Service Fabric en de Azure-infra structuur, samen met upgrade-en configuratie beheer van Service Fabric clusters.
 - **Service Fabric standalone**: een set hulpprogram ma's voor installatie en configuratie om [service Fabric-clusters overal](./service-fabric-deploy-anywhere.md) (on-premises of op een Cloud provider) te implementeren. Niet beheerd door Azure.
 - **Service Fabric Development cluster**: biedt een lokale ontwikkel ervaring op Windows, Linux of Mac voor het ontwikkelen van service Fabric toepassingen.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Ondersteunings matrix voor omgeving, Framework en implementatie model

Verschillende omgevingen hebben verschillende ondersteunings niveaus voor frameworks en implementatie modellen. In de volgende tabel worden de ondersteunde combi Naties van Framework en implementatie modellen beschreven.

| Type toepassing | Beschreven door | Azure Service Fabric Mesh | Azure Service Fabric-clusters (elk besturings systeem)| Lokaal cluster | Zelfstandig cluster |
|---|---|---|---|---|---|
| Service Fabric mesh-toepassingen | Resource model (YAML & JSON) | Ondersteund |Niet ondersteund | Windows: ondersteund, Linux en Mac: niet ondersteund | Windows-niet ondersteund |
|Service Fabric systeem eigen toepassingen | Systeem eigen toepassings model (XML) | Niet ondersteund| Ondersteund|Ondersteund|Windows: ondersteund|

In de volgende tabel worden de verschillende toepassings modellen en de hulp middelen beschreven die voor hen bestaan, vergeleken met Service Fabric.

| Type toepassing | Beschreven door | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric mesh-toepassingen | Resource model (YAML & JSON) | VS 2017 |Niet ondersteund |Niet ondersteund | Alleen ondersteunde mesh-omgeving | Niet ondersteund|
|Service Fabric systeem eigen toepassingen | Systeem eigen toepassings model (XML) | VS 2017 en VS 2015| Ondersteund|Ondersteund|Ondersteund|Ondersteund|

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Service Fabric:

* [Overzicht van Service Fabric](service-fabric-overview.md)
* [Waarom een microservices-benadering voor het ontwikkelen van toepassingen?](service-fabric-overview-microservices.md)
* [Toepassingsscenario's](service-fabric-application-scenarios.md)

Meer informatie over Service Fabric mesh:

* [Overzicht van Service Fabric mesh](../service-fabric-mesh/service-fabric-mesh-overview.md)
