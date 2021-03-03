---
title: Overwegingen bij het plannen van Service Fabric cluster capaciteit
description: Knooppunt typen, duurzaamheid, betrouw baarheid en andere zaken waarmee u rekening moet houden bij het plannen van uw Service Fabric cluster.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.openlocfilehash: b3361337bb0cf60e47efe198aad7aa8cc20ae7b3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714932"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Overwegingen bij het plannen van Service Fabric cluster capaciteit

Planning van de cluster capaciteit is belang rijk voor elke Service Fabric productie omgeving. Belang rijke overwegingen zijn onder andere:

* **Oorspronkelijk aantal en eigenschappen van cluster *knooppunt typen***

* ***Duurzaamheids* niveau van elk type knoop punt**, waarmee service Fabric VM-bevoegdheden in de Azure-infra structuur worden bepaald

* ***Betrouwbaarheids* niveau van het cluster**, waarmee de stabiliteit van service Fabric systeem services en de algehele cluster functie wordt bepaald

Dit artikel begeleidt u stapsgewijs door de belang rijke beslissings punten voor elk van deze gebieden.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>Oorspronkelijk aantal en eigenschappen van cluster knooppunt typen

Een *knooppunt type* definieert de grootte, het aantal en de eigenschappen van een set knoop punten (virtuele machines) in het cluster. Elk knooppunt type dat in een Service Fabric cluster is gedefinieerd, wordt toegewezen aan een [schaalset voor virtuele machines](../virtual-machine-scale-sets/overview.md).

Omdat elk knooppunt type een afzonderlijke schaalset is, kan deze afzonderlijk worden uitgebreid of omlaag worden geschaald, er verschillende sets poorten zijn geopend en verschillende capaciteits metrieken hebben. Zie [service Fabric cluster knooppunt typen](service-fabric-cluster-nodetypes.md)voor meer informatie over de relatie tussen knooppunt typen en virtuele-machine schaal sets.

Voor elk cluster is één **primair knooppunt type** vereist, waarmee essentiële systeem services worden uitgevoerd die de mogelijkheden van service Fabric platform bieden. Hoewel het mogelijk is om ook primaire knooppunt typen te gebruiken om uw toepassingen uit te voeren, is het raadzaam ze alleen te reserveren voor het uitvoeren van systeem services.

**Niet-primaire knooppunt typen** kunnen worden gebruikt om toepassings rollen (zoals *front-end-* en *back-end-* Services) te definiëren en om services in een cluster fysiek te isoleren. Service Fabric clusters kunnen nul of meer niet-primaire knooppunt typen hebben.

Het primaire knooppunt type is geconfigureerd met behulp `isPrimary` van het kenmerk onder de definitie van het knooppunt type in de sjabloon Azure Resource Manager-implementatie. Zie het [object NodeTypeDescription](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) voor de volledige lijst met eigenschappen van knooppunt typen. Open bijvoorbeeld een *AzureDeploy.js* in het bestand in [service Fabric cluster voorbeelden](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) en zoek *op pagina* zoeken naar het `nodeTypes` object.

### <a name="node-type-planning-considerations"></a>Overwegingen bij de planning van knooppunt typen

Het aantal eerste typen knoop punten is afhankelijk van het doel van het cluster en de toepassingen en services die erop worden uitgevoerd. Denk na over de volgende vragen:

* ***Biedt uw toepassing meerdere services en moeten hiervan een of meer service openbaar zijn of op internet gericht?***

    Typische toepassingen bevatten een front-end Gateway Service die invoer ontvangt van een client en een of meer back-end-services die communiceren met de front-end-services, met afzonderlijke netwerken tussen de front-end-en back-end-services. Deze gevallen vereisen doorgaans drie knooppunt typen: één primair knooppunt type en twee niet-primaire knooppunt typen (één voor de front-en back-end-service).

* ***Hebben de services die uw toepassing vormen, verschillende vereisten voor de infra structuur, zoals meer RAM-geheugen of hogere CPU-cycli?***

    De front-end-service kan vaak worden uitgevoerd op kleinere Vm's (VM-grootten zoals D2) die poorten hebben geopend op internet.  Computerintensieve back-end-services moeten mogelijk worden uitgevoerd op grotere Vm's (met VM-grootten zoals D4, D6, D15) die niet Internet gericht zijn. Als u verschillende knooppunt typen voor deze services definieert, kunt u het gebruik van onderliggende Service Fabric Vm's efficiënter en veilig maken en kunnen ze onafhankelijk van elkaar worden geschaald. Zie [capaciteits planning voor service Fabric-toepassingen](service-fabric-capacity-planning.md) voor meer informatie over het schatten van de hoeveelheid resources die u nodig hebt

