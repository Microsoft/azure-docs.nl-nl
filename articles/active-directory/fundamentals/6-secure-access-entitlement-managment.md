---
title: Externe toegang beheren met Azure Active Directory Rechtenbeheer
description: Het gebruik van Azure Active Directory Rechtenbeheer als onderdeel van uw algehele beveiligingsplan voor externe toegang.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89744b63a555cc02d35815b4066ce572b7f77e38
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531890"
---
# <a name="manage-external-access-with-entitlement-management"></a>Externe toegang beheren met Rechtenbeheer 


[Rechtenbeheer](../governance/entitlement-management-overview.md) is een functie voor identiteitsbeheer waarmee organisaties de levenscyclus van identiteiten en toegang op schaal kunnen beheren door werkstromen voor toegangsverzoeken, toegangstoewijzingen, beoordelingen en vervaldatum te automatiseren. Met rechtenbeheer kunnen gedelegeerde niet-beheerders [toegangspakketten](../governance/entitlement-management-overview.md) maken waarmee externe gebruikers van andere organisaties toegang kunnen aanvragen. Een werkstroom voor goedkeuring met meerdere fases kan [](../governance/what-is-provisioning.md) worden geconfigureerd voor het evalueren van aanvragen en het inrichten van gebruikers voor beperkte toegang met terugkerende beoordelingen. Met rechtenbeheer kunt u externe accounts inrichten en de inrichting van externe accounts op basis van beleid verwijderen.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Belangrijkste concepten voor het inschakelen van rechtenbeheer

De volgende belangrijke concepten zijn belangrijk om te begrijpen voor rechtenbeheer.

### <a name="access-packages"></a>Toegangspakketten

Een [toegangspakket](../governance/entitlement-management-overview.md) is de basis van rechtenbeheer. Toegangspakketten zijn groeperingen van door beleid beheerde resources die een gebruiker nodig heeft om samen te werken aan een project of om andere taken uit te voeren. Een toegangspakket kan bijvoorbeeld het volgende omvatten:

* toegang tot specifieke SharePoint-sites.

* bedrijfstoepassingen, waaronder uw aangepaste in-house SaaS-apps, zoals Salesforce.

* Microsoft Teams-kanalen.

* Microsoft 365-groepen. 

### <a name="catalogs"></a>Catalogussen

Toegangspakketten bevinden zich in [catalogi](../governance/entitlement-management-catalog-create.md). U maakt een catalogus wanneer u gerelateerde resources en toegangspakketten wilt groepeert en de mogelijkheid wilt delegeren om deze te beheren. Eerst voegt u resources toe aan een catalogus en vervolgens kunt u deze resources toevoegen voor toegang tot pakketten. U kunt bijvoorbeeld een catalogus 'Financiën' maken [](../governance/entitlement-management-delegate.md) en het beheer ervan delegeren aan een lid van het financiële team. Deze persoon kan vervolgens [resources toevoegen,](../governance/entitlement-management-catalog-create.md)toegangspakketten maken en toegangsgoedkeuring voor die pakketten beheren.

In het volgende diagram ziet u een typische levenscyclus voor governance voor een externe gebruiker die toegang krijgt tot een toegangspakket met een vervaldatum.

