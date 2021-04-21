---
title: Meer informatie over gedelegeerd resourcebeheer
description: Gedelegeerd resourcebeheer van Azure is een belangrijk onderdeel van Azure Lighthouse, zodat serviceproviders gedelegeerde resources op schaal kunnen beheren met flexibiliteit en precisie.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 93a3de257fe88de4915eff3582ff38fc03ef380e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767759"
---
# <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Gedelegeerd resourcebeheer van Azure is een van de belangrijkste onderdelen van [Azure Lighthouse](../overview.md). Met gedelegeerd resourcebeheer van Azure kunnen serviceproviders klantbetrokkenheid en onboarding-ervaringen vereenvoudigen en gedelegeerde resources op schaal met flexibiliteit en precisie beheren. Klanten behouden de controle over wie toegang heeft tot hun tenant. tot welke resources ze toegang hebben en welke acties ze kunnen uitvoeren.

## <a name="what-is-azure-delegated-resource-management"></a>Wat is Gedelegeerd resourcebeheer van Azure?

Azure gedelegeerd resourcebeheer maakt logische projectie van resources van de ene tenant naar een andere tenant mogelijk. Hierdoor kunnen gemachtigde gebruikers in één Azure Active Directory (Azure AD)-tenant beheerbewerkingen uitvoeren in verschillende Azure AD-tenants die tot hun klanten behoren. Serviceproviders kunnen zich aanmelden bij hun eigen Azure AD-tenant en autorisatie hebben om te werken in gedelegeerde klantabonnementen en resourcegroepen. Hierdoor kunnen ze beheerbewerkingen uitvoeren namens hun klanten, zonder dat ze zich bij elke afzonderlijke klant-tenant moeten aanmelden.

> [!TIP]
> Gedelegeerd resourcebeheer van Azure kan ook worden gebruikt binnen een onderneming die meerdere [eigen Azure AD-tenants](enterprise.md) heeft om het beheer van meerdere tenants te vereenvoudigen.

Met gedelegeerd resourcebeheer van Azure kunnen gemachtigde gebruikers rechtstreeks in de context van een klantabonnement werken zonder dat ze een account in de tenant van die klant hebben of mede-eigenaar van de tenant van de klant zijn.

Dankzij [de beheerervaring voor meerdere tenants](cross-tenant-management-experience.md) kunt u efficiënter werken met Azure-beheerservices zoals Azure Policy, Azure Security Center en meer. Alle activiteiten van de serviceprovider worden bijgeslagen in het activiteitenlogboek, dat is opgeslagen in de tenant van de klant (en kan worden bekeken door gebruikers in de beherende tenant). Dit betekent dat gebruikers in zowel de beherende als de beheerde tenant gemakkelijk de gebruiker kunnen identificeren die is gekoppeld aan wijzigingen.

U kunt [het nieuwe aanbiedingstype beheerde service](../how-to/publish-managed-services-offers.md) publiceren naar Azure Marketplace om klanten eenvoudig te onboarden voor Azure Lighthouse. U kunt het [onboardingproces ook voltooien door de sjablonen Azure Resource Manager implementeren.](../how-to/onboard-customer.md)

## <a name="how-azure-delegated-resource-management-works"></a>Hoe azure gedelegeerd resourcebeheer werkt

Op hoog niveau werkt azure gedelegeerd resourcebeheer als volgende:

1. Eerst identificeert u de toegang (rollen) die uw groepen, service-principals of gebruikers nodig hebben om de Azure-resources van de klant te beheren. De toegangsdefinitie bevat de tenant-id voor beheer, samen met **principalId-identiteiten** van uw tenant die zijn toe te kennen aan [ingebouwde **roleDefinition-waarden**](../../role-based-access-control/built-in-roles.md) (Inzender, Inzender voor VM, Lezer, enzovoort).
2. U geeft deze toegang op en onboardt de klant Azure Lighthouse op een van de volgende twee manieren:
   - [Een beheerde Azure Marketplace (privé](../how-to/publish-managed-services-offers.md) of openbaar) publiceren die de klant accepteert
   - [Een Azure Resource Manager implementeren in de tenant van de klant](../how-to/onboard-customer.md) voor een of meer specifieke abonnementen of resourcegroepen

3. Zodra de onboarding van de klant is uitgevoerd, kunnen gemachtigde gebruikers zich aanmelden bij uw beheer-tenant en taken uitvoeren op het opgegeven klantbereik, op basis van de toegang die u hebt gedefinieerd. Klanten kunnen de acties van de serviceprovider bekijken en de mogelijkheid hebben om de toegang zo nodig te verwijderen.

> [!NOTE]
> U kunt gedelegeerde resources beheren die zich in verschillende regio's [bevinden.](../../availability-zones/az-overview.md#regions) Delegering van abonnementen in een [nationale cloud](../../active-directory/develop/authentication-national-cloud.md) en de openbare Azure-cloud, of in twee afzonderlijke nationale clouds, wordt echter niet ondersteund.

## <a name="support-for-azure-delegated-resource-management"></a>Ondersteuning voor gedelegeerd Azure-resourcebeheer

Als u hulp nodig hebt met betrekking tot gedelegeerd resourcebeheer van Azure, kunt u een ondersteuningsaanvraag openen in de Azure Portal. Kies **bij Probleemtype** de optie **Technisch.** Selecteer een abonnement en selecteer vervolgens **Lighthouse** (onder **Bewaking & Management).**

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](cross-tenant-management-experience.md).
- Meer informatie over [aanbiedingen voor beheerde services in Azure Marketplace](managed-services-offers.md).
