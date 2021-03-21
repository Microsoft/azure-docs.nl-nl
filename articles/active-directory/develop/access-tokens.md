---
title: Toegangs tokens voor micro soft Identity platform | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over toegangs tokens die worden verzonden door de Azure AD v 1.0-en micro soft Identity platform (v 2.0)-eind punten.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 2/18/2021
ms.author: hirsin
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 8630dd2fb1157fbeba99f2a06d73712ab46a63f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035064"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Toegangs tokens van micro soft Identity platform

Met toegangs tokens kunnen clients beveiligde web-Api's veilig aanroepen en worden ze door Web-Api's gebruikt om verificatie en autorisatie uit te voeren. Met de OAuth-specificatie zijn toegangs tokens ondoorzichtige teken reeksen zonder een ingestelde indeling-sommige id-providers (id) gebruiken GUID'S, andere gebruiken versleutelde blobs. Het micro soft Identity-platform maakt gebruik van verschillende toegangs token indelingen, afhankelijk van de configuratie van de API waarmee het token wordt geaccepteerd. [Aangepaste api's die zijn geregistreerd door ontwikkel aars](quickstart-configure-app-expose-web-apis.md) op het micro soft Identity-platform kunnen kiezen uit twee verschillende indelingen van JSON-webtokens (JWTs), ' v1 ' en ' v2 ', en door micro soft ontwikkelde api's, zoals Microsoft Graph of Api's in azure, hebben extra eigen token indelingen. Deze eigen indelingen kunnen versleutelde tokens, JWTs of speciale JWT-achtige tokens zijn die niet worden gevalideerd.