* ***Moeten uw toepassings services buiten 100 knoop punten uitschalen?***

    Een type knoop punt kan niet betrouwbaar worden geschaald naast 100 knoop punten per virtuele-machine schaalset voor Service Fabric toepassingen. Voor het uitvoeren van meer dan 100-knoop punten zijn extra virtuele-machine schaal sets (en dus extra knooppunt typen) vereist.

* ***Bespant uw cluster zich over Beschikbaarheidszones?***

    Service Fabric ondersteunt clusters die over meerdere [Beschikbaarheidszones](../availability-zones/az-overview.md) beschikken door knooppunt typen te implementeren die zijn vastgemaakt aan specifieke zones, waardoor uw toepassingen een hoge Beschik baarheid bieden. Beschikbaarheidszones vereisen extra planning van het type knoop punt en minimale vereisten. Zie voor meer informatie [aanbevolen topologie voor het primaire knooppunt type van service Fabric clusters over Beschikbaarheidszones](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones). 

Bij het bepalen van het aantal en de eigenschappen van knooppunt typen voor het maken van de eerste keer dat u uw cluster maakt, moet u ervoor zorgen dat u altijd (niet-primaire) knooppunt typen kunt toevoegen, wijzigen of verwijderen zodra het cluster is geïmplementeerd. [Primaire knooppunt typen kunnen ook worden gewijzigd](service-fabric-scale-up-primary-node-type.md) in actieve clusters (hoewel voor dergelijke bewerkingen veel planning en waarschuwing in productie omgevingen nodig zijn).

Een verdere overweging voor de eigenschappen van het knooppunt type is duurzaamheids niveau, waarmee de bevoegdheden worden bepaald van de Vm's van een knooppunt type in de Azure-infra structuur. Gebruik de grootte van de virtuele machines die u voor uw cluster kiest en het aantal instanties dat u toewijst voor afzonderlijke knooppunt typen om te helpen bij het bepalen van de juiste duurzaamheids categorie voor elk van uw knooppunt typen, zoals hierna wordt beschreven.

## <a name="durability-characteristics-of-the-cluster"></a>Duurzaamheids kenmerken van het cluster

Het *duurzaamheids niveau* geeft u de bevoegdheden op die uw service Fabric vm's hebben met de onderliggende Azure-infra structuur. Met deze bevoegdheid kunnen Service Fabric alle infrastructuur aanvragen op VM-niveau (zoals opnieuw opstarten, opnieuw installatie kopieën of migratie) onderbreken die van invloed zijn op de quorum vereisten voor Service Fabric systeem services en uw stateful Services.

> [!IMPORTANT]
> Duurzaamheids niveau is ingesteld per knooppunt type. Als er geen is opgegeven, wordt de categorie *Bronze* gebruikt, maar biedt deze geen automatische upgrades van besturings systemen. *Silver* -of *Gold* -duurzaamheid wordt aanbevolen voor productie werkbelastingen.

De volgende tabel bevat een lijst met Service Fabric duurzaamheids lagen, hun vereisten en affordances.

| Duurzaamheids categorie  | Vereist minimum aantal Vm's | Ondersteunde VM-grootten                                                                  | Updates die u aanbrengt in de schaalset voor virtuele machines                               | Updates en onderhoud geïnitieerd door Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Goud             | 5                              | Volledige grootten die zijn toegewezen aan één klant (bijvoorbeeld L32s, GS5, G5, DS15_v2, D15_v2) | Kan worden uitgesteld tot goedgekeurd door het Service Fabric-cluster | Kan gedurende 2 uur per upgrade domein worden onderbroken om meer tijd te bieden voor het herstellen van eerdere fouten in replica's |
| Zilver           | 5                              | Vm's van één kern of hoger met ten minste 50 GB lokale SSD                      | Kan worden uitgesteld tot goedgekeurd door het Service Fabric-cluster | Kan gedurende een belang rijke periode niet worden uitgesteld                                                    |
| Bron          | 1                              | Vm's met ten minste 50 GB lokale SSD                                              | Wordt niet vertraagd door het Service Fabric cluster           | Kan gedurende een belang rijke periode niet worden uitgesteld                                                    |

