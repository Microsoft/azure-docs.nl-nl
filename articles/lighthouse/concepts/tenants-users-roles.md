---
title: Tenants, gebruikers en rollen in azure Lighthouse-scenario's
description: Inzicht in de concepten van Azure Active Directory-tenants, gebruikers en rollen, en hoe ze kunnen worden gebruikt in azure Lighthouse-scenario's.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: d78828cc739030f8e456c64885d77ddf59dd13fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98233913"
---
# <a name="tenants-users-and-roles-in-azure-lighthouse-scenarios"></a>Tenants, gebruikers en rollen in azure Lighthouse-scenario's

Voordat u klanten voor [Azure Lighthouse](../overview.md)kunt voorbereiden, is het belang rijk om te begrijpen hoe Azure Active Directory (Azure AD)-tenants, gebruikers en rollen werken, en hoe ze kunnen worden gebruikt in azure Lighthouse-scenario's.

Een *Tenant* is een speciaal en vertrouwd exemplaar van Azure AD. Normaal gesp roken vertegenwoordigt elke Tenant één organisatie. Met het [gedelegeerde resource beheer van Azure](azure-delegated-resource-management.md) kunt u resources van de ene Tenant naar een andere Tenant maken. Hierdoor kunnen gebruikers in de Tenant beheren (zoals één van een service provider) toegang krijgen tot gedelegeerde resources in de Tenant van een klant, of kunnen [ondernemingen met meerdere tenants hun beheer bewerkingen centraliseren](enterprise.md).

Als u deze logische *projectie wilt uitvoeren* , moet een abonnement (of een of meer resource groepen binnen een abonnement) in de Tenant van de klant worden voorstaan met Azure Lighthouse. Dit voorbereidings proces kan worden uitgevoerd [via Azure Resource Manager sjablonen](../how-to/onboard-customer.md) of door [een open bare of persoonlijke aanbieding naar Azure Marketplace te publiceren](../how-to/publish-managed-services-offers.md).

Welke methode u ook kiest, u moet *autorisaties* definiëren. Elke autorisatie geeft een **principalId** die toegang heeft tot de gedelegeerde resources en een ingebouwde rol die de machtigingen instelt die elk van deze gebruikers heeft voor deze resources. Deze **principalId** definieert een Azure AD-gebruiker,-groep of-Service-Principal in de beherende Tenant.

> [!NOTE]
> Tenzij expliciet opgegeven, verwijzingen naar een ' gebruiker ' in de Azure Lighthouse-documentatie kunnen van toepassing zijn op een Azure AD-gebruiker,-groep of-Service-Principal in een autorisatie.

## <a name="best-practices-for-defining-users-and-roles"></a>Aanbevolen procedures voor het definiëren van gebruikers en rollen

Wanneer u een autorisatie maakt, raden we u aan de volgende aanbevolen procedures uit te voeren:

- In de meeste gevallen moet u machtigingen toewijzen aan een Azure AD-gebruikers groep of Service-Principal, in plaats van aan een reeks afzonderlijke gebruikers accounts. Hiermee kunt u toegang voor afzonderlijke gebruikers toevoegen of verwijderen zonder dat u het plan hoeft bij te werken en opnieuw te publiceren wanneer uw toegangs vereisten veranderen.
- Zorg ervoor dat u het principe van minimale bevoegdheden volgt, zodat gebruikers alleen over de benodigde machtigingen beschikken om hun taak te volt ooien, waardoor de kans op onbedoelde fouten wordt verminderd. Zie [aanbevolen beveiligings procedures](../concepts/recommended-security-practices.md)voor meer informatie.
- Neem een gebruiker op met de functie voor het verwijderen van de [registratie toewijzing van beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , zodat u [de toegang tot de overdracht](../how-to/remove-delegation.md) later indien nodig kunt verwijderen. Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de Tenant van de klant.
- Zorg ervoor dat alle gebruikers die [de pagina mijn klanten moeten weer geven in de Azure Portal](../how-to/view-manage-customers.md) over de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) beschikken (of een andere ingebouwde rol die lezers toegang bevat).

> [!IMPORTANT]
> Als u machtigingen wilt toevoegen voor een Azure AD-groep, moet u het **groeps type** instellen op **beveiliging**. Deze optie wordt geselecteerd wanneer de groep wordt gemaakt. Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie.

## <a name="role-support-for-azure-lighthouse"></a>Functie ondersteuning voor Azure Lighthouse

Bij het definiëren van een autorisatie moet aan elk gebruikers account een van de [ingebouwde Azure-rollen](../../role-based-access-control/built-in-roles.md)worden toegewezen. Aangepaste rollen en [beheerders rollen voor klassieke abonnementen](../../role-based-access-control/classic-administrators.md) worden niet ondersteund.

Alle [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) worden momenteel ondersteund met Azure Lighthouse, met de volgende uitzonde ringen:

- De rol van [eigenaar](../../role-based-access-control/built-in-roles.md#owner) wordt niet ondersteund.
- Ingebouwde rollen met de machtiging [DataActions](../../role-based-access-control/role-definitions.md#dataactions) worden niet ondersteund.
- De ingebouwde rol [gebruikers toegangs beheerder](../../role-based-access-control/built-in-roles.md#user-access-administrator) wordt ondersteund, maar alleen voor het beperkte doel van het [toewijzen van rollen aan een beheerde identiteit in de Tenant van de klant](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Er worden gewoonlijk geen andere machtigingen verleend door deze rol. Als u een gebruiker met deze rol definieert, moet u ook de ingebouwde rol (len) opgeven die deze gebruiker aan beheerde identiteiten kan toewijzen.

> [!NOTE]
> Zodra een nieuwe toepasselijke ingebouwde rol aan Azure wordt toegevoegd, kan deze worden toegewezen wanneer een klant wordt ingecheckt [met Azure Resource Manager sjablonen](../how-to/onboard-customer.md). Er kan een vertraging optreden voordat de toegevoegde rol beschikbaar komt in het partner centrum wanneer [een beheerde service aanbieding wordt gepubliceerd](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [aanbevolen beveiligings procedures voor Azure Lighthouse](recommended-security-practices.md).
- Laat uw klanten onboarden naar Azure Lighthouse, door [gebruik te maken van Azure Resource Manager sjablonen](../how-to/onboard-customer.md) of door [een aanbieding met persoonlijke of open bare services te publiceren op Azure Marketplace](../how-to/publish-managed-services-offers.md).
