---
title: Aanmelden met wachtwoord referenties voor de resource-eigenaar | Azure
titleSuffix: Microsoft identity platform
description: Ondersteuning voor browser-minder authenticatie stromen met behulp van de ROPC-subsidie (resource owner password Credential).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bf469b79fa532978e904a54f32c80280706ee7cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174577"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Referenties voor het micro soft Identity platform en het OAuth 2,0 Resource owner-wacht woord

Het micro soft-identiteits platform ondersteunt de [OAuth 2,0 Resource owner-machtiging voor wacht woord (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), waarmee een toepassing zich kan aanmelden bij de gebruiker door rechtstreeks hun wacht woord af te handelen.  In dit artikel wordt beschreven hoe u direct kunt Program meren met het protocol in uw toepassing.  Als dat mogelijk is, kunt u het beste de ondersteunde micro soft-verificatie bibliotheken (MSAL) gebruiken in plaats van [tokens te verkrijgen en beveiligde web-api's](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)aan te roepen.  Bekijk ook de voor beeld- [apps die gebruikmaken van MSAL](sample-v2-code.md).

> [!WARNING]
> Micro soft raadt u aan om de ROPC-stroom _niet_ te gebruiken. In de meeste scenario's zijn veiligere alternatieven beschikbaar en aanbevolen. Deze stroom vereist een zeer hoge mate van vertrouwen in de toepassing en voert Risico's uit die niet aanwezig zijn in andere stromen. Gebruik deze stroom alleen wanneer andere beveiligde stromen niet kunnen worden gebruikt.

> [!IMPORTANT]
>
> * Het micro soft Identity-platform ondersteunt alleen ROPC voor Azure AD-tenants, niet voor persoonlijke accounts. Dit betekent dat u een Tenant-specifiek eind punt ( `https://login.microsoftonline.com/{TenantId_or_Name}` ) of het `organizations` eind punt moet gebruiken.
> * Persoonlijke accounts die worden uitgenodigd voor een Azure AD-Tenant, kunnen ROPC niet gebruiken.
> * Accounts die geen wacht woorden hebben, kunnen zich niet aanmelden via ROPC. Voor dit scenario raden we u aan om in plaats daarvan een andere stroom te gebruiken voor uw app.
> * Als gebruikers [multi-factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) moeten gebruiken om zich aan te melden bij de toepassing, zullen ze in plaats daarvan worden geblokkeerd.
> * ROPC wordt niet ondersteund in [hybride identiteits Federatie](../hybrid/whatis-fed.md) scenario's (bijvoorbeeld Azure AD en ADFS gebruikt voor het verifiëren van on-premises accounts). Als gebruikers worden omgeleid naar een volledige pagina naar een on-premises ID-provider, kan Azure AD de gebruikers naam en het wacht woord niet testen voor die id-aanbieder. [Pass-Through-verificatie](../hybrid/how-to-connect-pta.md) wordt echter ondersteund met ROPC.
> * Een uitzonde ring op een scenario met hybride identiteits Federatie zou het volgende kunnen zijn: beleid voor het detecteren van basis van realms met AllowCloudPasswordValidation ingesteld op TRUE zorgt ervoor dat ROPC flow werkt voor federatieve gebruikers wanneer het on-premises wacht woord wordt gesynchroniseerd met de Cloud. Zie [directe ROPC-verificatie van federatieve gebruikers inschakelen voor oudere toepassingen](../manage-apps/configure-authentication-for-federated-users-portal.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications)voor meer informatie.

## <a name="protocol-diagram"></a>Protocol diagram

In het volgende diagram ziet u de ROPC-stroom.

![Diagram van de referentie stroom van het wacht woord voor de resource-eigenaar](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Autorisatie aanvraag

De ROPC-stroom is een enkele aanvraag: de client-id en de referenties van de gebruiker worden verzonden naar de IDP, waarna de tokens in retour worden ontvangen. De client moet het e-mail adres (UPN) en het wacht woord van de gebruiker aanvragen voordat dit wordt gedaan. Direct na een geslaagde aanvraag moet de client de referenties van de gebruiker veilig vrijgeven uit het geheugen. Ze mogen ze nooit opslaan.

> [!TIP]
> Probeer deze aanvraag uit te voeren in postman!
> [![Probeer deze aanvraag uit te voeren in postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parameter | Voorwaarde | Beschrijving |
| --- | --- | --- |
| `tenant` | Vereist | De Directory-Tenant waarvan u de gebruiker wilt registreren. Dit kan een GUID of beschrijvende naam zijn. Deze para meter kan niet worden ingesteld op `common` of `consumers` , maar kan worden ingesteld op `organizations` . |
| `client_id` | Vereist | De ID van de toepassing (client) die de [Azure Portal-app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) pagina die aan uw app is toegewezen. |
| `grant_type` | Vereist | Moet worden ingesteld op `password`. |
| `username` | Vereist | Het e-mailadres van de gebruiker. |
| `password` | Vereist | Het wacht woord van de gebruiker. |
| `scope` | Aanbevolen | Een door spaties gescheiden lijst met [bereiken](v2-permissions-and-consent.md)of machtigingen die voor de app zijn vereist. In een interactieve stroom moet de beheerder of de gebruiker vóór de tijd toestemming geven aan deze bereiken. |
| `client_secret`| Soms vereist | Als uw app een open bare client is, dan `client_secret` kan de of `client_assertion` niet worden opgenomen.  Als de app een vertrouwelijke client is, moet deze worden opgenomen. |
| `client_assertion` | Soms vereist | Een andere vorm van `client_secret` , gegenereerd met een certificaat.  Zie [certificaat referenties](active-directory-certificate-credentials.md) voor meer informatie. |

### <a name="successful-authentication-response"></a>Geslaagde verificatie reactie

In het volgende voor beeld ziet u een geslaagd token antwoord:

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
| `token_type` | Tekenreeks | Altijd ingesteld op `Bearer` . |
| `scope` | Door spaties gescheiden teken reeksen | Als er een toegangs token is geretourneerd, worden in deze para meter de scopes vermeld waarvoor het toegangs token geldig is. |
| `expires_in`| int | Aantal seconden dat het opgenomen toegangs token geldig is voor. |
| `access_token`| Dekkende teken reeks | Uitgegeven voor de aangevraagde [bereiken](v2-permissions-and-consent.md) . |
| `id_token` | JWT | Uitgegeven als de oorspronkelijke `scope` para meter het `openid` bereik bevat. |
| `refresh_token` | Dekkende teken reeks | Verleend als de oorspronkelijke `scope` para meter is opgenomen `offline_access` . |

U kunt het vernieuwings token gebruiken om nieuwe toegangs tokens te verkrijgen en tokens te vernieuwen met behulp van dezelfde stroom die wordt beschreven in de [documentatie over de OAuth-code stroom](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Fout bericht

Als de gebruiker geen juiste gebruikers naam of wacht woord heeft opgegeven, of als de client niet de aangevraagde toestemming heeft ontvangen, zal de verificatie mislukken.

| Fout | Beschrijving | Client actie |
|------ | ----------- | -------------|
| `invalid_grant` | De verificatie is mislukt | De referenties zijn onjuist of de client heeft geen toestemming voor de aangevraagde bereiken. Als de bereiken niet worden verleend, `consent_required` wordt een fout geretourneerd. Als dit het geval is, moet de client de gebruiker naar een interactieve prompt verzenden met een webweergave of browser. |
| `invalid_request` | De aanvraag is onjuist samengesteld | Het toekennings type wordt niet ondersteund voor de `/common` or- `/consumers` verificatie contexten.  Gebruik `/organizations` in plaats daarvan een Tenant-id. |

## <a name="learn-more"></a>Meer informatie

Voor een voor beeld van het gebruik van ROPC raadpleegt u het [.net Core-Console toepassings](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) code voorbeeld op github.
