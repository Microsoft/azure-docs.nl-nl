---
title: Een toegangstoken aanvragen - Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het aanvragen van een toegangstoken van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5168d5e5e3935da267fb26f38735a88bdfd7837
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654473"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Een toegangstoken aanvragen in Azure Active Directory B2C

Een *toegangstoken* bevat claims die u in Azure Active Directory B2C (Azure AD B2C) kunt gebruiken om de toegekende machtigingen voor uw API's te identificeren. Bij het aanroepen van een resourceserver moet er een toegangstoken aanwezig zijn in de HTTP-aanvraag. Een toegangstoken wordt aangeduid als **access_token** in de reacties van Azure AD B2C.

In dit artikel wordt beschreven hoe u een toegangstoken kunt aanvragen voor een webtoepassing en web-API. Zie het [overzicht van tokens in Azure Active Directory B2C](tokens-overview.md) voor meer informatie over tokens in Azure AD B2C.

> [!NOTE]
> **Web-API-ketens (On-Behalf-Of) worden niet ondersteund door Azure AD B2C.** - Veel architecturen bevatten een web-API die een andere downstream web-API moet aanroepen, beide beveiligd door Azure AD B2C. Dit scenario is gebruikelijk bij clients met een web API-back-end, die op zijn beurt een andere service aanroept. Dit scenario met web-API-keten kan worden ondersteund met behulp van de OAuth 2.0 JWT bearer-token-referentietoekenning, ook wel de On-Behalf-Of-stroom genoemd. De On-Behalf-Of-stroom is momenteel echter niet geïmplementeerd in Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

- [Maak een gebruikersstroom](tutorial-create-user-flows.md) zodat gebruikers zich bij uw toepassing kunnen registreren en aanmelden.
- [Een web-API-toepassing toevoegen aan uw Azure Active Directory B2C-tenant](add-web-api-application.md), als u dit nog niet hebt gedaan.

## <a name="scopes"></a>Bereiken

Bereiken bieden een manier om machtigingen tot beveiligde resources te beheren. Wanneer een toegangstoken wordt aangevraagd, moet de clienttoepassing de gewenste machtigingen opgeven in de parameter **bereik** van de aanvraag. Als u bijvoorbeeld de **Bereikwaarde** wilt opgeven van `read` voor de API met de **App-id-URI** van `https://contoso.onmicrosoft.com/api`, zou het bereik `https://contoso.onmicrosoft.com/api/read` zijn.

Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. Als u meerdere machtigingen in dezelfde aanvraag wilt ophalen, kunt u meerdere vermeldingen toevoegen in de parameter voor het enkele **bereik** van de aanvraag, gescheiden door spaties.

In het volgende voorbeeld ziet u de bereiken die in een URL zijn gedecodeerd:

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

In het volgende voorbeeld ziet u de bereiken die in een URL zijn gecodeerd:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

Als u meer bereiken aanvraagt dan voor uw clienttoepassing worden verleend, wordt de aanroep uitgevoerd als er ten minste één machtiging wordt verleend. De **scp**-claim in het resulterende toegangstoken is gevuld met alleen de machtigingen die zijn verleend. 

### <a name="openid-connect-scopes"></a>OpenID Connect Connect-scopes

De OpenID Connect-standaard specificeert verschillende speciale bereikwaarden. De volgende bereiken vertegenwoordigen de machtiging voor toegang tot het gebruikersprofiel:

- **openid** - Vraagt een id-token aan.
- **offline_access** - Vraagt een vernieuwingstoken aan met behulp van [Auth Code-stromen](authorization-code-flow.md).
- **00000000-0000-0000-0000-000000000000** : het gebruik van de client-id als het bereik geeft aan dat uw app een toegangs token nodig heeft dat kan worden gebruikt voor uw eigen service of Web-API, die wordt vertegenwoordigd door dezelfde client-id.

Als de parameter **response_type** in een `/authorize` aanvraag `token` bevat, moet de parameter **bereik** ten minste één resourcebereik bevatten, behalve `openid` en `offline_access`, dat wordt verleend. Anders wordt de aanvraag `/authorize` geweigerd.

## <a name="request-a-token"></a>Een token aanvragen

Als u een toegangstoken wilt aanvragen, hebt u een autorisatiecode nodig. Hieronder ziet u een voorbeeld van een aanvraag voor het `/authorize`-eindpunt voor een autorisatiecode. Aangepaste domeinen worden niet ondersteund voor gebruik met toegangstokens. Gebruik uw tenant-name.onmicrosoft.com-domein in de aanvraag-URL.

In het volgende voorbeeld vervangt u deze waarden:

- `<tenant-name>` - De naam van de Azure AD B2C-tenant.
- `<policy-name>` - De naam van uw aangepaste beleid of gebruikersstroom.
- `<application-ID>` - De toepassings-id van de webtoepassing die u hebt geregistreerd ter ondersteuning van de gebruikersstroom.
- `<redirect-uri>` - De **Omleidings-URI** die u hebt ingevoerd tijdens het registreren van de clienttoepassing.

```http
GET https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

Het antwoord met de autorisatiecode moet vergelijkbaar zijn met dit voorbeeld:

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

Nadat u de autorisatiecode hebt ontvangen, kunt u deze gebruiken om een toegangstoken aan te vragen:

```http
POST <tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

De uitvoer moet er ongeveer uitzien als het volgende antwoord:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

Wanneer u https://jwt.ms gebruikt om het toegangstoken te onderzoeken dat is geretourneerd, ziet u iets dat lijkt op het volgende voorbeeld:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van tokens in Azure AD B2C](configure-tokens.md)
