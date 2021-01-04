---
title: Wat is Azure Lighthouse?
description: Met Azure Lighthouse kunnen serviceproviders beheerde services leveren voor hun klanten, met een grotere mate van automatisering en efficiëntie op schaal.
ms.date: 12/10/2020
ms.topic: overview
ms.openlocfilehash: bef6107878f36e02a896b3c38b8913ebb50169a7
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356358"
---
# <a name="what-is-azure-lighthouse"></a>Wat is Azure Lighthouse?

Met Azure Lighthouse kunt u meerdere tenants beheren, meer automatiseren en schalen en hebt u verbeterde governance in resources en tenants.

Met Azure Lighthouse kunnen serviceproviders beheerde services leveren met behulp van uitgebreide en krachtige beheerprogramma's die zijn ingebouwd in het Azure-platform. Klanten behouden de controle over wie toegang heeft tot hun tenant. tot welke resources ze toegang hebben en welke acties ze kunnen uitvoeren. Deze aanbieding kan ook van pas komen voor [zakelijke IT-organisaties](concepts/enterprise.md) die resources van meerdere tenants beheren.

![Overzichtsdiagram van Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Voordelen

Met Azure Lighthouse kunt u op een efficiënte manier beheerde services bouwen en leveren. Voordelen zijn:

- **Beheer op schaal**: Bewerkingen op het gebied van klantbetrokkenheid en levenscyclus voor het beheer van klantresources zijn eenvoudiger en beter schaalbaar. Bestaande API's, beheerprogramma's en werkstromen kunnen worden gebruikt met gedelegeerde resources, waaronder buiten Azure gehoste machines, ongeacht de regio's waarin ze zich bevinden.
- **Betere zichtbaarheid en controle voor klanten**: Klanten hebben nauwkeurige controle over de scopes die ze delegeren voor beheer en de machtigingen die zijn toegestaan. Ze kunnen de acties van de service provider controleren en de toegang indien gewenst volledig verwijderen.
- **Uitgebreide en geïntegreerde hulpprogramma's op het platform**: Onze ervaring met hulpprogramma's betekent dat we kunnen inspelen op belangrijke scenario's van serviceproviders, met inbegrip van verschillende licentiemodellen, zoals EA, CSP en betalen naar gebruik. Azure Lighthouse werkt met bestaande hulpprogramma's en API's, licentiemodellen, [met Azure beheerde toepassingen](concepts/managed-applications.md) en partnerprogramma's zoals het [Cloud Solution Provider-programma (CSP)](/partner-center/csp-overview). U kunt Azure Lighthouse integreren in bestaande werkstromen en toepassingen, en u kunt uw impact op klantinteracties bijhouden door [uw partner-id te koppelen](./how-to/partner-earned-credit.md).

Er zijn geen extra kosten verbonden aan het gebruik van Azure Lighthouse voor het beheren van de Azure-resources. Elke Azure-klant of -partner kan Azure Lighthouse gebruiken.

## <a name="capabilities"></a>Functionaliteit

Azure Lighthouse biedt meerdere manieren om betrokkenheid en -beheer te stroomlijnen:

- **Gedelegeerd resourcebeheer van Azure**: [Beheer de Azure-resources van uw klanten veilig vanuit uw eigen tenant](concepts/azure-delegated-resource-management.md), zonder dat u van context of besturingsvlak hoeft te veranderen. Klantabonnementen en resourcegroepen kunnen worden gedelegeerd aan opgegeven gebruikers en rollen in de tenant die het beheer uitvoert, met de mogelijkheid om de toegang zo nodig te verwijderen.
- **Ervaringen met nieuwe Azure-portal**: Bekijk informatie voor meerdere tenants op de [pagina **Mijn klanten**](how-to/view-manage-customers.md) in Azure Portal. Via een bijbehorende [pagina **Serviceproviders**](how-to/view-manage-service-providers.md) kunnen uw klanten de toegang tot hun serviceproviders bekijken en beheren.
- **Azure Resource Manager-sjablonen**: Gebruik ARM-sjablonen om [gedelegeerde klantresources te onboarden](how-to/onboard-customer.md) en [beheertaken voor meerdere tenants uit te voeren](samples/index.md).
- **Aanbiedingen voor beheerde service in Azure Marketplace**: [Bied uw services aan klanten aan](concepts/managed-services-offers.md) via privé- of openbare aanbiedingen en onboard ze automatisch in Azure Lighthouse.

> [!TIP]
> Een vergelijkbare aanbieding, [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181), helpt IT-partners bij onboarden en om hun Microsoft 365-klanten op schaal te controleren en te beheren. Microsoft 365 Lighthouse is momenteel beschikbaar als beperkte preview.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](concepts/azure-delegated-resource-management.md).
- Verken [Beheerervaring in meerdere tenants](concepts/cross-tenant-management-experience.md).
- Zie [Azure Lighthouse gebruiken in een bedrijf](concepts/enterprise.md).
- Bekijk de details over [beschikbaarheid](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) en [FedRAMP- en DoD CC SRG-controlebereik](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) voor Azure Lighthouse.
