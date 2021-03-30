---
title: Code Ring OAuth 2,0-apparaat | Azure
titleSuffix: Microsoft identity platform
description: Gebruikers zonder een browser aanmelden. Maak Inge sloten en browser-minder verificatie stromen met behulp van de autorisatie toekenning van het apparaat.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272447"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Micro soft Identity platform en de OAuth 2,0-autorisatie voor het weigeren van apparaten

Het micro soft-identiteits platform ondersteunt de [machtiging verlenen voor apparaten](https://tools.ietf.org/html/rfc8628), waarmee gebruikers zich kunnen aanmelden bij apparaten met invoer beperkingen, zoals een Smart TV, IOT-apparaat of printer.  Om deze stroom in te scha kelen, heeft de gebruiker van het apparaat een webpagina in de browser op een ander apparaat om u aan te melden.  Zodra de gebruiker zich aanmeldt, kan het apparaat toegangs tokens verkrijgen en tokens vernieuwen als dat nodig is.

In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

## <a name="protocol-diagram"></a>Protocol diagram

De gehele apparaatcode stroom ziet er ongeveer uit als in het volgende diagram. Verderop in dit artikel worden de stappen beschreven.

![Stroom voor apparaatcode](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Autorisatie aanvraag voor apparaat

De client moet eerst controleren met de verificatie server voor een apparaat en gebruikers code die wordt gebruikt om verificatie te initiëren. De client verzamelt deze aanvraag van het `/devicecode` eind punt. In deze aanvraag moet de client ook de machtigingen bevatten die nodig zijn voor het verkrijgen van de gebruiker. Vanaf het moment dat deze aanvraag wordt verzonden, heeft de gebruiker slechts 15 minuten om zich aan te melden (de normale waarde voor `expires_in` ), dus deze aanvraag alleen als de gebruiker heeft aangegeven dat deze klaar is om zich aan te melden.

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman!
> [![Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | Kan/veelvoorkomende,/consumers of/organizations. zijn  Het kan ook de Directory Tenant zijn waarvoor u een machtiging wilt aanvragen in de indeling GUID of beschrijvende naam.  |
| `client_id` | Vereist | De **client-id** van de toepassing die de [Azure Portal – app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) ervaring die aan uw app is toegewezen. |
| `scope` | Vereist | Een lijst met door spaties gescheiden [bereiken](v2-permissions-and-consent.md) waarvan u wilt dat de gebruiker toestemming geeft.  |

### <a name="device-authorization-response"></a>Reactie van het apparaat autorisatie

Een geslaagde reactie is een JSON-object met de vereiste gegevens om de gebruiker in staat te stellen zich aan te melden.

| Parameter | Indeling | Beschrijving |
| ---              | --- | --- |
|`device_code`     | Tekenreeks | Een lange teken reeks die wordt gebruikt om de sessie tussen de client en de autorisatie server te controleren. De client gebruikt deze para meter om het toegangs token van de autorisatie server aan te vragen. |
|`user_code`       | Tekenreeks | Een korte teken reeks die wordt weer gegeven aan de gebruiker die wordt gebruikt om de sessie op een secundair apparaat te identificeren.|
|`verification_uri`| URI | De URI waarnaar de gebruiker moet gaan met de om `user_code` zich aan te melden. |
|`expires_in`      | int | Het aantal seconden voor de `device_code` en de `user_code` verval datum. |
|`interval`        | int | Het aantal seconden dat de client moet wachten tussen polling aanvragen. |
| `message`        | Tekenreeks | Een teken reeks met lees bare tekst met instructies voor de gebruiker. Dit kan worden gelokaliseerd door een **query parameter** op te nemen in de aanvraag van het formulier `?mkt=xx-XX` en de juiste taal cultuur code in te vullen. |

> [!NOTE]
> Het `verification_uri_complete` antwoord veld wordt op dit moment niet opgenomen of ondersteund.  Dit wordt vermeld omdat u de [standaard](https://tools.ietf.org/html/rfc8628) ziet die `verification_uri_complete` wordt weer gegeven als een optioneel onderdeel van de apparaatcode flow-standaard.

## <a name="authenticating-the-user"></a>Verificatie van de gebruiker

Na ontvangst `user_code` van de en `verification_uri` , wordt deze door de client weer gegeven aan de gebruiker en wordt hen gevraagd zich aan te melden met hun mobiele telefoon of PC-browser.

Als de gebruiker zich verifieert met een persoonlijk account (op/veelvoorkomende of/consumers), wordt u gevraagd om u opnieuw aan te melden om de verificatie status naar het apparaat te verzenden.  Ze worden ook gevraagd toestemming te geven om ervoor te zorgen dat ze op de hoogte zijn van de machtigingen die worden verleend.  Dit is niet van toepassing op werk-of school accounts die worden gebruikt voor verificatie.

Terwijl de gebruiker zich aanmeldt `verification_uri` , moet de client het `/token` eind punt voor het aangevraagde token pollen met behulp van de `device_code` .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parameter | Vereist | Beschrijving|
| -------- | -------- | ---------- |
| `tenant`  | Vereist | Dezelfde Tenant of Tenant alias die in de eerste aanvraag wordt gebruikt. |
| `grant_type` | Vereist | Moet `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Vereist | Moet overeenkomen met het `client_id` gebruik in de eerste aanvraag. |
| `device_code`| Vereist | De `device_code` geretourneerde in de autorisatie aanvraag voor het apparaat.  |

### <a name="expected-errors"></a>Verwachte fouten

De code stroom van het apparaat is een polling-protocol, zodat uw client fouten kan ontvangen voordat de verificatie van de gebruiker is voltooid.

| Fout | Beschrijving | Client actie |
| ------ | ----------- | -------------|
| `authorization_pending` | De gebruiker heeft de verificatie nog niet voltooid, maar de stroom is niet geannuleerd. | Herhaal de aanvraag na minstens `interval` seconden. |
| `authorization_declined` | De eind gebruiker heeft de autorisatie aanvraag geweigerd.| Stop polling en herstel naar een niet-geverifieerde status.  |
| `bad_verification_code`| De `device_code` verzonden naar het `/token` eind punt is niet herkend. | Controleer of de client de juiste `device_code` in de aanvraag verzendt. |
| `expired_token` | Er zijn ten minste `expires_in` seconden verstreken en verificatie is niet meer mogelijk `device_code` . | Stop polling en terugkeren naar een niet-geverifieerde status. |

### <a name="successful-authentication-response"></a>Geslaagde verificatie reactie

Een geslaagd token antwoord ziet er als volgt uit:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Indeling | Beschrijving |
| --------- | ------ | ----------- |
| `token_type` | Tekenreeks| Altijd ' Bearer '. |
| `scope` | Door spaties gescheiden teken reeksen | Als er een toegangs token is geretourneerd, worden hier de scopes weer gegeven waarvoor het toegangs token geldig is. |
| `expires_in`| int | Aantal seconden voordat het opgenomen toegangs token geldig is. |
| `access_token`| Dekkende teken reeks | Uitgegeven voor de aangevraagde [bereiken](v2-permissions-and-consent.md) .  |
| `id_token`   | JWT | Uitgegeven als de oorspronkelijke `scope` para meter het `openid` bereik bevat.  |
| `refresh_token` | Dekkende teken reeks | Verleend als de oorspronkelijke `scope` para meter is opgenomen `offline_access` .  |

U kunt het vernieuwings token gebruiken om nieuwe toegangs tokens te verkrijgen en tokens te vernieuwen met behulp van dezelfde stroom die wordt beschreven in de [documentatie over de OAuth-code stroom](v2-oauth2-auth-code-flow.md#refresh-the-access-token).
