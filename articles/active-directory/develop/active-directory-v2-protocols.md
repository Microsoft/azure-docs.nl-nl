---
title: OAuth 2,0 en OpenID Connect Connect protocollen op het micro soft Identity platform | Azure
titleSuffix: Microsoft identity platform
description: Een hand leiding voor OAuth 2,0 en OpenID Connect Connect-protocollen die worden ondersteund door het micro soft Identity-platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 765c363542b07deac44d47b94731e1109fcba045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755742"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>OAuth 2,0 en OpenID Connect Connect protocollen op het micro soft Identity-platform

Met het micro soft Identity platform-eind punt voor Identity-as-a-service wordt verificatie en autorisatie geïmplementeerd met respectievelijk de industrie Standard-protocollen OpenID Connect Connect (OIDC) en OAuth 2,0. Hoewel de service compatibel is met standaarden, kunnen er subtiele verschillen zijn tussen twee implementaties van deze protocollen. Deze informatie is nuttig als u ervoor kiest om uw code te schrijven door rechtstreeks HTTP-aanvragen te verzenden en af te handelen of door een open-source bibliotheek van derden te gebruiken in plaats van een van de [open source-bibliotheken](reference-v2-libraries.md)te gebruiken.

## <a name="the-basics"></a>De basisbeginselen

In bijna alle OAuth 2,0-en OpenID Connect Connect-stromen zijn er vier partijen betrokken bij de uitwisseling:

![Diagram van de OAuth 2,0-rollen](./media/active-directory-v2-flows/protocols-roles.svg)

* De **autorisatie server** is het micro soft-identiteits platform en is verantwoordelijk voor het garanderen van de identiteit van de gebruiker, het verlenen en intrekken van de toegang tot bronnen en het uitgeven van tokens. De autorisatie server staat ook bekend als de ID-provider: deze zorgt er veilig voor dat u alles kunt doen met de informatie van de gebruiker, hun toegang en de vertrouwens relaties tussen partijen in een stroom.
* De **resource-eigenaar** is doorgaans de eind gebruiker. Het is de partij die eigenaar is van de gegevens en de kracht heeft om clients toegang te geven tot die gegevens of bron.
* De **OAuth-client** is uw app, geïdentificeerd door de toepassings-id. De OAuth-client is doorgaans de partij waarmee de eind gebruiker communiceert en die tokens van de autorisatie server aanvraagt. Aan de client moet een machtiging worden verleend voor toegang tot de resource door de resource-eigenaar.
* De **resource server** is de locatie waar de resource of gegevens zich bevinden. Het vertrouwt de autorisatie server voor het veilig verifiëren en autoriseren van de OAuth-client en maakt gebruik van toegangs tokens van Bearer om ervoor te zorgen dat toegang tot een bron kan worden verleend.

## <a name="app-registration"></a>App-registratie

