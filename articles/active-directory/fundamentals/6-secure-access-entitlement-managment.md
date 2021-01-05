---
title: Externe toegang beheren met Azure Active Directory rechten beheer
description: Azure Active Directory rechten beheer gebruiken als onderdeel van het algemene beveiligings plan voor externe toegang.
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
ms.openlocfilehash: aeba704f315c6aabb2f9892777e483074e4bc90d
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744028"
---
# <a name="manage-external-access-with-entitlement-management"></a>Externe toegang beheren met het rechten beheer 


Het [beheer van rechten](../governance/entitlement-management-overview.md) is een identiteits bestuur waarmee organisaties identiteits-en toegangs levenscyclus op schaal kunnen beheren door het automatiseren van werk stromen voor toegangs aanvragen, toegangs toewijzingen, beoordelingen en verloop tijd. Het beheer van rechten staat gedelegeerde niet-beheerders toe om [toegangs pakketten](../governance/entitlement-management-overview.md) te maken waarmee externe gebruikers van andere organisaties toegang kunnen vragen. Een werk stroom met meerdere fase goedkeuringen kan worden geconfigureerd om aanvragen te evalueren en gebruikers in te [richten](../governance/what-is-provisioning.md) voor beperkte toegang met terugkerende revisies. Het beheer van rechten maakt het op beleid gebaseerde inrichting en het ongedaan maken van de inrichting van externe accounts mogelijk.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Belangrijkste concepten voor het inschakelen van het beheer van rechten

De volgende belang rijke concepten zijn belang rijk om te begrijpen wat het rechten beheer is.

### <a name="access-packages"></a>Toegangs pakketten

Een [toegangs pakket](../governance/entitlement-management-overview.md) is de basis van het beheer van rechten. Toegangs pakketten zijn groeperingen van op beleid gebaseerde resources die een gebruiker nodig heeft om te kunnen samen werken aan een project of andere taken uit te voeren. Een toegangs pakket kan bijvoorbeeld het volgende bevatten:

* toegang tot specifieke share point-sites.

* zakelijke toepassingen, inclusief uw aangepaste interne en SaaS-apps zoals Sales Force.

* Micro soft teams-kanalen.

* Microsoft 365-groepen. 

### <a name="catalogs"></a>Catalogussen

Toegangs pakketten bevinden zich in [catalogussen](../governance/entitlement-management-catalog-create.md). U maakt een catalogus wanneer u gerelateerde resources en toegangs pakketten wilt groeperen en de mogelijkheid wilt delegeren om ze te beheren. Eerst voegt u resources toe aan een catalogus en vervolgens kunt u deze resources toevoegen voor toegang tot pakketten. U kunt bijvoorbeeld een ' Finance ' catalogus maken en [het beheer ervan overdragen](../governance/entitlement-management-delegate.md) aan een lid van het financiële team. Deze persoon kan vervolgens [resources toevoegen](../governance/entitlement-management-catalog-create.md), toegangs pakketten maken en de goed keuring van toegang tot deze pakketten beheren.

In het volgende diagram ziet u een typische levens cyclus van het bedrijfs beleid voor een externe gebruiker die toegang krijgt tot een toegangs pakket dat een verval datum heeft.

