---
title: Verificatie van Azure AD-service naar service met OAuth 2.0 | Microsoft Docs
description: In dit artikel wordt beschreven hoe u HTTP-berichten gebruikt om service te implementeren voor service verificatie met behulp van de OAuth 2.0-client referenties toewijzen stroom.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 977dfea28c5c0dc3f34ada0c138556d70c979e04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85551716"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Service-to-service aanroepen met behulp van client referenties (gedeeld geheim of certificaat)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Met de OAuth 2,0-client referenties toewijzen stroom kan een webservice (*vertrouwelijke client*) eigen referenties gebruiken in plaats van een gebruiker te imiteren, om te verifiëren wanneer een andere webservice wordt aangeroepen. In dit scenario is de client doorgaans een middelste laag, een daemon-service of website. Voor een hoger niveau van Assurance kan Azure AD ook gebruikmaken van een certificaat (in plaats van een gedeeld geheim) als referentie.

## <a name="client-credentials-grant-flow-diagram"></a>Stroom diagram van toekenning van client referenties
In het volgende diagram wordt uitgelegd hoe de toewijzings stroom voor client referenties werkt in Azure Active Directory (Azure AD).

![Toekennings stroom voor OAuth 2.0-client referenties](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. De client toepassing verifieert het Azure AD token uitgifte-eind punt en vraagt een toegangs token aan.
2. Het Azure AD-token uitgifte-eind punt geeft het toegangs token uit.
3. Het toegangs token wordt gebruikt om te verifiëren bij de beveiligde bron.
4. Gegevens van de beveiligde bron worden geretourneerd naar de client toepassing.

## <a name="register-the-services-in-azure-ad"></a>De services registreren in azure AD
Registreer zowel de aanroepende service als de ontvangende service in Azure Active Directory (Azure AD). Zie [toepassingen integreren met Azure Active Directory](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor gedetailleerde instructies.

## <a name="request-an-access-token"></a>Een toegangs token aanvragen
Als u een toegangs token wilt aanvragen, gebruikt u een HTTP POST naar het Tenant-specifieke Azure AD-eind punt.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Aanvraag voor service-naar-service-toegangs token
Er zijn twee gevallen, afhankelijk van het feit of de client toepassing wordt beveiligd door een gedeeld geheim of een certificaat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Eerste case: toegangs token aanvraag met een gedeeld geheim
Bij gebruik van een gedeeld geheim bevat een aanvraag voor service-naar-service-toegangs token de volgende para meters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| grant_type |vereist |Hiermee geeft u het aangevraagde toekennings type op. In een client referenties toekenning stroom moet de waarde **client_credentials** zijn. |
| client_id |vereist |Hiermee geeft u de id op van de Azure AD-client van de aanroepende webservice. Als u de client-ID van de aanroepende toepassing wilt zoeken, klikt u in het [Azure Portal](https://portal.azure.com)op **Azure Active Directory**, klikt u op **app-registraties** en klikt u op de toepassing. De client_id is de *toepassings-id* |
| client_secret |vereist |Voer een sleutel in die is geregistreerd voor de aanroepende webservice of daemon-toepassing in azure AD. Als u een sleutel wilt maken, klikt u in de Azure Portal op **Azure Active Directory**, klikt u op **app-registraties**, klikt u op de toepassing, klikt u op **instellingen**, klikt u op **sleutels** en voegt u een sleutel toe.  URL: dit geheim coderen wanneer het wordt verstrekt. |
| resource |vereist |Voer de App-ID-URI in van de ontvangende webservice. Als u de URI van de App-ID wilt zoeken, klikt u in het Azure Portal op **Azure Active Directory**, klikt u op **app-registraties**, klikt u op de service toepassing en klikt u vervolgens op **instellingen** en **Eigenschappen**. |

#### <a name="example"></a>Voorbeeld
Met de volgende HTTP POST [wordt een toegangs token](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor de `https://service.contoso.com/` webservice aangevraagd. `client_id`Hiermee wordt de webservice geïdentificeerd waarmee het toegangs token wordt aangevraagd.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Tweede geval: toegangs token aanvraag met een certificaat
Een aanvraag voor service-naar-service-toegangs token met een certificaat bevat de volgende para meters:

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| grant_type |vereist |Hiermee geeft u het aangevraagde antwoord type op. In een client referenties toekenning stroom moet de waarde **client_credentials** zijn. |
| client_id |vereist |Hiermee geeft u de id op van de Azure AD-client van de aanroepende webservice. Als u de client-ID van de aanroepende toepassing wilt zoeken, klikt u in het [Azure Portal](https://portal.azure.com)op **Azure Active Directory**, klikt u op **app-registraties** en klikt u op de toepassing. De client_id is de *toepassings-id* |
| client_assertion_type |vereist |De waarde moet `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |vereist | Een bevestiging (een JSON Web Token) die u moet maken en ondertekenen met het certificaat dat u hebt geregistreerd als referenties voor uw toepassing. Lees de informatie over [certificaat referenties](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie over het registreren van uw certificaat en de indeling van de verklaring.|
| resource | vereist |Voer de App-ID-URI in van de ontvangende webservice. Als u de URI van de App-ID wilt zoeken, klikt u in het Azure Portal op **Azure Active Directory**, klikt u op **app-registraties**, klikt u op de service toepassing en klikt u vervolgens op **instellingen** en **Eigenschappen**. |

U ziet dat de para meters bijna hetzelfde zijn als in het geval van de aanvraag van het gedeelde geheim, behalve dat de para meter client_secret wordt vervangen door twee para meters: een client_assertion_type en client_assertion.

#### <a name="example"></a>Voorbeeld
Met de volgende HTTP POST wordt een toegangs token voor de `https://service.contoso.com/` webservice aangevraagd met een certificaat. `client_id`Hiermee wordt de webservice geïdentificeerd waarmee het toegangs token wordt aangevraagd.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Reactie van service-naar-service-toegangs token

Een antwoord van het succes bevat een JSON OAuth 2,0-antwoord met de volgende para meters:

| Parameter | Beschrijving |
| --- | --- |
| access_token |Het aangevraagde toegangs token. De aanroepende webservice kan dit token gebruiken om te verifiëren bij de ontvangende webservice. |
| token_type |Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is **Bearer**. Zie voor meer informatie over Bearer-tokens het [OAuth 2,0 Authorization Framework: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Hoe lang het toegangs token geldig is (in seconden). |
| expires_on |Het tijdstip waarop het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van 1970-01-01T0:0: 0Z UTC tot de verloop tijd. Deze waarde wordt gebruikt om de levens duur van tokens in de cache te bepalen. |
| not_before |Het tijdstip waarop het toegangs token bruikbaar wordt. De datum wordt weer gegeven als het aantal seconden van 1970-01-01T0:0: 0Z UTC totdat de geldigheids duur van het token.|
| resource |De App-ID-URI van de ontvangende webservice. |

#### <a name="example-of-response"></a>Voor beeld van antwoord
In het volgende voor beeld ziet u een reactie op een aanvraag voor een toegangs token bij een webservice.

```
{
"access_token":"eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```
## <a name="use-the-access-token-to-access-the-secured-resource"></a>Het toegangs token gebruiken om toegang te krijgen tot de beveiligde resource

De service kan het verkregen toegangs token gebruiken om geverifieerde aanvragen naar de downstream Web-API te maken door het token in de header in te stellen `Authorization` .

### <a name="example"></a>Voorbeeld

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="see-also"></a>Zie ook
* [OAuth 2,0 in azure AD](v1-protocols-oauth-code.md)
* Voor [beeld in c# van het service-to-Service-gesprek met een gedeeld geheim en een](https://github.com/Azure-Samples/active-directory-dotnet-daemon) [voor beeld in c# van de service to Service Call with a Certificate](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
