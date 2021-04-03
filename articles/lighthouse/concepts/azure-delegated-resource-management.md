---
title: Meer informatie over gedelegeerd resourcebeheer
description: Azure delegated resource management is een belang rijk onderdeel van Azure Lighthouse, zodat service providers gedelegeerde resources op schaal kunnen beheren met flexibiliteit en precisie.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: d484e61fc4ab3714eb362b26d64d449890065888
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92203854"
---
# <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Azure delegated resource management is een van de belangrijkste onderdelen van [Azure Lighthouse](../overview.md). Met Azure delegated resource management kunnen service providers de klant betrokkenheid en de onboarding-ervaring vereenvoudigen, terwijl gedelegeerde resources op schaal worden beheerd met flexibiliteit en precisie. Klanten behouden de controle over welke service providers toegang hebben tot hun Tenant, klanten houden controle over wie toegang heeft tot hun Tenant, welke bronnen ze kunnen gebruiken en welke acties er kunnen worden uitgevoerd.

## <a name="what-is-azure-delegated-resource-management"></a>Wat is Azure delegated resource management?

Met het gedelegeerde resource beheer van Azure kunt u resources van de ene Tenant naar een andere Tenant maken. Hierdoor kunnen geautoriseerde gebruikers in één Azure Active Directory (Azure AD)-Tenant beheer bewerkingen uitvoeren in verschillende Azure AD-tenants die deel uitmaken van hun klanten. Service providers kunnen zich aanmelden bij hun eigen Azure AD-Tenant en hebben toestemming om te werken in gedelegeerde klant abonnementen en resource groepen. Hierdoor kunnen ze beheer bewerkingen uitvoeren namens hun klanten, zonder dat ze zich hoeven aan te melden bij elke afzonderlijke klant Tenant.

> [!TIP]
> Azure delegated resource management kan ook worden gebruikt [binnen een onderneming met meerdere Azure AD-tenants](enterprise.md) , om het beheer van meerdere tenants te vereenvoudigen.

Met Azure delegated resource management kunnen geautoriseerde gebruikers rechtstreeks in de context van een klant abonnement werken zonder dat ze een account in de Tenant van de klant hebben of mede-eigenaar zijn van de Tenant van de klant.

Met de [beheer ervaring voor cross-tenants](cross-tenant-management-experience.md) kunt u efficiënter werken met Azure-beheer services zoals Azure Policy, Azure Security Center en meer. Alle activiteiten van de service provider worden bijgehouden in het activiteiten logboek, dat is opgeslagen in de Tenant van de klant (en kan worden weer gegeven door gebruikers in de Tenant beheren). Dit betekent dat gebruikers in zowel het beheer als de beheerde Tenant eenvoudig kunnen identificeren welke gebruiker is gekoppeld aan wijzigingen.

U kunt [het nieuwe type managed service-aanbieding publiceren naar Azure Marketplace](../how-to/publish-managed-services-offers.md) om eenvoudig klanten te doen met Azure Lighthouse. U kunt ook [het voorbereidings proces volt ooien door Azure Resource Manager sjablonen te implementeren](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Hoe Azure gedelegeerd resource beheer werkt

Op hoog niveau is dit de manier waarop Azure gedelegeerd resource management werkt:

1. Eerst identificeert u de toegang (rollen) die uw groepen, service-principals of gebruikers nodig hebben voor het beheren van de Azure-resources van de klant. De definitie van de toegang bevat het beheer van de Tenant-ID en de **principalId** -identiteiten van uw Tenant die is toegewezen aan [ingebouwde **roleDefinition** -waarden](../../role-based-access-control/built-in-roles.md) (Inzender, VM-bijdrager, lezer, enzovoort).
2. U geeft deze toegang en de klant op een van de volgende twee manieren een onboarding voor Azure Lighthouse:
   - [Een Azure Marketplace managed service-aanbieding](../how-to/publish-managed-services-offers.md) (privé of openbaar) publiceren die door de klant wordt geaccepteerd
   - [Een Azure Resource Manager-sjabloon implementeren naar de Tenant van de klant](../how-to/onboard-customer.md) voor een of meer specifieke abonnementen of resource groepen

3. Zodra de klant onboarded is, kunnen geautoriseerde gebruikers zich aanmelden bij uw Tenant beheren en taken uitvoeren voor het opgegeven bereik van de klant, op basis van de toegang die u hebt gedefinieerd. Klanten kunnen de acties van de service provider bekijken en de mogelijkheid hebben om de toegang te verwijderen, indien nodig.

> [!NOTE]
> U kunt gedelegeerde resources beheren die zich in verschillende [regio's](../../availability-zones/az-overview.md#regions)bevinden. Overdracht van abonnementen over een [nationale Cloud](../../active-directory/develop/authentication-national-cloud.md) en de open bare Azure-Cloud, of over twee afzonderlijke nationale Clouds, wordt echter niet ondersteund.

## <a name="support-for-azure-delegated-resource-management"></a>Ondersteuning voor Azure delegated resource management

Als u hulp nodig hebt met betrekking tot het beheer van de gedelegeerde resources van Azure, kunt u een ondersteunings aanvraag openen in de Azure Portal. Kies voor **type probleem** de optie **technisch**. Selecteer een abonnement en selecteer vervolgens **Lighthouse** (onder **bewaking & Management**).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](cross-tenant-management-experience.md).
- Meer informatie over [Managed Services-aanbiedingen in azure Marketplace](managed-services-offers.md).
