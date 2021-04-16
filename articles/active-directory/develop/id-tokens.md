---
title: Id-tokens voor Microsoft-identiteitsplatform | Azure
titleSuffix: Microsoft identity platform
description: Informatie over het gebruik id_tokens door de Azure AD v1.0- en Microsoft Identity Platform (v2.0)-eindpunten.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: 885379a02c8866f2829fb681683a93b1d8d314fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530026"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Id-tokens voor Microsoft Identity Platform

`id_tokens` worden verzonden naar de clienttoepassing als onderdeel van [OpenID Connect](v2-protocols-oidc.md) stroom (OIDC). Ze kunnen worden verzonden naast of in plaats van een toegangs token en worden gebruikt door de client om de gebruiker te verifiëren.

## <a name="using-the-id_token"></a>De id_token

Id-tokens moeten worden gebruikt om te valideren dat een gebruiker is wie hij of zij claimt te zijn en aanvullende nuttige informatie over deze gebruiker te verkrijgen. Deze mag niet worden gebruikt voor autorisatie in plaats van een [toegangstoken](access-tokens.md). De claims die worden verstrekt, kunnen worden gebruikt voor UX in uw toepassing, als sleutels in een [database](#using-claims-to-reliably-identify-a-user-subject-and-object-id)en voor toegang tot de clienttoepassing.  

## <a name="claims-in-an-id_token"></a>Claims in een id_token

`id_tokens` zijn [JWT's](https://tools.ietf.org/html/rfc7519) (JSON-webtokens), wat betekent dat ze bestaan uit een header, payload en handtekeninggedeelte. U kunt de header en handtekening gebruiken om de echtheid van het token te controleren, terwijl de nettolading de informatie bevat over de gebruiker die is aangevraagd door uw client. Behalve waar vermeld, worden alle hier vermelde JWT-claims weergegeven in zowel v1.0- als v2.0-tokens.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Bekijk dit v1.0-voorbeeld-token in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Bekijk dit v2.0-voorbeeld token in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Headerclaims

|Claim | Indeling | Beschrijving |
|-----|--------|-------------|
|`typ` | Tekenreeks - altijd 'JWT' | Geeft aan dat het token een JWT-token is.|
|`alg` | Tekenreeks | Geeft het algoritme aan dat is gebruikt om het token te ondertekenen. Voorbeeld: RS256 |
|`kid` | Tekenreeks | Vingerafdruk voor de openbare sleutel die wordt gebruikt om dit token te verifiëren. Wordt zowel in v1.0 als v2.0 `id_tokens` uitgezonden. |
|`x5t` | Tekenreeks | Hetzelfde (in gebruik en waarde) als `kid` . Dit is echter een verouderde claim die alleen wordt ingediend in v1.0 voor `id_tokens` compatibiliteitsdoeleinden. |

### <a name="payload-claims"></a>Nettoladingclaims

Deze lijst bevat de JWT-claims die zich in de meeste id_tokens standaard (behalve indien vermeld).  Uw app kan echter optionele [claims gebruiken om](active-directory-optional-claims.md) aanvullende JWT-claims aan te vragen in de id_token.  Deze kunnen variëren van `groups` de claim tot informatie over de naam van de gebruiker.

|Claim | Indeling | Beschrijving |
|-----|--------|-------------|
|`aud` |  Tekenreeks, een app-id-URI | Identificeert de beoogde ontvanger van het token. In is de doelgroep de toepassings-id van uw `id_tokens` app, die is toegewezen aan uw app in de Azure Portal. Uw app moet deze waarde valideren en het token afwijzen als de waarde niet overeen komt. |
|`iss` |  Tekenreeks, een STS-URI | Identificeert de beveiliging tokenservice (STS) die het token maakt en retourneert, en de Azure AD-tenant waarin de gebruiker is geverifieerd. Als het token is uitgegeven door het v2.0-eindpunt, eindigt de URI op `/v2.0` .  De GUID die aangeeft dat de gebruiker een consumentgebruiker van een Microsoft-account `9188040d-6c67-4c5b-b112-36a304b66dad` is. Uw app moet het GUID-gedeelte van de claim gebruiken om de set tenants te beperken die zich kunnen aanmelden bij de app, indien van toepassing. |
|`iat` |  int, een UNIX-tijdstempel | 'Uitgegeven om' geeft aan wanneer de verificatie voor dit token heeft plaatsgevonden.  |
|`idp`|Tekenreeks, meestal een STS-URI | Registreert de identiteitsprovider waarmee het onderwerp van het token is geverifieerd. Deze waarde is identiek aan de waarde van de claim vergever, tenzij het gebruikersaccount zich niet in dezelfde tenant als de vergever - bijvoorbeeld gasten. Als de claim niet aanwezig is, betekent dit dat de waarde `iss` van in plaats daarvan kan worden gebruikt.  Voor persoonlijke accounts die worden gebruikt in een organisatiecontext (bijvoorbeeld een persoonlijk account dat is uitgenodigd voor een Azure AD-tenant), kan de `idp` claim 'live.com' zijn of een STS-URI met de Microsoft-account-tenant. `9188040d-6c67-4c5b-b112-36a304b66dad` |
|`nbf` |  int, een UNIX-tijdstempel | De "nbf" (niet vóór) claim identificeert de tijd voordat de JWT MAG NIET worden geaccepteerd voor verwerking.|
|`exp` |  int, een UNIX-tijdstempel | De claim exp (vervaltijd) identificeert de vervaltijd op of waarna de JWT NIET mag worden geaccepteerd voor verwerking.  Het is belangrijk te weten dat een resource het token vóór deze tijd ook kan afwijzen, bijvoorbeeld als er een wijziging in de verificatie is vereist of een token intrekken is gedetecteerd. |
| `c_hash`| Tekenreeks |De code-hash is alleen opgenomen in id-tokens wanneer het id-token is uitgegeven met een OAuth 2.0-autorisatiecode. Het kan worden gebruikt om de echtheid van een autorisatiecode te valideren. Zie voor meer informatie over het uitvoeren van deze [validatie](https://openid.net/specs/openid-connect-core-1_0.html)de OpenID Connect specificatie . |
|`at_hash`| Tekenreeks |De hash van het toegangstoken is alleen opgenomen in id-tokens wanneer het id-token is uitgegeven vanaf het eindpunt met een `/authorize` OAuth 2.0-toegangstoken. Het kan worden gebruikt om de echtheid van een toegangs token te valideren. Zie voor meer informatie over het uitvoeren van deze [validatie](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken)de OpenID Connect specificatie . Dit wordt niet geretourneerd op id-tokens van `/token` het eindpunt. |
|`aio` | Ondoorzichtige tekenreeks | Een interne claim die door Azure AD wordt gebruikt om gegevens vast te stellen voor hergebruik van token. Moet worden genegeerd.|
|`preferred_username` | Tekenreeks | De primaire gebruikersnaam die de gebruiker vertegenwoordigt. Dit kan een e-mailadres, telefoonnummer of een algemene gebruikersnaam zijn zonder een opgegeven indeling. De waarde is veranderlijk en kan na een periode veranderen. Omdat deze veranderlijk is, moet deze waarde niet worden gebruikt om autorisatiebeslissingen te nemen. Het `profile` bereik is vereist om deze claim te ontvangen.|
|`email` | Tekenreeks | De `email` claim is standaard aanwezig voor gastaccounts die een e-mailadres hebben.  Uw app kan de e-mailclaim aanvragen voor beheerde gebruikers (die afkomstig zijn van dezelfde tenant als de resource) met behulp van de `email` [optionele claim](active-directory-optional-claims.md).  Op het v2.0-eindpunt kan uw app ook het OpenID Connect-bereik aanvragen. U hoeft niet zowel de optionele claim als het bereik aan te vragen om de claim op te `email` halen.  De e-mailclaim ondersteunt alleen adresseerbare e-mail uit de profielgegevens van de gebruiker. |
|`name` | Tekenreeks | De `name` claim biedt een door mensen leesbare waarde die het onderwerp van het token identificeert. De waarde is niet gegarandeerd uniek, maar veranderlijk en is ontworpen om alleen te worden gebruikt voor weergavedoeleinden. Het `profile` bereik is vereist om deze claim te ontvangen. |
|`nonce`| Tekenreeks | De nonce komt overeen met de parameter die is opgenomen in de oorspronkelijke /autoriseringsaanvraag voor de IDP. Als deze niet overeenkomen, moet uw toepassing het token afwijzen. |
|`oid` | Tekenreeks, een GUID | De onveranderbare id voor een object in het Microsoft-identiteitssysteem, in dit geval een gebruikersaccount. Deze id is een unieke identificatie van de gebruiker in toepassingen: twee verschillende toepassingen die zich aanmelden bij dezelfde gebruiker, ontvangen dezelfde waarde in de `oid` claim. De Microsoft Graph retourneren deze id als `id` de eigenschap voor een bepaald gebruikersaccount. Omdat met `oid` meerdere apps gebruikers kunnen worden gecorreleerd, is het bereik vereist om deze claim te `profile` ontvangen. Houd er rekening mee dat als één gebruiker in meerdere tenants bestaat, de gebruiker een andere object-id in elke tenant bevat. Ze worden beschouwd als verschillende accounts, zelfs als de gebruiker zich met dezelfde referenties bij elk account aanmeldt. De `oid` claim is een GUID en kan niet opnieuw worden gebruikt. |
|`roles`| Matrix van tekenreeksen | De set rollen die zijn toegewezen aan de gebruiker die zich wil aanmelden. |
|`rh` | Ondoorzichtige tekenreeks |Een interne claim die door Azure wordt gebruikt om tokens opnieuw tevalideren. Moet worden genegeerd. |
|`sub` | Tekenreeks | De principal waarover het token informatie bevestigt, zoals de gebruiker van een app. Deze waarde is onveranderbaar en kan niet opnieuw worden toegewezen of hergebruikt. Het onderwerp is een pairwise-id: deze is uniek voor een bepaalde toepassings-id. Als één gebruiker zich met twee verschillende client-ID's bij twee verschillende apps meldt, ontvangen deze apps twee verschillende waarden voor de onderwerpclaim. Dit kan al dan niet worden gezocht, afhankelijk van uw architectuur en privacyvereisten. |
|`tid` | Tekenreeks, een GUID | Een GUID die de Azure AD-tenant vertegenwoordigt waar de gebruiker vandaan komt. Voor werk- en schoolaccounts is de GUID de onveranderbare tenant-id van de organisatie waar de gebruiker bij hoort. Voor persoonlijke accounts is de waarde `9188040d-6c67-4c5b-b112-36a304b66dad` . Het `profile` bereik is vereist om deze claim te ontvangen. |
|`unique_name` | Tekenreeks | Biedt een voor mensen leesbare waarde waarmee het onderwerp van het token wordt geïdentificeerd. Deze waarde is op een bepaald moment uniek, maar omdat e-mailberichten en andere id's opnieuw kunnen worden gebruikt, kan deze waarde opnieuw worden weergegeven voor andere accounts en mag deze daarom alleen worden gebruikt voor weergavedoeleinden. Alleen uitgegeven in v1.0 `id_tokens` . |
|`uti` | Ondoorzichtige tekenreeks | Een interne claim die door Azure wordt gebruikt om tokens opnieuw tevalideren. Moet worden genegeerd. |
|`ver` | Tekenreeks, 1.0 of 2.0 | Geeft de versie van de id_token. |
|`hasgroups`|Booleaans|Als de gebruiker aanwezig is, altijd waar, maakt de gebruiker er ten minste één groep van. Wordt gebruikt in plaats van de groepenclaim voor JWT's in impliciete toekenningsstromen als de claim voor volledige groepen het URI-fragment zou uitbreiden tot buiten de URL-lengtelimieten (momenteel 6 of meer groepen). Geeft aan dat de client de api Microsoft Graph gebruiken om de gebruikersgroepen () te `https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects` bepalen.|
|`groups:src1`|JSON-object | Voor tokenaanvragen die niet beperkt zijn (zie hierboven), maar die nog steeds te groot zijn voor het token, wordt een koppeling naar de volledige lijst met groepen voor de `hasgroups` gebruiker opgenomen. Voor JWT's als een gedistribueerde claim, voor SAML als een nieuwe claim in plaats van de `groups` claim. <br><br>**Voorbeeld van JWT-waarde:** <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Zie Claim voor uitval van [groepen voor meer informatie.](#groups-overage-claim)|

> [!NOTE]
> De v1.0- en v2.0-id_token verschillen in de hoeveelheid informatie die ze bevatten, zoals te zien is in de bovenstaande voorbeelden. De versie is gebaseerd op het eindpunt van waar deze is aangevraagd. Hoewel bestaande toepassingen waarschijnlijk gebruikmaken van het Azure AD-eindpunt, moeten nieuwe toepassingen gebruikmaken van het Microsoft-identiteitsplatform.
>
> - v1.0: Azure AD-eindpunten: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v2.0: Microsoft Identity Platform-eindpunten: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Claims gebruiken om een gebruiker betrouwbaar te identificeren (onderwerp- en object-id)

Bij het identificeren van een gebruiker (bijvoorbeeld wanneer u deze opzoekt in een database of besluit welke machtigingen ze hebben), is het essentieel om informatie te gebruiken die constant en uniek blijft in de tijd. Oudere toepassingen gebruiken soms velden zoals het e-mailadres, een telefoonnummer of de UPN.  Al deze wijzigingen kunnen in de tijd veranderen en kunnen ook na een bepaalde periode opnieuw worden gebruikt: wanneer een werknemer zijn naam wijzigt of een werknemer een e-mailadres krijgt dat overeenkomt met dat van een vorige, niet langer aanwezig werknemer). Het **is** dus essentieel dat uw toepassing geen door mensen leesbare gegevens gebruikt om een gebruiker te identificeren. Door mensen te lezen betekent dit meestal dat iemand deze leest en deze wil wijzigen. Gebruik in plaats daarvan de claims die worden geleverd door de OIDC-standaard of de uitbreidingsclaims die door Microsoft worden geleverd- de `sub` claims en `oid` .

Als u gegevens per gebruiker correct wilt opslaan, gebruikt u of alleen (wat guid's uniek zijn), met gebruikt voor routering of `sub` `oid` `tid` sharding, indien nodig.  Als u gegevens wilt delen tussen services, is het beste omdat alle apps dezelfde en `oid` + `tid` claims voor een bepaalde `oid` gebruiker `tid` krijgen.  De `sub` claim in het Microsoft Identity Platform is 'paarsgewijs': deze is uniek op basis van een combinatie van de ontvanger, tenant en gebruiker van het token.  Twee apps die id-tokens voor een bepaalde gebruiker aanvragen, ontvangen dus verschillende `sub` claims, maar dezelfde `oid` claims voor die gebruiker.

>[!NOTE]
> Gebruik de claim niet om informatie over een gebruiker op te slaan in een poging `idp` om gebruikers te correleren tussen tenants.  Deze functie werkt niet, omdat de claims en voor een gebruiker in tenants per ontwerp veranderen, om ervoor te zorgen dat toepassingen gebruikers niet kunnen bijhouden `oid` `sub` in tenants.  
>
> Gastscenario's, waarbij een gebruiker zich in de ene tenant thuis heeft en zich in een andere tenant verifieert, moet de gebruiker behandelen alsof deze een nieuwe gebruiker van de service is.  Uw documenten en bevoegdheden in de Contoso-tenant mogen niet van toepassing zijn in de Fabrikam-tenant. Dit is belangrijk om onbedoelde gegevenslekken tussen tenants te voorkomen.

### <a name="groups-overage-claim"></a>Claim voor uitval van groepen
Om ervoor te zorgen dat de tokengrootte de groottelimieten van de HTTP-header niet overschrijdt, beperkt Azure AD het aantal object-ID's dat de token in de `groups` claim bevat. Als een gebruiker lid is van meer groepen dan de limiet voor uitval (150 voor SAML-tokens, 200 voor JWT-tokens), stuurt Azure AD de claim groepen niet in het token. In plaats daarvan bevat het een uitvalclaim in het token dat de toepassing aangeeft om een query uit te voeren op de Microsoft Graph-API om het groepslidmaatschap van de gebruiker op te halen.

```json
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

## <a name="id-token-lifetime"></a>Levensduur van id-token

Standaard is een id-token 1 uur geldig. Na 1 uur moet de client een nieuw id-token verkrijgen.

U kunt de levensduur van een id-token aanpassen om te bepalen hoe vaak de clienttoepassing de toepassingssessie verloopt en hoe vaak de gebruiker opnieuw moet worden geverifieerd (op de stille of interactieve manier). Lees Configureerbare [levensduur van token voor meer informatie.](active-directory-configurable-token-lifetimes.md)

## <a name="validating-an-id_token"></a>Een id_token

Valideren van een is vergelijkbaar met de eerste stap van het valideren van een toegangsken: uw client kan controleren of de juiste vergever het token heeft teruggestuurd en of er niet mee is `id_token` geknoeid. [](access-tokens.md#validating-tokens) Omdat altijd een JWT-token is, bestaan er veel bibliotheken om deze tokens te valideren. We raden u aan een van deze tokens te gebruiken in plaats van `id_tokens` dit zelf te doen.  Houd er rekening mee dat alleen vertrouwelijke clients (clients met een geheim) id-tokens moeten valideren.  Openbare toepassingen (code die volledig wordt uitgevoerd op een apparaat of netwerk die u niet kunt controleren, bijvoorbeeld de browser van een gebruiker of het thuisnetwerk), profiteren niet van het valideren van het id-token, omdat een kwaadwillende gebruiker de sleutels kan onderscheppen en bewerken die worden gebruikt voor validatie van het token.

Zie de stappen voor het valideren van een toegangsken om het token handmatig [te valideren.](access-tokens.md#validating-tokens) Nadat de handtekening op het token is gevalideerd, moeten de volgende JWT-claims worden gevalideerd in de id_token (deze kunnen ook worden uitgevoerd door uw tokenvalidatiebibliotheek):

* Tijdstempels: de tijdstempels , en moeten allemaal vóór of na de `iat` `nbf` huidige tijd `exp` vallen, indien van toepassing.
* Doelgroep: de `aud` claim moet overeenkomen met de app-id voor uw toepassing.
* Nonce: de claim in de nettolading moet overeenkomen met de parameter nonce die tijdens de eerste aanvraag is doorgegeven aan het `nonce` /authorize-eindpunt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [toegangstokens](access-tokens.md)
* Pas de JWT-claims in uw id_token met behulp van [optionele claims](active-directory-optional-claims.md).