Elke app die zowel privé-als werk-of school accounts wil accepteren, moet worden geregistreerd via de **app-registraties** ervaring in de [Azure Portal](https://aka.ms/appregistrations) voordat deze gebruikers kunnen worden ondertekend met OAuth 2,0 of OpenID Connect Connect. Tijdens het registratie proces van de app worden een aantal waarden verzameld en toegewezen aan uw app:

* Een **toepassings-id** waarmee uw app uniek wordt geïdentificeerd
* Een **omleidings-URI** (optioneel) die kan worden gebruikt om antwoorden terug te sturen naar uw app
* Een paar andere scenario-specifieke waarden.

Zie [Een app registreren](quickstart-register-app.md) voor meer informatie.

## <a name="endpoints"></a>Eindpunten

Zodra de app is geregistreerd, communiceert deze met het micro soft-identiteits platform door aanvragen te verzenden naar het eind punt:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Waarbij de `{tenant}` kan een van vier verschillende waarden aannemen:

| Waarde | Beschrijving |
| --- | --- |
| `common` | Hiermee kunnen gebruikers met persoonlijke micro soft-accounts en werk-of school accounts van Azure AD zich aanmelden bij de toepassing. |
| `organizations` | Hiermee kunnen alleen gebruikers met werk-of school accounts van Azure AD zich aanmelden bij de toepassing. |
| `consumers` | Hiermee kunnen alleen gebruikers met persoonlijke micro soft-accounts (MSA) zich aanmelden bij de toepassing. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` of `contoso.onmicrosoft.com` | Hiermee kunnen alleen gebruikers met werk-of school accounts van een bepaalde Azure AD-Tenant zich aanmelden bij de toepassing. De beschrijvende domein naam van de Azure AD-Tenant of de GUID-id van de Tenant kan worden gebruikt. |

Als u wilt weten hoe u deze eind punten kunt gebruiken, kiest u een bepaald type app in de sectie [protocollen](#protocols) en volgt u de koppelingen voor meer informatie.

> [!TIP]
> Elke app die is geregistreerd in azure AD kan gebruikmaken van het micro soft-identiteits platform, zelfs als ze geen persoonlijke accounts ondertekenen.  Op deze manier kunt u bestaande toepassingen migreren naar het micro soft Identity platform en [MSAL](reference-v2-libraries.md) zonder uw toepassing opnieuw te maken.

## <a name="tokens"></a>Tokens

OAuth 2,0 en OpenID Connect Connect maken uitgebreid gebruik van **Bearer-tokens**, die meestal worden weer gegeven als [JWTs (JSON-webtokens)](https://tools.ietf.org/html/rfc7519). Een Bearer-token is een licht gewicht beveiligings token dat de ' Bearer ' toegang verleent tot een beveiligde bron. In deze zin is de ' Bearer ' iedereen die een kopie van het token ontvangt. Een feestje moet eerst worden geverifieerd met het micro soft Identity-platform om het Bearer-token te ontvangen, als de vereiste stappen niet worden uitgevoerd om het token in trans missie en opslag te beveiligen, het kan worden onderschept en gebruikt door een onbedoelde partij. Hoewel sommige beveiligings tokens een ingebouwd mechanisme hebben om te voor komen dat niet-geautoriseerde partijen deze gebruiken, hebben Bearer-tokens dit mechanisme niet en moeten ze worden getransporteerd in een beveiligd kanaal, zoals trans port Layer Security (HTTPS). Als een Bearer-token in de heldere staat wordt verzonden, kan een kwaadwillende partij een man-in-the-middle-aanval gebruiken om het token te verkrijgen en dit te gebruiken voor onbevoegde toegang tot een beveiligde bron. Dezelfde beveiligings principes zijn van toepassing bij het opslaan of caching van Bearer-tokens voor later gebruik. Zorg er altijd voor dat uw app Bearer-tokens op een veilige manier verzendt en opslaat. Zie [RFC 6750 Section 5](https://tools.ietf.org/html/rfc6750)(Engelstalig) voor meer informatie over de beveiligings overwegingen voor Bearer-tokens.

Er worden voornamelijk drie typen tokens gebruikt in OAuth 2,0/OIDC:

* [Toegangs tokens](access-tokens.md) : tokens die een bron server ontvangt van een client, met de machtigingen die de client heeft gekregen.  
* [Id-tokens](id-tokens.md) : tokens die een client ontvangt van de autorisatie server, die wordt gebruikt om een gebruiker aan te melden en basis informatie over hen te krijgen.
* Vernieuwings tokens: wordt gebruikt door een client om nieuwe toegangs-en ID-tokens in de loop van de tijd te verkrijgen.  Dit zijn ondoorzichtige teken reeksen en kunnen alleen worden herkend door de autorisatie server.

## <a name="protocols"></a>Protocollen

Als u een aantal voorbeeld aanvragen wilt zien, gaat u aan de slag met een van de onderstaande protocol documenten. Elk abonnement komt overeen met een bepaald verificatie scenario. Als u hulp nodig hebt bij het bepalen van de juiste stroom voor u, bekijkt [u de typen apps die u met micro soft Identity platform kunt bouwen](v2-app-types.md).

* [Een mobiele, native-en web-app bouwen met OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Gebruikers aanmelden met OpenID Connect Connect](v2-protocols-oidc.md)
* [Bouw daemons of processen aan de server zijde met de OAuth 2,0-client referenties stroom](v2-oauth2-client-creds-grant-flow.md)
* [Tokens ophalen in een web-API met de OAuth 2,0 namens-stroom](v2-oauth2-on-behalf-of-flow.md)
* [Apps met één pagina bouwen met de impliciete OAuth 2,0-stroom](v2-oauth2-implicit-grant-flow.md)
