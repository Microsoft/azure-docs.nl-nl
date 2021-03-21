---
title: Service-naar-service verificatie met OAuth 2.0 namens-of-flow | Microsoft Docs
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt voor het implementeren van service-naar-service-verificatie met de OAuth 2.0 namens-stroom.
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 08/5/2020
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: f746cc654934464d907c6ad669eb7470e4dcaeeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88117733"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>Service-naar-service-aanroepen die een overgedragen gebruikers-id gebruiken in namens-of-flow

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

De OAuth 2,0-stroom (OBO) maakt een toepassing mogelijk die een service of Web-API aanroept om gebruikers verificatie door te geven aan een andere service of Web-API. De OBO-stroom geeft de gedelegeerde gebruikers identiteit en-machtigingen door via de aanvraag keten. Voor de middelste service voor het maken van geverifieerde aanvragen voor de downstream-service moet het een toegangs token van Azure Active Directory (Azure AD) beveiligen namens de gebruiker.

> [!IMPORTANT]
> Vanaf mei 2018 `id_token` kan er geen gebruik worden gemaakt van de namens-stroom.  Apps met één pagina (SPAs) moeten een toegangs token door geven aan een vertrouwelijke client voor de middelste laag om OBO-stromen uit te voeren. Zie [beperkingen](#client-limitations)voor meer details over de clients die namens-aanroepen kunnen uitvoeren.

## <a name="on-behalf-of-flow-diagram"></a>Diagram van namens-van stroom

De OBO-stroom wordt gestart nadat de gebruiker is geverifieerd op een toepassing die gebruikmaakt van de [OAuth 2,0-autorisatie code toekenning stroom](v1-protocols-oauth-code.md). Op dat moment verzendt de toepassing een toegangs token (token A) naar de Web-API (API A) op de middelste laag met de claims van de gebruiker en toestemming voor toegang tot API A. Vervolgens maakt API A een geverifieerde aanvraag voor de downstream Web API (API B).

Deze stappen vormen de namen van de stroom: de ![ stappen in de OAuth 2.0-stroom voor het uitvoeren van de opdracht](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. De client toepassing maakt een aanvraag naar API A met het token A.
1. API A verifieert het Azure AD token uitgifte-eind punt en vraagt een token aan voor toegang tot API B.
1. Het Azure AD-eind punt voor token uitgifte valideert API A-referenties met token A en geeft het toegangs token voor API B (token B).
1. De aanvraag voor API B bevat token B in de autorisatie-header.
1. API B retourneert gegevens van de beveiligde bron.

>[!NOTE]
>De claim van een doel groep in een toegangs token dat wordt gebruikt om een token aan te vragen voor een downstream-service, moet de ID zijn van de service die de OBO-aanvraag maakt. Het token moet ook worden ondertekend met de Azure Active Directory globale handtekening sleutel (dit is de standaard waarde voor toepassingen die zijn geregistreerd via **app-registraties** in de portal).

## <a name="register-the-application-and-service-in-azure-ad"></a>De toepassing en service registreren in azure AD

Registreer zowel de middelste laag service als de client toepassing in azure AD.

### <a name="register-the-middle-tier-service"></a>De service voor de middelste laag registreren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer uw account in de bovenste balk en zoek in de lijst met **mappen** een Active Directory-Tenant voor uw toepassing.
1. Selecteer **meer services** in het linkerdeel venster en kies **Azure Active Directory**.
1. Selecteer **app-registraties** en vervolgens **nieuwe registratie**.
1. Voer een beschrijvende naam in voor de toepassing en selecteer het toepassings type.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Stel de omleidings-URI in op de basis-URL.
1. Selecteer **Registreren** om de toepassing te maken.
1. Kies in het Azure Portal uw toepassing en selecteer **certificaten & geheimen**.
1. Selecteer **Nieuw client geheim** en voeg een geheim toe met een duur van één jaar of twee jaar.
1. Wanneer u deze pagina opslaat, wordt de geheime waarde door de Azure Portal weer gegeven. Kopieer de geheime waarde en sla deze op een veilige locatie op.
1. Maak een scope in uw toepassing in de pagina **een API beschikbaar** maken voor uw app en klik op een bereik toevoegen.  Mogelijk moet u ook een URI voor de toepassings-ID maken. 

> [!IMPORTANT]
> U hebt het geheim nodig om de toepassings instellingen in uw implementatie te configureren. Deze geheime waarde wordt niet opnieuw weer gegeven en kan niet worden opgehaald op een andere manier. Leg de gegevens vast zodra deze zichtbaar zijn in de Azure Portal.

### <a name="register-the-client-application"></a>De client toepassing registreren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer uw account in de bovenste balk en zoek in de lijst met **mappen** een Active Directory-Tenant voor uw toepassing.
1. Selecteer **meer services** in het linkerdeel venster en kies **Azure Active Directory**.
1. Selecteer **app-registraties** en vervolgens **nieuwe registratie**.
1. Voer een beschrijvende naam in voor de toepassing en selecteer het toepassings type.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Stel de omleidings-URI in op de basis-URL.
1. Selecteer **Registreren** om de toepassing te maken.
1. Configureer machtigingen voor uw toepassing. Selecteer in **API**-machtigingen **de optie een machtiging toevoegen** en vervolgens **mijn api's**.
1. Typ de naam van de middelste laag service in het tekst veld.
1. Kies **machtigingen selecteren** en selecteer vervolgens het bereik dat u hebt gemaakt in de laatste stap van het registreren van de middelste laag.

### <a name="configure-known-client-applications"></a>Bekende client toepassingen configureren

In dit scenario moet de middelste laag service de toestemming van de gebruiker verkrijgen om toegang te krijgen tot de downstream API zonder tussen komst van de gebruiker. De optie om toegang tot de downstream API te verlenen, moet worden weer gegeven als onderdeel van de stap voor toestemming tijdens de verificatie.

Volg de onderstaande stappen om de registratie van de client-app in azure AD expliciet te binden met de registratie van de middelste laag van de service. Met deze bewerking wordt de vereiste toestemming voor zowel de client als de middelste laag samengevoegd in één dialoog venster.

1. Ga naar de service registratie voor de middelste laag en selecteer **manifest** om de manifest editor te openen.
1. Zoek de `knownClientApplications` eigenschap matrix en voeg de client-id van de client toepassing toe als een-element.
1. Sla het manifest op door **Opslaan** te selecteren.

## <a name="service-to-service-access-token-request"></a>Aanvraag voor service-naar-service-toegangs token

Als u een toegangs token wilt aanvragen, maakt u een HTTP POST naar het Tenant-specifieke Azure AD-eind punt met de volgende para meters:

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

De client toepassing wordt beveiligd door een gedeeld geheim of door een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste case: toegangs token aanvraag met een gedeeld geheim

Bij gebruik van een gedeeld geheim bevat een aanvraag voor service-naar-service-toegangs token de volgende para meters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| grant_type |vereist | Het type van de token aanvraag. Een OBO-aanvraag gebruikt een JSON Web Token (JWT), dus de waarde moet **urn zijn: IETF: params: OAuth: Grant-type: JWT-Bearer**. |
| Assertion |vereist | De waarde van het toegangs token dat in de aanvraag wordt gebruikt. |
| client_id |vereist | De App-ID die is toegewezen aan de aanroepende service tijdens de registratie bij Azure AD. Als u de App-ID in de Azure Portal wilt zoeken, selecteert u **Active Directory**, kiest u de map en selecteert u vervolgens de naam van de toepassing. |
| client_secret |vereist | De sleutel die is geregistreerd voor de aanroepende service in azure AD. Deze waarde moet worden vermeld op het moment van registratie. |
| resource |vereist | De App-ID-URI van de ontvangende service (beveiligde resource). Als u de URI van de App-ID wilt vinden in de Azure Portal, selecteert u **Active Directory** en kiest u de map. Selecteer de naam van de toepassing, kies **alle instellingen** en selecteer vervolgens **Eigenschappen**. |
| requested_token_use |vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In namens-of uitstroom moet de waarde **on_behalf_of** zijn. |
| scope |vereist | Een lijst met door spaties gescheiden bereiken voor de token aanvraag. Voor OpenID Connect Connect moet de scope **OpenID Connect** worden opgegeven.|

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt een toegangs token voor de https://graph.microsoft.com Web-API. `client_id`Hiermee wordt de service geïdentificeerd waarmee het toegangs token wordt aangevraagd.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.ewogICJhdWQiOiAiaHR0cHM6Ly9ncmFwaC5taWNyb3NvZnQuY29tIiwKICAiaXNzIjogImh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLAogICJpYXQiOiAxNDkzNDIzMTY4LAogICJuYmYiOiAxNDkzNDIzMTY4LAogICJleHAiOiAxNDkzNDY2OTUxLAogICJhY3IiOiAiMSIsCiAgImFpbyI6ICJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsCiAgImFtciI6IFsKICAgICJwd2QiCiAgXSwKICAiYXBwaWQiOiAiNjI1MzkxYWYtYzY3NS00M2U1LThlNDQtZWRkM2UzMGNlYjE1IiwKICAiYXBwaWRhY3IiOiAiMSIsCiAgImVfZXhwIjogMzAyNjgzLAogICJmYW1pbHlfbmFtZSI6ICJUZXN0IiwKICAiZ2l2ZW5fbmFtZSI6ICJOYXZ5YSIsCiAgImlwYWRkciI6ICIxNjcuMjIwLjEuMTc3IiwKICAibmFtZSI6ICJOYXZ5YSBUZXN0IiwKICAib2lkIjogIjFjZDRiY2FjLWI4MDgtNDIzYS05ZTJmLTgyN2ZiYjFiYjczOSIsCiAgInBsYXRmIjogIjMiLAogICJwdWlkIjogIjEwMDMzRkZGQTEyRUQ3RkUiLAogICJzY3AiOiAiVXNlci5SZWFkIiwKICAic3ViIjogIjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLAogICJ0aWQiOiAiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwKICAidW5pcXVlX25hbWUiOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1cG4iOiAibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLAogICJ1dGkiOiAieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsCiAgInZlciI6ICIxLjAiCn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede geval: toegangs token aanvraag met een certificaat

Een aanvraag voor service-naar-service-toegangs token met een certificaat bevat de volgende para meters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| grant_type |vereist | Het type van de token aanvraag. Een OBO-aanvraag maakt gebruik van een JWT-toegangs token, dus de waarde moet **urn zijn: IETF: params: OAuth: toekenning-type: JWT-Bearer**. |
| Assertion |vereist | De waarde van het token dat in de aanvraag wordt gebruikt. |
| client_id |vereist | De App-ID die is toegewezen aan de aanroepende service tijdens de registratie bij Azure AD. Als u de App-ID in de Azure Portal wilt zoeken, selecteert u **Active Directory**, kiest u de map en selecteert u vervolgens de naam van de toepassing. |
| client_assertion_type |vereist |De waarde moet `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |vereist | Een JSON Web Token die u maakt en ondertekent met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Zie  [certificaat referenties](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie over de bevestigings indeling en over het registreren van uw certificaat.|
| resource |vereist | De App-ID-URI van de ontvangende service (beveiligde resource). Als u de URI van de App-ID wilt vinden in de Azure Portal, selecteert u **Active Directory** en kiest u de map. Selecteer de naam van de toepassing, kies **alle instellingen** en selecteer vervolgens **Eigenschappen**. |
| requested_token_use |vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In namens-of uitstroom moet de waarde **on_behalf_of** zijn. |
| scope |vereist | Een lijst met door spaties gescheiden bereiken voor de token aanvraag. Voor OpenID Connect Connect moet de scope **OpenID Connect** worden opgegeven.|

Deze para meters zijn bijna hetzelfde als met de aanvraag van het gedeelde geheim, behalve dat de `client_secret parameter` is vervangen door twee para meters: `client_assertion_type` en `client_assertion` .

#### <a name="example"></a>Voorbeeld

De volgende HTTP POST vraagt een toegangs token voor de https://graph.microsoft.com Web-API met een certificaat. `client_id`Hiermee wordt de service geïdentificeerd waarmee het toegangs token wordt aangevraagd.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.microsoft.com
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Reactie van service-naar-service-toegangs token

Een reactie op geslaagde pogingen is een JSON OAuth 2,0-antwoord met de volgende para meters:

| Parameter | Beschrijving |
| --- | --- |
| token_type |Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is **Bearer**. Zie voor meer informatie over Bearer-tokens het [OAuth 2,0 Authorization Framework: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Het bereik van toegang dat in het token wordt verleend. |
| expires_in |De tijds duur dat het toegangs token geldig is (in seconden). |
| expires_on |Het tijdstip waarop het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van 1970-01-01T0:0: 0Z UTC tot de verloop tijd. Deze waarde wordt gebruikt om de levens duur van tokens in de cache te bepalen. |
| resource |De App-ID-URI van de ontvangende service (beveiligde resource). |
| access_token |Het aangevraagde toegangs token. De aanroepende service kan dit token gebruiken om te verifiëren bij de ontvangende service. |
| id_token |Het aangevraagde ID-token. De aanroepende service kan dit token gebruiken om de identiteit van de gebruiker te controleren en een sessie met de gebruiker te starten. |
| refresh_token |Het vernieuwings token voor het aangevraagde toegangs token. De aanroepende service kan dit token gebruiken om een ander toegangs token aan te vragen nadat het huidige toegangs token is verlopen. |

### <a name="success-response-example"></a>Voor beeld van een geslaagd antwoord

Het volgende voor beeld toont een geslaagde reactie op een aanvraag voor een toegangs token voor de https://graph.microsoft.com Web-API.

```json
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.microsoft.com",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Voor beeld van fout antwoorden

Het Azure AD-token eindpunt retourneert een fout bericht wanneer er wordt geprobeerd een toegangs token te verkrijgen voor een downstream API die is ingesteld met een beleid voor voorwaardelijke toegang (bijvoorbeeld multi-factor Authentication). De middelste laag service moet deze fout op de client toepassing belicht, zodat de client toepassing de gebruikers interactie kan bieden om te voldoen aan het beleid voor voorwaardelijke toegang.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Het toegangs token gebruiken om toegang te krijgen tot de beveiligde resource

De middelste laag service kan het verkregen toegangs token gebruiken om geverifieerde aanvragen voor de downstream Web-API te maken door het token in de header in te stellen `Authorization` .

### <a name="example"></a>Voorbeeld

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>SAML-bevestigingen verkregen met een OAuth 2.0 OBO-stroom

Sommige op OAuth gebaseerde webservices moeten toegang hebben tot andere web service-Api's die SAML-bevestigingen in niet-interactieve stromen accepteren. Azure Active Directory kunt een SAML-verklaring geven in reactie op een naam van een stroom die gebruikmaakt van een SAML-gebaseerde webservice als doel bron.

>[!NOTE]
>Dit is een niet-standaard extensie voor de OAuth 2,0-stroom, waarmee een OAuth2 toepassing toegang kan krijgen tot Web Service API-eind punten die gebruikmaken van SAML-tokens.

> [!TIP]
> Wanneer u een met SAML beveiligde webservice aanroept vanuit een front-end-webtoepassing, kunt u gewoon de API aanroepen en een normale interactieve verificatie stroom initiëren met de bestaande sessie van de gebruiker. U hoeft alleen een OBO-stroom te gebruiken wanneer een service-naar-service-oproep een SAML-token vereist om gebruikers context te bieden.

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>Een SAML-token verkrijgen met behulp van een OBO-aanvraag met een gedeeld geheim

Een service-naar-service-aanvraag voor een SAML-verklaring bevat de volgende para meters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| grant_type |vereist | Het type van de token aanvraag. Voor een aanvraag die gebruikmaakt van een JWT, moet de waarde **urn zijn: IETF: params: OAuth: Grant-type: JWT-Bearer**. |
| Assertion |vereist | De waarde van het toegangs token dat in de aanvraag wordt gebruikt.|
| client_id |vereist | De App-ID die is toegewezen aan de aanroepende service tijdens de registratie bij Azure AD. Als u de App-ID in de Azure Portal wilt zoeken, selecteert u **Active Directory**, kiest u de map en selecteert u vervolgens de naam van de toepassing. |
| client_secret |vereist | De sleutel die is geregistreerd voor de aanroepende service in azure AD. Deze waarde moet worden vermeld op het moment van registratie. |
| resource |vereist | De App-ID-URI van de ontvangende service (beveiligde resource). Dit is de resource die de doel groep is van het SAML-token. Als u de URI van de App-ID wilt vinden in de Azure Portal, selecteert u **Active Directory** en kiest u de map. Selecteer de naam van de toepassing, kies **alle instellingen** en selecteer vervolgens **Eigenschappen**. |
| requested_token_use |vereist | Hiermee geeft u op hoe de aanvraag moet worden verwerkt. In namens-of uitstroom moet de waarde **on_behalf_of** zijn. |
| requested_token_type | vereist | Hiermee geeft u het aangevraagde type token op. De waarde kan **urn zijn: IETF: params: OAuth: token-type: saml2** of **urn: IETF: params: OAuth: token-type: saml1** , afhankelijk van de vereisten van de toegang tot de resource. |

Het antwoord bevat een SAML-token dat is gecodeerd in UTF8 en Base64url.

- **SubjectConfirmationData voor een SAML-verklaring die is gebrond vanuit een OBO-aanroep**: als voor de doel toepassing een waarde voor een ontvanger is vereist in **SubjectConfirmationData**, moet de waarde een antwoord-URL voor niet-joker tekens zijn in de configuratie van de bron toepassing.
- **Het knoop punt SubjectConfirmationData**: het knoop punt kan geen **InResponseTo** -kenmerk bevatten omdat het geen deel UITmaakt van een SAML-reactie. De toepassing die het SAML-token ontvangt, moet de SAML-bewering zonder het kenmerk **InResponseTo** kunnen accepteren.

- **Instemming**: toestemming moet zijn verleend om een SAML-token met gebruikers gegevens op een OAuth-stroom te ontvangen. Zie [machtigingen en toestemming in het Azure Active Directory v 1.0-eind punt](./v1-permissions-consent.md)voor meer informatie over machtigingen en het verkrijgen van toestemming voor de beheerder.

### <a name="response-with-saml-assertion"></a>Antwoord met SAML-bevestiging

| Parameter | Beschrijving |
| --- | --- |
| token_type |Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is **Bearer**. Zie [OAuth 2,0 Authorization Framework: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)voor meer informatie over Bearer-tokens. |
| scope |Het bereik van toegang dat in het token wordt verleend. |
| expires_in |De tijds duur dat het toegangs token geldig is (in seconden). |
| expires_on |Het tijdstip waarop het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van 1970-01-01T0:0: 0Z UTC tot de verloop tijd. Deze waarde wordt gebruikt om de levens duur van tokens in de cache te bepalen. |
| resource |De App-ID-URI van de ontvangende service (beveiligde resource). |
| access_token |De para meter die de SAML-bewering retourneert. |
| refresh_token |Het vernieuwings token. De aanroepende service kan dit token gebruiken om een ander toegangs token aan te vragen nadat de huidige SAML-bewering verloopt. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- resource `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn: IETF: params: OAuth: token-type: saml2
- refresh_token: \<Refresh token\>

## <a name="client-limitations"></a>Client beperkingen

Open bare clients met antwoord-Url's voor joker tekens kunnen geen `id_token` voor OBO-stromen gebruiken. Een vertrouwelijke client kan echter nog steeds **toegangs** tokens inwisselen die zijn verkregen via de impliciete toekennings stroom, zelfs als voor de open bare client een omleidings-URI voor joker tekens is geregistreerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het OAuth 2,0-protocol en een andere manier om service-to-service-verificatie uit te voeren die gebruikmaakt van client referenties:

* [Service to service-authenticatie met OAuth 2,0 client referenties toekenning in azure AD](v1-oauth2-client-creds-grant-flow.md)
* [OAuth 2,0 in azure AD](v1-protocols-oauth-code.md)