Clients moeten toegangs tokens behandelen als ondoorzichtige teken reeksen, omdat de inhoud van het token alleen bedoeld is voor de bron (de API). Ontwikkel aars kunnen JWTs, *alleen* voor validatie en fout opsporing, decoderen met behulp van een site zoals [JWT.MS](https://jwt.ms). Houd er echter rekening mee dat de tokens die u voor een micro soft API ontvangt mogelijk niet altijd een JWT zijn en dat u ze niet altijd kunt decoderen.

Voor meer informatie over de inhoud van het toegangs token moeten clients de token antwoord gegevens gebruiken die worden geretourneerd met het toegangs token voor uw client. Als uw client een toegangs token aanvraagt, retourneert het micro soft Identity-platform ook meta gegevens over het toegangs token voor het verbruik van uw app. Deze informatie omvat de verloop tijd van het toegangs token en de scopes waarvoor deze geldig is. Met deze gegevens kan uw app intelligente caching van toegangs tokens uitvoeren zonder dat het toegangs token zelf moet worden geparseerd.

Raadpleeg de volgende secties voor meer informatie over hoe uw API de claims in een toegangs token kan valideren en gebruiken.  

> [!NOTE]
> Alle documentatie op deze pagina, tenzij anders vermeld, is alleen van toepassing op tokens die zijn uitgegeven voor Api's die u hebt geregistreerd.  Het is niet van toepassing op tokens die zijn uitgegeven voor Api's van micro soft en kunnen niet worden gebruikt om te valideren hoe het micro soft Identity-platform tokens uitgeeft voor een API die u maakt.  

## <a name="token-formats-and-ownership"></a>Token indelingen en eigendom

### <a name="v10-and-v20"></a>v 1.0 en v 2.0 

Er zijn twee versies van toegangs tokens beschikbaar in het micro soft Identity-platform: v 1.0 en v 2.0.  Deze versies bepalen welke claims zich in het token bevinden. Zo zorgt u ervoor dat een web-API kan bepalen hoe hun tokens eruitzien. Web-Api's hebben een van deze als standaard waarde geselecteerd tijdens registratie-v 1.0 voor Azure AD-Only apps en v 2.0 voor apps die consumenten accounts ondersteunen.  Dit kan worden bestuurd door toepassingen met behulp van de `accessTokenAcceptedVersion` instelling in het [app-manifest](reference-app-manifest.md#manifest-reference), waar `null` en als `1` resultaat in v 1.0-tokens, en `2` resulteert in v 2.0-tokens.

### <a name="what-app-is-a-token-for"></a>Welke app is een token voor?

Er zijn twee partijen betrokken bij een aanvraag voor een toegangs token: de client, die het token aanvraagt en de bron (de API) die het token accepteert wanneer de API wordt aangeroepen. De `aud` claim in een token geeft aan op welke bron het token is bedoeld (de *doel groep*). Clients gebruiken het token, maar moeten deze niet begrijpen of proberen te parseren. Resources accepteren het token.  

Het micro soft Identity-platform ondersteunt het uitgeven van een token versie vanuit elk versie-eind punt, ze zijn niet gerelateerd. Daarom ontvangt een bron instelling `accessTokenAcceptedVersion` om aan te geven `2` dat een client die het v 1.0-eind punt aanroept om een token op te halen voor die API een v 2.0-toegangs token zal ontvangen.  Resources zijn altijd eigenaar van hun tokens (die met hun `aud` claim) en zijn de enige toepassingen die hun token details kunnen wijzigen. Daarom is het wijzigen van de [optionele claims](active-directory-optional-claims.md) van het toegangs token voor uw *client* geen invloed op het toegangs token dat wordt ontvangen wanneer een token wordt aangevraagd voor `user.read` , dat eigendom is van de Microsoft Graph bron.

### <a name="sample-tokens"></a>Voorbeeld tokens

v 1.0 en v 2.0-tokens zien er ongeveer hetzelfde uit en bevatten veel van dezelfde claims. Hier vindt u een voor beeld van elk. Deze voorbeeld tokens worden niet [gevalideerd](#validating-tokens), omdat de sleutels voorafgaand aan de publicatie en persoonlijke gegevens worden verwijderd.

#### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

Bekijk dit v 1.0-token in [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd).

#### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

Bekijk dit v 2.0-token in [JWT.MS](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Claims in toegangs tokens

JWTs (JSON-webtokens) zijn onderverdeeld in drie delen:

* **Header** : bevat informatie over het [valideren van het token](#validating-tokens) , inclusief informatie over het type token en hoe deze is ondertekend.
* **Payload** : bevat alle belang rijke gegevens over de gebruiker of app die probeert uw service aan te roepen.
* **Hand tekening** : is de grond stof die wordt gebruikt om het token te valideren.

Elk onderdeel wordt gescheiden door een punt ( `.` ) en afzonderlijk base64-gecodeerd.

Claims zijn alleen aanwezig als er een waarde bestaat om deze op te vullen. Uw app mag geen afhankelijkheid hebben van een claim die aanwezig is. Voor beelden zijn onder meer `pwd_exp` (niet elke Tenant vereist dat wacht woorden verlopen) en `family_name` ([client referentie](v2-oauth2-client-creds-grant-flow.md) stromen bestaan uit namens toepassingen die geen namen hebben). De claims die worden gebruikt voor de validatie van het toegangs token, zijn altijd aanwezig.

Sommige claims worden gebruikt voor het beveiligen van Azure AD-tokens in het geval van hergebruik. Deze zijn gemarkeerd als niet als openbaar verbruik in de beschrijving als ' dekkend '. Deze claims kunnen al dan niet worden weer gegeven in een token en nieuwe kunnen zonder kennisgeving worden toegevoegd.

### <a name="header-claims"></a>Header claims

|Claim | Indeling | Beschrijving |
|--------|--------|-------------|
| `typ` | Teken reeks-altijd "JWT" | Geeft aan dat het token een JWT-waarde is.|
| `nonce` | Tekenreeks | Een unieke id die wordt gebruikt voor beveiliging tegen token replay-aanvallen. Uw resource kan deze waarde vastleggen om te beschermen tegen herhalingen. |
| `alg` | Tekenreeks | Hiermee wordt het algoritme aangegeven dat is gebruikt voor het ondertekenen van het token, bijvoorbeeld ' RS256 ' |
| `kid` | Tekenreeks | Hiermee geeft u de vinger afdruk voor de open bare sleutel die wordt gebruikt om dit token te ondertekenen. Verzonden in de toegangs tokens v 1.0 en v 2.0. |
| `x5t` | Tekenreeks | Werkt hetzelfde (in gebruik en waarde) als `kid` . `x5t` is een verouderde claim die alleen wordt verzonden in toegangs tokens v 1.0 voor compatibiliteits doeleinden. |

### <a name="payload-claims"></a>Nettolading claims

| Claim | Indeling | Beschrijving |
|-----|--------|-------------|
| `aud` | Teken reeks, een app-ID-URI of-GUID | Identificeert de beoogde ontvanger van het token.  Uw API moet deze waarde valideren en het token afwijzen als de waarde niet overeenkomt. In v 2.0-tokens is dit altijd de client-ID van de API, terwijl in de v 1.0-tokens de client-ID of de resource-URI in de aanvraag kan worden gebruikt, afhankelijk van hoe de client het token heeft aangevraagd.|
| `iss` | Teken reeks, een STS-URI | Identificeert de Security Token Service (STS) die het token bouwt en retourneert en de Azure AD-Tenant waarin de gebruiker is geverifieerd. Als het uitgegeven token een v 2.0-token is (Zie de `ver` claim), wordt de URI beëindigd in `/v2.0` . De GUID waarmee wordt aangegeven dat de gebruiker een consumenten gebruiker van een Microsoft-account is `9188040d-6c67-4c5b-b112-36a304b66dad` . Uw app kan gebruikmaken van het GUID-gedeelte van de claim om de set tenants te beperken die zich kunnen aanmelden bij de app, indien van toepassing. |
|`idp`| Teken reeks, meestal een STS-URI | Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. Deze waarde is gelijk aan de waarde van de verlener-claim tenzij het gebruikers account zich niet in dezelfde Tenant bevindt als de verlener-gasten, bijvoorbeeld. Als de claim niet aanwezig is, betekent dit dat de waarde van `iss` kan worden gebruikt.  Voor persoonlijke accounts die worden gebruikt in een organisatie context (bijvoorbeeld een persoonlijk account dat is uitgenodigd voor een Azure AD-Tenant), is de `idp` claim mogelijk ' Live.com ' of een STS-URI met de Microsoft-account Tenant `9188040d-6c67-4c5b-b112-36a304b66dad` . |
| `iat` | int, een UNIX-time stamp | ' Uitgegeven op ' geeft aan wanneer de authenticatie voor dit token is opgetreden. |
| `nbf` | int, een UNIX-time stamp | De claim ' NBF ' (niet vóór) identificeert de tijd waarna de JWT niet moet worden geaccepteerd voor verwerking. |
| `exp` | int, een UNIX-time stamp | De claim ' exp ' (verval tijd) identificeert de verval tijd op of waarna de JWT niet moet worden geaccepteerd voor verwerking. Het is belang rijk te weten dat een resource het token vóór deze tijd kan afwijzen, zoals wanneer een wijziging in de verificatie vereist is of een intrekking van het token is gedetecteerd. |
| `aio` | Dekkende teken reeks | Een interne claim die door Azure AD wordt gebruikt om gegevens te registreren voor het opnieuw gebruiken van tokens. Resources mogen deze claim niet gebruiken. |
| `acr` | Teken reeks, een ' 0 ' of ' 1 ' | Alleen aanwezig in v 1.0-tokens. Claim van de verificatie context klasse. De waarde ' 0 ' geeft aan dat de verificatie van de eind gebruiker niet voldoet aan de vereisten van ISO/IEC 29115. |
| `amr` | JSON-matrix met teken reeksen | Alleen aanwezig in v 1.0-tokens. Hiermee wordt aangegeven hoe het onderwerp van het token is geverifieerd. Zie [de sectie AMR claim](#the-amr-claim) voor meer informatie. |
| `appid` | Teken reeks, een GUID | Alleen aanwezig in v 1.0-tokens. De toepassings-ID van de client die het token gebruikt. De toepassing kan optreden als zichzelf of namens een gebruiker. De toepassings-ID vertegenwoordigt meestal een toepassings object, maar kan ook een Service Principal object in azure AD vertegenwoordigen. |
| `azp` | Teken reeks, een GUID | Alleen aanwezig in v 2.0-tokens, een vervanging voor `appid` . De toepassings-ID van de client die het token gebruikt. De toepassing kan optreden als zichzelf of namens een gebruiker. De toepassings-ID vertegenwoordigt meestal een toepassings object, maar kan ook een Service Principal object in azure AD vertegenwoordigen. |
| `appidacr` | "0", "1" of "2" | Alleen aanwezig in v 1.0-tokens. Hiermee wordt aangegeven hoe de client is geverifieerd. Voor een open bare client is de waarde 0. Als de client-ID en het client geheim worden gebruikt, is de waarde "1". Als er een client certificaat voor verificatie is gebruikt, is de waarde 2. |
| `azpacr` | "0", "1" of "2" | Alleen aanwezig in v 2.0-tokens, een vervanging voor `appidacr` . Hiermee wordt aangegeven hoe de client is geverifieerd. Voor een open bare client is de waarde 0. Als de client-ID en het client geheim worden gebruikt, is de waarde "1". Als er een client certificaat voor verificatie is gebruikt, is de waarde 2. |
| `preferred_username` | Tekenreeks | De primaire gebruikers naam die de gebruiker vertegenwoordigt. Dit kan een e-mail adres, telefoon nummer of een algemene gebruikers naam zijn zonder een opgegeven indeling. De waarde is onveranderbaar en kan in de loop van de tijd veranderen. Omdat de waarde is ververanderbaar, mag deze niet worden gebruikt om autorisatie beslissingen te nemen.  Het kan worden gebruikt voor hints voor gebruikers namen, maar ook in een gebruikers interface die door een gebruiker kan worden gelezen. Het `profile` bereik is vereist om deze claim te kunnen ontvangen. Alleen aanwezig in v 2.0-tokens. |
| `name` | Tekenreeks | Biedt een lees bare waarde waarmee het onderwerp van het token wordt geïdentificeerd. De waarde is niet gegarandeerd uniek, is onveranderbaar en is ontworpen om alleen te worden gebruikt voor weergave doeleinden. Het `profile` bereik is vereist om deze claim te kunnen ontvangen. |
| `scp` | Teken reeks, een door spaties gescheiden lijst met bereiken | De set bereiken die wordt weer gegeven door uw toepassing waarvoor de client toepassing toestemming heeft aangevraagd (en ontvangen). Uw app moet controleren of deze bereiken geldig zijn voor uw app en autorisatie beslissingen nemen op basis van de waarde van deze bereiken. Alleen opgenomen voor [gebruikers tokens](#user-and-application-tokens). |
| `roles` | Matrix van teken reeksen, een lijst met machtigingen | De set machtigingen die door uw toepassing worden weer gegeven en waarvoor de aanvraag of gebruiker toestemming heeft gegeven om deze aan te roepen. Voor [toepassings tokens](#user-and-application-tokens)wordt dit gebruikt tijdens de client referentie stroom ([v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), [v 2.0](v2-oauth2-client-creds-grant-flow.md)) in plaats van de gebruikers scopes.  Voor [gebruikers tokens](#user-and-application-tokens) wordt dit ingevuld met de rollen waaraan de gebruiker is toegewezen in de doel toepassing. |
| `wids` | Matrix van [RoleTemplateID](../roles/permissions-reference.md#all-roles) -guid's | Hiermee worden de rollen voor de Tenant opgegeven die aan deze gebruiker zijn toegewezen, in het gedeelte van de rollen in [Azure AD ingebouwde rollen](../roles/permissions-reference.md#all-roles).  Deze claim wordt per toepassing geconfigureerd op basis `groupMembershipClaims` van de eigenschap van het [toepassings manifest](reference-app-manifest.md).  Het instellen op ' all ' of ' DirectoryRole ' is vereist.  Mag niet aanwezig zijn in tokens die zijn verkregen via de impliciete stroom vanwege problemen met de token lengte. |
| `groups` | JSON-matrix met GUID'S | Bevat object-Id's die de groepslid maatschappen van het onderwerp vertegenwoordigen. Deze waarden zijn uniek (zie object-ID) en kunnen veilig worden gebruikt voor het beheren van toegang, zoals het afdwingen van autorisatie voor toegang tot een bron. De groepen die zijn opgenomen in de claim groepen, worden per toepassing geconfigureerd via de `groupMembershipClaims` eigenschap van het [toepassings manifest](reference-app-manifest.md). Een waarde van Null sluit alle groepen uit, de waarde ' beveiligings groep ' bevat alleen Active Directory beveiligings groepslid maatschappen en de waarde ' all ' bevat zowel beveiligings groepen als Microsoft 365 distributie lijsten. <br><br>Zie `hasgroups` onderstaande claim voor meer informatie over het gebruik van de `groups` claim met de impliciete toekenning. <br>Voor andere stromen geldt dat als het aantal groepen dat de gebruiker in de loop van een limiet is (150 voor SAML, 200 voor JWT), een overschrijding-claim wordt toegevoegd aan de claim bronnen die naar het Microsoft Graph-eind punt met de lijst met groepen voor de gebruiker verwijzen. |
| `hasgroups` | Booleaans | Indien aanwezig, `true` wordt het identificeren van de gebruiker altijd in ten minste één groep. Wordt gebruikt in plaats van de `groups` claim voor JWTs in impliciete toekennings stromen als de claim van de volledige groep het URI-fragment zou uitbreiden dat groter is dan de URL-lengte limieten (momenteel 6 of meer groepen). Geeft aan dat de client de Microsoft Graph-API moet gebruiken om de groepen van de gebruiker te bepalen `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` . |
| `groups:src1` | JSON-object | Voor token aanvragen die geen beperkte lengte hebben (Zie `hasgroups` hierboven), maar nog steeds te groot zijn voor het token, wordt een koppeling naar de lijst met volledige groepen voor de gebruiker opgenomen. Voor JWTs als een gedistribueerde claim voor SAML als een nieuwe claim in plaats van de `groups` claim. <br><br>**Voor beeld-JWT-waarde**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | Tekenreeks | De principal over welke het token informatie bedient, zoals de gebruiker van een app. Deze waarde is onveranderbaar en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om autorisatie controles veilig uit te voeren, zoals wanneer het token wordt gebruikt om toegang te krijgen tot een resource, en kan worden gebruikt als sleutel in database tabellen. Omdat het onderwerp altijd aanwezig is in de tokens die door Azure AD worden uitgegeven, raden we u aan deze waarde te gebruiken in een autorisatie systeem voor algemeen gebruik. Het onderwerp is echter een Pairwise-id en is uniek voor een bepaalde toepassings-ID. Als één gebruiker zich bij twee verschillende apps aanmeldt met twee verschillende client-Id's, ontvangen die apps daarom twee verschillende waarden voor de claim van de certificaat houder. Dit kan al dan niet gewenst zijn, afhankelijk van de vereisten van uw architectuur en privacy. Zie ook de `oid` claim (die hetzelfde blijft voor apps binnen een Tenant). |
| `oid` | Teken reeks, een GUID | De onveranderbare id voor de principal van de aanvraag-de gebruiker of Service-Principal waarvan de identiteit is geverifieerd.  In ID-tokens en app + gebruikers tokens is dit de object-ID van de gebruiker.  In alleen-app-tokens is dit de object-id van de aanroepende Service-Principal. Het kan ook worden gebruikt om autorisatie controles veilig en als sleutel in database tabellen uit te voeren. Deze ID is een unieke identificatie van de principal voor alle toepassingen. twee verschillende toepassingen die in dezelfde gebruiker worden ondertekend, ontvangen dezelfde waarde in de `oid` claim. Kan dus `oid` worden gebruikt bij het uitvoeren van query's naar micro soft onlineservices, zoals de Microsoft Graph. De Microsoft Graph retourneert deze ID als de `id` eigenschap voor een bepaald [gebruikers account](/graph/api/resources/user). Omdat de `oid` principals van meerdere apps mogen correleren, `profile` is de scope vereist om deze claim voor gebruikers te ontvangen. Houd er rekening mee dat als één gebruiker bestaat in meerdere tenants, de gebruiker een andere object-ID in elke Tenant bevat. deze worden beschouwd als verschillende accounts, zelfs als de gebruiker zich aanmeldt bij elke account met dezelfde referenties. |
| `tid` | Teken reeks, een GUID | Hiermee wordt de Azure AD-Tenant aangegeven waarvan de gebruiker zich bevindt. Voor werk-en school accounts is de GUID de onveranderlijke Tenant-ID van de organisatie waartoe de gebruiker behoort. De waarde is voor persoonlijke accounts `9188040d-6c67-4c5b-b112-36a304b66dad` . Het `profile` bereik is vereist om deze claim te kunnen ontvangen. |
| `unique_name` | Tekenreeks | Alleen aanwezig in v 1.0-tokens. Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. Deze waarde is niet gegarandeerd uniek binnen een Tenant en mag alleen worden gebruikt voor weergave doeleinden. |
| `uti` | Dekkende teken reeks | Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Resources mogen deze claim niet gebruiken. |
| `rh` | Dekkende teken reeks | Een interne claim die door Azure wordt gebruikt om tokens opnieuw te valideren. Resources mogen deze claim niet gebruiken. |
| `ver` | Teken reeks, ofwel `1.0` of `2.0` | Hiermee wordt de versie van het toegangs token aangegeven. |

**Claim van groepen overschrijding**

Om ervoor te zorgen dat de token grootte niet groter is dan de grootte limieten voor de HTTP-header, beperkt Azure AD het aantal object-Id's dat in de claim van de groep wordt opgenomen. Als een gebruiker lid is van meer groepen dan de limiet van overschrijding (150 voor SAML-tokens, 200 voor JWT-tokens en slechts 6 als deze is uitgegeven via de impliciete stroom), verzendt Azure AD de groeperings claim niet in het token. In plaats daarvan bevat het een overschrijding-claim in het token dat aangeeft dat de toepassing een query moet uitvoeren op de Microsoft Graph-API om het groepslid maatschap van de gebruiker op te halen.

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

U kunt de `BulkCreateGroups.ps1` gegeven in de map [scripts](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) voor het maken van apps gebruiken om overschrijding-scenario's te testen.

#### <a name="v10-basic-claims"></a>v 1.0 basis claims

De volgende claims worden opgenomen in v 1.0-tokens, indien van toepassing, maar zijn niet standaard opgenomen in de v 2.0-tokens. Als u gebruikmaakt van v 2.0 en een van deze claims nodig hebt, vraagt u deze aan met behulp van [optionele claims](active-directory-optional-claims.md).

| Claim | Indeling | Beschrijving |
|-----|--------|-------------|
| `ipaddr`| Tekenreeks | Het IP-adres waarmee de gebruiker is geverifieerd. |
| `onprem_sid`| Teken reeks, in [sid-indeling](/windows/desktop/SecAuthZ/sid-components) | In gevallen waarin de gebruiker een on-premises verificatie heeft, levert deze claim de SID. U kunt gebruiken `onprem_sid` voor autorisatie in oudere toepassingen.|
| `pwd_exp`| int, een UNIX-time stamp | Hiermee wordt aangegeven wanneer het wacht woord van de gebruiker verloopt. |
| `pwd_url`| Tekenreeks | Een URL waar gebruikers kunnen worden verzonden om hun wacht woord opnieuw in te stellen. |
| `in_corp`| booleaans | Geeft aan of de client zich aanmeldt vanuit het bedrijfs netwerk. Als dat niet het geval is, is de claim niet opgenomen. |
| `nickname`| Tekenreeks | Een extra naam voor de gebruiker, gescheiden van de voor-en achternaam.|
| `family_name` | Tekenreeks | Hiermee geeft u de achternaam, de achternaam of de familynaam van de gebruiker op zoals gedefinieerd in het gebruikers object. |
| `given_name` | Tekenreeks | Hiermee wordt de eerste of de voor naam van de gebruiker opgegeven, zoals ingesteld op het gebruikers object. |
| `upn` | Tekenreeks | De gebruikers naam van de gebruiker. Dit kan een telefoon nummer, e-mail adres of niet-opgemaakte teken reeks zijn. Moet alleen worden gebruikt voor weergave doeleinden en het bieden van gebruikers naam hints in scenario's voor herauthenticatie. |

#### <a name="the-amr-claim"></a>De `amr` claim

Micro soft-identiteiten kunnen op verschillende manieren worden geverifieerd. Dit kan relevant zijn voor uw toepassing. De `amr` claim is een matrix die meerdere items kan bevatten, zoals `["mfa", "rsa", "pwd"]` , voor een verificatie waarbij zowel een wacht woord als de verificator-app wordt gebruikt.

| Waarde | Beschrijving |
|-----|-------------|
| `pwd` | Wachtwoord verificatie: het micro soft-wacht woord van een gebruiker of het client geheim van de app. |
| `rsa` | Verificatie is gebaseerd op het bewijs van een RSA-sleutel, bijvoorbeeld met de [app Microsoft Authenticator](https://aka.ms/AA2kvvu). Dit geldt ook als verificatie is uitgevoerd door een zelfondertekende JWT met een x509-certificaat van een service. |
| `otp` | Eenmalige wachtwoord code met een e-mail of een tekst bericht. |
| `fed` | Er is een Federated Authentication-bevestiging (zoals JWT of SAML) gebruikt. |
| `wia` | Geïntegreerde Windows-verificatie |
| `mfa` | [Multi-factor Authentication](../authentication/concept-mfa-howitworks.md) is gebruikt. Als dit het geval is, worden ook de andere verificatie methoden opgenomen. |
| `ngcmfa` | Gelijk aan `mfa` , gebruikt voor het inrichten van bepaalde geavanceerde referentie typen. |
| `wiaormfa`| De gebruiker heeft Windows of een MFA-referentie gebruikt voor verificatie. |
| `none` | Er is geen verificatie uitgevoerd. |

## <a name="validating-tokens"></a>Tokens valideren

Niet alle apps moeten tokens valideren. In specifieke scenario's moeten apps een token valideren:

* [Web-api's](quickstart-configure-app-expose-web-apis.md) moeten de door een client verzonden toegangs tokens valideren.  Ze mogen alleen tokens accepteren die hun `aud` claim bevatten.
* Vertrouwelijke web-apps zoals ASP.NET Core moeten ID-tokens valideren die naar hen worden verzonden via de browser van de gebruiker in de hybride stroom, voordat toegang tot de gegevens van een gebruiker wordt toegestaan of een sessie tot stand wordt gebracht.

Als geen van de bovenstaande scenario's van toepassing is, kan uw toepassing niet profiteren van het valideren van het token en kan dit een beveiligings-en betrouwbaarheids risico opleveren als er beslissingen worden genomen op basis van de geldigheid van het token.  Open bare clients, zoals systeem eigen apps of SPAs, profiteren niet van het valideren van tokens: de app communiceert rechtstreeks met de IDP, dus SSL-beveiliging zorgt ervoor dat de tokens geldig zijn.

Api's en web apps moeten alleen tokens valideren die een `aud` claim hebben die overeenkomen met hun toepassing; andere resources kunnen aangepaste token validatie regels hebben. Tokens voor Microsoft Graph worden bijvoorbeeld niet volgens deze regels gevalideerd vanwege hun eigen indeling. Het valideren en accepteren van tokens die zijn bedoeld voor een andere resource is een voor beeld van het probleem met [verwarde adjunct](https://cwe.mitre.org/data/definitions/441.html) .

Als uw toepassing een id_token of een access_token moet valideren volgens het bovenstaande, moet uw app eerst de hand tekening van het token valideren en de uitgever van de waarden in het detectie document voor OpenID Connect. De Tenant-onafhankelijke versie van het document bevindt zich bijvoorbeeld in [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

De volgende informatie wordt verstrekt voor degenen die het onderliggende proces willen begrijpen. De Azure AD-middleware heeft ingebouwde mogelijkheden voor het valideren van toegangs tokens, en u kunt bladeren door onze voor [beelden](sample-v2-code.md) om er een te vinden in de taal van uw keuze. Er zijn ook verschillende open source-bibliotheken van derden beschikbaar voor JWT-validatie-er is ten minste één optie voor vrijwel elk platform en elke taal. Zie voor meer informatie over Azure AD-verificatie bibliotheken en code voorbeelden de [verificatie bibliotheken](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>De hand tekening valideren

Een JWT bevat drie segmenten, gescheiden door het `.` teken. Het eerste segment wordt de **kop**, het tweede als de **hoofd tekst** en de derde als de **hand tekening** genoemd. Het handtekening segment kan worden gebruikt om de echtheid van het token te valideren, zodat het kan worden vertrouwd door uw app.

Tokens die zijn uitgegeven door Azure AD, worden ondertekend met behulp van standaard algoritmen voor asymmetrische versleuteling van de industrie, zoals RS256. De kop van de JWT bevat informatie over de sleutel en versleutelings methode die wordt gebruikt om het token te ondertekenen:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

De `alg` claim geeft het algoritme aan dat is gebruikt voor het ondertekenen van het token, terwijl de `kid` claim de specifieke open bare sleutel aangeeft die is gebruikt om het token te valideren.

Op elk gewenst moment kan Azure AD een id_token ondertekenen met behulp van een van een of meer combi Naties van open bare en persoonlijke sleutels. Azure AD roteert de mogelijke set sleutels periodiek. Daarom moet uw app worden geschreven om deze sleutel wijzigingen automatisch af te handelen. Een redelijke frequentie om te controleren op updates voor de open bare sleutels die door Azure AD worden gebruikt, is om de 24 uur.

U kunt de gegevens van de handtekening sleutel verkrijgen die nodig zijn om de hand tekening te valideren met behulp van het [OpenID Connect Connect meta data-document](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) op de volgende locatie:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> Probeer deze [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) in een browser.

Dit meta gegevens document:

* Is een JSON-object dat verschillende nuttige informatie bevat, zoals de locatie van de verschillende eind punten die vereist zijn voor het uitvoeren van OpenID Connect Connect-verificatie.
* Bevat een `jwks_uri` , waarmee de locatie wordt opgegeven van de set open bare sleutels die worden gebruikt voor het ondertekenen van tokens. De JSON-websleutel (JWK) die zich bevindt `jwks_uri` in de bevat alle informatie over de open bare sleutel die op dat moment in gebruik is.  De JWK-indeling wordt beschreven in [RFC 7517](https://tools.ietf.org/html/rfc7517).  Uw app kan de `kid` claim in de JWT-header gebruiken om te selecteren welke open bare sleutel in dit document is gebruikt voor het ondertekenen van een bepaald token. Vervolgens kan de hand tekening worden gevalideerd met de juiste open bare sleutel en het aangegeven algoritme.

> [!NOTE]
> Het is raadzaam `kid` om de claim te gebruiken om uw token te valideren. Hoewel v 1.0-tokens zowel de `x5t` als `kid` -claims bevatten, bevatten de v 2.0-tokens alleen de `kid` claim.

Het uitvoeren van de handtekening validatie valt buiten het bereik van dit document. er zijn veel open-source bibliotheken beschikbaar om u zo nodig te helpen.  Het micro soft Identity-platform heeft echter een uitbrei ding voor token-ondertekening van de standaard-aangepaste handtekening sleutels.

Als uw app aangepaste handtekening sleutels heeft als gevolg van het gebruik van de functie voor het [toewijzen van claims](active-directory-claims-mapping.md) , moet u een `appid` query parameter met de app-id toevoegen om een `jwks_uri` verwijzing naar de informatie van de handtekening sleutel van uw app te krijgen. deze moet worden gebruikt voor de validatie. Bijvoorbeeld: `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` bevat een `jwks_uri` van `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` .

### <a name="claims-based-authorization"></a>Autorisatie op basis van claims

Met de bedrijfs logica van uw toepassing wordt deze stap gedicteerd. sommige algemene autorisatie methoden worden hieronder beschreven.

* Controleer de `scp` of- `roles` claim om te controleren of alle huidige bereiken overeenkomen met die van uw API, en laat de client de aangevraagde actie uitvoeren.
* Zorg ervoor dat de aanroepende client uw API mag aanroepen met behulp van de `appid` claim.
* De verificatie status van de aanroepende client valideren met `appidacr` -deze mag niet 0 zijn als open bare clients uw API niet mogen aanroepen.
* Controleer op een lijst met achterstallige `nonce` claims om te controleren of het token niet opnieuw is afgespeeld.
* Controleer of het `tid` overeenkomt met een Tenant die uw API mag aanroepen.
* Gebruik de `amr` claim om te controleren of de gebruiker MFA heeft uitgevoerd. Dit moet worden afgedwongen met behulp van [voorwaardelijke toegang](../conditional-access/overview.md).
* Als u de `roles` of `groups` claims in het toegangs token hebt aangevraagd, controleert u of de gebruiker zich in de groep bevindt die deze actie mag uitvoeren.
  * Voor tokens die zijn opgehaald met behulp van de impliciete stroom moet u waarschijnlijk de [Microsoft Graph](https://developer.microsoft.com/graph/) voor deze gegevens opvragen, omdat het vaak te groot is voor het token.

## <a name="user-and-application-tokens"></a>Tokens van gebruikers en toepassingen

Uw toepassing kan tokens ontvangen voor de gebruiker (de stroom die gewoonlijk wordt besproken) of rechtstreeks vanuit een toepassing (via de [client referentie stroom](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)). Deze app-tokens geven aan dat deze aanroep afkomstig is van een toepassing en geen back-up van de-gebruiker heeft. Deze tokens worden grotendeels hetzelfde behandeld:

* Gebruik `roles` om machtigingen weer te geven die zijn verleend aan het onderwerp van het token.
* Gebruik `oid` of `sub` om te controleren of de aanroepende Service-Principal de verwachte is.

Als uw app onderscheid moet maken tussen alleen app-toegangs tokens en toegangs tokens voor gebruikers, gebruikt u de `idtyp` [optionele claim](active-directory-optional-claims.md).  Door de claim toe te voegen `idtyp` aan het `accessToken` veld en de waarde te controleren `app` , kunt u alleen app-toegangs tokens detecteren.  ID-tokens en toegangs tokens voor gebruikers worden niet opgenomen in de `idtyp` claim.

## <a name="token-revocation"></a>Token intrekking

Vernieuwings tokens kunnen op elk gewenst moment ongeldig worden gemaakt of ingetrokken om verschillende redenen. Deze zijn onderverdeeld in twee hoofd categorieën: time-outs en intrekken.

### <a name="token-timeouts"></a>Token-time-outs

Met de configuratie van de [levens duur van tokens](active-directory-configurable-token-lifetimes.md)kan de levens duur van vernieuwings tokens worden gewijzigd.  Het is normaal en verwacht dat sommige tokens zonder gebruik worden uitgevoerd (bijvoorbeeld de gebruiker heeft de app drie maanden niet geopend) en verloopt daarom.  Apps zullen scenario's ondervinden waarbij de aanmeldings server een vernieuwings token weigert als gevolg van de leeftijd.

* MaxInactiveTime: als het vernieuwings token niet is gebruikt binnen de tijd die is bepaald door de MaxInactiveTime, is het vernieuwings token niet langer geldig.
* MaxSessionAge: als MaxAgeSessionMultiFactor of MaxAgeSessionSingleFactor is ingesteld op een andere waarde dan de standaard instelling (tot-ingetrokken), is de verificatie vereist na de tijd die is ingesteld in de MaxAgeSession * verstreken.
* Voorbeelden:
  * De Tenant heeft een MaxInactiveTime van vijf dagen en de gebruiker heeft gedurende een week op vakantie gezet, waarna Azure AD gedurende 7 dagen geen nieuwe token aanvraag van de gebruiker heeft gezien. De volgende keer dat de gebruiker een nieuw token aanvraagt, wordt het vernieuwings token ingetrokken en moeten ze hun referenties opnieuw invoeren.
  * Een gevoelige toepassing heeft een MaxAgeSessionSingleFactor van één dag. Als een gebruiker zich op maandag aanmeldt en op dinsdag (nadat 25 uur is verstreken), moeten ze opnieuw worden geverifieerd.

### <a name="revocation"></a>Intrekkings

Het vernieuwen van tokens kan door de server worden ingetrokken vanwege een wijziging in de referenties of door het gebruik of de beheerder.  Vernieuwings tokens kunnen worden onderverdeeld in twee klassen, die zijn uitgegeven aan vertrouwelijke clients (de meest rechtse kolom) en die worden verleend aan open bare clients (alle andere kolommen).

| Wijziging | Cookie op basis van wacht woorden | Token op basis van wacht woorden | Cookie op basis van niet-wacht woord | Niet-op wacht woord gebaseerde token | Vertrouwelijk client token |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Wacht woord verloopt | Blijft actief | Blijft actief | Blijft actief | Blijft actief | Blijft actief |
| Het wacht woord is gewijzigd door de gebruiker | Revoked | Revoked | Blijft actief | Blijft actief | Blijft actief |
| Gebruiker heeft SSPR | Revoked | Revoked | Blijft actief | Blijft actief | Blijft actief |
| Beheerder wacht woord opnieuw instellen | Revoked | Revoked | Blijft actief | Blijft actief | Blijft actief |
| Gebruiker trekt de vernieuwings tokens in [via Power shell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revoked | Revoked | Revoked | Revoked | Revoked |
| De beheerder trekt alle vernieuwings tokens voor een gebruiker in [via Power shell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revoked | Revoked |Revoked | Revoked | Revoked |
| Eenmalige afmelding ([v 1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out), [v 2.0](v2-protocols-oidc.md#single-sign-out) ) op Internet | Revoked | Blijft actief | Revoked | Blijft actief | Blijft actief |

#### <a name="non-password-based"></a>Niet op wacht woord gebaseerd

Een aanmelding *zonder wacht woord* is een aanmeldings locatie waar de gebruiker geen wacht woord heeft getypt om deze op te halen. Voor beelden van aanmelding op basis van een wacht woord zijn:

- Uw gezicht gebruiken met Windows hello
- FIDO2-sleutel
- Sms
- Spraak
- PIN

Bekijk de [primaire vernieuwings tokens](../devices/concept-primary-refresh-token.md) voor meer informatie over primaire vernieuwings tokens.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [ `id_tokens` Azure AD](id-tokens.md).
* Meer informatie over machtigingen en toestemming ( [v 1.0](../azuread-dev/v1-permissions-consent.md), [v 2.0](v2-permissions-and-consent.md)).
