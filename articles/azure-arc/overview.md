---
title: Overzicht van Azure Arc
description: Lees meer over wat Azure Arc is en hoe het klanten helpt om het beheer en de governance van hun hybride resources met andere Azure-services en -functies mogelijk te maken.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8f39dff16037b5f52a050b7d07193b0ad7cab93d
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672683"
---
# <a name="azure-arc-overview"></a>Overzicht van Azure Arc

Vandaag de dag hebben bedrijven moeite om een omgeving te controleren en te beheren die steeds complexer wordt. Deze omgevingen omvatten datacentra, meerdere clouds en de rand. Alle omgevingen en clouds hebben hun eigen set beheerprogramma's waarmee u moet leren werken en die u moet gebruiken.

Tegelijkertijd is het moeilijk nieuwe operationele DevOps- en ITOps-modellen te implementeren, omdat de bestaande hulpprogramma's de nieuwe cloudeigen patronen niet ondersteunen.

Azure Arc vereenvoudigt governance en beheer door een consistent beheerplatform voor meerdere clouds en on-premises te leveren. Met Azure Arc kunt u uw hele omgeving beheren op één glasplaat, door uw bestaande resources te projecteren naar Azure Resource Manager. U kunt nu virtuele machines, Kubernetes-clusters en databases beheren alsof ze worden uitgevoerd in Azure. Ongeacht waar ze zich bevinden, kunt u gebruikmaken van vertrouwde services en beheermogelijkheden van Azure. Met Azure Arc kunt u traditionele ITOps blijven gebruiken, terwijl u DevOps-procedures introduceert ter ondersteuning van nieuwe cloudeigen patronen in uw omgeving.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagram van het besturingsvlak van Azure Arc" border="false":::

Vandaag de dag kunt u met Azure Arc de volgende resourcetypen beheren die buiten Azure worden gehost:

* Server: zowel fysieke als virtuele machines met Windows of Linux.
* Kubernetes-clusters: ondersteuning voor meerdere Kubernetes-distributies.
* Azure-gegevensservices: - Azure SQL Database- en PostgreSQL Hyperscale-services.

## <a name="what-does-azure-arc-deliver"></a>Wat doet Azure Arc?

Belangrijke functies van Azure Arc zijn:

* Implementeren van een consistente inventaris, beheer, governance en beveiliging voor de servers in uw gehele omgeving.

* [Azure VM-extensies](./servers/manage-vm-extensions.md) configureren om Azure Management Services te gebruiken om uw servers te controleren, te beveiligen en bij te werken.

* Kubernetes-clusters op schaal beheren.

* Configuratie op basis van GitOps gebruiken als codebeheer om toepassingen en configuratie in een of meer clusters rechtstreeks vanuit broncodebeheer, zoals GitHub, te implementeren.

* 'Zero touch' compatibiliteit en configuratie van uw Kubernetes-clusters met behulp van Azure Policy.

* Azure-gegevensservices uitvoeren in elke Kubernetes-omgeving, in het bijzonder Azure SQL Managed Instance en Azure Database for PostgreSQL Hyperscale, met voordelen zoals upgrades/updates, beveiliging en bewaking alsof het in Azure wordt uitgevoerd. Profiteren van elastisch schalen, updates toepassen, zonder downtime van toepassingen, zelfs zonder voortdurende verbinding met Azure.

* Een uniforme ervaring voor het weergeven van uw Azure Arc-resources, ongeacht of u de Azure-portal, de Azure CLI, Azure PowerShell of Azure REST API gebruikt.

## <a name="how-much-does-azure-arc-cost"></a>Wat kost Azure Arc?

Hieronder vindt u de prijsinformatie voor de functies die momenteel beschikbaar zijn met Azure Arc.

### <a name="arc-enabled-servers"></a>Servers met ingeschakelde Arc

De functionaliteit van het beheerpaneel van Azure Arc wordt gratis aangeboden. Dit omvat:

* Resource-organisatie via Azure-beheergroepen en -tags.

* Zoeken en indexeren via Azure Resource Graph.

* Toegang en beveiliging via Azure RBAC en abonnementen.

* Omgevingen en automatisering via sjablonen en uitbreidingen.

* Updatebeheer

Elke Azure-service die wordt gebruikt op servers met Arc-functionaliteit, bijvoorbeeld Azure Security Center of Azure Monitor, wordt in rekening gebracht volgens de prijzen voor die service. Zie de pagina [Azure-prijzen](https://azure.microsoft.com/pricing/) voor meer informatie.

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes met Azure Arc

In de huidige preview-fase wordt Kubernetes met Azure Arc aangeboden zonder extra kosten.

### <a name="azure-arc-enabled-data-services"></a>Azure Arc-gegevensservices

In de huidige preview-fase worden gegevensservices met Azure Arc aangeboden zonder extra kosten.

## <a name="next-steps"></a>Volgende stappen

* Zie de volgende [overzicht](./servers/overview.md) voor meer informatie over Arc-servers

* Zie de volgende [overzicht](./kubernetes/overview.md) voor meer informatie over Kubernetes met Arc

* Zie de volgende [overzicht](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) voor meer informatie over gegevensservices met Arc

* Probeer services met Arc uit vanuit het artikel over [aan de slag gaan met het proof-of-concept](https://azurearcjumpstart.io/azure_arc_jumpstart/)
