---
title: Veelgestelde vragen over Service Fabric beheerde clusters
description: Veelgestelde vragen over Service Fabric beheerde clusters, met inbegrip van mogelijkheden, use cases en algemene scenario's.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633084"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Veelgesteld vragen over beheerde Service Fabric-clusters

Hier vindt u enkele veelgestelde vragen en antwoorden voor Service Fabric beheerde clusters (preview-versie).

## <a name="general"></a>Algemeen

### <a name="what-are-service-fabric-managed-clusters"></a>Wat zijn Service Fabric beheerde clusters?

Service Fabric Managed clusters zijn een evolutie van het Service Fabric cluster resource model dat is ontworpen om het implementeren en beheren van clusters eenvoudiger te maken. Een door Service Fabric beheerd cluster maakt gebruik van het Azure Resource Manager encapsulation-model, zodat een gebruiker slechts één cluster bron hoeft te definiëren en implementeren in vergelijking met de vele onafhankelijke resources die ze vandaag moeten implementeren (virtuele-machine Schaalset, Load Balancer, IP en meer).

### <a name="what-regions-are-supported-in-the-preview"></a>Welke regio's worden in de preview-versie ondersteund?

Ondersteunde regio's voor de service Fabric Managed clusters preview includes `centraluseuap` ,,,, `eastus2euap` `eastasia` `northeurope` `westcentralus` en `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Kan ik een in-place migratie uitvoeren van mijn bestaande Service Fabric cluster naar een beheerde cluster resource?

Nee. Op dit moment moet u een nieuwe Service Fabric cluster bron maken om het nieuwe Service Fabric beheerde cluster resource type te gebruiken.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Zijn er extra kosten verbonden aan Service Fabric beheerde clusters?

Nee. Er zijn geen extra kosten verbonden aan een door Service Fabric beheerd cluster dan de kosten van de onderliggende compute-, opslag-en netwerk bronnen die nodig zijn voor het cluster.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Is er een nieuwe SLA geïntroduceerd door de Service Fabric beheerde cluster resource?

De SLA wordt niet gewijzigd ten opzichte van het huidige Service Fabric resource model.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Wat is het verschil tussen een basis-en standaard SKU-cluster?

Een Basic SKU-cluster betekent dat de meeste configuraties worden verschaft door de resource provider van Service Fabric. Basis-SKU-clusters zijn bedoeld om te worden gebruikt voor test-en pre-productie omgevingen. Met een standaard SKU-cluster kunnen gebruikers het cluster zo configureren dat ze specifiek aan hun behoeften voldoen. Zie [service Fabric Managed cluster sku's](./overview-managed-cluster.md#service-fabric-managed-cluster-skus)voor meer informatie.

## <a name="cluster-deployment-and-management"></a>Cluster implementatie en-beheer

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Ik voer aangepaste script extensies uit op mijn Schaalset voor virtuele machines, kan ik toch door gaan met een beheerde Service Fabric resource?

Ja, u kunt VM-extensies op beheerde cluster knooppunt typen opgeven. Zie een extensie voor een [schaalset toevoegen aan een service Fabric beheerd cluster knooppunt type](how-to-managed-cluster-vmss-extension.md)voor meer informatie.

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Ik wil dat ik een load balancer met alleen interne mogelijkheden?

Nee. Het is momenteel niet mogelijk om een load balancer voor alleen intern te hebben. We raden u aan de regels voor de netwerk beveiligings groep (NSG) te vergren delen om ongewenste binnenkomende/uitgaande verkeer te blok keren.

### <a name="can-i-autoscale-my-cluster"></a>Kan ik mijn cluster automatisch schalen?

Automatisch schalen is momenteel niet beschikbaar in de preview-versie.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Kan ik mijn cluster implementeren in verschillende beschikbaarheids zones?

Zone clusters met meerdere Beschik baarheid zijn momenteel niet beschikbaar in de preview-versie.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Kan ik kiezen tussen automatische en hand matige upgrades voor mijn cluster runtime?

In de preview-versie worden alle runtime-upgrades automatisch voltooid.

## <a name="applications"></a>Toepassingen

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Is er een lokale ontwikkel ervaring voor Service Fabric beheerde clusters?

De lokale ontwikkel ervaring blijft ongewijzigd ten opzichte van bestaande Service Fabric clusters. Zie [uw ontwikkel omgeving instellen](./service-fabric-get-started.md) voor meer informatie over de lokale ontwikkelings ervaring.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Kan ik mijn toepassingen implementeren als een Azure Resource Manager resource?

Ja. Er is ondersteuning toegevoegd om toepassingen te implementeren als een Azure Resource Manager bron (naast implementatie met Power shell en CLI). Zie [een service Fabric beheerde cluster toepassing implementeren met arm-sjabloon](how-to-managed-cluster-app-deployment-template.md)om aan de slag te gaan.
