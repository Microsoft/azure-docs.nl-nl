---
title: Azure-beveiligings basislijn voor Azure resource Graph
description: De beveiligings basislijn van Azure resource Graph biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad8968fdb6548da29a031f0e44bd3671f67b5553
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557714"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Azure-beveiligings basislijn voor Azure resource Graph

Deze beveiligings basislijn is van toepassing op de richt lijnen [Azure Security Bench Mark version 1,0](../../../security/benchmarks/overview-v1.md) to Azure resource Graph. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op Azure resource Graph. **Besturings elementen** die niet van toepassing zijn op een Azure-resource grafiek, zijn uitgesloten.

 
Voor een overzicht van de manier waarop Azure resource Graph volledig is toegewezen aan de Azure Security-Bench Mark, raadpleegt u het [volledige bestand met de beveiliging basislijn toewijzing van Azure resource Graph](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../../../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure resource Graph biedt toegang tot bron typen en eigenschappen op basis van op rollen gebaseerde toegangs beheer functies van Azure (Azure RBAC). Controleer en controleer of de toegang die is verleend aan beveiligings-principals (gebruikers, groepen en service accounts) regel matig wordt gecontroleerd om ervoor te zorgen dat query's resultaten voor de juiste resources retour neren.

- [Machtigingen in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Beoordelingen over Azure Identity Access gebruiken](../../../active-directory/governance/access-reviews-overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Benchmark: gegevensbescherming](../../../security/benchmarks/security-control-data-protection.md) voor meer informatie.*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren 

**Hulp**: Azure RBAC gebruiken om de toegang tot gegevens en resources te beheren. Als u Azure resource Graph wilt gebruiken, moet u ook de juiste toegang hebben tot de resources die u wilt doorzoeken. Deze toegang moet worden beperkt tot alleen-lezen en alleen worden verleend aan het vereiste personeel.

- [Machtigingen in Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph)

- [Azure RBAC configureren](../../../role-based-access-control/role-assignments-rest.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../../../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken om alle ondersteunde resources binnen uw abonnementen, beheer groepen en tenants op te vragen en te detecteren. Zorg ervoor dat u de juiste machtigingen hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

- [Query's maken met Azure resource Graph](../first-query-portal.md)

- [Meer informatie over Azure RBAC](../../../role-based-access-control/overview.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources conform uw organisatie behoeften. Gebruik Azure resource Graph om te zoeken naar goedgekeurde Azure-resources en wijzigings geschiedenis (preview) om moment opnamen te controleren en te zien wat er is gewijzigd.

- [Query uitvoeren op Azure-resources met Azure resource Graph](../first-query-portal.md)

- [Resourcewijzigingen ophalen](../how-to/get-resource-changes.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: Azure resource Graph gebruiken om resources in uw abonnementen, beheer groepen en tenants te doorzoeken en te detecteren. Zorg ervoor dat alle Azure-resources in de omgeving zijn goedgekeurd.

- [Query uitvoeren op Azure-resources met Azure resource Graph](../first-query-portal.md)

- [Voor beelden: start query's voor Azure resource Graph](../samples/starter.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../../../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../../../security/benchmarks/security-baselines-overview.md)