> [!NOTE]
> Het hierboven vermelde minimum aantal Vm's is een vereiste vereiste voor elk niveau van de duurzaamheid. Er zijn geldige validaties waardoor het maken of aanpassen van bestaande virtuele-scalesets die niet aan deze vereisten voldoen, wordt voor komen.

> [!WARNING]
> Met Bronze duurzaamheid is de automatische upgrade van de installatie kopie van het besturings systeem niet beschikbaar. Hoewel [patch Orchestration-toepassing](service-fabric-patch-orchestration-application.md) (alleen bedoeld voor niet door Azure gehoste clusters) *niet wordt aanbevolen* voor Silver-of hogere duurzaamheids niveaus, is dit de enige optie om Windows-updates te automatiseren met betrekking tot service Fabric upgrade domeinen.

> [!IMPORTANT]
> Ongeacht het duurzaamheids niveau wordt het cluster vernietigd als er een [toewijzings](/rest/api/compute/virtualmachinescalesets/deallocate) bewerking wordt uitgevoerd op een schaalset voor virtuele machines.

### <a name="bronze"></a>Bron

Voor knooppunt typen met de duurzaamheid Bronze zijn geen bevoegdheden nodig. Dit betekent dat infrastructuur taken die van invloed zijn op uw stateful werk belastingen niet worden gestopt of uitgesteld. Gebruik Bronze duurzaamheid voor knooppunt typen die alleen stateless werk belastingen uitvoeren. Voor werk belastingen met Silver of hoger wordt aanbevolen.

### <a name="silver-and-gold"></a>Zilver en goud

Gebruik Silver of Gold-duurzaamheid voor alle knooppunt typen die stateful services hosten die u verwacht te schalen, en waar u wilt dat implementatie bewerkingen worden vertraagd en de capaciteit moet worden verminderd om het proces te vereenvoudigen. Scale-out-scenario's mogen niet van invloed zijn op uw keuze van de duurzaamheids categorie.

#### <a name="advantages"></a>Voordelen

* Hiermee beperkt u het aantal vereiste stappen voor schaal bewerkingen (het deactiveren van knoop punten en Remove-ServiceFabricNodeState worden automatisch aangeroepen).
* Vermindert het risico op gegevens verlies als gevolg van een in-place VM-grootte wijzigings bewerkingen en Azure-infrastructuur bewerkingen.

#### <a name="disadvantages"></a>Nadelen

* Implementaties van virtuele-machine schaal sets en andere verwante Azure-resources kunnen een time-out veroorzaken, worden vertraagd of volledig worden geblokkeerd door problemen in uw cluster of op het niveau van de infra structuur.
* Hiermee verhoogt u het aantal gebeurtenissen voor de [levens cyclus](service-fabric-reliable-services-lifecycle.md) van de replica (bijvoorbeeld primaire swaps) als gevolg van automatische activering van knoop punten tijdens Azure-infrastructuur bewerkingen.
* Neemt knoop punten buiten dienst voor Peri Oden terwijl software-updates van Azure platform of activiteiten voor onderhoud van hardware worden uitgevoerd. U ziet mogelijk knoop punten met de status uitschakelen/uitgeschakeld tijdens deze activiteiten. Dit vermindert de capaciteit van uw cluster tijdelijk, maar heeft geen invloed op de beschik baarheid van uw cluster of toepassingen.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>Aanbevolen procedures voor de knooppunt typen zilver en goud, duurzaamheid

Volg deze aanbevelingen voor het beheren van knooppunt typen met Silver of Gold duurzaamheid:

