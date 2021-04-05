---
title: User info-eind punt voor micro soft Identity platform | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het user info-eind punt op het micro soft Identity-platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752727"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>User info-eind punt voor micro soft Identity platform

Het user info-eind punt maakt deel uit van de [OpenID Connect Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC), ontworpen om claims te retour neren over de gebruiker die is geverifieerd.  Voor het micro soft-identiteits platform wordt het user info-eind punt gehost op Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>Zoek het. well-bekende configuratie-eind punt

U kunt het user info-eind punt met behulp van het OpenID Connect Connect Discovery-document op een programmatische manier detecteren `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . Deze wordt weer gegeven in het `userinfo_endpoint` veld en dit patroon kan worden gebruikt in verschillende Clouds om het juiste eind punt aan te wijzen.  Het is niet raadzaam het user info-eind punt in uw app op te nemen. gebruik in plaats daarvan het OIDC Discovery-document om dit eind punt tijdens runtime te vinden.

Als onderdeel van de OpenID Connect Connect-specificatie wordt het user info-eind punt vaak automatisch aangeroepen door [OIDC compatibele bibliotheken](https://openid.net/developers/certified/)  om informatie over de gebruiker te verkrijgen.  Zonder dat dit eind punt wordt gehost, is het micro soft Identity-platform niet compatibel met de standaarden en kunnen sommige tape wisselaars mislukken.  In de [lijst met claims die zijn geïdentificeerd in de OIDC-standaard](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) , produceren we de naam claims, de onderwerps claim en de e-mail wanneer deze beschikbaar zijn en worden verzonden voor.  

## <a name="consider-use-an-id-token-instead"></a>Overwegen: gebruik in plaats daarvan een ID-token

De informatie die beschikbaar is in het ID-token dat uw app kan ontvangen, is een superset van de informatie die kan worden opgehaald uit het user info-eind punt.  Omdat u tegelijkertijd een ID-token kunt ophalen om het user info-eind punt aan te roepen, wordt u aangeraden dat ID-token te gebruiken voor het ophalen van informatie over de gebruiker in plaats van het user info-eind punt aan te roepen.  Het gebruik van het ID-token elimineert één tot twee netwerk aanvragen van uw toepassing starten, waardoor de latentie in uw toepassing wordt verminderd.

Als u meer informatie over de gebruiker nodig hebt, moet u de [Microsoft Graph- `/user` API](/graph/api/user-get) aanroepen om gegevens op te halen, zoals een Office-nummer of-functie.   U kunt ook [optionele claims](active-directory-optional-claims.md) gebruiken om aanvullende gebruikers gegevens op te geven in uw id-en toegangs tokens.

## <a name="calling-the-userinfo-endpoint"></a>Het user info-eind punt aanroepen

User info is een standaard OAuth Bearer-token-API, die net als andere Microsoft Graph-API wordt aangeroepen met behulp van het toegangs token dat wordt ontvangen bij het ophalen van een token voor Microsoft Graph. Het retourneert een JSON-antwoord met claims over de gebruiker.

### <a name="permissions"></a>Machtigingen

Gebruik de volgende [OIDC-machtigingen](v2-permissions-and-consent.md#openid-connect-scopes) om de user info-API aan te roepen. `openid` is vereist en de `profile` en- `email` bereiken zorgen ervoor dat er aanvullende informatie wordt verstrekt in het antwoord.

|Machtigings type      | Machtigingen    |
|:--------------------|:---------------------------------------------------------|
|Gedelegeerd (werk-of school account) | OpenID Connect (vereist), profiel, e-mail |
|Gedelegeerd (persoonlijk Microsoft-account) | OpenID Connect (vereist), profiel, e-mail |
|Toepassing | Niet van toepassing |

> [!TIP]
> Kopieer deze URL in uw browser om een token op te halen voor het user info-eind punt en een [id-token](id-tokens.md) en vervang de client-id en omleidings-URI door uw eigen. Houd er rekening mee dat het alleen scopes voor OpenID Connect-of graph-bereiken en niets anders opvraagt.  Dit is vereist omdat u geen machtigingen voor twee verschillende resources in dezelfde token aanvraag kunt aanvragen.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> U kunt dit toegangs token gebruiken in de volgende sectie.

Net als bij andere Microsoft Graph tokens is het token dat u hier ontvangt mogelijk geen JWT. Als u bent aangemeld bij een Microsoft-account gebruiker, wordt dit een versleutelde token-indeling. Dit komt omdat Microsoft Graph een speciaal patroon voor token uitgifte heeft. Dit heeft geen invloed op de mogelijkheid om het toegangs token te gebruiken om het user info-eind punt aan te roepen.

### <a name="calling-the-api"></a>De API aanroepen

De user info-API ondersteunt zowel GET als POST, per OIDC spec.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>User info-antwoord

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

De claims die hier worden weer gegeven, zijn alle claims die het user info-eind punt kan retour neren.  Dit zijn dezelfde waarden die in de app worden weer gegeven in het [id-token](id-tokens.md) dat is uitgegeven voor de app.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>Opmerkingen en aanvullende informatie over het user info-eind punt

* Als u dit user info-eind punt wilt aanroepen, moet u het v 2.0-eind punt gebruiken.  Als u het v 1.0-eind punt gebruikt, ontvangt u een token voor het v 1.0 user info-eind punt dat wordt gehost op login.microsoftonline.com.  We raden aan dat alle OIDC-compatibele apps en bibliotheken het v 2.0-eind punt gebruiken om compatibiliteit te garanderen.
* Het antwoord van het user info-eind punt kan niet worden aangepast.  Als u claims wilt aanpassen, gebruikt u [claim toewijzing]( active-directory-claims-mapping.md) om de informatie te bewerken die in de tokens is geretourneerd.
* Het antwoord van het user info-eind punt kan niet worden toegevoegd aan.  Als u meer claims over de gebruiker wilt ontvangen, gebruikt u [optionele claims]( active-directory-optional-claims.md) om nieuwe claims toe te voegen aan de tokens.

## <a name="next-steps"></a>Volgende stappen

* [De inhoud van ID-tokens controleren](id-tokens.md)
* [De inhoud van een ID-token aanpassen met behulp van optionele claims](active-directory-optional-claims.md)
* [Een toegangs token en ID-token aanvragen met het OAuth2-Protocol](v2-protocols-oidc.md)
