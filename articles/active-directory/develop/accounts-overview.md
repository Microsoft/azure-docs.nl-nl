---
title: Micro soft Identity platform accounts & Tenant profielen op Android | Azure
description: Een overzicht van de micro soft Identity platform-accounts voor Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: fac66e8f82ea4c04e866b28fed5f8d0860ab81ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755964"
---
# <a name="accounts--tenant-profiles-android"></a>Accounts en tenantprofielen (Android)

Dit artikel bevat een overzicht van wat er `account` in het micro soft-identiteits platform is.

De micro soft Authentication Library (MSAL)-API vervangt de term *gebruiker* met het *account* voor de term. Een reden is dat een gebruiker (Human of software agent) mogelijk meerdere accounts heeft of kan gebruiken. Deze accounts kunnen zich in de eigen organisatie van de gebruiker bevinden en/of in andere organisaties waarvan de gebruiker lid is.

Een account in het micro soft Identity platform bestaat uit:

- Een unieke id.  
- Een of meer referenties die worden gebruikt om het eigendom/beheer van het account te demonstreren.
- Een of meer profielen die bestaan uit kenmerken, zoals:
  - Afbeelding, voor naam, familie naam, titel, kantoor locatie
- Een account heeft een bron van de instantie of het record systeem. Dit is het systeem waar het account wordt gemaakt en waar de referenties zijn opgeslagen die aan dat account zijn gekoppeld. In multi tenant systemen zoals het micro soft-identiteits platform is het record systeem de `tenant` waar het account is gemaakt. Deze Tenant wordt ook wel de genoemd `home tenant` .
- Accounts in het micro soft Identity-platform hebben de volgende record systemen:
  - Azure Active Directory, inclusief Azure Active Directory B2C.
  - Microsoft-account (Live).
- Accounts van systemen met een record buiten het micro soft-identiteits platform worden weer gegeven in het micro soft-identiteits platform, waaronder:
  - identiteiten van verbonden on-premises directory's (Windows Server Active Directory)
  - externe identiteiten van LinkedIn, GitHub, enzovoort.
  In deze gevallen heeft een account zowel een bron systeem als een systeem van de record in het micro soft Identity-platform.
- Met het micro soft Identity-platform kan één account worden gebruikt voor toegang tot resources die deel uitmaken van meerdere organisaties (Azure Active Directory tenants).
  - Als u wilt vastleggen dat een account van het ene record systeem (AAD Tenant A) toegang heeft tot een resource in een ander record systeem (AAD Tenant B), moet het account worden weer gegeven in de Tenant waarin de resource is gedefinieerd. Dit doet u door een lokale record van het account te maken op systeem A in systeem B.
  - Deze lokale record, die de weer gave van het account is, is gekoppeld aan het oorspronkelijke account.
  - MSAL maakt deze lokale record beschikbaar als een `Tenant Profile` .
  - Het Tenant profiel kan verschillende kenmerken hebben die relevant zijn voor de lokale context, zoals de functie, de kantoor locatie, de contact gegevens, enzovoort.
- Omdat een account in een of meer tenants aanwezig kan zijn, heeft een account mogelijk meer dan één profiel.

> [!NOTE]
> MSAL behandelt het Microsoft-account systeem (Live, MSA) als een andere Tenant binnen het micro soft Identity-platform. De Tenant-id van de Microsoft-account Tenant is: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagram van account overzicht

![Diagram van account overzicht](./media/accounts-overview/accounts-overview.svg)

In het bovenstaande diagram:

- Het account `bob@contoso.com` wordt gemaakt in het on-premises Windows Server-Active Directory (lokaal systeem van de record).
- Het account `tom@live.com` wordt gemaakt in de Microsoft-account Tenant.
- `bob@contoso.com` heeft toegang tot ten minste één resource in de volgende Azure Active Directory tenants:
  - contoso.com (Cloud systeem van de record die is gekoppeld aan een on-premises systeem van record)
  - fabrikam.com
  - woodgrovebank.com
  - Een Tenant profiel voor `bob@contoso.com` bestaat in elk van deze tenants.
- `tom@live.com` heeft toegang tot resources in de volgende micro soft-tenants:
  - contoso.com
  - fabrikam.com
  - Een Tenant profiel voor `tom@live.com` bestaat in elk van deze tenants.
- Informatie over Tom en Bob in andere tenants kan afwijken van die in het record systeem. Ze kunnen verschillen met kenmerken zoals de functie, de kantoor locatie, enzovoort. Ze kunnen lid zijn van groepen en/of rollen binnen elke organisatie (Azure Active Directory Tenant). We verwijzen naar deze informatie als bob@contoso.com Tenant profiel.

