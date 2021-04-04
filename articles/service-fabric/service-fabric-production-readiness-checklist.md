---
title: Controle lijst voor productie voorbereiding van Azure Service Fabric
description: Down load uw Service Fabric-toepassing en cluster productie gereed door de aanbevolen procedures te volgen.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: a03df40a8ce213c5de9ed7017d47713c4de3449d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87835864"
---
# <a name="production-readiness-checklist"></a>Controlelijst voor productiegereedheid

Is uw toepassing en cluster klaar om productie verkeer te nemen? Het uitvoeren en testen van uw toepassing en uw cluster betekent niet noodzakelijkerwijs dat deze klaar is om in productie te gaan. Zorg ervoor dat uw toepassing en cluster probleemloos werken door de volgende controle lijst te gebruiken. We raden u ten zeerste aan om al deze items uit te scha kelen. U kunt er natuurlijk voor kiezen om alternatieve oplossingen te gebruiken voor een bepaald regel item (bijvoorbeeld uw eigen diagnose raamwerken).


## <a name="prerequisites-for-production"></a>Vereisten voor productie
1. Aanbevolen procedures voor Azure Service Fabric: [toepassings ontwerp](./service-fabric-best-practices-applications.md), [beveiliging](./service-fabric-best-practices-security.md), [netwerken](./service-fabric-best-practices-networking.md), [capaciteits planning en schalen](./service-fabric-best-practices-capacity-scaling.md), [infra structuur als code](./service-fabric-best-practices-infrastructure-as-code.md)en [controle en diagnose](./service-fabric-best-practices-monitoring.md). 
1. [Configureer FabricTransport-instellingen](./service-fabric-reliable-actors-fabrictransportsettings.md) als u het programmeer model van reliable actors gebruikt en beveiligde communicatie tussen services vereist.
1. Voor clusters met meer dan 20 kernen of tien knoop punten maakt u een specifiek primair knooppunt type voor systeem services. Voeg [plaatsings beperkingen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) toe om het primaire knooppunt type voor systeem services te reserveren.
1. Gebruik een virtuele d2v2 of hogere SKU voor het primaire knooppunt type. U kunt het beste een SKU kiezen met ten minste 50 GB aan vaste schijf capaciteit.
1. Productie clusters moeten [veilig](service-fabric-cluster-security.md)zijn. Zie deze [cluster sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)voor een voor beeld van het instellen van een beveiligd cluster. Gebruik algemene namen voor certificaten en Vermijd het gebruik van zelf-ondertekende certificaten.
1. Voeg [resource beperkingen toe aan containers en services](service-fabric-resource-governance.md), zodat ze niet meer dan 75% van knooppunt bronnen gebruiken. 
1. Begrijp en stel het [duurzaamheids niveau](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)in. Het niveau van Silver of hogere duurzaamheid wordt aanbevolen voor knooppunt typen die stateful werk belastingen uitvoeren.
1. Het [betrouwbaarheids niveau](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) van het knooppunt type begrijpen en kiezen. De betrouw baarheid van zilver of hoger wordt aanbevolen.
1. Laad en schaal uw workloads om de [capaciteits vereisten](service-fabric-cluster-capacity.md) voor uw cluster te bepalen. 
1. Uw services en toepassingen worden bewaakt en toepassings logboeken worden gegenereerd en opgeslagen, met waarschuwingen. Zie bijvoorbeeld [logboek registratie toevoegen aan uw service Fabric-toepassing](service-fabric-how-to-diagnostics-log.md) en [containers controleren met Azure monitor-logboeken](service-fabric-diagnostics-oms-containers.md).
1. Het cluster wordt bewaakt met waarschuwingen (bijvoorbeeld met [Azure monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md)). 
1. De onderliggende infra structuur voor virtuele-machine schaal sets wordt bewaakt met waarschuwingen (bijvoorbeeld met [Azure monitor-logboeken](service-fabric-diagnostics-oms-agent.md).
1. Het cluster heeft altijd [primaire en secundaire certificaten](service-fabric-cluster-security-update-certs-azure.md) (zodat u geen vergren deling krijgt).
1. Afzonderlijke clusters onderhouden voor ontwikkeling, fase ring en productie. 
1. [Toepassings upgrades](service-fabric-application-upgrade.md) en [cluster upgrades](service-fabric-tutorial-upgrade-cluster.md) worden eerst getest in ontwikkelings-en faserings clusters. 
1. Schakel automatische upgrades in productie clusters uit en schakel deze in voor ontwikkelings-en faserings clusters (indien nodig terugdraaien). 
1. Stel een beoogd herstel punt (RPO) in voor uw service en installeer een [nood herstel proces](service-fabric-disaster-recovery.md) en test het.
1. Plan uw cluster hand matig of via een programma te [schalen](service-fabric-cluster-scaling.md) .
1. Plan voor het [patchen](service-fabric-patch-orchestration-application.md) van uw cluster knooppunten. 
1. Stel een CI/CD-pijp lijn in zodat uw meest recente wijzigingen continu worden getest. Bijvoorbeeld met behulp van [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) of [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster)
1. Test uw ontwikkeling & faserings clusters onder belasting met de [fout Analysis-Service](service-fabric-testability-overview.md) en stel beheerde [chaos](service-fabric-controlled-chaos.md). 
1. Plan uw toepassingen te [schalen](service-fabric-concepts-scalability.md) . 


Als u de Service Fabric Reliable Services of Reliable Actors programmeer model gebruikt, moeten de volgende items worden uitgecheckt:
1. Upgrade toepassingen tijdens de lokale ontwikkeling om te controleren of uw service code het annulerings token in de `RunAsync` methode en de aangepaste communicatie-listeners heeft geaccepteerd.
1. Vermijd [veelvoorkomende Valk uilen](service-fabric-work-with-reliable-collections.md) wanneer u betrouw bare verzamelingen gebruikt.
1. Bewaak de prestatie meter items voor .NET CLR-geheugen bij het uitvoeren van belasting tests en controleer op hoge frequenties van garbagecollection of overmatige heap-groei.
1. Onderhoud van de offline back-up van [reliable Services en reliable actors](service-fabric-reliable-services-backup-restore.md) en het herstel proces testen.
1. Het aantal primaire NodeType van de virtuele machine moet idea liter gelijk zijn aan het minimum voor de betrouwbaarheids categorie van uw clusters; de voor waarden die nodig zijn om de laag mini maal te overschrijden, zijn: tijdelijk bij het verticaal schalen van uw primaire NodeTypes voor de Schaalset van virtuele machines.

## <a name="optional-best-practices"></a>Optionele aanbevolen procedures

Hoewel de bovenstaande lijsten vereisten zijn om in productie te gaan, moeten de volgende items ook worden beschouwd:
1. Sluit u aan bij het [service Fabric status model](service-fabric-health-introduction.md) voor het uitbreiden van de ingebouwde status evaluatie en rapportage.
1. Implementeer een aangepaste watchdog die uw toepassingen en rapporten bewaken die worden [geladen](service-fabric-cluster-resource-manager-metrics.md) voor [resource verdeling](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Volgende stappen
* [Een Service Fabric Windows-cluster implementeren](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Een Service Fabric Linux-cluster implementeren](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Meer informatie over de [levenscyclus](service-fabric-application-lifecycle.md) van de Service Fabric-toepassing.