* Houd uw cluster en toepassingen te allen tijde in orde en zorg ervoor dat toepassingen reageren op alle gebeurtenissen van de [levens cyclus van de service replica](service-fabric-reliable-services-lifecycle.md) (zoals replica in Build is vastgelopen) tijdig.
* U kunt veiliger manieren instellen om een VM-grootte te wijzigen (omhoog/omlaag schalen). Het wijzigen van de VM-grootte van een schaalset voor virtuele machines vereist een zorgvuldige planning en voorzichtigheid. Zie [een service Fabric knooppunt type omhoog schalen](service-fabric-scale-up-primary-node-type.md) voor meer informatie
* Behoud een minimum aantal van vijf knoop punten voor een schaalset voor virtuele machines met duurzaamheids niveau goud of zilver ingeschakeld. In uw cluster wordt de fout status ingevoerd als u onder deze drempel waarde schaalt en u moet de status () hand matig opschonen `Remove-ServiceFabricNodeState` voor de verwijderde knoop punten.
* Elke schaalset voor virtuele machines met duurzaamheids niveau zilver of goud moet worden toegewezen aan het eigen knooppunt type in het Service Fabric cluster. Als u meerdere virtuele-machine schaal sets aan één knooppunt type toewijst, kan de coördinatie tussen het Service Fabric cluster en de Azure-infra structuur niet goed werken.
* Verwijder geen wille keurige VM-exemplaren, gebruik altijd schaal sets voor virtuele machines in functie. Als u wille keurige VM-exemplaren verwijdert, is het mogelijk dat er onbalansen in de VM-instantie worden gemaakt over [upgrade domeinen](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) en [fout domeinen](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains). Dit onevenwichtige kan een nadelige invloed hebben op de systeem capaciteit van de service-exemplaren/service-replica's.
* Als u automatisch schalen gebruikt, stelt u de regels zodanig in dat er slechts één knoop punt tegelijk wordt geschaald (verwijderen van VM-exemplaren). Het omlaag schalen van meer dan één exemplaar op een keer is niet veilig.
* Als u Vm's verwijdert of ongedaan maakt voor het primaire knooppunt type, verlaagt u nooit het aantal toegewezen Vm's hieronder wat de betrouwbaarheids categorie nodig heeft. Deze bewerkingen worden voor onbepaalde tijd geblokkeerd in een schaalset met een duurzaamheids niveau van zilver of goud.

### <a name="changing-durability-levels"></a>Duurzaamheids niveaus wijzigen

Binnen bepaalde beperkingen kan het duurzaamheids niveau van het knooppunt type worden aangepast:

* Knooppunt typen met duurzaamheids niveau zilver of goud kunnen niet worden gedowngraded tot Bronze.
* Een upgrade van bronzen naar Silver of Gold kan enkele uren duren.
* Bij het wijzigen van het duurzaamheids niveau moet u het bijwerken in zowel de configuratie van de Service Fabric extensie in de resource van de virtuele-machine schaalset als in de definitie van het knooppunt type in uw Service Fabric cluster bron. Deze waarden moeten overeenkomen.

Een andere overweging bij het plannen van de capaciteit is het betrouwbaarheids niveau voor uw cluster, waarmee de stabiliteit van systeem services en het algehele cluster wordt bepaald, zoals beschreven in de volgende sectie.

## <a name="reliability-characteristics-of-the-cluster"></a>Betrouwbaarheids kenmerken van het cluster

Het *betrouwbaarheids niveau* van het cluster bepaalt het aantal systeem services-replica's dat wordt uitgevoerd op het primaire knooppunt type van het cluster. Hoe meer replica's, hoe betrouwbaarder de systeem services zijn (en dus het cluster als geheel).

> [!IMPORTANT]
> Het betrouwbaarheids niveau is ingesteld op het cluster niveau en bepaalt het minimum aantal knoop punten van het primaire knooppunt type. Voor productie werkbelastingen is een betrouwbaarheids niveau van zilver (groter of gelijk aan vijf knoop punten) of hoger vereist.  

De betrouwbaarheids categorie kan de volgende waarden hebben:

* **Platinum** -systeem services worden uitgevoerd met het aantal negen doel replica sets
* **Gold** -systeem services worden uitgevoerd met het aantal zeven in de doel replicaset
* **Silver** -systeem services worden uitgevoerd met het aantal vijf voor de doel replicaset
* **Bronze** -systeem services worden uitgevoerd met het aantal drie doel replica sets

Hier volgt een aanbeveling bij het kiezen van de betrouwbaarheids categorie. Het aantal Seed-knoop punten is ook ingesteld op het minimum aantal knoop punten voor een betrouwbaarheids categorie.


| **Aantal knooppunten** | **Betrouwbaarheids niveau** |
| --- | --- |
| 1 | *Geef de `reliabilityLevel` para meter niet op: het systeem berekent deze.* |
| 3 | Bron |
| 5 of 6| Zilver |
| 7 of 8 | Goud |
| 9 en Maxi maal | Platina |

Wanneer u de grootte van uw cluster (de som van VM-exemplaren in alle knooppunt typen) verhoogt of verlaagt, kunt u overwegen de betrouw baarheid van het cluster bij te werken van de ene laag naar de andere. Hiermee worden de cluster upgrades geactiveerd die nodig zijn om het aantal replica sets van de systeem services te wijzigen. Wacht totdat de upgrade wordt uitgevoerd voordat u andere wijzigingen aanbrengt in het cluster, zoals het toevoegen van knoop punten.  U kunt de voortgang van de upgrade op Service Fabric Explorer controleren of door [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) uit te voeren

