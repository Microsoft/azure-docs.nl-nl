---
title: Overwegingen voor een Cloud Solution Provider-programma
description: Voor CSP-partners zorgt Azure gedelegeerd resource beheer voor betere beveiliging en controle door gedetailleerde machtigingen in te scha kelen.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 8736cf913739f2bd16fb519aed98fd336f6876a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419385"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse en het programma Cloud Solution Provider

Als u een CSP-partner [(Cloud Solution Provider)](/partner-center/csp-overview) bent, hebt u al toegang tot de Azure-abonnementen die zijn gemaakt voor uw klanten via het CSP-programma met behulp van de functie [beheren namens (administrate)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) . Met deze toegang kunt u de abonnementen van uw klanten rechtstreeks ondersteunen, configureren en beheren.

Met [Azure Lighthouse](../overview.md)kunt u Azure delegated Resource Management gebruiken samen met administrate. Dit helpt de beveiliging te verbeteren en de toegang te verminderen door meer gedetailleerde machtigingen in te scha kelen voor uw gebruikers. Het biedt ook een grotere efficiëntie en schaal baarheid, omdat uw gebruikers met één aanmelding in uw Tenant kunnen werken op meerdere abonnementen van klanten.

> [!TIP]
> Bekijk en volg de [aanbevolen beveiligings procedures](recommended-security-practices.md) in combi natie met de [beveiligings vereisten](/partner-center/partner-security-requirements)van de partner om de resources van klanten te helpen beschermen.

## <a name="administer-on-behalf-of-aobo"></a>Beheren namens (ADMINISTRATE)

Met ADMINISTRATE krijgt elke gebruiker met de rol [beheerder agent](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) in uw Tenant administrate toegang tot Azure-abonnementen die u via het CSP-programma maakt. Gebruikers die toegang nodig hebben tot abonnementen van klanten, moeten lid zijn van deze groep. ADMINISTRATE staat niet toe dat de flexibiliteit om afzonderlijke groepen te maken die met verschillende klanten werken, of om verschillende rollen voor groepen of gebruikers in te scha kelen.

![Diagram met het Tenant beheer met ADMINISTRATE.](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Meer informatie over gedelegeerd resourcebeheer

Met Azure Lighthouse kunt u verschillende groepen toewijzen aan verschillende klanten of rollen, zoals in het volgende diagram wordt weer gegeven. Omdat gebruikers over het juiste toegangs niveau beschikken via het beheer van gedelegeerde resources van Azure, kunt u het aantal gebruikers met de rol beheerder (en dus volledige ADMINISTRATE) verminderen. Dit helpt de beveiliging te verbeteren door het beperken van onnodige toegang tot de resources van uw klanten. Daarnaast hebt u meer flexibiliteit om meerdere klanten op schaal te beheren.

Als u een abonnement hebt gemaakt via het CSP-programma, volgt u de stappen die worden beschreven in [onboarding van een abonnement op Azure Lighthouse](../how-to/onboard-customer.md). Gebruikers met de rol beheerder agent in uw Tenant kunnen dit onboarding uitvoeren.

![Diagram van het beheer van tenants met behulp van ADMINISTRATE en Azure gedelegeerd resource beheer.](../media/csp-2.jpg)

> [!TIP]
> [Aanbiedingen voor beheerde services](managed-services-offers.md) met persoonlijke abonnementen worden niet ondersteund met-abonnementen die zijn gemaakt via een wederverkoper van het programma van de Cloud Solution Provider (CSP). U kunt deze abonnementen op Azure Lighthouse onboarden met [behulp van Azure Resource Manager-sjablonen](../how-to/onboard-customer.md).

> [!NOTE]
> De [pagina **mijn klanten** in de Azure Portal](../how-to/view-manage-customers.md) bevat nu een **Cloud Solution Provider (preview)** -sectie waarin facturerings gegevens en resources voor CSP-klanten worden weer gegeven die [de micro soft-klant overeenkomst (MCA) hebben ondertekend](/partner-center/confirm-customer-agreement) en [onder het Azure-abonnement](/partner-center/azure-plan-get-started)vallen. Zie [aan de slag met uw micro soft-partner overeenkomst](../../cost-management-billing/understand/mpa-overview.md)voor meer informatie.
>
> CSP-klanten kunnen in deze sectie worden weer gegeven, ongeacht of ze ook zijn geboardd naar Azure Lighthouse. Als dat het geval is, worden ze ook weer gegeven in de sectie **klanten** , zoals wordt beschreven in [klanten en gedelegeerde resources weer geven en beheren](../how-to/view-manage-customers.md). Op dezelfde manier hoeft een CSP-klant niet te worden weer gegeven in de sectie **Cloud Solution Provider (preview)** van **mijn klanten** , zodat u deze kunt opheffen in azure Lighthouse.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerervaring in meerdere tenants](cross-tenant-management-experience.md).
- Meer informatie over het [voorbereiden van een abonnement op Azure Lighthouse](../how-to/onboard-customer.md).
- Meer informatie over het [programma Cloud Solution Provider](/partner-center/csp-overview).
