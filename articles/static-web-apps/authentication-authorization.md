---
title: Verificatie en autorisatie voor statische Azure-Web Apps
description: Meer informatie over het gebruik van verschillende autorisatie providers om uw statische app te beveiligen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: ab41a336c32a1827c23f4c4619f47dc294a4d2ea
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419283"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Verificatie en autorisatie voor API-apps in Azure Static Web Apps Preview

Azure static Web Apps stroomlijnt de verificatie-ervaring door verificatie met de volgende providers te beheren:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

Provider-specifieke [uitnodigingen](#invitations) koppelen gebruikers met rollen en gemachtigde gebruikers krijgen toegang tot [routes](routes.md) op basis van regels die zijn gedefinieerd in de _routes.jsvoor_ het bestand.

Alle verificatie providers zijn standaard ingeschakeld. Als u een verificatie provider wilt beperken, blokkeert u de [toegang](#block-an-authorization-provider) met een aangepaste route regel.

De onderwerpen van verificatie en autorisatie zijn aanzienlijk overlappende routerings concepten. Lees de [route ring](routes.md) in combi natie met dit artikel.

## <a name="roles"></a>Rollen

Elke gebruiker die toegang heeft tot een statische web-app, maakt deel uit van een of meer rollen.  Er zijn twee ingebouwde rollen waarmee gebruikers kunnen behoren:

- **anoniem**: alle gebruikers horen automatisch bij de _anonieme_ rol.
- **geverifieerd**: alle gebruikers die zijn aangemeld, behoren tot de _geauthenticeerde_ rol.

Naast de ingebouwde rollen kunt u nieuwe rollen maken, ze toewijzen aan gebruikers via uitnodigingen en hiernaar verwijzen in de _routes.jsin_ het bestand.

## <a name="role-management"></a>Rolbeheer

### <a name="add-a-user-to-a-role"></a>Een gebruiker toevoegen aan een rol

Als u gebruikers wilt toevoegen aan uw website, genereert u uitnodigingen waarmee u gebruikers kunt koppelen aan specifieke rollen. Rollen worden gedefinieerd en onderhouden in de _routes.jsvoor_ het bestand.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Een uitnodiging maken

Uitnodigingen zijn specifiek voor afzonderlijke autorisatie providers. Houd daarom rekening met de behoeften van uw app wanneer u selecteert welke providers moeten worden ondersteund. Sommige providers bieden het e-mail adres van een gebruiker, terwijl anderen alleen de gebruikers naam van de site opgeven.

<a name="provider-user-details" id="provider-user-details"></a>

| Autorisatie provider | Beschrijft de gebruikers  |
| ---------------------- | ----------------- |
| Azure Active Directory | e-mailadres     |
| Facebook               | e-mailadres     |
| GitHub                 | gebruikersnaam          |
| Google<sup>1</sup>     | e-mailadres     |
| Twitter                | gebruikersnaam          |

1. Navigeer naar een statische Web Apps resource in de [Azure Portal](https://portal.azure.com).
1. Klik onder _instellingen_ op **functie beheer**.
1. Klik op de knop **uitnodiging** .
1. Selecteer een _autorisatie provider_ in de lijst met opties.
1. Voeg de gebruikers naam of het e-mail adres van de ontvanger toe in het vak Details van de _uitnodiging_ .
    - Voor GitHub en Twitter voert u de gebruikers naam in. Voer het e-mail adres van de ontvanger in voor alle andere gebruikers.
1. Selecteer het domein van uw statische site in de vervolg keuzelijst van het _domein_ .
    - Het domein dat u selecteert, is het domein dat wordt weer gegeven in de uitnodiging. Als u een aangepast domein aan uw site hebt gekoppeld, wilt u waarschijnlijk het aangepaste domein kiezen.
1. Voeg een lijst met door komma's gescheiden namen toe in het vak _rol_ .
1. Voer het maximum aantal uren in dat u wilt dat de uitnodiging geldig blijft.
    - De Maxi maal mogelijke limiet is 168 uur, 7 dagen.
1. Klik op de knop **Genereren**.
1. Kopieer de koppeling vanuit het vak _koppeling uitnodigen_ .
1. De uitnodigings koppeling per e-mail verzenden naar de persoon die u toegang verleent tot uw app.

Wanneer de gebruiker op de koppeling in de uitnodiging klikt, wordt u gevraagd zich aan te melden met het bijbehorende account. Als de gebruiker is aangemeld, wordt deze gekoppeld aan de geselecteerde rollen.

> [!CAUTION]
> Zorg ervoor dat uw route regels niet conflicteren met de geselecteerde verificatie providers. Als u een provider met een route regel blokkeert, kunnen gebruikers geen uitnodigingen accepteren.

### <a name="update-role-assignments"></a>Roltoewijzingen bijwerken

1. Navigeer naar een statische Web Apps resource in de [Azure Portal](https://portal.azure.com).
1. Klik onder _instellingen_ op **functie beheer**.
1. Klik op de gebruiker in de lijst.
1. Bewerk de lijst met rollen in het vak _rol_ .
1. Klik op de knop **bijwerken** .

### <a name="remove-user"></a>Gebruiker verwijderen

1. Navigeer naar een statische Web Apps resource in de [Azure Portal](https://portal.azure.com).
1. Klik onder _instellingen_ op **functie beheer**.
1. Zoek de gebruiker in de lijst.
1. Schakel het selectie vakje in de rij van de gebruiker in.
1. Klik op de knop **Verwijderen**.

Houd bij het verwijderen van een gebruiker de volgende items in acht:

1. Als een gebruiker wordt verwijderd, worden de machtigingen ongeldig.
1. Wereld wijde doorgifte kan enkele minuten duren.
1. Als de gebruiker weer wordt toegevoegd aan de app, worden de [ `userId` wijzigingen doorgevoerd](user-information.md).

## <a name="remove-personal-identifying-information"></a>Persoonlijke identificatie gegevens verwijderen

Wanneer u een toepassing als eind gebruiker toewijst, heeft de toepassing toegang tot uw e-mail adres of uw gebruikers naam, afhankelijk van de ID-provider. Zodra deze informatie is verstrekt, bepaalt de eigenaar van de toepassing hoe persoons gegevens kunnen worden beheerd.

Eind gebruikers moeten contact opnemen met beheerders van afzonderlijke web-apps om deze informatie van hun systemen in te trekken.

Als u persoons gegevens wilt verwijderen uit het statische Azure-Web Apps platform en wilt voor komen dat het platform deze informatie verstrekt over toekomstige aanvragen, moet u een aanvraag indienen via de URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Als u wilt voor komen dat het platform deze informatie verstrekt over toekomstige aanvragen voor afzonderlijke apps, dient u een aanvraag in bij de volgende URL:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Systeemmap

Azure static Web Apps gebruikt de `/.auth` systeemmap om toegang te bieden tot autorisatie-gerelateerde api's. In plaats van een van de routes onder de `/.auth` map rechtstreeks naar eind gebruikers weer te geven, kunt u overwegen om [routerings regels](routes.md) te maken om beschrijvende url's te maken.

## <a name="login"></a>Aanmelden

Gebruik de volgende tabel om de provider-specifieke aanmeldings route te vinden.

| Autorisatie provider | Aanmeldings route             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Als u zich bijvoorbeeld wilt aanmelden met GitHub, kunt u een aanmeldings koppeling toevoegen zoals in het volgende code fragment:

```html
<a href="/.auth/login/github">Login</a>
```

Als u hebt gekozen voor het ondersteunen van meer dan één provider, moet u een providerspecifieke koppeling beschikbaar stellen voor elk van uw websites.

U kunt een [route regel](./configuration.md#routes) gebruiken om een standaard provider toe te wijzen aan een beschrijvende route, zoals _/login_.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Omleiding van post-aanmelding

Als u een gebruiker wilt terugkeren naar een specifieke pagina na de aanmelding, geeft u een URL op in de `post_login_redirect_uri` query teken reeks parameter.

## <a name="logout"></a>Afmelden

De `/.auth/logout` route meldt gebruikers van de website. U kunt een koppeling toevoegen aan uw site navigatie zodat de gebruiker zich afmeldt, zoals wordt weer gegeven in het volgende voor beeld.

```html
<a href="/.auth/logout">Log out</a>
```

U kunt een [route regel](./configuration.md#routes) gebruiken om een beschrijvende route te koppelen, zoals _/logout_.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Omleiding na afmelding

Als u wilt dat een gebruiker na de afmelding naar een specifieke pagina terugkeert, geeft u een URL op in de `post_logout_redirect_uri` query teken reeks parameter.

## <a name="block-an-authorization-provider"></a>Een autorisatie provider blok keren

U kunt de app beperken door een autorisatie provider te gebruiken. Uw app kan bijvoorbeeld alleen worden gestandardization op [providers die e-mail adressen openbaren](#provider-user-details).

Als u een provider wilt blok keren, kunt u [routerings regels](routes.md) maken om een 404 te retour neren voor aanvragen naar de geblokkeerde provider-specifieke route. Als u bijvoorbeeld Twitter als provider wilt beperken, voegt u de volgende route regel toe.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Beperkingen

Zie het [artikel quota's](quotas.md) voor algemene beperkingen en beperkingen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Toegang tot gegevens voor gebruikers verificatie en-autorisatie](user-information.md)

<sup>1</sup> wachtend op externe certificering.