In het diagram bob@contoso.com en tom@live.com toegang hebben tot resources in verschillende Azure Active Directory tenants. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../external-identities/add-users-administrator.md)voor meer informatie.

## <a name="accounts-and-single-sign-on-sso"></a>Accounts en eenmalige aanmelding (SSO)

In de MSAL-token cache wordt *één vernieuwings token* per account opgeslagen. Het vernieuwings token kan worden gebruikt om toegangs tokens op de achtergrond te vragen bij meerdere micro soft-identiteits platform-tenants. Wanneer een Broker op een apparaat is geïnstalleerd, wordt het account beheerd door de Broker en wordt de eenmalige aanmelding op het apparaat mogelijk.

> [!IMPORTANT]
> Het gedrag van Business to consumer (B2C) en het vernieuwen van tokens verschilt van de rest van het micro soft Identity-platform. Zie [B2C policies & accounts](#b2c-policies--accounts)(Engelstalig) voor meer informatie.

## <a name="account-identifiers"></a>Account-id's

De MSAL-account-ID is geen account object-ID. Het is niet bedoeld om te worden geparseerd en/of veranderd bij het overbrengen van iets anders dan de uniekheid binnen het micro soft Identity-platform.

Voor compatibiliteit met de Azure AD-verificatie bibliotheek (ADAL) en om de migratie van ADAL naar MSAL te vereenvoudigen, kan MSAL accounts opzoeken met een geldige id voor het account dat beschikbaar is in de MSAL-cache.  Met de volgende code wordt bijvoorbeeld altijd hetzelfde account object opgehaald, tom@live.com omdat elk van de id's geldig is:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Toegang tot claims over een account

Naast het aanvragen van een toegangs token, vraagt MSAL ook altijd een ID-token aan van elke Tenant. Dit gebeurt door altijd de volgende bereiken aan te vragen:

- OpenID Connect
- profiel

Het ID-token bevat een lijst met claims. `Claims` zijn naam/waarde-paren over het account en worden gebruikt om de aanvraag te doen.

Zoals eerder vermeld, kan elke Tenant waarbij een account bestaat, verschillende informatie over het account opslaan, inclusief, maar niet beperkt tot kenmerken zoals: taak titel, kantoor locatie, enzovoort.

Een account kan een lid of gast in meerdere organisaties zijn, maar MSAL geen query uitvoeren op een service om een lijst op te halen van de tenants waarvan het account lid is. In plaats daarvan bouwt MSAL een lijst op met de tenants waarin het account zich bevindt, als gevolg van token aanvragen die zijn gemaakt.

De claims die worden weer gegeven op het account object zijn altijd de claims van de/{Authority} Home Tenant voor een account. Als dat account niet is gebruikt om een token aan te vragen voor hun thuis Tenant, kan MSAL geen claims leveren via het account object.  Bijvoorbeeld:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Raadpleeg [claims in een id_token](./id-tokens.md#claims-in-an-id_token) voor een lijst met beschik bare claims van het account object

> [!TIP]
> Als u aanvullende claims in uw id_token wilt toevoegen, raadpleegt u de documentatie voor de optionele claims in [How to: optionele claims voor uw Azure AD-App opgeven](./active-directory-optional-claims.md)

### <a name="access-tenant-profile-claims"></a>Claims voor toegang tot Tenant profielen

Als u toegang wilt krijgen tot claims over een account die in andere tenants worden weer gegeven, moet u eerst uw account object converteren naar `IMultiTenantAccount` . Alle accounts kunnen meerdere tenants zijn, maar het aantal Tenant profielen dat via MSAL beschikbaar is, is gebaseerd op de tenants waarvan u de tokens hebt aangevraagd met behulp van het huidige account.  Bijvoorbeeld:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C-beleid & accounts

Het vernieuwen van tokens voor een account wordt niet gedeeld via B2C-beleid. Als gevolg hiervan is eenmalige aanmelding met tokens niet mogelijk. Dit betekent niet dat eenmalige aanmelding niet mogelijk is. Dit betekent dat eenmalige aanmelding een interactieve ervaring heeft waarin een cookie beschikbaar is om eenmalige aanmelding mogelijk te maken.

Dit betekent ook dat in het geval van MSAL tokens worden verkregen met behulp van verschillende B2C-beleids regels. deze worden vervolgens beschouwd als afzonderlijke accounts, elk met hun eigen id. Als u een account wilt gebruiken om een token aan te vragen `acquireTokenSilent` , moet u het account selecteren in de lijst met accounts die overeenkomen met het beleid dat u gebruikt met de token aanvraag. Bijvoorbeeld:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```