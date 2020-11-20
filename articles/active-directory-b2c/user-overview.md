---
title: Overzicht van gebruikers accounts in Azure Active Directory B2C
description: Meer informatie over de typen gebruikers accounts die kunnen worden gebruikt in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f7a4d0852edded516b7fbe42f15abfe160e44cd3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952791"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Overzicht van gebruikers accounts in Azure Active Directory B2C

In Azure Active Directory B2C (Azure AD B2C) zijn er verschillende typen accounts die kunnen worden gemaakt. Azure Active Directory, Active Directory B2B en Active Directory B2C share in de typen gebruikers accounts die kunnen worden gebruikt.

De volgende typen accounts zijn beschikbaar:

- **Werk account** : een werk account kan toegang krijgen tot bronnen in een Tenant en met een beheerdersrol, en kan tenants beheren.
- **Gast account** : een gast account kan alleen een Microsoft-account of een Azure Active Directory gebruiker zijn die kan worden gebruikt voor toegang tot toepassingen of tenants beheren.
- **Consument account** : een consument account wordt gebruikt door een gebruiker van de toepassingen die u hebt geregistreerd bij Azure AD B2C. Consumenten accounts kunnen worden gemaakt door:
  - De gebruiker doorloopt een aanmeldings stroom van een gebruiker in een Azure AD B2C-toepassing
  - Microsoft Graph-API gebruiken
  - Azure Portal gebruiken

## <a name="work-account"></a>Werk account

Een werk account is op dezelfde manier gemaakt voor alle tenants op basis van Azure AD. Als u een werk account wilt maken, kunt u de informatie gebruiken in [Quick Start: nieuwe gebruikers toevoegen aan Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Er wordt een werk account gemaakt met de optie **nieuwe gebruiker** in de Azure Portal.

Wanneer u een nieuw werk account toevoegt, moet u rekening houden met de volgende configuratie-instellingen:

- **Naam** en **gebruikers naam** : de eigenschap **name** bevat de opgegeven en de achternaam van de gebruiker. De **gebruikers naam** is de id waarmee de gebruiker zich aanmeldt. De gebruikers naam bevat het volledige domein. Het domein naam gedeelte van de gebruikers naam moet de oorspronkelijke standaard domein naam *your-domain.onmicrosoft.com* of een geverifieerde, niet-federatieve [aangepaste domein](../active-directory/fundamentals/add-custom-domain.md) naam zijn, zoals *contoso.com*.
- **Profiel** : het account is ingesteld met een profiel van gebruikers gegevens. U hebt de mogelijkheid om een voor naam, achternaam, functie en afdelings naam in te voeren. U kunt het profiel bewerken nadat het account is gemaakt.
- **Groepen** : gebruik een groep om beheer taken uit te voeren, zoals het toewijzen van licenties of machtigingen aan een aantal gebruikers of apparaten tegelijk. U kunt het nieuwe account in een bestaande [groep](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) in uw Tenant plaatsen.
- **Directory-functie** : u moet het toegangs niveau opgeven dat de gebruikers account heeft voor de resources in uw Tenant. De volgende machtigings niveaus zijn beschikbaar:

    - **Gebruikers** -gebruikers hebben toegang tot toegewezen bronnen maar kunnen de meeste Tenant bronnen niet beheren.
    - **Globale beheerder** : globale beheerders hebben volledige controle over alle Tenant bronnen.
    - **Beperkte beheerder** : Selecteer de administratieve rol of rollen voor de gebruiker. Zie [beheerders rollen toewijzen in azure Active Directory](../active-directory/roles/permissions-reference.md)voor meer informatie over de rollen die kunnen worden geselecteerd.

### <a name="create-a-work-account"></a>Een werk account maken

U kunt de volgende informatie gebruiken om een nieuw werk account te maken:

- [Azure-portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Een gebruikers profiel bijwerken

U kunt de volgende informatie gebruiken om het profiel van een gebruiker bij te werken:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Een wacht woord voor een gebruiker opnieuw instellen

U kunt de volgende informatie gebruiken om het wacht woord van een gebruiker opnieuw in te stellen:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Gast gebruiker

U kunt externe gebruikers als gast gebruiker uitnodigen voor uw Tenant. Een typisch scenario voor het uitnodigen van een gastgebruiker voor uw Azure AD B2C-tenant is voor het delen van beheerderstaken. Zie [Eigenschappen van een Azure Active Directory B2B-samenwerkings gebruiker](../active-directory/external-identities/user-properties.md)voor een voor beeld van het gebruik van een gast account.

Wanneer u een gast gebruiker uitnodigt voor uw Tenant, geeft u het e-mail adres van de ontvanger en een bericht waarin de uitnodiging wordt beschreven. De uitnodigings koppeling neemt de gebruiker op de pagina toestemming waar de knop **aan de slag** is geselecteerd en de controle van machtigingen wordt geaccepteerd. Als een postvak in niet aan het e-mail adres is gekoppeld, kan de gebruiker naar een micro soft-pagina gaan met behulp van de genodigde referenties om naar de pagina met de toestemming te gaan. De gebruiker kan de uitnodiging vervolgens op dezelfde manier inwisselen als klikken op de koppeling in het e-mail bericht. Bijvoorbeeld: `https://myapps.microsoft.com/B2CTENANTNAME`.

U kunt ook de [Microsoft Graph-API](/graph/api/invitation-post?view=graph-rest-beta) gebruiken om een gast gebruiker uit te nodigen.

## <a name="consumer-user"></a>Consumenten gebruiker

De consumenten gebruiker kan zich aanmelden bij toepassingen die zijn beveiligd door Azure AD B2C, maar kunnen geen toegang krijgen tot Azure-resources zoals de Azure Portal. De gebruiker van de consument kan een lokaal account of federatieve accounts gebruiken, zoals Facebook of Twitter. Een Consumer account wordt gemaakt met behulp van een [registratie-of aanmeldings gebruikers stroom](user-flow-overview.md), met behulp van de Microsoft Graph-API of met behulp van de Azure Portal.

U kunt de gegevens opgeven die worden verzameld wanneer een gebruikers account van een consument wordt gemaakt met behulp van aangepaste gebruikers kenmerken. Zie [aangepaste kenmerken definiëren in azure Active Directory B2C](user-flow-custom-attributes.md)voor meer informatie.

Zie [Azure AD B2C gebruikers accounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md)voor meer informatie over het beheren van consumenten accounts.

### <a name="migrate-consumer-user-accounts"></a>Gebruikers accounts van consumenten migreren

Mogelijk moet u bestaande consumenten gebruikers accounts van een id-provider migreren naar Azure AD B2C. Zie [Gebruikers migreren naar Azure AD B2C](user-migration.md)voor meer informatie.