---
title: Aanbiedingen voor beheerde service in Azure Marketplace
description: Met managed service kunt u aanbiedingen van resource beheer verkopen aan klanten in azure Marketplace.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: e66a87751e0375804031e777a40c5fc1d0e8a7e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093004"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Aanbiedingen voor beheerde service in Azure Marketplace

In dit artikel wordt het type **beheerde service** aanbieding in [Azure Marketplace](https://azuremarketplace.microsoft.com)beschreven. Met beheerde service aanbiedingen kunt u resource beheer services aan klanten aanbieden via [Azure Lighthouse](../overview.md). U kunt deze aanbiedingen beschikbaar stellen aan alle potentiële klanten of alleen aan een of meer specifieke klanten. Omdat u klanten rechtstreeks factureert voor kosten met betrekking tot deze beheerde services, zijn er geen kosten in rekening gebracht door micro soft.

## <a name="understand-managed-service-offers"></a>Meer informatie over beheerde service aanbiedingen

Met beheerde services kunt u het proces van het onboarding van klanten naar Azure Lighthouse stroom lijnen. Wanneer een klant een aanbieding in azure Marketplace koopt, kunnen ze opgeven welke abonnementen en/of resource groepen onboarding moeten worden uitgevoerd.

Daarna kunnen gebruikers in uw organisatie aan deze resources werken vanuit uw Tenant beheren via [Azure delegated resource management](azure-delegated-resource-management.md), volgens de toegang die u hebt gedefinieerd bij het maken van de aanbieding. Dit wordt gedaan via een manifest waarmee de gebruikers, groepen en service-principals van Azure Active Directory (Azure AD) worden opgegeven die toegang hebben tot de resources van de klant, samen met [rollen](tenants-users-roles.md) die hun toegangs niveau bepalen.

> [!NOTE]
> Aanbiedingen van beheerde services zijn mogelijk niet beschikbaar in Azure Government en andere nationale Clouds.

## <a name="public-and-private-offers"></a>Open bare en persoonlijke aanbiedingen

Elk beheerd service aanbod omvat een of meer abonnementen. Plannen kunnen privé of openbaar zijn.

Als u uw aanbieding wilt beperken tot specifieke klanten, kunt u een privé-abonnement publiceren. Wanneer u dit doet, kan het abonnement alleen worden aangeschaft voor de specifieke abonnements-Id's die u opgeeft. Zie voor meer informatie [persoonlijke aanbiedingen](../../marketplace/private-offers.md).

> [!NOTE]
> Persoonlijke aanbiedingen worden niet ondersteund met abonnementen die zijn gemaakt via een wederverkoper van het programma van de Cloud Solution Provider (CSP).

Met open bare abonnementen kunt u uw services promo veren tot nieuwe klanten. Dit zijn doorgaans handiger wanneer u alleen beperkte toegang tot de Tenant van de klant nodig hebt. Zodra u een relatie met een klant tot stand hebt gebracht, kunt u dit doen door een nieuw privé-abonnement alleen voor die klant te publiceren of door [ze voor verdere toegang te gebruiken met behulp van Azure Resource Manager sjablonen](../how-to/onboard-customer.md).

Indien nodig kunt u zowel open bare als privé-abonnementen in dezelfde aanbieding toevoegen.

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, kunt u het niet wijzigen in persoonlijk. Gebruik een privé abonnement om te bepalen welke klanten uw aanbieding mogen accepteren en resources kunnen delegeren. Met een openbaar abonnement kunt u de beschik baarheid van bepaalde klanten of zelfs voor een bepaald aantal klanten niet beperken (hoewel u ervoor kiest om het abonnement niet volledig te verkopen). U kunt de [toegang tot een overdracht verwijderen](../how-to/remove-delegation.md) nadat een klant alleen een aanbieding heeft geaccepteerd als u een **autorisatie** hebt opgenomen waarbij de **roldefinitie** is ingesteld op de functie voor het verwijderen van de [registratie toewijzing van beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) tijdens het publiceren van de aanbieding. U kunt ook contact opnemen met de klant en vragen om [uw toegang te verwijderen](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Aanbiedingen voor beheerde services publiceren

Zie [een beheerde service aanbieding naar Azure Marketplace publiceren](../how-to/publish-managed-services-offers.md)voor meer informatie over het publiceren van een beheerde service aanbieding.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure delegated resource management](azure-delegated-resource-management.md) en [Cross-Tenant beheer ervaring](cross-tenant-management-experience.md).
- [Publiceer managed service aanbiedingen](../how-to/publish-managed-services-offers.md) voor Azure Marketplace.