![Een diagram van de governancecyclus voor externe gebruikers.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Selfservice voor externe toegang

U kunt toegangspakketten beschikbaar maken via de [Azure AD Mijn toegang Portal](../governance/entitlement-management-request-access.md) zodat externe gebruikers toegang kunnen aanvragen. Beleidsregels bepalen wie een toegangspakket kan aanvragen. U geeft op wie het toegangspakket mag aanvragen:

* Specifieke [verbonden organisaties](../governance/entitlement-management-organization.md)

* Alle geconfigureerde verbonden organisaties

* Alle gebruikers van elke organisatie

* Lid- of gastgebruikers die al in uw tenant zijn

### <a name="approvals"></a>Approvals   
Toegangspakketten kunnen verplichte goedkeuring voor toegang bevatten. **Implementeert altijd goedkeuringsprocessen voor externe gebruikers.** Goedkeuringen kunnen een goedkeuring met één of meerdere fases zijn. Goedkeuringen worden bepaald door beleidsregels. Als zowel interne als externe gebruikers toegang moeten hebben tot hetzelfde pakket, stelt u waarschijnlijk verschillende toegangsbeleidsregels in voor verschillende categorieën verbonden organisaties en voor interne gebruikers.

### <a name="expiration"></a>Verloopdatum  
Toegangspakketten kunnen een vervaldatum bevatten. Vervaldatum kan worden ingesteld op een specifieke dag of de gebruiker een bepaald aantal dagen voor toegang geven. Wanneer het toegangspakket verloopt en de gebruiker geen andere toegang heeft, kan het B2B-gastgebruikersobject dat de gebruiker vertegenwoordigt, worden verwijderd of geblokkeerd voor aanmelding. U wordt aangeraden verloop af te dwingen voor toegangspakketten voor externe gebruikers. Niet alle toegangspakketten hebben een vervaldatum. Voor degenen die dat niet doen, moet u toegangsbeoordelingen uitvoeren.

### <a name="access-reviews"></a>Toegangsbeoordelingen

Voor toegangspakketten kunnen periodieke [toegangsbeoordelingen](../governance/manage-guest-access-with-access-reviews.md)vereist zijn, waarvoor de eigenaar van het pakket of een ontwerper is vereist om aan te geven dat gebruikers nog steeds toegang nodig hebben. 

Voordat u uw beoordeling in stelt, moet u het volgende bepalen.

* Die

   * Wat zijn de criteria voor voortdurende toegang?

   * Wie zijn de opgegeven revisoren?

* Hoe vaak moeten geplande beoordelingen plaatsvinden?

   * Ingebouwde opties zijn maandelijks, elk kwartaal, twee jaar of jaarlijks. 

   * We raden elk kwartaal of vaker aan voor pakketten die externe toegang ondersteunen. 

 

> [!IMPORTANT]
> Toegangsbeoordelingen van toegangspakketten beoordelen alleen de toegang die wordt verleend via Rechtenbeheer. Daarom moet u andere processen instellen om alle toegang te controleren die externe gebruikers buiten Rechtenbeheer hebben.

Zie Een implementatie van Azure AD-toegangsbeoordelingen plannen voor meer informatie over [toegangsbeoordelingen.](../governance/deploy-access-reviews.md)

## <a name="using-automation-in-entitlement-management"></a>Automatisering gebruiken in Rechtenbeheer

U kunt [rechtenbeheerfuncties uitvoeren met behulp van Microsoft Graph](/graph/tutorial-access-package-api), waaronder

* [Toegangspakketten beheren](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [Toegangsbeoordelingen beheren](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [Verbonden organisaties beheren](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [Instellingen voor Rechtenbeheer beheren](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>Aanbevelingen 

We raden de procedures aan voor het beheren van externe toegang met Rechtenbeheer.

**Maak en gebruik [toegangspakketten](../governance/entitlement-management-access-package-create.md) voor** projecten met een of meer zakelijke partners om de gebruikers van die partner toegang te geven tot resources en deze in te delen. 

* Als u al B2B-gebruikers in uw directory hebt, kunt u ze ook rechtstreeks toewijzen aan de juiste toegangspakketten.

* U kunt toegang toewijzen in de [Azure Portal](../governance/entitlement-management-access-package-assignments.md)of via [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true).

**Gebruik uw identity governance-instellingen om gebruikers uit uw directory te verwijderen wanneer hun toegangspakketten verlopen.**

![Schermopname van het configureren van het beheren van de levenscyclus van externe gebruikers.](media/secure-external-access/6-manage-external-lifecycle.png)

Deze instellingen zijn alleen van toepassing op gebruikers met onboarding via Rechtenbeheer.

**[Delegeer het beheer van catalogi en toegangspakketten](../governance/entitlement-management-delegate.md)** aan bedrijfseigenaren die meer informatie hebben over wie toegang moet hebben.

![Schermopname van het configureren van een catalogus.](media/secure-external-access/6-catalog-management.png)

**[Verval van toegangspakketten afdwingen](../governance/entitlement-management-access-package-lifecycle-policy.md) waarvoor externe gebruikers toegang hebben.**


![Schermopname van het configureren van de vervaldatum van het toegangspakket.](media/secure-external-access/6-access-package-expiration.png)

* Als u de einddatum van een op een project gebaseerd toegangspakket weet, gebruikt u op de datum om de specifieke datum in te stellen. 

* Anders raden we u aan de vervaldatum niet langer dan 365 dagen te laten verlopen, tenzij bekend is dat het om een overeenkomst voor meerdere jaren gaat.

* Gebruikers toestaan de toegang uit te breiden.

* Goedkeuring vereisen om de extensie te verlenen.

**[Toegangsbeoordelingen van pakketten afdwingen om](../governance/manage-guest-access-with-access-reviews.md) ongepaste toegang voor gasten te voorkomen.**

![Schermopname van het maken van een nieuw toegangspakket.](media/secure-external-access/6-new-access-package.png)

* Afdwingen van beoordelingen per kwartaal.

* Stel voor nalevingsgevoelige projecten de revisoren in op specifieke revisoren in plaats van zelfbeoordeling voor externe gebruikers. De gebruikers die toegangspakketbeheerders zijn, zijn een goede plek om te beginnen voor revisoren. 

* Voor minder gevoelige projecten vermindert de zelfbeoordeling van gebruikers de belasting voor de organisatie om toegang te verwijderen van gebruikers die niet langer bij hun thuisorganisatie zijn.

Zie Toegang voor externe gebruikers [beheren in Azure AD-rechtenbeheer](../governance/entitlement-management-external-users.md) voor meer informatie 

### <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen over het beveiligen van externe toegang tot resources. U wordt aangeraden de acties in de vermelde volgorde uit te voeren.

1. [Uw beveiligingsstatus voor externe toegang bepalen](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governanceplan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Beveiligde toegang met Rechtenbeheer](6-secure-access-entitlement-managment.md) (u bent hier.)

7. [Toegang beveiligen met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Toegang beveiligen met gevoeligheidslabels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot Microsoft Teams, OneDrive en SharePoint](9-secure-access-teams-sharepoint.md)

 

