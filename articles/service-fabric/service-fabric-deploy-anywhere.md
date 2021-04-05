---
title: Overzicht van Azure en zelfstandige Service Fabric clusters
description: U kunt Service Fabric clusters maken op alle Vm's of computers met Windows Server of Linux. Dit betekent dat u Service Fabric-toepassingen kunt implementeren en uitvoeren in elke omgeving waar u een set Windows Server-of Linux-computers hebt die met elkaar zijn verbonden, Microsoft Azure of met een Cloud provider.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576125"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Azure en zelfstandige Service Fabric clusters in Windows Server en Linux vergelijken

Een Service Fabric-cluster is een met het netwerk verbonden reeks virtuele of fysieke machines waarop uw microservices worden geïmplementeerd en beheerd. Een computer of virtuele machine die deel uitmaakt van een cluster, wordt een cluster knooppunt genoemd. Clusters kunnen worden geschaald naar duizenden knoop punten. Als u nieuwe knoop punten aan het cluster toevoegt, worden in Service Fabric de service partitie replica's en instanties over het verhoogde aantal knoop punten gebalanceerd. De algehele prestaties van toepassingen verbeteren en conflicten voor toegang tot het geheugen neemt af. Als de knoop punten in het cluster niet efficiënt worden gebruikt, kunt u het aantal knoop punten in het cluster verlagen. Service Fabric opnieuw, worden de partitie replica's en instanties over het aantal knoop punten verkleind om beter gebruik te maken van de hardware op elk knoop punt.

Met Service Fabric kunt u Service Fabric-clusters maken op alle Vm's of computers met Windows Server of Linux. Dit betekent dat u Service Fabric-toepassingen kunt implementeren en uitvoeren in elke omgeving waar u een set Windows Server-of Linux-computers hebt die onderling zijn verbonden, deze on-premises, Microsoft Azure of met een Cloud provider.

## <a name="benefits-of-clusters-on-azure"></a>Voor delen van clusters op Azure

In azure bieden we integratie met andere Azure-functies en-services, waardoor bewerkingen en beheer van het cluster eenvoudiger en betrouwbaarder kunnen zijn.

* **Azure portal:** Azure Portal maakt het eenvoudig om clusters te maken en te beheren.
* **Azure Resource Manager:** Gebruik van Azure Resource Manager maakt eenvoudig beheer mogelijk van alle resources die door het cluster als eenheid worden gebruikt en vereenvoudigt het bijhouden en factureren van kosten.
* **Service Fabric cluster als een Azure-resource** Een Service Fabric cluster is een Azure-resource, zodat u het kunt model leren als andere resources in Azure.
* **Integratie met Azure-infra structuur** Service Fabric coördineert met de onderliggende Azure-infra structuur voor het besturings systeem, het netwerk en andere upgrades om de beschik baarheid en betrouw baarheid van uw toepassingen te verbeteren.  
* **Diagnostische gegevens:** In azure bieden we integratie met Azure Diagnostics en Azure Monitor logs.
* Automatisch **schalen:** Voor clusters op Azure bieden we ingebouwde functionaliteit voor automatisch schalen via schaal sets van virtuele machines. In on-premises en andere Cloud omgevingen moet u uw eigen functie voor automatisch schalen maken of hand matig schalen met behulp van de Api's die Service Fabric voor het schalen van clusters beschikbaar stelt.

## <a name="benefits-of-standalone-clusters"></a>Voor delen van zelfstandige clusters

* U kunt kiezen voor een Cloud provider om uw cluster te hosten.
* Service Fabric toepassingen, zodra ze zijn geschreven, kunnen worden uitgevoerd in meerdere hosting omgevingen met minimale tot geen wijzigingen.
* Kennis van het bouwen van Service Fabric toepassingen van een hostomgeving naar een andere omgeving.
* Operationele ervaring van het uitvoeren en beheren van Service Fabric clusters is van de ene omgeving naar de andere.
* Breed bereik van de klant is onbegrensd door de beperkingen van de hosting omgeving.
* Er bestaat een extra laag van betrouw baarheid en beveiliging tegen wijde uitval, omdat u de Services kunt verplaatsen naar een andere implementatie omgeving als een Data Center of Cloud provider een stand-by heeft.

## <a name="next-steps"></a>Volgende stappen

* Lees het overzicht van [service Fabric clusters op Azure](service-fabric-azure-clusters-overview.md)
* Lees het overzicht van [service Fabric zelfstandige clusters](service-fabric-standalone-clusters-overview.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)