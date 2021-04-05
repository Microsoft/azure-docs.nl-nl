---
title: Schaal aanpassing van het zelfstandige Azure-Cluster Service Fabric
description: Meer informatie over het schalen van Service Fabric zelfstandige clusters in of uit en omhoog of omlaag.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 8184b2455e938fa0500308b462176e78b8dadcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843028"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Service Fabric van zelfstandige clusters schalen
Een Service Fabric-cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een knoop punt genoemd. Clusters kunnen mogelijk duizenden knoop punten bevatten. Nadat u een Service Fabric cluster hebt gemaakt, kunt u het cluster horizon taal schalen (Wijzig het aantal knoop punten) of verticaal (Wijzig de resources van de knoop punten).  U kunt de schaal van het cluster op elk gewenst moment aanpassen, zelfs als er workloads op het cluster worden uitgevoerd.  Tijdens het schalen van het cluster worden uw toepassingen ook automatisch geschaald.

Waarom het cluster schalen? Toepassings vereisten veranderen in de loop van de tijd.  Mogelijk moet u cluster bronnen verg Roten om te voldoen aan de toegenomen werk belasting van de toepassing of het netwerk verkeer of cluster bronnen verlagen wanneer de vraag wordt neergezet.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>In-en uitschalen of horizon taal schalen
Hiermee wijzigt u het aantal knoop punten in het cluster.  Zodra de nieuwe knoop punten aan het cluster zijn toegevoegd, worden de services door de [cluster resource manager](service-fabric-cluster-resource-manager-introduction.md) verplaatst, waardoor de belasting van de bestaande knoop punten vermindert.  U kunt ook het aantal knoop punten verlagen als de resources van het cluster niet efficiënt worden gebruikt.  Als knoop punten het cluster verlaten, worden deze knoop punten door de services verplaatst en nemen de taken toe op de resterende knoop punten.  Het verminderen van het aantal knoop punten in een cluster dat in azure wordt uitgevoerd, bespaart u geld, omdat u betaalt voor het aantal Vm's dat u gebruikt en niet de werk belasting van deze Vm's.  

- Voor delen: oneindig schalen, in theorie.  Als uw toepassing is ontworpen voor schaal baarheid, kunt u onbeperkte groei inschakelen door meer knoop punten toe te voegen.  Met het hulp programma in Cloud omgevingen kunt u eenvoudig knoop punten toevoegen of verwijderen, zodat u de capaciteit eenvoudig kunt aanpassen en u alleen betaalt voor de resources die u gebruikt.  
- Nadelen: toepassingen moeten zijn [ontworpen voor schaal baarheid](service-fabric-concepts-scalability.md).  Voor toepassings databases en-persistentie is het mogelijk dat extra architectuur werk ook kan worden geschaald.  [Betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md) in service Fabric stateful Services, maar maak het veel eenvoudiger om uw toepassings gegevens te schalen.

Met zelfstandige clusters kunt u Service Fabric cluster on-premises of in de Cloud provider van uw keuze implementeren.  Knooppunt typen bestaan uit fysieke machines of virtuele machines, afhankelijk van uw implementatie. Vergeleken met clusters die worden uitgevoerd in azure, is het proces van het schalen van een zelfstandig cluster iets meer betrokken.  U moet het aantal knoop punten in het cluster hand matig wijzigen en vervolgens een upgrade van de cluster configuratie uitvoeren.

Het verwijderen van knoop punten kan meerdere upgrades initiëren. Sommige knoop punten zijn gemarkeerd met `IsSeedNode=”true”` tag en kunnen worden geïdentificeerd door het cluster manifest te doorzoeken met [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Het verwijderen van deze knoop punten kan langer duren dan andere omdat de Seed-knoop punten in dergelijke scenario's moeten worden verplaatst. Het cluster moet mini maal drie knoop punten van het primaire knooppunt type onderhouden.

> [!WARNING]
> We raden u aan het aantal knoop punten te verlagen onder de [cluster grootte van de betrouwbaarheids categorie](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) voor het cluster. Dit zorgt ervoor dat de Service Fabric systeem services niet via het cluster kunnen worden gerepliceerd en het cluster kan worden overschreven of vernietigd.
>

Houd bij het schalen van een zelfstandig cluster de volgende richt lijnen in acht:
- De vervanging van primaire knoop punten moet na een andere knoop punt worden uitgevoerd, in plaats van verwijderen en vervolgens in batches toe te voegen.
- Controleer voordat u een knooppunt type verwijdert of er knoop punten zijn die verwijzen naar het knooppunt type. Verwijder deze knoop punten voordat u het bijbehorende knooppunt type verwijdert. Als alle bijbehorende knoop punten zijn verwijderd, kunt u het NodeType verwijderen uit de cluster configuratie en een configuratie-upgrade starten met [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Zie [een zelfstandig cluster schalen](service-fabric-cluster-windows-server-add-remove-nodes.md)voor meer informatie.

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Omhoog en omlaag schalen of verticaal schalen 
Hiermee wijzigt u de resources (CPU, geheugen of opslag) van knoop punten in het cluster.
- Voor delen: de software-en toepassings architectuur blijft hetzelfde.
- Nadelen: eindige schaal, omdat er een limiet is voor de hoeveelheid resources op afzonderlijke knoop punten. Uitval tijd, omdat u fysieke of virtuele machines offline moet halen om resources toe te voegen of te verwijderen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in-of uitschalen](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met behulp van de Fluent Azure Compute SDK.
* [Een zelfstandige cluster in-of uitschalen](service-fabric-cluster-windows-server-add-remove-nodes.md).

