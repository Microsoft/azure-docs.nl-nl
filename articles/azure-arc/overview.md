---
title: Overzicht van Azure Arc
description: Lees meer over wat Azure Arc is en hoe het klanten helpt om het beheer en de governance van hun hybride resources met andere Azure-services en -functies mogelijk te maken.
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650189"
---
# <a name="azure-arc-overview"></a>Overzicht van Azure Arc

Tegenwoordig hebben bedrijven moeite om steeds complexe omgevingen te beheren en te bepalen. Deze omgevingen omvatten datacentra, meerdere clouds en de rand. Elke omgeving en Cloud beschikt over een eigen set van niet-aaneengesloten beheer hulpprogramma's die u nodig hebt om te leren en te werken.

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

* Gebruik GitOps voor het implementeren van configuratie over een of meer clusters van Git-opslag plaatsen.

*  Zero-Touch-compatibiliteit en configuratie voor uw Kubernetes-clusters met behulp van Azure Policy.

* Voer Azure Data Services uit op elke Kubernetes-omgeving alsof deze wordt uitgevoerd in azure (met name Azure SQL Managed instance en Azure Database for PostgreSQL grootschalige, met voor delen zoals upgrades, updates, beveiliging en controle). Elastisch schalen gebruiken en updates Toep assen zonder uitval tijd van toepassingen, zelfs zonder doorlopende verbinding met Azure

* Een uniforme ervaring voor het weergeven van uw Azure Arc-resources, ongeacht of u de Azure-portal, de Azure CLI, Azure PowerShell of Azure REST API gebruikt.

## <a name="how-much-does-azure-arc-cost"></a>Wat kost Azure Arc?

Hieronder vindt u de prijsinformatie voor de functies die momenteel beschikbaar zijn met Azure Arc.

### <a name="arc-enabled-servers"></a>Servers met ingeschakelde Arc

De volgende functionaliteit voor het beheer vlak van Azure Arc wordt gratis aangeboden:

* Resource-organisatie via Azure-beheergroepen en -tags.

* Zoeken en indexeren via Azure Resource Graph.

* Toegang en beveiliging via Azure RBAC en abonnementen.

* Omgevingen en automatisering via sjablonen en uitbreidingen.

* Updatebeheer

Elke Azure-service die wordt gebruikt op servers met Arc-functionaliteit, bijvoorbeeld Azure Security Center of Azure Monitor, wordt in rekening gebracht volgens de prijzen voor die service. Zie de pagina met prijzen voor [Azure](https://azure.microsoft.com/pricing/)voor meer informatie.

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes met Azure Arc

Voor elke Azure-service die wordt gebruikt op Kubernetes met Arc-functionaliteit, bijvoorbeeld Azure Security Center of Azure Monitor, wordt in rekening gebracht volgens de prijzen voor die service. Zie de [pagina met prijzen voor Azure](https://azure.microsoft.com/pricing/)voor meer informatie over de prijzen voor configuraties boven op Azure Arc enabled Kubernetes.

### <a name="azure-arc-enabled-data-services"></a>Azure Arc-gegevensservices

In de huidige preview-fase worden gegevens services van Azure Arc ingeschakeld zonder extra kosten.

## <a name="next-steps"></a>Volgende stappen

* Zie de volgende [overzicht](./servers/overview.md) voor meer informatie over Arc-servers

* Zie de volgende [overzicht](./kubernetes/overview.md) voor meer informatie over Kubernetes met Arc

* Zie de volgende [overzicht](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) voor meer informatie over gegevensservices met Arc

* Probeer services met Arc uit vanuit het artikel over [aan de slag gaan met het proof-of-concept](https://azurearcjumpstart.io/azure_arc_jumpstart/)
