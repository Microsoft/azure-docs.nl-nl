---
title: Aangepaste Service Fabric status rapporten toevoegen
description: Hierin wordt beschreven hoe u aangepaste status rapporten naar Azure Service Fabric status entiteiten verzendt. Geeft aanbevelingen voor het ontwerpen en implementeren van kwaliteits rapporten.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: 6df434610a8f595ecca7f16e31f8a302373b02f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001861"
---
# <a name="add-custom-service-fabric-health-reports"></a>Aangepaste Service Fabric status rapporten toevoegen
Azure Service Fabric introduceert een [status model](service-fabric-health-introduction.md) dat is ontworpen voor het markeren van slechte cluster-en toepassings voorwaarden voor specifieke entiteiten. Het status model gebruikt **status rapporten** (systeem onderdelen en watchdog). Het doel is eenvoudig en snel te diagnosticeren en te herstellen. Service schrijvers moeten vooraf denken over de status. Elke voor waarde die de status van invloed kan hebben, moet worden gerapporteerd op, met name als het kan helpen om problemen in de buurt van de hoofdmap te markeren. De status informatie kan tijd en moeite besparen bij fout opsporing en onderzoek. Het nut is vooral duidelijk wanneer de service op schaal wordt uitgevoerd in de Cloud (privé of Azure).