### <a name="capacity-planning-for-reliability"></a>Capaciteits planning voor betrouw baarheid

De capaciteits behoeften van uw cluster worden bepaald door uw specifieke workload-en betrouwbaarheids vereisten. In deze sectie vindt u algemene richt lijnen om aan de slag te gaan met capaciteits planning.

#### <a name="virtual-machine-sizing"></a>Grootte van virtuele machine

**Voor werk belastingen voor productie is de aanbevolen VM-grootte (SKU) [standaard D2_V2](../virtual-machines/dv2-dsv2-series.md) (of gelijkwaardig) met mini maal 50 GB lokale SSD, 2 kernen en 4 GiB geheugen.** Een lokale SSD van 50 GB wordt aanbevolen, maar sommige werk belastingen (zoals computers met Windows-containers) vereisen een grotere schijf. Houd bij het kiezen van andere [VM-grootten](../virtual-machines/sizes-general.md) voor werk belastingen de volgende beperkingen in acht:

- Gedeeltelijke VM-grootten, zoals Standard a0, worden niet ondersteund.
- *A-serie* VM-grootten worden niet ondersteund om prestatie redenen.
- Vm's met een lage prioriteit worden niet ondersteund.

#### <a name="primary-node-type"></a>Type primair knoop punt

Voor **productie werkbelastingen** op Azure zijn mini maal vijf primaire knoop punten (VM-exemplaren) en de betrouwbaarheids laag zilver vereist. Het is raadzaam om het primaire knooppunt type van het cluster toe te wijzen aan systeem services en plaatsings beperkingen te gebruiken voor het implementeren van uw toepassing naar secundaire knooppunt typen.

**Test werkbelastingen** in azure kunnen mini maal één of drie primaire knoop punten uitvoeren. Als u een cluster met één knoop punt wilt configureren, moet u ervoor zorgen dat de `reliabilityLevel` instelling volledig is wegge laten in uw Resource Manager-sjabloon (het opgeven van een lege teken reeks waarde voor `reliabilityLevel` is niet voldoende). Als u het cluster met één knoop punt instelt met Azure Portal, wordt deze configuratie automatisch uitgevoerd.

> [!WARNING]
> Clusters met één knoop punt worden uitgevoerd met een speciale configuratie zonder betrouw baarheid en waarbij uitschalen niet wordt ondersteund.

#### <a name="non-primary-node-types"></a>Niet-primaire knooppunt typen

Het minimum aantal knoop punten voor een niet-primair knooppunt type is afhankelijk van het niveau van de specifieke [duurzaamheid](#durability-characteristics-of-the-cluster) van het knooppunt type. U moet het aantal knoop punten (en duurzaamheids niveau) plannen op basis van het aantal replica's van toepassingen of services dat u wilt uitvoeren voor het knooppunt type en, afhankelijk van het feit of de werk belasting stateful of stateless is. Houd er rekening mee dat u het aantal Vm's in een knooppunt type op elk gewenst moment kunt verhogen of verlagen nadat u het cluster hebt geïmplementeerd.

##### <a name="stateful-workloads"></a>Stateful werk belastingen

Voor stateful productie workloads die gebruikmaken van Service Fabric [betrouw bare verzamelingen of betrouw bare actors](service-fabric-choose-framework.md), wordt een minimum-en doel replica aantal van vijf aanbevolen. Met deze, in constante status, loopt u in elk fout domein en upgrade domein een replica (van een replicaset). In het algemeen gebruikt u het betrouwbaarheids niveau dat u hebt ingesteld voor systeem services als richt lijn voor het aantal replica's dat u voor uw stateful Services gebruikt.

##### <a name="stateless-workloads"></a>Staatloze workloads

Voor stateless productie workloads is de minimale ondersteunde grootte van het niet-primaire knooppunt type drie voor het behouden van quorum, maar een knooppunt grootte van vijf wordt aanbevolen.

## <a name="next-steps"></a>Volgende stappen

Voordat u uw cluster configureert, controleert `Not Allowed` u het [cluster upgrade beleid](service-fabric-cluster-fabric-settings.md) om te voor komen dat u later uw cluster opnieuw moet maken vanwege andere onveranderbare systeem configuratie-instellingen.

Zie voor meer informatie over het plannen van clusters:

* [Compute-planning en schalen](service-fabric-best-practices-capacity-scaling.md)
* [Capaciteits planning voor Service Fabric toepassingen](service-fabric-capacity-planning.md)
* [Planning voor nood herstel](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
