---
title: Verschillen tussen Cloud Services en Service Fabric
description: Een conceptueel overzicht voor het migreren van toepassingen van Cloud Services naar Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: c7e7d346b5a39a262d1d55265becadb1c718cc04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575768"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Meer informatie over de verschillen tussen Cloud Services en Service Fabric voordat u toepassingen migreert.
Microsoft Azure Service Fabric is het Cloud toepassings platform van de volgende generatie voor zeer schaal bare, zeer betrouw bare gedistribueerde toepassingen. Het bevat een groot aantal nieuwe functies voor het verpakken, implementeren, bijwerken en beheren van gedistribueerde Cloud toepassingen. 

Dit is een inleidende hand leiding voor het migreren van toepassingen van Cloud Services naar Service Fabric. Het richt zich voornamelijk op architectuur-en ontwerp verschillen tussen Cloud Services en Service Fabric.

## <a name="applications-and-infrastructure"></a>Toepassingen en infra structuur
Een fundamenteel verschil tussen Cloud Services en Service Fabric is de relatie tussen Vm's, werk belastingen en toepassingen. Hier is een workload gedefinieerd als de code die u schrijft om een specifieke taak uit te voeren of een service te bieden.

* **Cloud Services is het implementeren van toepassingen als Vm's.** De code die u schrijft, is nauw gekoppeld aan een VM-exemplaar, zoals een web-of worker-rol. Voor het implementeren van een werk belasting in Cloud Services is het implementeren van een of meer VM-exemplaren die de werk belasting uitvoeren. Er zijn geen schei ding van toepassingen en Vm's, en daarom is er geen formele definitie van een toepassing. Een toepassing kan worden beschouwd als een reeks web-of worker-instanties binnen een Cloud Services-implementatie of als een volledige Cloud Services-implementatie. In dit voor beeld wordt een toepassing weer gegeven als een set rolinstanties.

![Cloud Services toepassingen en topologie][1]

* **Service Fabric is het implementeren van toepassingen op bestaande Vm's of machines met Service Fabric in Windows of Linux.** De services die u schrijft, zijn volledig losgekoppeld van de onderliggende infra structuur, die wordt afgeleid van het Service Fabric toepassings platform, zodat een toepassing in meerdere omgevingen kan worden geïmplementeerd. Een werk belasting in Service Fabric wordt een ' service ' genoemd en een of meer services worden gegroepeerd in een formeel gedefinieerde toepassing die wordt uitgevoerd op het Service Fabric toepassings platform. Meerdere toepassingen kunnen worden geïmplementeerd op één Service Fabric cluster.

![Service Fabric toepassingen en topologie][2]

Service Fabric zichzelf is een Application platform-laag die wordt uitgevoerd op Windows of Linux, terwijl Cloud Services een systeem is voor de implementatie van door Azure beheerde virtuele machines waaraan werk belastingen zijn gekoppeld.
Het Service Fabric toepassings model heeft een aantal voor delen:

* Snelle implementatie tijden. Het maken van VM-exemplaren kan tijdrovend zijn. In Service Fabric worden Vm's slechts eenmaal geïmplementeerd om een cluster te vormen dat als host fungeert voor het Service Fabric toepassings platform. Vanaf dat moment kunnen toepassings pakketten zeer snel worden geïmplementeerd in het cluster.
* Hosting met hoge dichtheid. In Cloud Services wordt een VM van een werk rollen gehost op één werk belasting. In Service Fabric zijn toepassingen gescheiden van de virtuele machines waarop ze worden uitgevoerd, wat betekent dat u een groot aantal toepassingen kunt implementeren op een klein aantal virtuele machines, wat de totale kosten voor grotere implementaties kan verlagen.
* Het Service Fabric-platform kan overal worden uitgevoerd met Windows Server-of Linux-machines, of het nu Azure of on-premises is. Het platform biedt een abstractie laag voor de onderliggende infra structuur, zodat uw toepassing kan worden uitgevoerd in verschillende omgevingen. 
* Gedistribueerd toepassings beheer. Service Fabric is een platform dat niet alleen als host fungeert voor gedistribueerde toepassingen, maar ook voor het beheren van de levens cyclus onafhankelijk van de hosting-VM of de levens cyclus van machines.

## <a name="application-architecture"></a>Toepassingsarchitectuur
De architectuur van een Cloud Services toepassing omvat meestal talloze externe service afhankelijkheden, zoals Service Bus, Azure Table en Blob Storage, SQL, redis en andere voor het beheren van de status en de gegevens van een toepassing en communicatie tussen Web-en werk rollen in een Cloud Services-implementatie. Een voor beeld van een complete Cloud Services-toepassing ziet er ongeveer als volgt uit:  

![Cloud Services architectuur][9]

Service Fabric toepassingen kunnen er ook voor kiezen om dezelfde externe services in een volledige toepassing te gebruiken. Met dit voor beeld Cloud Services architectuur, het eenvoudigste migratie traject van Cloud Services naar Service Fabric, is om alleen de implementatie van Cloud Services te vervangen door een Service Fabric toepassing, waardoor de algehele architectuur hetzelfde blijft. De web-en werk rollen kunnen worden geporteerd naar Service Fabric stateless Services met minimale code wijzigingen.

![Service Fabric architectuur na eenvoudige migratie][10]

In deze fase moet het systeem blijven werken zoals voorheen. Als u gebruikmaakt van stateful functies van Service Fabric, kunnen externe status archieven worden geinternal als stateful Services, indien van toepassing. Dit is meer betrokken dan een eenvoudige migratie van web-en werk rollen naar Service Fabric stateless Services, omdat hiervoor aangepaste services moeten worden geschreven die vergelijk bare functionaliteit bieden voor uw toepassing als de externe services. De voor delen hiervan zijn: 

