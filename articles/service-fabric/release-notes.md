---
title: Releases van Azure Service Fabric
description: Release opmerkingen bij Azure Service Fabric. Bevat informatie over de nieuwste functies en verbeteringen in Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 0eedd5ce67e96c9d732cf24cbe56a8c3c5e92aaa
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880488"
---
# <a name="service-fabric-releases"></a>Service Fabric releases

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Richt lijnen voor probleem oplossing</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Bijhouden van problemen</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Ondersteuningsopties</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Ondersteunde versies</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Code voorbeelden</a>

In dit artikel vindt u meer informatie over de nieuwste releases en updates voor de Service Fabric runtime en Sdk's.

## <a name="service-fabric-72"></a>Service Fabric 7,2

Met trots kondigen we aan dat 7,2-versie van de Service Fabric runtime is begonnen met het implementeren van de verschillende Azure-regio's, samen met hulpprogram ma's en SDK-updates. De updates voor .NET SDK, Java SDK en Service Fabric runtime zijn beschikbaar via het installatie programma voor het web platform, NuGet-pakketten en Maven-opslag plaatsen.

### <a name="key-announcements"></a>Belang rijke aankondigingen

- **Voor beeld**: [**service Fabric beheerde clusters**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) zijn nu beschikbaar als open bare preview. Service Fabric beheerde clusters zijn gericht op het vereenvoudigen van het implementeren en beheren van clusters door de onderliggende resources die samen een Service Fabric cluster vormen, in te kapselen in één ARM-resource. Zie [service Fabric Managed cluster Overview](./overview-managed-cluster.md)(Engelstalig) voor meer informatie.
- **Voor beeld**: [**ondersteuning voor stateless Services met een aantal exemplaren dat groter is dan het aantal knoop punten**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) is nu beschikbaar als open bare preview. Met een plaatsings beleid kan meerdere stateless instanties van een partitie op een knoop punt worden gemaakt.
- [**FabricObserver (fo) 3,0**](https://aka.ms/sf/fabricobserver) is nu beschikbaar.
    - U kunt nu FabricObserver uitvoeren in Linux-en Windows-clusters.
    - U kunt nu aangepaste waarnemers-invoeg toepassingen maken. Raadpleeg het [Leesmij-bestand voor plugins](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) en het [voorbeeld project voor de invoeg toepassing](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) voor meer informatie en code.
    - U kunt nu de instelling van een waarnemer wijzigen via de upgrade van de toepassings parameters. Dit betekent dat u niet meer hoeft te implementeren om specifieke instellingen voor de waarnemer te wijzigen. Bekijk het voor [beeld](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Ondersteuning voor Ubuntu 18,04 Onebox-container installatie kopieën**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Voor beeld**: de [ **Naslag informatie voor de sleutel kluis voor service Fabric toepassingen ondersteunt **alleen versie-geheimen**. Geheimen zonder versies worden niet ondersteund.**](./service-fabric-keyvault-references.md)
- Voor SF SDK is de nieuwste VS 2019-update 16.7.6 of 16,8 Preview 4 vereist om nieuwe .NET Framework stateless/statussen/actors-projecten te kunnen maken. Als u niet de nieuwste VS update hebt, gebruikt u na het maken van het service project pakket beheer om micro soft. ServiceFabric. Services (versie 4.2. x) te installeren voor stateful/stateless projecten en micro soft. ServiceFabric. actors (versie 4.2. x) voor actor projecten van nuget.org.
- **RunToCompletion**: Service Fabric ondersteunt het concept van Run to completion voor uitvoer bare gast bestanden. Als deze update eenmaal is uitgevoerd, worden de cluster resources die zijn toegewezen aan deze replica, vrijgegeven.
- [**Ondersteuning van resource governance is verbeterd**](./service-fabric-resource-governance.md): aanvragen en limieten voor de CPU-en geheugen resources worden toegestaan.

### <a name="service-fabric-72-releases"></a>Service Fabric 7,2 releases
| Releasedatum | Release | Meer informatie |
|---|---|---|
| 21 oktober 2020 | [Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 november 2020 | [Azure Service Fabric 7,2 tweede versie vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 november 2020  | Release van de derde vernieuwing van Azure Service Fabric 7,2 | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 december 2020 | [Azure Service Fabric 7,2 vierde versie vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 januari 2021 | [Release van de vijfde vernieuwing van Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)

## <a name="previous-versions"></a>Vorige versies

### <a name="service-fabric-71"></a>Service Fabric 7,1

Als gevolg van de huidige COVID-crisis, en rekening houdend met de uitdagingen van onze klanten, maken we 7,1 beschikbaar, maar worden er geen automatische upgrade van de clusters ingesteld die automatisch worden bijgewerkt. De automatische upgrades worden onderbroken tot verdere kennisgeving, om ervoor te zorgen dat klanten upgrades kunnen Toep assen wanneer ze het meest geschikt zijn om onverwachte onderbrekingen te voor komen.

U kunt bijwerken naar 7,1 via de [Azure Portal](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) of via een [Azure Resource Manager-implementatie](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template).

Service Fabric clusters waarvoor automatische upgrades zijn ingeschakeld, worden automatisch de 7,1-update ontvangen zodra de standaard implementatie procedure wordt hervat. We zullen een andere aankondiging opgeven voordat de standaard implementatie begint op de website van de [service Fabric tech-Community](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
We hebben ook updates gepubliceerd naar het einde van de ondersteunings datum voor grote releases, te beginnen bij 6,5 [tot 7,1.](./service-fabric-versions.md#supported-versions) 

#### <a name="key-announcements"></a>Belang rijke aankondigingen

- **Algemene Beschik baarheid** van [ **service Fabric beheerde identiteiten voor service Fabric toepassingen**](./concepts-managed-identity.md)
- [**Ondersteuning voor Ubuntu 18,04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Voor beeld: virtuele-machine schaalset ondersteuning tijdelijke besturingssysteem schijf**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: tijdelijke besturingssysteem schijven zijn opslag die is gemaakt op de lokale virtuele machine en niet opgeslagen op externe Azure Storage. Deze worden aanbevolen voor alle Service Fabric knooppunt typen (primair en secundair), omdat deze worden vergeleken met traditionele permanente besturingssysteem schijven, tijdelijke besturingssysteem schijven:
      -  Lees-en schrijf latentie beperken tot de besturingssysteem schijf
      -  Sneller terugzetten/opnieuw instellen van knoop punten voor knooppunt beheer inschakelen
      -  De totale kosten verlagen (de schijven zijn gratis en er zijn geen extra opslag kosten in rekening gebracht)
- Ondersteuning voor het declareren van [**service-eindpunt certificaten van service Fabric toepassingen op basis van de algemene naam**](./service-fabric-service-manifest-resources.md)van het onderwerp.
- [**Ondersteuning voor status tests voor in containers**](./probes-codepackage.md)geplaatste Services: ondersteuning voor het mechanisme voor de duur van de bewaarde toepassingen. Met de duur van de online-test kunt u de levens duur van de container toepassing aankondigen. Wanneer deze niet tijdig reageren, wordt de computer opnieuw opgestart. 
- [**Ondersteuning voor het initialiseren van code pakketten**](./initializer-codepackages.md) voor [containers](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) en [uitvoer bare gast](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) toepassingen. Hierdoor kunnen code pakketten (bijvoorbeeld containers) in een opgegeven volg orde worden uitgevoerd om service pakket initialisatie uit te voeren.
- **FabricObserver en ClusterObserver** zijn stateless toepassingen die service Fabric telemetrie vastleggen die betrekking hebben op verschillende aspecten van een SF-cluster. Beide toepassingen zijn gereed voor implementatie naar Windows-productie clusters voor het vastleggen van rijke telemetrie met geïmplementeerde ondersteuning voor ApplicationInsights, Event source en LogAnalytics.
    - [**FabricObserver (fo) 2,0**](https://github.com/microsoft/service-fabric-observer): wordt uitgevoerd op alle knoop punten, genereert status gebeurtenissen, verzendt telemetrie wanneer door de gebruiker geconfigureerde drempel waarden voor resource gebruik zijn bereikt. Deze release bevat verschillende verbeteringen in bewaking, gegevens beheer, Details van de status gebeurtenis, gestructureerde telemetrie.
     - [**ClusterObserver (CO) 1,1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) : op één knoop punt wordt uitgevoerd, wordt de status van de telemetrie op cluster niveau vastgelegd. In deze release bewaakt ClusterObserver ook de knooppunt status en verzendt telemetrie wanneer het knoop punt niet meer dan een door de gebruiker opgegeven tijds periode wordt uitgeschakeld.

#### <a name="improve-application-life-cycle-experience"></a>De levens cyclus van toepassingen verbeteren

- **[Voor beeld: aanvraag](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** verwerkings stop: tijdens geplande service onderhoud, zoals service-upgrades of het deactiveren van knoop punten, wilt u toestaan dat de Services verbindingen zonder problemen kunnen verbreken. Met deze functie wordt een tijds duur voor het sluiten van een exemplaar toegevoegd in de service configuratie. Tijdens geplande bewerkingen verwijdert SF het adres van de service uit detectie en wordt deze duur gewacht voordat de service wordt afgesloten.
- **[Automatische detectie en taak verdeling van subclusteren](./cluster-resource-manager-subclustering.md)**: subclustering treedt op wanneer Services met verschillende plaatsings beperkingen een gemeen schappelijke [metrische belasting](./service-fabric-cluster-resource-manager-metrics.md)hebben. Als de belasting van de verschillende sets knoop punten aanzienlijk verschilt, is het Service Fabric cluster resource manager van mening dat het cluster niet meer in balans is, zelfs wanneer het het best mogelijke saldo heeft vanwege de plaatsings beperkingen. Als gevolg hiervan wordt geprobeerd het cluster opnieuw in balans te brengen, waardoor onnodig service bewegingen mogelijk worden veroorzaakt (omdat het evenwicht niet aanzienlijk kan worden verbeterd). Vanaf deze release zal cluster resource manager nu proberen om deze sorteringen automatisch te detecteren en te begrijpen wanneer het onevenwicht kan worden verholpen en wanneer in plaats daarvan dingen moeten worden overgelaten omdat er geen aanzienlijke verbetering kan worden aangebracht.  
- [**Verschillende verplaatsings kosten voor secundaire replica's**](./service-fabric-cluster-resource-manager-movement-cost.md): we hebben een nieuwe waarde voor de VeryHigh voor de verplaatsing geïntroduceerd die in sommige scenario's extra flexibiliteit biedt om te bepalen of een afzonderlijke plaatsings kosten moeten worden gebruikt voor secundaire replica's.
- Ingeschakelde methode voor het testen van de [**liveiteit**](./probes-codepackage.md) voor container toepassingen. Met de duur van de online-test kunt u de levens duur van de container toepassing aankondigen. Wanneer deze niet tijdig reageren, wordt de computer opnieuw opgestart.
- [**Uitvoeren naar voltooid/eenmaal voor services**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Image Store verbeteringen
 - Service Fabric 7,1 gebruikt **aangepaste Trans Port om standaard bestands overdracht tussen knoop punten te beveiligen**. De afhankelijkheid op SMB-bestands share is verwijderd uit versie 7,1. De beveiligde SMB-bestands shares bestaan nog steeds op knoop punten die Image Store service replica bevatten die de klant kan kiezen uit standaard en voor een upgrade en een downgrade naar een oude versie.
       
 #### <a name="reliable-collections-improvements"></a>Verbeteringen van betrouw bare verzamelingen

- [**In het geheugen wordt alleen ondersteuning voor stateful-Services met behulp van betrouw bare verzamelingen opgeslagen. met**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)vluchtige betrouw bare verzamelingen kunnen gegevens worden bewaard op schijf voor duurzaamheid tegen grootschalige storingen, kan deze worden gebruikt voor werk belastingen zoals gerepliceerde cache, bijvoorbeeld waar incidenteel gegevens verlies kan worden toegestaan. Op basis van de [beperkingen en beperkingen van vluchtige betrouw bare verzamelingen](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)wordt u aangeraden voor werk belastingen die geen persistentie nodig hebben, voor services die de zeldzame gelegenheden van quorum verlies afhandelen.
- [**Preview: Service Fabric backup Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): als u het beheer van betrouw bare verzamelingen back-ups voor service Fabric stateful toepassingen wilt vereenvoudigen service Fabric, kunt u met back-up Verkenner gebruikers
    - De inhoud van de betrouw bare verzamelingen controleren en bekijken
    - Huidige status bijwerken naar een consistente weer gave
    - Maak een back-up van de huidige moment opname van de betrouw bare verzamelingen
    - Gegevens beschadiging herstellen
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7,1 releases
| Releasedatum | Release | Meer informatie |
|---|---|---|
| 20 april 2020 | [Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 juni 2020 | [Microsoft Azure Service Fabric 7,1 eerst vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 juli 2020 | [Microsoft Azure Service Fabric 7,1 seconde vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 augustus 2020 | [Microsoft Azure Service Fabric 7,1 derde vernieuwing](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 september 2020 | [Microsoft Azure 7,1 Service Fabric vierde vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 oktober 2020 | Microsoft Azure Service Fabric 7,1 zesde vernieuwen | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 november 2020 | Microsoft Azure Service Fabric 7,1 achtste vernieuwen | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 is nu beschikbaar. U kunt bijwerken naar 7,0 via de Azure Portal of via een Azure Resource Manager-implementatie. Als gevolg van feedback van klanten over de duur van de feest dagen, worden de clusters die zijn ingesteld om automatische upgrades tot en met januari te ontvangen, niet automatisch bijgewerkt.
In januari gaan we de standaard procedure voor samen vouwen en clusters met automatische upgrades hervatten, wordt de 7,0-update automatisch ontvangen. We zullen een andere aankondiging opgeven voordat de implementatie begint.
We zullen onze geplande release datums ook bijwerken om aan te geven dat we dit beleid in overweging nemen. Hier vindt u updates voor de toekomstige [release planningen](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).

#### <a name="key-announcements"></a>Belang rijke aankondigingen
 - [**KeyVaultReference-ondersteuning voor toepassings geheimen (preview-versie)**](./service-fabric-keyvault-references.md): Service Fabric toepassingen die [beheerde identiteiten](./concepts-managed-identity.md) hebben ingeschakeld, kunnen nu rechtstreeks verwijzen naar een Key Vault geheime URL als omgevings variabele, toepassings parameter of container opslagplaats referentie. Service Fabric zal het geheim automatisch oplossen met behulp van de beheerde identiteit van de toepassing. 
     
- **Verbeterde beveiliging van upgrades voor stateless Services**: als u de beschik baarheid tijdens een upgrade van een toepassing wilt garanderen, hebben we nieuwe configuraties geïntroduceerd om het [minimum aantal instanties te definiëren voor stateless Services](/dotnet/api/system.fabric.description.statelessservicedescription) die beschikbaar moeten worden gesteld. Voorheen was deze waarde 1 voor alle services en kan deze niet worden gewijzigd. Met deze nieuwe beveiligings controle per service kunt u ervoor zorgen dat uw services een minimum aantal exemplaren bewaren tijdens de upgrade van toepassingen, cluster upgrades en andere onderhouds werkzaamheden die afhankelijk zijn van de status-en veiligheids controles van Service Fabric.
  
- [**Resource limieten voor gebruikers services**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): gebruikers kunnen resource limieten instellen voor de gebruikers services op een knoop punt om te voor komen dat scenario's zoals bron uitputting van de service Fabric systeem services. 
  
- [**Zeer hoge service kosten**](./service-fabric-cluster-resource-manager-movement-cost.md) voor een replica type. Replica's met zeer hoge verplaatsings kosten worden alleen verplaatst als er een beperkings schending in het cluster is die op een andere manier niet kan worden opgelost. Raadpleeg het gekoppelde document voor meer informatie over wanneer het gebruik van een zeer hoge transport kosten redelijk is en voor aanvullende overwegingen.
  
-  **Aanvullende controle van de cluster beveiliging**: in deze release hebben we een Configureer bare quorum controle voor het Seed-knoop punt geïntroduceerd. Zo kunt u aanpassen hoeveel Seed-knoop punten beschikbaar moeten zijn tijdens de cluster levenscyclus-en beheer scenario's. Bewerkingen die het cluster onder de geconfigureerde waarde zouden nemen, worden geblokkeerd. Vandaag de standaard waarde is altijd een quorum van de Seed-knoop punten, bijvoorbeeld als u 7 Seed-knoop punten hebt, een bewerking die u onder 5 Seed-knoop punten zou nemen, wordt standaard geblokkeerd. Met deze wijziging kunt u de minimale veilige waarde 6 instellen, waardoor slechts één Seed-knoop punt tegelijk kan worden ingewisseld.
   
- Er is ondersteuning toegevoegd voor [**het beheer van de service voor back-up en herstel in service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). Hierdoor kunnen de volgende activiteiten rechtstreeks vanuit SFX worden uitgevoerd: de service Backup en Restore detecteren, back-upbeleid maken, automatische back-ups inschakelen, ad hoc-back-ups uitvoeren, herstel bewerkingen activeren en bladeren door bestaande back-ups.

- De beschik baarheid van de [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)wordt aangekondigd: met dit hulp programma kunt u valideren of de typen die in betrouw bare verzamelingen worden gebruikt, vooruit en achterwaarts compatibel zijn tijdens de upgrade van een rolling toepassing. Dit helpt bij het voor komen van problemen met de upgrade of gegevens verlies en beschadiging van gegevens als gevolg van ontbrekende of incompatibele typen.

- [**Stabiele Lees bewerkingen inschakelen voor secundaire replica's**](./service-fabric-reliable-services-configuration.md#configuration-names-1): met stabiele Lees bewerkingen worden secundaire replica's beperkt tot waarden die zijn bevestigd met een quorum.

Daarnaast bevat deze release andere nieuwe functies, oplossingen voor problemen en ondersteunings-, betrouw baarheid en prestatie verbeteringen. Raadpleeg de opmerkingen bij de [release](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)voor een volledige lijst met wijzigingen.

#### <a name="service-fabric-70-releases"></a>Service Fabric 7,0 releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 18 november 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 januari 2020 | [Release van Azure Service Fabric 7,0 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 februari 2020 | [Release van Azure Service Fabric 7,0 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 maart 2020 | [Release van Azure Service Fabric 7,0 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 mei 2020 | [Release van de zesde vernieuwing van Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 oktober 2020 | Release van de negende vernieuwing van Azure Service Fabric 7,0 | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6,5

Deze release bevat verbeteringen op het gebied van ondersteuning, betrouw baarheid en prestaties, nieuwe functies, fout oplossingen en verbeteringen voor het vereenvoudigen van het beheer van de cluster-en toepassings levenscyclus.

> [!IMPORTANT]
> Service Fabric 6,5 is de definitieve versie met de ondersteuning voor Service Fabric-hulpprogram ma's in Visual Studio 2015. Klanten wordt geadviseerd om over te stappen op [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) .

Wat is er nieuw in Service Fabric 6,5:

- Service Fabric Explorer bevat een [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) voor het controleren van de toepassingen die u hebt geüpload naar het archief met installatie kopieën.

- De [poa-versie (patch Orchestration Application)](service-fabric-patch-orchestration-application.md) [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) bevat veel zelf diagnostische verbeteringen. Klanten van POA worden aanbevolen om over te stappen op deze versie.

- De [Event Store-service is standaard ingeschakeld](service-fabric-visualizing-your-cluster.md#event-store) voor service Fabric 6,5-clusters, tenzij u ervoor hebt gekozen.

- Er zijn [replica levenscyclus gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md#replica-events) voor stateful services toegevoegd.

- [Betere zicht baarheid van de status van het Seed-knoop punt](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusief waarschuwingen op cluster niveau als een Seed-knoop punt een slechte status heeft (niet *beschikbaar*, *verwijderd* of *onbekend*).

- Met [service Fabric hulp programma voor nood herstel](https://github.com/Microsoft/Service-Fabric-AppDRTool) van de toepassing kunnen service Fabric stateful Services snel worden hersteld wanneer het primaire cluster een nood geval tegen komt. Gegevens van een primair cluster worden voortdurend gesynchroniseerd met de secundaire stand-by-toepassing met periodieke back-ups en herstel bewerkingen.

- Visual Studio-ondersteuning voor [het publiceren van .net core-apps naar op Linux gebaseerde clusters](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure service Fabric cli (SFCTL)](./service-fabric-cli.md) wordt automatisch geïnstalleerd voor service Fabric 6,5 (en latere versies) wanneer u een upgrade uitvoert of een nieuw Linux-cluster maakt in Azure.

- [SFCTL](./service-fabric-cli.md) wordt standaard geïnstalleerd in MacOS/Linux Onebox-clusters.

Zie de [release opmerkingen voor Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)voor meer informatie.

#### <a name="service-fabric-65-releases"></a>Service Fabric 6,5 Releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 11 juni 2019 | [Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 juli 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 juli 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Augustus 23, 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Releaseopmerkingen](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 okt 2019 | [Release van Azure Service Fabric 6,5 vernieuwen](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Opmerkingen bij de release] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric 6,4 Releases

| Releasedatum | Release | Meer informatie |
|---|---|---|
| 30 november 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 december 2018 | [Azure Service Fabric 6,4-release voor Windows-clusters vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 februari 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 maart 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 april 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 mei 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 mei 2019 | [Release van Azure Service Fabric 6,4 vernieuwen](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Releaseopmerkingen](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