De Service Fabric rapporters bewaken de geïdentificeerde voor waarden. Ze rapporteren over deze voor waarden op basis van hun lokale weer gave. De [Health Store](service-fabric-health-introduction.md#health-store) verzamelt status gegevens die door alle rapporten worden verzonden om te bepalen of entiteiten wereld wijd in orde zijn. Het model is bedoeld als rijk, flexibel en eenvoudig te gebruiken. De kwaliteit van de status rapporten bepaalt de nauw keurigheid van de status weergave van het cluster. Fout-positieven die onjuiste slechte problemen weer geven, kunnen een negatieve invloed hebben op upgrades of andere services die gebruikmaken van status gegevens. Voor beelden van dergelijke services zijn Reparatie Services en waarschuwings mechanismen. Daarom is er een aantal Denkingen nodig om rapporten te bieden waarin de voor waarden van belang op de beste manier worden vastgelegd.

Voor het ontwerpen en implementeren van Health Reporting moeten watchdog en systeem onderdelen:

* Definieer de voor waarde waarin ze zijn geïnteresseerd, de manier waarop deze wordt gecontroleerd en de invloed op de functionaliteit van het cluster of de toepassing. Bepaal op basis van deze informatie de status rapport eigenschap en de status.
* Bepaal de [entiteit](service-fabric-health-introduction.md#health-entities-and-hierarchy) waarop het rapport van toepassing is.
* Bepaal waar de rapportage plaatsvindt, vanuit de service of vanuit een interne of externe watchdog.
* Definieer een bron die wordt gebruikt om het rapport te identificeren.
* Kies een rapportage strategie, hetzij periodiek of op overgangen. De aanbevolen manier is periodiek, omdat hiervoor eenvoudiger code nodig is en minder gevoelig is voor fouten.
* Bepaal hoe lang het rapport voor slechte condities in de Health Store moet blijven en hoe het moet worden gewist. Gebruik deze informatie om te bepalen hoe lang het rapport actief is en wat er moet worden verwijderd.

Zoals vermeld, kan rapportage worden uitgevoerd vanaf:

* De bewaakte Service Fabric service replica.
* Interne watchdog worden geïmplementeerd als een Service Fabric-service (bijvoorbeeld een Service Fabric stateless service waarmee voor waarden en rapporten van problemen worden gecontroleerd). De watchdog kunnen een of meer knoop punten implementeren of kunnen worden stelt teamlid aan de bewaakte service.
* Interne watchdog die worden uitgevoerd op de Service Fabric knoop punten, maar die *niet* zijn geïmplementeerd als service Fabric Services.
* Externe watchdog die de bron van *buiten* het service Fabric cluster testen (bijvoorbeeld bewakings service zoals Gomez).

> [!NOTE]
> In het vak wordt het cluster gevuld met status rapporten die worden verzonden door de systeem onderdelen. Meer informatie vindt [u in systeem status rapporten gebruiken om problemen op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). De gebruikers rapporten moeten worden verzonden naar [status entiteiten](service-fabric-health-introduction.md#health-entities-and-hierarchy) die al zijn gemaakt door het systeem.
> 
> 

Zodra het ontwerp voor status rapportage duidelijk is, kunnen status rapporten gemakkelijk worden verzonden. U kunt [FabricClient](/dotnet/api/system.fabric.fabricclient) gebruiken om de status te rapporteren als het cluster niet is [beveiligd](service-fabric-cluster-security.md) of als de Fabric-client beheerders bevoegdheden heeft. Rapportage kan worden uitgevoerd via de API met behulp van [FabricClient. HealthManager. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), via Power shell of via rest. Configuratie knoppen batch rapporten voor betere prestaties.

> [!NOTE]
> Rapport status is synchroon en bevat alleen het validatie werk aan de client zijde. Het feit dat het rapport wordt geaccepteerd door de Health-client of de `Partition` or- `CodePackageActivationContext` objecten betekenen niet dat het wordt toegepast in de Store. Het wordt asynchroon verzonden en kan eventueel worden gebatcheerd met andere rapporten. De verwerking op de server kan nog steeds mislukken: het Volg nummer kan worden verouderd. de entiteit waarop het rapport moet worden toegepast, is verwijderd, enzovoort.
> 
> 

## <a name="health-client"></a>Health-client
De status rapporten worden verzonden naar de Health Manager via een Health-client, die zich binnen de Fabric-client bevindt. De Health Manager slaat rapporten op in de Health Store. De Health-client kan worden geconfigureerd met de volgende instellingen:

* **HealthReportSendInterval**: de vertraging tussen het moment dat het rapport wordt toegevoegd aan de client en de tijd die wordt verzonden naar de Health Manager. Wordt gebruikt voor het uitvoeren van batch rapporten in één bericht, in plaats van een bericht voor elk rapport te verzenden. De batch verwerking verbetert de prestaties. Standaard: 30 seconden.
* **HealthReportRetrySendInterval**: het interval waarmee de Health-client samengevoegde status rapporten opnieuw verzendt naar de Health Manager. Standaard: 30 seconden, mini maal: 1 seconde.
* **HealthOperationTimeout**: de time-outperiode voor een rapport bericht dat naar de Health Manager wordt verzonden. Als er een time-out optreedt voor een bericht, wordt het door de Health-client opnieuw geprobeerd totdat de Health Manager bevestigt dat het rapport is verwerkt. Standaard: twee minuten.

> [!NOTE]
> Wanneer de rapporten in batch worden opgenomen, moet de Fabric-client voor ten minste de HealthReportSendInterval worden bewaard om ervoor te zorgen dat ze worden verzonden. Als het bericht verloren is gegaan of als de Health Manager deze niet kan Toep assen wegens tijdelijke fouten, moet de Fabric-client leven langer worden bewaard om het een nieuwe poging te geven.
> 
> 

De buffering op de client neemt de uniekheid van de rapporten in overweging. Als bijvoorbeeld een bepaalde slechte rapporter 100 rapporten per seconde rapporteert over dezelfde eigenschap van dezelfde entiteit, worden de rapporten vervangen door de laatste versie. Er bestaat Maxi maal één rapport in de client wachtrij. Als batch verwerking is geconfigureerd, is het aantal rapporten dat naar de Health Manager wordt verzonden, slechts één per verzend interval. Dit rapport is het laatst toegevoegde rapport, dat de meest actuele status van de entiteit weerspiegelt.
Geef configuratie parameters `FabricClient` op wanneer deze worden gemaakt door [FabricClientSettings](/dotnet/api/system.fabric.fabricclientsettings) door te geven met de gewenste waarden voor aan de status gerelateerde vermeldingen.

In het volgende voor beeld wordt een Fabric-client gemaakt en wordt opgegeven dat de rapporten moeten worden verzonden wanneer ze worden toegevoegd. Bij time-outs en fouten die opnieuw kunnen worden geprobeerd, wordt elke 40 seconden opnieuw geprobeerd.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

We raden u aan de standaard instellingen voor de Fabric-client te houden, die zijn ingesteld `HealthReportSendInterval` op 30 seconden. Deze instelling zorgt voor optimale prestaties vanwege batch verwerking. Voor kritieke rapporten die zo snel mogelijk moeten worden verzonden, gebruikt u `HealthReportSendOptions` direct `true` in [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) -API. Directe rapporten slaan het interval voor batch verwerking over. Gebruik deze vlag met zorg; We willen zo mogelijk gebruikmaken van de batch verwerking voor de Health-client. Direct verzenden is ook handig wanneer de Fabric-client wordt afgesloten (bijvoorbeeld omdat het proces een ongeldige status heeft bepaald en moet worden afgesloten om neven effecten te voor komen). Het zorgt voor een beste verzen ding van de verzamelde rapporten. Wanneer één rapport wordt toegevoegd met de vlag Immediate, worden de verzamelde rapporten sinds de laatste verzen ding door de Health-client in batches opgenomen.

U kunt dezelfde para meters opgeven wanneer een verbinding met een cluster wordt gemaakt via Power shell. In het volgende voor beeld wordt een verbinding met een lokaal cluster gestart:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Net als bij API kunnen rapporten worden verzonden met behulp `-Immediate` van de switch die onmiddellijk moet worden verzonden, ongeacht de `HealthReportSendInterval` waarde.

Voor REST worden de rapporten verzonden naar de Service Fabric-gateway, die een interne Fabric-client heeft. Deze client is standaard geconfigureerd voor het verzenden van rapporten die elke 30 seconden worden gebatcheerd. U kunt de batch-interval wijzigen met de cluster configuratie-instelling `HttpGatewayHealthReportSendInterval` op `HttpGateway` . Zoals gezegd, is het een betere optie om de rapporten met `Immediate` waar te verzenden. 

> [!NOTE]
> Om ervoor te zorgen dat niet-geautoriseerde services de status niet kunnen rapporteren aan de entiteiten in het cluster, configureert u de server om alleen aanvragen van beveiligde clients te accepteren. Voor de `FabricClient` rapportage die wordt gebruikt, moet beveiliging zijn ingeschakeld om te kunnen communiceren met het cluster (bijvoorbeeld met Kerberos of verificatie op basis van een certificaat). Meer informatie over [cluster beveiliging](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Rapport vanuit Services met weinig bevoegdheid
Als Service Fabric Services geen beheerders toegang tot het cluster hebben, kunt u de status van entiteiten rapporteren vanuit de huidige context via `Partition` of `CodePackageActivationContext` .

* Gebruik [IStatelessServicePartition. ReportInstanceHealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) voor stateless Services om te rapporteren over het huidige service-exemplaar.
* Gebruik [IStatefulServicePartition. ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) voor stateful Services om te rapporteren over de huidige replica.
* Gebruik [IServicePartition. ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) om te rapporteren over de huidige partitie-entiteit.
* Gebruik [CodePackageActivationContext. ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) om te rapporteren over de huidige toepassing.
* Gebruik [CodePackageActivationContext. ReportDeployedApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) om te rapporteren over de huidige toepassing die op het huidige knoop punt is geïmplementeerd.
* Gebruik [CodePackageActivationContext. ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) om te rapporteren over een service pakket voor de toepassing die op het huidige knoop punt is geïmplementeerd.

> [!NOTE]
> Intern, de `Partition` en de `CodePackageActivationContext` bewaring van een Health-client die is geconfigureerd met de standaard instellingen. Zoals wordt uitgelegd voor de [Health-client](service-fabric-report-health.md#health-client), worden rapporten batches en verzonden naar een timer. De objecten moeten worden bewaard, zodat de kans bestaat dat het rapport wordt verzonden.
> 
> 

U kunt opgeven `HealthReportSendOptions` Wanneer u rapporten verzendt via `Partition` en `CodePackageActivationContext` status-api's. Als u kritieke rapporten hebt die zo snel mogelijk moeten worden verzonden, gebruikt u `HealthReportSendOptions` direct `true` . Directe rapporten slaan het batch interval van de interne Health-client over. Zoals eerder vermeld, kunt u deze vlag gebruiken. We willen zo mogelijk gebruikmaken van de batch verwerking voor de Health-client.

## <a name="design-health-reporting"></a>Ontwerp status rapportage
De eerste stap bij het genereren van rapporten van hoge kwaliteit is het identificeren van de voor waarden die van invloed kunnen zijn op de status van de service. Elke voor waarde die het mogelijk maakt om problemen in de service of het cluster te markeren wanneer het wordt gestart, of nog beter, voordat een probleem zich voordoet, kan er miljarden dollars besparen. De voor delen omvatten minder tijd, minder nacht uren die zijn besteed aan het onderzoeken en repareren van problemen en een hogere klant tevredenheid.

Als aan de voor waarden wordt voldaan, moeten watchdog-schrijvers de beste manier gebruiken om ze te bewaken voor een evenwicht tussen overhead en bruikbaarheid. Denk bijvoorbeeld aan een service met complexe berekeningen die gebruikmaken van enkele tijdelijke bestanden op een share. Een watchdog kan de share controleren om ervoor te zorgen dat er voldoende ruimte beschikbaar is. Er kan worden geluisterd naar meldingen van wijzigingen in bestanden of mappen. Er kan een waarschuwing worden weer gegeven als een drempel waarde wordt bereikt en er een fout melding wordt weer gegeven als de share vol is. Als er een waarschuwing wordt gegeven, kan een herstel systeem oudere bestanden op de share gaan opschonen. Als er een fout optreedt, kan een herstel systeem de service replica naar een ander knoop punt verplaatsen. Houd er rekening mee dat de conditie status wordt beschreven in de status voorwaarden: de status van de voor waarde die als goed kan worden beschouwd (OK) of beschadigd (waarschuwing of fout).

Zodra de bewakings gegevens zijn ingesteld, moet een watchdog-Writer bepalen hoe de watchdog moet worden geïmplementeerd. Als de voor waarden in de service kunnen worden bepaald, kan de watchdog deel uitmaken van de bewaakte service zelf. De service code kan bijvoorbeeld het gebruik van de share controleren en vervolgens rapporteren elke keer dat er wordt geprobeerd een bestand te schrijven. Het voor deel van deze benadering is dat rapportage eenvoudig is. Het is belang rijk om te voor komen dat de service-functionaliteit wordt beïnvloed door watchdog-fouten.

Rapportage vanuit de bewaakte service is niet altijd een optie. Een watchdog in de service kan de voor waarden mogelijk niet detecteren. Het kan zijn dat het niet mogelijk is de logica of gegevens te bepalen. De overhead van het bewaken van de voor waarden kan hoog zijn. De voor waarden zijn ook mogelijk niet specifiek voor een service, maar zijn van invloed op interacties tussen services. Een andere optie is om watchdog in het cluster als afzonderlijke processen te hebben. De watchdog bewaken de voor waarden en het rapport zonder dat dit van invloed is op de belangrijkste services. Deze watchdog kunnen bijvoorbeeld worden geïmplementeerd als stateless Services in dezelfde toepassing, geïmplementeerd op alle knoop punten of op dezelfde knoop punten als de service.

Soms is een watchdog die in het cluster wordt uitgevoerd, geen optie. Als de bewaakte toestand de beschik baarheid of de functionaliteit van de service is wanneer gebruikers deze zien, is het raadzaam om de watchdog op dezelfde locatie als de gebruikers clients te hebben. Daar kunnen ze de bewerkingen op dezelfde manier testen als de gebruikers. U kunt bijvoorbeeld een watchdog hebben die zich buiten het cluster bevindt, aanvragen afvraagt naar de service en de latentie en de juistheid van het resultaat controleren. (Voor een rekenmachine service kan bijvoorbeeld 2 + 2 in een redelijke hoeveelheid tijd 4 worden geretourneerd?)

Zodra de gegevens van de watchdog zijn voltooid, moet u beslissen over een bron-ID die deze uniek identificeert. Als meerdere watchdog van hetzelfde type in het cluster wonen, moeten ze rapporteren over verschillende entiteiten of, als ze rapporteren over dezelfde entiteit, een andere bron-ID of-eigenschap gebruiken. Op deze manier kunnen de rapporten naast elkaar bestaan. De eigenschap van het status rapport moet de bewaakte voor waarde vastleggen. (Voor het bovenstaande voor beeld is de eigenschap mogelijk **ShareSize**.) Als meerdere rapporten op dezelfde voor waarde van toepassing zijn, moet de eigenschap een deel van de dynamische informatie bevatten zodat rapporten naast elkaar kunnen bestaan. Als meerdere shares bijvoorbeeld moeten worden bewaakt, kan de naam van de eigenschap **ShareSize** zijn.

> [!NOTE]
> Gebruik *niet* de Health Store om de status informatie te bedenken. Alleen aan Health gerelateerde informatie moet worden gerapporteerd als status, aangezien deze informatie van invloed is op de status evaluatie van een entiteit. Het Health Store is niet ontworpen als een opslag voor algemeen gebruik. De status evaluatie logica wordt gebruikt voor het samen voegen van alle gegevens in de status. Het verzenden van informatie die niet gerelateerd is aan de status (zoals de rapportage status met een status van OK) heeft geen invloed op de geaggregeerde status, maar kan een negatieve invloed hebben op de prestaties van de Health Store.
> 
> 

Het volgende besluit punt is de entiteit die u wilt rapporteren. In de meeste gevallen identificeert de voor waarde de entiteit duidelijk. Kies de entiteit met de best mogelijke granulatie. Als een voor waarde van invloed is op alle replica's in een partitie, rapporteert u op de partitie, niet op de service. Er zijn hoek gevallen waarin u meer Denkingen nodig hebt, maar. Als de voor waarde van invloed is op een entiteit, zoals een replica, maar de wens is om de voor waarde te markeren voor meer dan de duur van de replica, moet deze op de partitie worden gerapporteerd. Als de replica wordt verwijderd, worden alle rapporten in de Health Store opschonen. Watchdog-schrijvers moeten denken over de levens duur van de entiteit en het rapport. Het moet duidelijk zijn wanneer een rapport moet worden opgeschoond vanuit een archief (bijvoorbeeld wanneer een fout op een entiteit niet meer van toepassing is).

Laten we eens kijken naar een voor beeld waarin de hierboven beschreven punten worden geplaatst. Houd rekening met een Service Fabric toepassing die bestaat uit een Master stateful permanente service en secundaire stateless services die zijn geïmplementeerd op alle knoop punten (één secundair Service type voor elk type taak). De Master heeft een verwerkings wachtrij met opdrachten die door secundaire zones moeten worden uitgevoerd. De secundaire zones voeren de inkomende aanvragen uit en verzenden signalen van de bevestiging. Een voor waarde die kan worden bewaakt, is de lengte van de verwerkings wachtrij van de hoofd server. Als de lengte van de hoofd wachtrij een drempel waarde bereikt, wordt er een waarschuwing gerapporteerd. De waarschuwing geeft aan dat de secundairen de belasting niet kan afhandelen. Als de wachtrij de maximum lengte bereikt en de opdrachten worden verwijderd, wordt een fout gerapporteerd, omdat de service niet kan worden hersteld. De rapporten kunnen de eigenschap **QueueStatus** hebben. De watchdog bevindt zich in de service en wordt regel matig verzonden op de primaire hoofd replica. De TTL duurt twee minuten en wordt elke 30 seconden periodiek verzonden. Als de primaire gaat omlaag, wordt het rapport automatisch uit de Store opgeruimd. Als de service replica actief is, maar een deadlock is of andere problemen ondervindt, verloopt het rapport in de Health Store. In dit geval wordt de entiteit geëvalueerd bij fout.

Een andere voor waarde die kan worden bewaakt is de taak uitvoerings tijd. Het hoofd project distribueert taken naar de secundaire zones op basis van het taak type. Afhankelijk van het ontwerp kan het model de secundaire zones voor taak status pollen. Er kan ook worden gewacht op de secundaire zones voor het verzenden van bevestigings signalen wanneer deze zijn voltooid. In het tweede geval moet ervoor worden gezorgd dat er sprake is van het detecteren van situaties waarin de imvallen van de secundaire zones of berichten verloren zijn gegaan. De ene optie is voor het hoofd om een ping-aanvraag naar dezelfde secundaire te verzenden, die de status ervan terugstuurt. Als er geen status wordt ontvangen, beschouwt de Master een fout en wordt de taak opnieuw gepland. Dit gedrag gaat ervan uit dat de taken idempotent zijn.

De bewaakte voor waarde kan worden vertaald als een waarschuwing als de taak niet in een bepaalde tijd wordt **uitgevoerd (bijvoorbeeld** 10 minuten). Als de taak niet in de tijd is voltooid (**T2** bijvoorbeeld 20 minuten), kan de bewaakte voor waarde worden omgezet als fout. Deze rapportage kan op verschillende manieren worden uitgevoerd:

* De primaire hoofd replica-rapporten worden op zichzelf periodiek gerapporteerd. U kunt één eigenschap hebben voor alle taken in de wachtrij die in behandeling zijn. Als ten minste één taak langer duurt, is de status van het rapport in de eigenschap **PendingTasks** een waarschuwing of fout, indien van toepassing. Als er geen taken zijn die in behandeling zijn of als de uitvoering van alle taken is gestart, is de status van het rapport OK. De taken zijn permanent. Als de primaire laag uitvalt, kan de zojuist gepromoveerde primaire op de juiste manier blijven rapporteren.
* Een ander watchdog-proces (in de Cloud of extern) controleert de taken (van buiten, op basis van het gewenste taak resultaat) om na te gaan of ze zijn voltooid. Als ze de drempel waarden niet respecteren, wordt een rapport verzonden op de Master-service. Er wordt ook een rapport verzonden voor elke taak met daarin de taak-id, zoals **PendingTask + taskId**. Rapporten moeten alleen worden verzonden met een onjuiste status. Stel time-to-Live in op een paar minuten en markeer de rapporten die moeten worden verwijderd wanneer deze verlopen om het opschonen te garanderen.
* Het secundaire item dat een taak rapport uitvoert wanneer het langer duurt dan verwacht om het uit te voeren. Er wordt gerapporteerd over het service-exemplaar op de eigenschap **PendingTasks**. Het rapport bevat een overzicht van het service-exemplaar met problemen, maar er wordt geen situatie vastgelegd waarin het exemplaar sterft. De rapporten worden vervolgens opgeschoond. Het rapport kan worden gerapporteerd op de secundaire service. Als de secundaire taak is voltooid, wordt het rapport uit de Store gewist. In het rapport wordt niet de situatie vastgelegd waarin het bevestigings bericht verloren gaat en de taak wordt niet voltooid vanuit het weergave punt van het model.

De rapportage wordt echter uitgevoerd in de hierboven beschreven gevallen, de rapporten worden vastgelegd in de status van de toepassing wanneer de status wordt geëvalueerd.

## <a name="report-periodically-vs-on-transition"></a>Rapporten regel matig versus over overgang
Met behulp van het status rapport model kunnen met watchdog rapporten periodiek of op overgangen worden verzonden. De aanbevolen manier voor watchdog-rapportage is periodiek, omdat de code veel eenvoudiger en minder gevoelig voor fouten is. De watchdog moeten zo eenvoudig mogelijk zijn om te voor komen dat er fouten optreden die onjuiste rapporten activeren. Onjuiste *slechte* rapporten hebben betrekking op status evaluaties en scenario's op basis van de status, inclusief upgrades. Onjuiste *rapporten verbergen* problemen in het cluster. Dit is niet gewenst.

Voor periodieke rapportage kan de watchdog worden geïmplementeerd met een timer. Bij een timer retour aanroep kan de watchdog de status controleren en een rapport verzenden op basis van de huidige status. U hoeft niet te zien welk rapport er eerder is verzonden of om optimalisaties te maken in het kader van berichten. De Health-client heeft logica voor batches om de prestaties te helpen. Hoewel de Health-client actief wordt gehouden, worden deze intern opnieuw geprobeerd, totdat het rapport door de Health Store wordt bevestigd of de watchdog een nieuwer rapport genereert met dezelfde entiteit, eigenschap en bron.

Voor rapportage over overgangen is een zorgvuldige verwerking van de status vereist. De watchdog controleert enkele voor waarden en rapporten alleen wanneer de voor waarden veranderen. De kant van deze benadering is dat er minder rapporten nodig zijn. Het nadeel is dat de logica van de watchdog complex is. De watchdog moet de voor waarden of rapporten behouden, zodat deze kunnen worden geïnspecteerd om status wijzigingen te bepalen. Bij failover moet er rekening mee worden gehouden met rapporten die zijn toegevoegd, maar nog niet naar het Health Store zijn verzonden. Het Volg nummer moet steeds groter worden. Als dat niet het geval is, worden de rapporten afgewezen als verouderd. In de zeldzame gevallen waarin gegevens verlies wordt gemaakt, kan het nodig zijn om de status van de rapporter en de status van de Health Store te synchroniseren.

Rapportage over overgangen is zinvol voor services die op zichzelf worden gerapporteerd via `Partition` of `CodePackageActivationContext` . Wanneer het lokale object (replica of geïmplementeerde service pakket/geïmplementeerde toepassing) wordt verwijderd, worden alle bijbehorende rapporten ook verwijderd. Deze automatische opschoning versoepelt de nood zaak van synchronisatie tussen rereporter en Health Store. Als het rapport voor de bovenliggende partitie of bovenliggende toepassing is, moet u ervoor zorgen dat er een failover wordt uitgevoerd om verouderde rapporten in de Health Store te voor komen. Logica moet worden toegevoegd om de juiste status te behouden en het rapport uit de Store te wissen wanneer het niet meer nodig is.

## <a name="implement-health-reporting"></a>Status rapportage implementeren
Zodra de gegevens van de entiteit en het rapport duidelijk zijn, kunnen de verzend status rapporten worden uitgevoerd via de API, Power shell of de REST.

### <a name="api"></a>API
Als u wilt rapporteren via de API, moet u een status rapport maken dat specifiek is voor het entiteits type waarover ze willen rapporteren. Geef het rapport een Health-client. U kunt ook een status informatie maken en deze door geven om rapportage methoden te corrigeren op `Partition` of `CodePackageActivationContext` te rapporteren over huidige entiteiten.

In het volgende voor beeld wordt periodieke rapportage weer gegeven van een watchdog in het cluster. De watchdog controleert of een externe bron kan worden geopend vanuit een knoop punt. De resource is nodig voor een service manifest binnen de toepassing. Als de resource niet beschikbaar is, kunnen de andere services in de toepassing nog steeds goed functioneren. Daarom wordt het rapport elke 30 seconden verzonden op de entiteit van het geïmplementeerde service pakket.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Verzend status rapporten met **Send-ServiceFabric *EntityType* HealthReport**.

In het volgende voor beeld wordt periodieke rapportage over CPU-waarden op een knoop punt weer gegeven. De rapporten worden elke 30 seconden verzonden en ze hebben een tijd om twee minuten live te zijn. Als de rapporten verlopen, worden er problemen gemeld, waardoor het knoop punt wordt geëvalueerd met een fout. Wanneer de CPU boven een drempel waarde ligt, heeft het rapport de status waarschuwing. Wanneer de CPU boven een drempel waarde voor meer dan de geconfigureerde tijd blijft, wordt deze als een fout gerapporteerd. Als dat niet het geval is, wordt de status van OK door de rapporter verzonden.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

In het volgende voor beeld wordt een tijdelijke waarschuwing in een replica gerapporteerd. Eerst wordt de partitie-ID opgehaald en vervolgens de replica-ID voor de service waarin deze is geïnteresseerd. Vervolgens wordt een rapport verzonden vanuit **PowershellWatcher** in de eigenschap **ResourceDependency**. Het rapport is slechts twee minuten van belang en wordt automatisch uit de Store verwijderd.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Status rapporten verzenden met behulp van POST-aanvragen die naar de gewenste entiteit gaan en de beschrijving van het status rapport hebben in de hoofd tekst. Zie bijvoorbeeld informatie over het verzenden van REST [cluster status rapporten](/rest/api/servicefabric/report-the-health-of-a-cluster) of [service status rapporten](/rest/api/servicefabric/report-the-health-of-a-service). Alle entiteiten worden ondersteund.

## <a name="next-steps"></a>Volgende stappen
Op basis van de status gegevens kunnen service schrijvers en cluster/toepassings beheerders zien hoe ze deze gegevens gebruiken. Ze kunnen bijvoorbeeld waarschuwingen instellen op basis van de status om ernstige problemen te ondervangen voordat ze storingen veroorzaken. Beheerders kunnen ook reparatie systemen instellen om problemen automatisch op te lossen.

[Inleiding tot Service Fabric status controle](service-fabric-health-introduction.md)

[Service Fabric status rapporten weer geven](service-fabric-view-entities-aggregated-health.md)

[Service status rapporteren en controleren](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Systeem status rapporten gebruiken om problemen op te lossen](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Services lokaal controleren en een diagnose uitvoeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade van toepassing Service Fabric](service-fabric-application-upgrade.md)