![Een diagram van de beheer cyclus voor externe gebruikers.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Self-service externe toegang

U kunt toegangs pakketten belichten via de [Azure AD My Access-Portal](../governance/entitlement-management-request-access.md) om externe gebruikers in staat te stellen toegang aan te vragen. Beleids regels bepalen wie een toegangs pakket kan aanvragen. U geeft aan wie het toegangs pakket mag aanvragen:

* Specifieke [verbonden organisaties](../governance/entitlement-management-organization.md)

* Alle geconfigureerde verbonden organisaties

* Alle gebruikers van een organisatie

* Lid of gast gebruikers al in uw Tenant

### <a name="approvals"></a>Approvals   
Toegangs pakketten kunnen verplichte goed keuring voor toegang bevatten. **Implementeer altijd goedkeurings processen voor externe gebruikers**. Goed keuringen kunnen één of meerdere fasen goed keuren zijn. Goed keuringen worden bepaald door beleid. Als zowel interne als externe gebruikers toegang tot hetzelfde pakket nodig hebben, stelt u waarschijnlijk verschillende toegangs beleid in voor verschillende categorieën van verbonden organisaties en voor interne gebruikers.

### <a name="expiration"></a>Verloopdatum  
Toegangs pakketten kunnen een verval datum bevatten. Verval datum kan worden ingesteld op een specifieke dag of de gebruiker een specifiek aantal dagen voor toegang geven. Wanneer het toegangs pakket verloopt en de gebruiker geen toegang heeft, kan het B2B-gast gebruikers object dat de gebruiker vertegenwoordigt, worden verwijderd of geblokkeerd om zich aan te melden. We raden u aan om de verval datum van toegangs pakketten voor externe gebruikers af te dwingen. Niet alle toegangs pakketten hebben verlopen. Voor degenen die dat niet doen, moet u ervoor zorgen dat u toegangs beoordelingen uitvoert.

### <a name="access-reviews"></a>Toegangsbeoordelingen

Voor toegangs pakketten kunnen periodieke [toegangs beoordelingen](../governance/manage-guest-access-with-access-reviews.md)zijn vereist, waarvoor de eigenaar van het pakket of een beheerder moet worden verplicht om te bevestigen dat gebruikers toegang hebben. 

Voordat u de beoordeling instelt, bepaalt u het volgende.

* Kopers

   * Wat zijn de criteria voor voortdurende toegang?

   * Wie zijn de opgegeven revisoren?

* Hoe vaak moeten geplande beoordelingen worden uitgevoerd?

   * Ingebouwde opties zijn maandelijks, elk kwar taal, bi-jaarlijks of jaarlijks. 

   * Voor pakketten die ondersteuning bieden voor externe toegang, wordt het per kwar taal aangeraden. 

 

> [!IMPORTANT]
> Toegangs beoordelingen van toegangs pakketten controleren alleen toegang via het rechten beheer. U moet daarom andere processen instellen om de toegang te controleren die wordt gegeven aan externe gebruikers buiten het beheer van rechten.

Zie [de implementatie van Azure AD-toegangs beoordelingen plannen](../governance/deploy-access-reviews.md)voor meer informatie over toegangs Beoordelingen.

## <a name="using-automation-in-entitlement-management"></a>Automatisering gebruiken in het beheer van rechten

U kunt [rechten op het beheer uitvoeren met behulp van Microsoft Graph](https://docs.microsoft.com/graph/tutorial-access-package-api), waaronder

* [Toegangs pakketten beheren](https://docs.microsoft.com/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Toegangs beoordelingen beheren](https://docs.microsoft.com/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Verbonden organisaties beheren](https://docs.microsoft.com/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Instellingen voor rechten beheer beheren](https://docs.microsoft.com/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Aanbevelingen 

U wordt aangeraden de procedures voor externe toegang te bepalen met het beheer van rechten.

**Voor projecten met een of meer zaken partners [maakt en gebruikt u toegangs pakketten](../governance/entitlement-management-access-package-create.md) om de gebruikers van de partner toegang tot bronnen** te geven en in te richten. 

* Als u B2B-gebruikers al in uw directory hebt, kunt u ze ook rechtstreeks toewijzen aan de juiste toegangs pakketten.

* U kunt toegang toewijzen in de [Azure Portal](../governance/entitlement-management-access-package-assignments.md)of via [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Gebruik uw instellingen voor identiteits beheer om gebruikers uit uw directory te verwijderen wanneer hun toegangs pakketten verlopen**.

![Scherm opname van het configureren van de levens cyclus van externe gebruikers.](media/secure-external-access/6-manage-external-lifecycle.png)

Deze instellingen zijn alleen van toepassing op gebruikers die zijn opgedaan via rechten beheer.

**[Beheer van catalogi en toegangs pakketten](../governance/entitlement-management-delegate.md) aan zakelijke eigen aren overdragen, met meer informatie over wie toegang moet hebben**.

![Scherm opname van het configureren van een catalogus.](media/secure-external-access/6-catalog-management.png)

**[Afdwinging van de verval datum van toegangs pakketten](../governance/entitlement-management-access-package-lifecycle-policy.md) waartoe externe gebruikers toegang hebben.**


![Scherm opname van het configureren van het verval van het toegangs pakket.](media/secure-external-access/6-access-package-expiration.png)

* Als u de eind datum van een op het project gebaseerd toegangs pakket weet, gebruikt u de op datum om de specifieke datum in te stellen. 

* Anders is het raadzaam om de verval datum niet langer 365 dagen te hebben, tenzij bekend is dat deze een meerjarige betrokkenheid is.

* Gebruikers toestaan om toegang uit te breiden.

* Goed keuring vereist om de uitbrei ding te verlenen.

**[Toegangs beoordelingen van pakketten afdwingen](../governance/manage-guest-access-with-access-reviews.md) om ongeoorloofde toegang voor gasten te voor komen.**

![Scherm opname van het maken van een nieuw toegangs pakket.](media/secure-external-access/6-new-access-package.png)

* Beoordelingen per kwar taal afdwingen.

* Stel, voor nalevings gevoelige projecten, de controleurs in op specifieke revisoren in plaats van zelf te controleren op externe gebruikers. De gebruikers die toegang hebben tot pakket beheerders zijn een goede plaats om te beginnen voor revisoren. 

* Voor minder gevoelige projecten heeft de gebruiker zelf de last van de organisatie om de toegang te verminderen van gebruikers die niet meer met hun thuis organisatie zijn.

Zie voor meer informatie reguleren van [toegang voor externe gebruikers in azure AD-rechts beheer](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Uw beveiligings postuur voor externe toegang bepalen](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met rechten beheer](6-secure-access-entitlement-managment.md) (u bent hier.)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)

 

 
