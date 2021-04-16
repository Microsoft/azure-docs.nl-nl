---
title: Azure Service Fabric releases
description: Releasenotities voor Azure Service Fabric. Bevat informatie over de nieuwste functies en verbeteringen in Service Fabric.
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 2a035f531e03dc42e8be4f3dab403406eb7c8f14
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518623"
---
# <a name="service-fabric-releases"></a>Service Fabric releases

Dit artikel bevat meer informatie over de nieuwste releases en updates voor de Service Fabric runtime en SDK's.

De volgende resources zijn ook beschikbaar:
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Handleidingen voor probleemoplossing</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Bijhouden van problemen</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Ondersteuningsopties</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Ondersteunde versies</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Codevoorbeelden</a>


## <a name="service-fabric-80"></a>Service Fabric 8.0

Met trots kondigen we aan dat de 8.0-release van de Service Fabric-runtime is uitgerold naar de verschillende Azure-regio's, samen met hulpprogramma's en SDK-updates. De updates voor .NET SDK, Java SDK en Service Fabric runtime zijn beschikbaar via webplatform installer, NuGet-pakketten en Maven-opslagplaatsen.

### <a name="key-announcements"></a>Belangrijkste aankondigingen

- **Algemene beschikbaarheid** van ondersteuning voor .NET 5 voor Windows
- **Algemene beschikbaarheid** van [stateless NodeTypes](https://docs.microsoft.com/azure/service-fabric/service-fabric-stateless-node-types)
- Mogelijkheid om stateless service verplaatsen
- Mogelijkheid om geparameteriseerde DefaultLoad toe te voegen aan het toepassingsmanifest
- Voor upgrades van singleton-replica's: de mogelijkheid om enkele van de instellingen op clusterniveau te definiëren op toepassingsniveau
- Mogelijkheid voor slimme plaatsing op basis van knooppunttags
- Mogelijkheid om drempelpercentages te definiëren van knooppunten met een slechte status die invloed hebben op de status van het cluster
- Mogelijkheid om query's uit te voeren op de best geladen services
- Mogelijkheid om een nieuw interval voor nieuwe foutcodes toe te voegen
- Mogelijkheid om service-exemplaar als voltooid te markeren
- Ondersteuning voor wave-implementatiemodel voor automatische upgrades
- Gereedheidstest toegevoegd voor containertoepassingen
- UseSeparateSecondaryMoveCost standaard inschakelen op true
- StateManager is opgelost om de verwijzing zo snel mogelijk vrij te geven
- Verwijdering van central secret service tijdens opslaan van gebruikersgeheimen blokkeren


### <a name="service-fabric-80-releases"></a>Service Fabric 8.0-releases
| Releasedatum | Release | Meer informatie |
|---|---|---|
| 8 april 2021 | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|


## <a name="previous-versions"></a>Vorige versies

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>Belangrijkste aankondigingen

- **Preview:** [**Service Fabric beheerde clusters**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) zijn nu beschikbaar als openbare preview. Service Fabric beheerde clusters zijn erop gericht clusterimplementatie en -beheer te vereenvoudigen door de onderliggende resources die samen een Service Fabric-cluster vormen, in te kapselen in één ARM-resource. Zie overzicht van Service Fabric [beheerd cluster voor meer informatie.](./overview-managed-cluster.md)
- **Preview:** [**Ondersteuning van staatloze services met een aantal**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) exemplaren groter dan het aantal knooppunten is nu in openbare preview. Een plaatsingsbeleid maakt het mogelijk om meerdere staatloze exemplaren van een partitie op een knooppunt te maken.
- [**FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) is nu beschikbaar.
    - U kunt nu FabricObserver uitvoeren in Linux- en Windows-clusters.
    - U kunt nu aangepaste waarnemer-invoegingen bouwen. Zie [readme voor invoegvoegingen](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) en het [voorbeeldproject voor invoegvoegingen](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) voor meer informatie en code.
    - U kunt nu elke waarnemerinstelling wijzigen via de upgrade van toepassingsparameters. Dit betekent dat u de FO niet meer hoeft te opnieuw te gebruiken om specifieke waarnemerinstellingen te wijzigen. Raadpleeg het [voorbeeld](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Ondersteuning voor Ubuntu 18.04 OneBox-containerafbeeldingen.**](https://hub.docker.com/_/microsoft-service-fabric-onebox)
- **Preview:** [ **KeyVault Reference for Service Fabric applications supports **ONLY versioned secrets**. Geheimen zonder versies worden niet ondersteund.**](./service-fabric-keyvault-references.md)
- Voor SF SDK is de meest recente VS 2019-update 16.7.6 of 16.8 Preview 4 vereist om nieuwe .NET Framework stateless/stateful/actors-projecten te kunnen maken. Als u niet de nieuwste VS-update hebt, gebruikt u na het maken van het serviceproject package manager om Microsoft.ServiceFabric.Services (versie 4.2.x) te installeren voor stateful/stateless projecten en Microsoft.ServiceFabric.Actors (versie 4.2.x) voor actorprojecten van nuget.org.
- **RunToCompletion:** Service Fabric ondersteuning voor het concept van uitvoeren tot voltooiing voor uitvoerbare gast-bestanden. Met deze update worden de clusterresources die aan deze replica zijn toegewezen, vrijgegeven zodra de replica is voltooid.
- [**Ondersteuning voor resourcebeheer is verbeterd: het**](./service-fabric-resource-governance.md)toestaan van aanvragen en limietspecificaties voor CPU- en geheugenresources.

#### <a name="service-fabric-72-releases"></a>Service Fabric 7.2-releases
| Releasedatum | Release | Meer informatie |
|---|---|---|
| 21 oktober 2020 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 november 2020 | [Azure Service Fabric 7.2 Second Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 november 2020  | Azure Service Fabric 7.2 Derde vernieuwingsversie | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 december 2020 | [Azure Service Fabric 7.2 Vierde vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 januari 2021 | [Azure Service Fabric 7.2 Release voor vijfde vernieuwing](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 februari 2021 | [Azure Service Fabric 7.2 Zesde vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10 maart 2021 | [Azure Service Fabric 7.2 Vernieuwt release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1

Vanwege de huidige COVID-19-crisis en rekening houden met de uitdagingen waarmee onze klanten te maken hebben, maken we 7.1 beschikbaar, maar worden clusters die zijn ingesteld voor automatische upgrades niet automatisch bijgewerkt. Automatische upgrades worden tot nader order onderbroken om ervoor te zorgen dat klanten upgrades kunnen toepassen wanneer ze daar het meest geschikt voor zijn, om onverwachte onderbrekingen te voorkomen.

U kunt bijwerken naar 7.1 via de Azure Portal [of](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal) via een [Azure Resource Manager implementatie](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Service Fabric clusters met automatische upgrades ingeschakeld, ontvangt de update 7.1 automatisch zodra we de standaardprocedure voor de implementatie hervatten. We zullen nog een aankondiging doen voordat de standaard implementatie begint op de [Service Fabric Tech Community Site.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)
We hebben hier ook updates gepubliceerd tot de einddatum van de ondersteuning voor [](./service-fabric-versions.md)belangrijke releases van 6.5 tot 7.1. 

#### <a name="key-announcements"></a>Belangrijkste aankondigingen

- **Algemene beschikbaarheid** van [ **Service Fabric beheerde identiteiten voor Service Fabric toepassingen**](./concepts-managed-identity.md)
- [**Ondersteuning voor Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Preview:**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)Tijdelijke besturingssysteemschijfondersteuning voor virtuele-machineschaalsets **: tijdelijke besturingssysteemschijven worden op de lokale virtuele machine gemaakt en niet opgeslagen op externe Azure Storage. Ze worden aanbevolen voor alle Service Fabric knooppunttypen (primair en secundair), omdat in vergelijking met traditionele permanente besturingssysteemschijven kortstondige besturingssysteemschijven:
      -  Lees-/schrijflatentie naar besturingssysteemschijf verminderen
      -  Sneller opnieuw instellen/opnieuw instellen van knooppuntbeheerbewerkingen inschakelen
      -  De totale kosten verlagen (de schijven zijn gratis en er zijn geen extra opslagkosten)
- Ondersteuning voor [**declaratie van service-eindpuntcertificaten van Service Fabric toepassingen op algemene onderwerpnaam**](./service-fabric-service-manifest-resources.md).
- [**Ondersteuning voor statustests voor in containers geplaatste services:**](./probes-codepackage.md)ondersteuning voor het liveness-testmechanisme voor containertoepassingen. Liveness Probe kondigt de liveness-toepassing aan en wanneer deze niet tijdig reageert, resulteert dit in een herstart. 
- [**Ondersteuning voor initialisatiecodepakketten voor**](./initializer-codepackages.md) [containers en](./service-fabric-containers-overview.md) [uitvoerbare gasttoepassingen.](./service-fabric-guest-executables-introduction.md) Hierdoor kunnen codepakketten (bijvoorbeeld containers) in een opgegeven volgorde worden uitgevoerd om Service Package-initialisatie uit te voeren.
- **FabricObserver en ClusterObserver** zijn staatloze toepassingen die Service Fabric telemetrie vastleggen die betrekking heeft op verschillende aspecten van een SF-cluster. Beide toepassingen zijn gereed voor implementatie in Windows-productieclusters om uitgebreide telemetrie vast te leggen met geïmplementeerde ondersteuning voor ApplicationInsights, EventSource en LogAnalytics.
    - [**FabricObserver (FO) 2.0:**](https://github.com/microsoft/service-fabric-observer)wordt uitgevoerd op alle knooppunten, genereert statusgebeurtenissen, genereert telemetrie wanneer door de gebruiker geconfigureerde drempelwaarden voor resourcegebruik worden bereikt. Deze release bevat verschillende verbeteringen in bewaking, gegevensbeheer, statusgebeurtenisdetails, gestructureerde telemetrie.
     - [**ClusterObserver (CO) 1.1:**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) wordt uitgevoerd op één knooppunt en legt de statusstelemetrie op clusterniveau vast. In deze release bewaakt ClusterObserver ook de knooppuntstatus en worden telemetriegegevens weergegeven wanneer het knooppunt langer dan de door de gebruiker opgegeven periode niet beschikbaar of uitgeschakeld is.

#### <a name="improve-application-life-cycle-experience"></a>De levenscyclus van toepassingen verbeteren

- **[Preview:Aanvraagafvoer:](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** tijdens gepland serviceonderhoud, zoals service-upgrades of het deactiveren van knooppunt, wilt u de services toestaan verbindingen probleemloos te laten leeglaten. Met deze functie wordt een vertragingsduur voor het sluiten van exemplaren toegevoegd in de serviceconfiguratie. Tijdens geplande bewerkingen verwijdert SF het adres van de service uit de detectie en wacht deze duur voordat de service wordt afgesloten.
- **[Automatische detectie en taakverdeling van](./cluster-resource-manager-subclustering.md)** subcluster: Subclustering vindt plaats wanneer services met verschillende plaatsingsbeperkingen een gemeenschappelijke [metrische belasting hebben.](./service-fabric-cluster-resource-manager-metrics.md) Als de belasting van de verschillende sets knooppunten aanzienlijk verschilt, denkt de Service Fabric Cluster Resource Manager dat het cluster uit balans is, zelfs wanneer het het best mogelijke evenwicht heeft vanwege de plaatsingsbeperkingen. Als gevolg hiervan wordt geprobeerd het cluster opnieuw in balans te brengen, waardoor onnodige serviceverleningen kunnen ontstaan (omdat de 'onevenwichtigheid' niet aanzienlijk kan worden verbeterd). Vanaf deze release probeert de Cluster Resource Manager dit soort configuraties automatisch te detecteren en te begrijpen wanneer de onevenwichtigheid kan worden opgelost door middel van verplaatsing, en wanneer het in plaats daarvan zaken met rust moet laten omdat er geen aanzienlijke verbetering kan worden aangebracht.  
- [**Verschillende kosten voor**](./service-fabric-cluster-resource-manager-movement-cost.md)verplaatsen voor secundaire replica's: We hebben de nieuwe waarde voor verplaatsen van kosten ZeerHigh geïntroduceerd die in sommige scenario's extra flexibiliteit biedt om te bepalen of er afzonderlijke kosten voor verplaatsen moeten worden gebruikt voor secundaire replica's.
- Ingeschakelde [**testmechanisme voor liveheid**](./probes-codepackage.md) voor toepassingen in containers. Liveness Probe helpt bij het aankondigen van de liveness van de containertoepassing en wanneer deze niet tijdig reageert, resulteert dit in een herstart.
- [**Uitvoeren tot voltooiing/eenmaal voor services**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Image Store verbeteringen
 - Service Fabric 7.1 maakt standaard gebruik van aangepast transport om **bestandsoverdracht tussen knooppunten te beveiligen.** De afhankelijkheid van de SMB-bestands share is verwijderd uit versie 7.1. De beveiligde SMB-bestands shares bestaan nog steeds op knooppunten die Image Store Service Replica bevatten, zodat de klant kan kiezen voor het uitschakelen van de standaardinstelling en voor upgraden en downgraden naar een oude versie.
       
 #### <a name="reliable-collections-improvements"></a>Verbeteringen in Betrouwbare verzamelingen

- In het geheugen kan alleen ondersteuning worden opgeslagen voor stateful services die gebruikmaken van [**Reliable Collections:**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)Met vluchtige betrouwbare verzamelingen kunnen gegevens op schijf worden opgeslagen voor duurzaamheid tegen grootschalige uitval. Ze kunnen bijvoorbeeld worden gebruikt voor werkbelastingen zoals gerepliceerde cache, waarbij incidenteel gegevensverlies kan worden getolereerd. Op basis van de beperkingen en beperkingen van [Vluchtige betrouwbare](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)verzamelingen raden we dit aan voor werkbelastingen die geen persistentie nodig hebben, voor services die omgaan met zeldzame gevallen van quorumverlies.
- [**Preview: Service Fabric Back-upverkenner:**](https://github.com/microsoft/service-fabric-backup-explorer)om het beheer van reliable collections-back-ups voor Service Fabric stateful toepassingen te gemakt, kunnen Service Fabric Back-upverkenner gebruikers
    - Controleer en controleer de inhoud van de betrouwbare verzamelingen,
    - Huidige status bijwerken naar een consistente weergave
    - Back-up maken van de huidige momentopname van de betrouwbare verzamelingen
    - Beschadiging van gegevens oplossen
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7.1-releases
| Releasedatum | Release | Meer informatie |
|---|---|---|
| 20 april 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 juni 2020 | [Microsoft Azure Service Fabric 7.1 eerste vernieuwing](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 juli 2020 | [Microsoft Azure Service Fabric 7,1 seconde vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 augustus 2020 | [Microsoft Azure Service Fabric 7.1 Derde vernieuwing](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 september 2020 | [Microsoft Azure Service Fabric 7.1 Vierde vernieuwing](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 oktober 2020 | Microsoft Azure Service Fabric 7.1 Zesde vernieuwing | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 november 2020 | Microsoft Azure Service Fabric 7.1 Vernieuwing vernieuwen | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 is nu beschikbaar. U kunt bijwerken naar 7.0 via de Azure Portal of via een Azure Resource Manager implementatie. Vanwege feedback van klanten over releases rond de feestdagen worden clusters die zijn ingesteld voor automatische upgrades pas in januari automatisch bijgewerkt.
In januari wordt de standaarduitrolprocedure hervat. Clusters met automatische upgrades ingeschakeld krijgen automatisch de update van 7.0. We zullen nog een aankondiging doen voordat de implementatie begint.
We werken ook onze geplande releasedatums bij om aan te geven dat we rekening houden met dit beleid. Kijk hier voor updates over onze toekomstige [releaseplanningen.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)

#### <a name="key-announcements"></a>Belangrijkste aankondigingen
 - [**KeyVaultReference-ondersteuning**](./service-fabric-keyvault-references.md)voor toepassingsgeheimen (preview) : Service Fabric-toepassingen die beheerde identiteiten hebben ingeschakeld, kunnen nu rechtstreeks verwijzen naar een geheime URL van een Key Vault als een omgevingsvariabele, [toepassingsparameter](./concepts-managed-identity.md) of containeropslagplaatsreferentie. Service Fabric wordt het geheim automatisch opgelost met behulp van de beheerde identiteit van de toepassing. 
     
- Verbeterde veiligheid van upgrades **voor staatloze services:** om beschikbaarheid te garanderen tijdens een toepassingsupgrade, hebben we nieuwe configuraties geïntroduceerd om het minimum aantal instanties te definiëren voor [stateless services](/dotnet/api/system.fabric.description.statelessservicedescription) die als beschikbaar moeten worden beschouwd. Voorheen was deze waarde 1 voor alle services en kon deze niet worden gewijzigd. Met deze nieuwe veiligheidscontrole per service kunt u ervoor zorgen dat uw services een minimum aantal up-exemplaren behouden tijdens toepassingsupgrades, clusterupgrades en ander onderhoud dat afhankelijk is van de status- en veiligheidscontroles van Service Fabric.
  
- [**Resourcelimieten voor gebruikersservices:**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)gebruikers kunnen resourcelimieten instellen voor de gebruikersservices op een knooppunt om scenario's zoals resource-uitputting van de Service Fabric te voorkomen. 
  
- [**Zeer hoge serviceverhuiskosten**](./service-fabric-cluster-resource-manager-movement-cost.md) voor een replicatype. Replica's met zeer hoge verplaatsskosten worden alleen verplaatst als er een beperkingsschending in het cluster is die niet op een andere manier kan worden opgelost. Raadpleeg het gekoppelde document voor meer informatie over wanneer het gebruik van een zeer hoge verplaatsskosten redelijk is en voor aanvullende overwegingen.
  
-  **Aanvullende beveiligingscontroles voor clusters:** in deze release hebben we een configureerbare quorumcontrole voor seed-knooppunt geïntroduceerd. Hiermee kunt u aanpassen hoeveel seed-knooppunten er beschikbaar moeten zijn tijdens de levenscyclus- en beheerscenario's van het cluster. Bewerkingen die het cluster onder de geconfigureerde waarde zouden nemen, worden geblokkeerd. Tegenwoordig is de standaardwaarde altijd een quorum van de seed-knooppunten. Als u bijvoorbeeld 7 seed-knooppunten hebt, wordt een bewerking die u onder de vijf seed-knooppunten zou brengen standaard geblokkeerd. Met deze wijziging kunt u de minimale veilige waarde 6 maken, waardoor slechts één seed-knooppunt tegelijkertijd niet beschikbaar is.
   
- Ondersteuning toegevoegd voor het [**beheren van de back-up-**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)en herstelservice in Service Fabric Explorer . Dit maakt de volgende activiteiten rechtstreeks vanuit SFX mogelijk: het detecteren van de back-up- en herstelservice, het maken van back-upbeleid, het inschakelen van automatische back-ups, het maken van ad-hoc back-ups, het activeren van herstelbewerkingen en het bladeren door bestaande back-ups.

- De beschikbaarheid van [**reliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)aankondigen: dit hulpprogramma helpt te valideren dat typen die worden gebruikt in betrouwbare verzamelingen, compatibel zijn met vooruit en achterwaarts tijdens een rolling toepassingsupgrade. Dit voorkomt upgradefouten of gegevensverlies en beschadiging van gegevens als gevolg van ontbrekende of niet-compatibele typen.

- [**Stabiele leeswaarden op secundaire replica's**](./service-fabric-reliable-services-configuration.md#configuration-names-1)inschakelen: stabiele leeswaarden beperken secundaire replica's tot het retourneren van waarden die quorum-acked zijn.

Daarnaast bevat deze release andere nieuwe functies, oplossingen voor fouten en verbeteringen in de ondersteuning, betrouwbaarheid en prestaties. Raadpleeg de opmerkingen bij de release voor een volledige [lijst met wijzigingen.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)

#### <a name="service-fabric-70-releases"></a>Service Fabric 7.0-releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 18 november 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 januari 2020 | [Azure Service Fabric 7.0-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 februari 2020 | [Azure Service Fabric 7.0-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 maart 2020 | [Azure Service Fabric 7.0-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 mei 2020 | [Azure Service Fabric 7.0 Zesde vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 oktober 2020 | Azure Service Fabric 7.0-release voor vernieuwen | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Deze release bevat ondersteunings-, betrouwbaarheids- en prestatieverbeteringen, nieuwe functies, opgeloste fouten en verbeteringen om het beheer van de levenscyclus van clusters en toepassingen te soepelen.

> [!IMPORTANT]
> Service Fabric 6.5 is de laatste release met ondersteuning Service Fabric hulpprogramma's in Visual Studio 2015. Klanten wordt aangeraden om in [de Visual Studio 2019](https://visualstudio.microsoft.com/vs/) over te stappen.

Wat is er nieuw in Service Fabric 6.5:

- Service Fabric Explorer bevat een [Image Store Viewer voor](service-fabric-visualizing-your-cluster.md#image-store-viewer) het inspecteren van toepassingen die u hebt geüpload naar de afbeeldingsopslag.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versie [1.4.0 bevat](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) veel zelfdiagnoseverbeteringen. Klanten van een poa wordt aangeraden over te gaan naar deze versie.

- [De EventStore-service is standaard ingeschakeld](service-fabric-visualizing-your-cluster.md#event-store) voor Service Fabric 6.5-clusters, tenzij u zich hebt uitge- of uitge?

- [Levenscyclusgebeurtenissen van replica's](service-fabric-diagnostics-event-generation-operational.md#replica-events) toegevoegd voor stateful services.

- [Betere zichtbaarheid van de status van het seed-knooppunt,](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)inclusief waarschuwingen op clusterniveau als een seed-knooppunt niet in orde is (*Omlaag,* *Verwijderd* of *Onbekend).*

- [Service Fabric Hulpprogramma](https://github.com/Microsoft/Service-Fabric-AppDRTool) voor herstel na noodherstel van toepassingen Service Fabric stateful services snel herstellen wanneer het primaire cluster een noodherstel tegenkomt. Gegevens van het primaire cluster worden continu gesynchroniseerd op de secundaire stand-bytoepassing met behulp van periodieke back-up en herstel.

- Visual Studio voor het [publiceren van .NET Core-apps naar Linux-clusters.](service-fabric-how-to-publish-linux-app-vs.md)

- [Azure Service Fabric CLI (SFCTL)](./service-fabric-cli.md) wordt automatisch geïnstalleerd voor Service Fabric 6.5 (en latere versies) wanneer u een upgrade of een nieuw Linux-cluster in Azure maakt.

- [SFCTL](./service-fabric-cli.md) is standaard geïnstalleerd op MacOS/Linux OneBox-clusters.

Zie de opmerkingen bij de [release Service Fabric 6.5 voor meer informatie.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)

#### <a name="service-fabric-65-releases"></a>Service Fabric 6.5-releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 11 juni 2019 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Azure Service Fabric 6.5-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Azure Service Fabric 6.5-vernieuwingsversie](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 augustus 2019 | [Azure Service Fabric 6.5-vernieuwingsversie](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 oktober 2019 | [Azure Service Fabric 6.5-vernieuwingsversie](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Opmerkingen bij de release] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric 6.4-releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 30 november 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6.4-vernieuwingsversie voor Windows-clusters](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Azure Service Fabric 6.4-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 maart 2019 | [Azure Service Fabric 6.4-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Azure Service Fabric 6.4-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 mei 2019 | [Azure Service Fabric 6.4-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 mei 2019 | [Azure Service Fabric 6.4-vernieuwingsversie](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