* Externe afhankelijkheden verwijderen 
* De implementatie-, beheer-en upgrade modellen worden aaneengeschakeld. 

Een voor beeld van een architectuur van internalizing deze services kan er als volgt uitzien:

![Architectuur Service Fabric na volledige migratie][11]

## <a name="communication-and-workflow"></a>Communicatie en werk stroom
De meeste Cloud service toepassingen bestaan uit meer dan één laag. Op dezelfde manier bestaat een Service Fabric toepassing uit meer dan één service (meestal veel services). Twee veelvoorkomende communicatie modellen zijn directe communicatie en via een externe, duurzame opslag.

### <a name="direct-communication"></a>Directe communicatie
Met directe communicatie kunnen lagen rechtstreeks communiceren via het eind punt dat door elke laag wordt weer gegeven. In stateless omgevingen, zoals Cloud Services, betekent dit dat u een exemplaar van een VM-rol selecteert, wille keurig of Round Robin om de belasting te verdelen en direct verbinding te maken met het eind punt.

![Cloud Services directe communicatie][5]

 Directe communicatie is een gemeen schappelijk communicatie model in Service Fabric. Het belangrijkste verschil tussen Service Fabric en Cloud Services is dat in Cloud Services u verbinding maakt met een virtuele machine, terwijl u in Service Fabric verbinding maakt met een service. Dit is een belang rijk onderscheid om een paar redenen:

* Services in Service Fabric zijn niet gebonden aan de virtuele machines die deze hosten; Services kunnen zich in het cluster voordoen, en zullen naar verwachting om verschillende redenen worden omzeild: resource verdeling, failover, toepassings-en infrastructuur upgrades en beperkingen voor plaatsing of laden. Dit betekent dat het adres van een service-exemplaar op elk gewenst moment kan worden gewijzigd. 
* Een virtuele machine in Service Fabric kan meerdere services hosten, elk met unieke eind punten.

Service Fabric biedt een service detectie mechanisme, de Naming Service, die kan worden gebruikt voor het omzetten van eindpunt adressen van services. 

![Diagram dat laat zien hoe Service Fabric een service detectie mechanisme biedt, de Naming Service, die kan worden gebruikt voor het omzetten van eindpunt adressen van services.][6]

### <a name="queues"></a>Wachtrijen
Een gemeen schappelijk communicatie mechanisme tussen lagen in stateless omgevingen, zoals Cloud Services, is het gebruik van een externe opslag wachtrij om werk taken van de ene laag naar de andere te blijvend. Een veelvoorkomend scenario is een weblaag die taken verzendt naar een Azure-wachtrij of Service Bus waar werk rollen instanties de taken in de wachtrij kunnen plaatsen en verwerken.

![Cloud Services wachtrij communicatie][7]

Hetzelfde communicatie model kan worden gebruikt in Service Fabric. Dit kan handig zijn bij het migreren van een bestaande Cloud Services-toepassing naar Service Fabric. 

![Service Fabric directe communicatie][8]

## <a name="parity"></a>Parity
[Cloud Services is vergelijkbaar met Service fabric in de mate van beheer en gebruiks gemak, maar het is nu een verouderde service en service Fabric wordt aanbevolen voor nieuwe ontwikkeling](/azure/architecture/guide/technology-choices/compute-decision-tree). Hier volgt een API-vergelijking:


| **Cloud Service-API** | **Service Fabric-API** | **Opmerkingen** |
| --- | --- | --- |
| RoleInstance. GetID | FabricRuntime. GetNodeContext. NodeId of. NodeName | ID is een eigenschap van de knooppunt naam |
| RoleInstance. GetFaultDomain | FabricClient. QueryManager. GetNodeList | Filteren op knooppunt naam en eigenschap FD gebruiken |
| RoleInstance. GetUpgradeDomain | FabricClient. QueryManager. GetNodeList | Filteren op knooppunt naam en de eigenschap upgrade gebruiken |
| RoleInstance. GetInstanceEndpoints | FabricRuntime. GetActivationContext of Naming (ResolveService) | CodePackageActivationContext die wordt weer gegeven door FabricRuntime. GetActivationContext en binnen de replica's via ServiceInitializationParameters. CodePackageActivationContext dat is gegeven tijdens. Initialiseren |
| RoleEnvironment.GetRoles | FabricClient. QueryManager. GetNodeList | Als u hetzelfde Sorteer filter op type wilt uitvoeren, kunt u de lijst met knooppunt typen uit het cluster manifest ophalen via FabricClient. ClusterManager. GetClusterManifest en daar de rol/knooppunt typen uit te pakken. |
| RoleEnvironment.GetIsAvailable | Connect-WindowsFabricCluster of een FabricRuntime punt maken naar een bepaald knoop punt | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext. log/temp/werk | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext. log/temp/werk | * |
| LocalResource.GetRootPath | CodePackageActivationContext. log/temp/werk | * |
| Role. GetInstances | FabricClient. QueryManager. GetNodeList of ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime. GetActivationContext of Naming (ResolveService) | * |

## <a name="next-steps"></a>Volgende stappen
Het eenvoudigste migratie traject van Cloud Services naar Service Fabric is om alleen de implementatie van Cloud Services te vervangen door een Service Fabric toepassing, waardoor de algehele architectuur van uw toepassing ongeveer hetzelfde blijft. Het volgende artikel bevat een hand leiding voor het converteren van een web-of worker-rol naar een Service Fabric stateless service.

* [Eenvoudige migratie: een web-of worker-rol converteren naar een Service Fabric stateless service](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png