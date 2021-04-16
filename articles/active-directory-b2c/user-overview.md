---
title: Overzicht van gebruikersaccounts in Azure Active Directory B2C
description: Meer informatie over de typen gebruikersaccounts die kunnen worden gebruikt in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 4b35cfeded13a50e5e27c240b0826f1d108ff7eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529441"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Overzicht van gebruikersaccounts in Azure Active Directory B2C

In Azure Active Directory B2C (Azure AD B2C) zijn er verschillende typen accounts die kunnen worden gemaakt. Azure Active Directory, Active Directory B2B en Active Directory B2C delen in de typen gebruikersaccounts die kunnen worden gebruikt.

De volgende typen accounts zijn beschikbaar:

- **Werkaccount:** een werkaccount heeft toegang tot resources in een tenant en kan met een beheerdersrol tenants beheren.
- **Gastaccount:** een gastaccount kan alleen een Microsoft-account of een Azure Active Directory die kan worden gebruikt voor toegang tot toepassingen of het beheren van tenants.
- **Consumentenaccount:** een consumentenaccount wordt gebruikt door een gebruiker van de toepassingen die u hebt geregistreerd bij Azure AD B2C. Consumentenaccounts kunnen worden gemaakt door:
  - De gebruiker doormaakt een gebruikersstroom voor aanmelding in een Azure AD B2C toepassing
  - Een Microsoft Graph-API gebruiken
  - Azure Portal gebruiken

## <a name="work-account"></a>Werkaccount

Een werkaccount wordt op dezelfde manier gemaakt voor alle tenants op basis van Azure AD. Als u een werkaccount wilt maken, kunt u de informatie in [Quickstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Er wordt een werkaccount gemaakt met **behulp van de optie** Nieuwe gebruiker in de Azure Portal.

Wanneer u een nieuw werkaccount toevoegt, moet u rekening houden met de volgende configuratie-instellingen:

- **Naam** en **Gebruikersnaam: de** **eigenschap Naam** bevat de opgegeven en achternaam van de gebruiker. De **Gebruikersnaam is de** id die de gebruiker invoert om zich aan te melden. De gebruikersnaam bevat het volledige domein. Het domeinnaamgedeelte van de gebruikersnaam moet de initiële standaarddomeinnaam *your-domain.onmicrosoft.com of* een geverifieerde, [](../active-directory/fundamentals/add-custom-domain.md) niet-federatief aangepaste domeinnaam zijn, *zoals contoso.com*. 
- **E-mailadres:** de nieuwe gebruiker kan zich ook aanmelden met een e-mailadres. Speciale tekens of tekens van meerderebytes in e-mail, bijvoorbeeld Japanse tekens, worden niet ondersteund.
- **Profiel:** het account wordt ingesteld met een profiel met gebruikersgegevens. U kunt een voornaam, achternaam, functie en afdelingsnaam invoeren. U kunt het profiel bewerken nadat het account is gemaakt.
- **Groepen:** gebruik groepen om beheertaken uit te voeren, zoals het toewijzen van licenties of machtigingen aan veel gebruikers of apparaten tegelijk. U kunt het nieuwe account in een bestaande groep in [uw](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) tenant zetten.
- **Directory-rol:** u moet het toegangsniveau opgeven dat het gebruikersaccount heeft voor resources in uw tenant. De volgende machtigingsniveaus zijn beschikbaar:

    - **Gebruiker:** gebruikers hebben toegang tot toegewezen resources, maar kunnen de meeste tenantbronnen niet beheren.
    - **Globale beheerder:** globale beheerders hebben volledige controle over alle tenantbronnen.
    - **Beperkte beheerder:** selecteer de beheerdersrol of -rollen voor de gebruiker. Zie Beheerdersrollen toewijzen in Azure Active Directory voor meer informatie over de rollen [die kunnen worden Azure Active Directory.](../active-directory/roles/permissions-reference.md)

### <a name="create-a-work-account"></a>Een werkaccount maken

U kunt de volgende informatie gebruiken om een nieuw werkaccount te maken:

- [Azure-portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Een gebruikersprofiel bijwerken

U kunt de volgende informatie gebruiken om het profiel van een gebruiker bij te werken:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Een wachtwoord voor een gebruiker opnieuw instellen

U kunt de volgende informatie gebruiken om het wachtwoord van een gebruiker opnieuw in te stellen:

- [Azure-portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Gastgebruiker

U kunt externe gebruikers uitnodigen voor uw tenant als gastgebruiker. Een typisch scenario voor het uitnodigen van een gastgebruiker voor uw Azure AD B2C-tenant is voor het delen van beheerderstaken. Zie Eigenschappen van een Azure Active Directory [B2B-samenwerkingsgebruiker](../active-directory/external-identities/user-properties.md)voor een voorbeeld van het gebruik van een gastaccount.

Wanneer u een gastgebruiker uitnodigt voor uw tenant, geeft u het e-mailadres van de ontvanger en een bericht op waarin de uitnodiging wordt beschreven. De uitnodigingskoppeling brengt de gebruiker naar de toestemmingspagina. Als er geen Postvak IN is gekoppeld aan het e-mailadres, kan de gebruiker naar de toestemmingspagina navigeren door naar een Microsoft-pagina te gaan met behulp van de uitgenodigde referenties. De gebruiker wordt vervolgens gedwongen de uitnodiging op dezelfde manier in te wisselen als op de koppeling in het e-mailbericht te klikken. Bijvoorbeeld: `https://myapps.microsoft.com/B2CTENANTNAME`.

U kunt ook de api voor [Microsoft Graph gebruiken om](/graph/api/invitation-post) een gastgebruiker uit te nodigen.

## <a name="consumer-user"></a>Consumentgebruiker

De consumentgebruiker kan zich aanmelden bij toepassingen die zijn beveiligd door Azure AD B2C, maar heeft geen toegang tot Azure-resources, zoals de Azure Portal. De consumentgebruiker kan een lokaal account of federatief account gebruiken, zoals Facebook of Twitter. Een consumentenaccount wordt gemaakt met behulp van een gebruikersstroom voor registreren of aanmelden, met behulp van de [Microsoft Graph-API](user-flow-overview.md)of met behulp van de Azure Portal.

U kunt de gegevens opgeven die worden verzameld wanneer een consumentengebruikersaccount wordt gemaakt. Zie Gebruikerskenmerken toevoegen en [gebruikersinvoer aanpassen voor meer informatie.](configure-user-input.md)

Zie Manage Azure AD B2C user accounts with Microsoft Graph (Gebruikersaccounts beheren met gebruikersaccounts met Microsoft Graph) voor meer informatie [over het Microsoft Graph.](./microsoft-graph-operations.md)

### <a name="migrate-consumer-user-accounts"></a>Gebruikersaccounts voor consumenten migreren

Mogelijk moet u bestaande consumentengebruikersaccounts migreren van een id-provider naar Azure AD B2C. Raadpleeg [Gebruikers migreren naar Azure AD B2C](user-migration.md) voor meer informatie.
