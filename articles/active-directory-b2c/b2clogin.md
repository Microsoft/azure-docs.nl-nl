---
title: Toepassingen en Api's migreren naar b2clogin.com
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van b2clogin.com in de omleidings-Url's voor Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20df5fc3a4d7c392be62df2b7778854d1e2e1cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109059"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Omleidings-Url's instellen op b2clogin.com voor Azure Active Directory B2C

Wanneer u een id-provider voor registratie instelt en u zich aanmeldt in uw Azure Active Directory B2C-toepassing (Azure AD B2C), moet u een omleidings-URL opgeven. U moet niet langer verwijzen naar *login.microsoftonline.com* in uw toepassingen en api's voor het verifiëren van gebruikers met Azure AD B2C. Gebruik in plaats daarvan *b2clogin.com* voor alle nieuwe toepassingen en migreer bestaande toepassingen van *login.microsoftonline.com* naar *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Afschaffing van login.microsoftonline.com

**Update van oktober 2020:** We verlengen een respijt periode voor tenants die niet kunnen voldoen aan de oorspronkelijke afschaffing datum van 04 december 2020. De buiten gebruiks telling van login.microsoftonline.com vindt nu niet eerder dan **14 januari 2021.**

**Achtergrond**: op 04 december 2019 werd de geplande buiten gebruiks telling van login.microsoftonline.com-ondersteuning in azure AD B2C op 04 december 2020 oorspronkelijk [aangekondigd](https://azure.microsoft.com/updates/b2c-deprecate-msol/) . Hiermee hebt u een (1) jaar bestaande tenants voor migratie naar b2clogin.com. Nieuwe tenants die zijn gemaakt na 04 december 2019, accepteren geen aanvragen van login.microsoftonline.com. Alle functionaliteit blijft hetzelfde op het b2clogin.com-eind punt.

De afschaffing van login.microsoftonline.com heeft geen invloed op Azure Active Directory tenants. Deze wijziging is alleen van invloed op Azure Active Directory B2C tenants.

## <a name="what-endpoints-does-this-apply-to"></a>Op welke eind punten is dit van toepassing
De overgang naar b2clogin.com is alleen van toepassing op verificatie-eind punten die gebruikmaken van Azure AD B2C beleid (gebruikers stromen of aangepaste beleids regels) om gebruikers te verifiëren. Deze eind punten hebben een `<policy-name>` para meter waarmee het beleid wordt opgegeven Azure AD B2C moet worden gebruikt. Meer [informatie over Azure AD B2C-beleid](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Deze eind punten kunnen er als volgt uitzien:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Het `<policy-name>` kan ook worden door gegeven als een query parameter:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> Eind punten die gebruikmaken van de para meter ' beleid ' moeten worden bijgewerkt, evenals de [omleidings-url's](#change-identity-provider-redirect-urls)voor de ID-provider.

Sommige Azure AD B2C klanten gebruiken de gedeelde mogelijkheden van Azure AD-ondernemings-tenants, zoals OAuth 2,0-client referenties toewijzen stroom. Deze functies zijn toegankelijk via de login.microsoftonline.com-eind punten van Azure AD, *die geen beleids parameter bevatten*. __Deze eind punten worden niet beïnvloed__.

## <a name="benefits-of-b2clogincom"></a>Voor delen van b2clogin.com

Wanneer u *b2clogin.com* als omleidings-URL gebruikt:

* De ruimte die wordt gebruikt in de cookie-header door micro soft-Services is beperkt.
* De omleidings-Url's hoeven niet langer een verwijzing naar micro soft te bevatten.
* Java script-client-side-code wordt ondersteund (momenteel in [Preview](javascript-and-page-layout.md)) op aangepaste pagina's. Vanwege beveiligings beperkingen worden java script-code en HTML-formulier elementen verwijderd uit aangepaste pagina's als u *login.microsoftonline.com* gebruikt.

## <a name="overview-of-required-changes"></a>Overzicht van vereiste wijzigingen

Er zijn verschillende wijzigingen die u mogelijk moet maken om uw toepassingen te migreren naar *b2clogin.com*:

* Wijzig de omleidings-URL in de toepassingen van uw identiteits provider om naar *b2clogin.com* te verwijzen.
* Werk uw Azure AD B2C-toepassingen bij om *b2clogin.com* in hun gebruikers stroom en Token-eindpunt verwijzingen te gebruiken. Dit kan ook het bijwerken van uw gebruik van een verificatie bibliotheek zoals micro soft Authentication Library (MSAL) zijn.
* Werk alle **toegestane oorsprongen** bij die u hebt gedefinieerd in de CORS-instellingen voor het aanpassen van de [gebruikers interface](customize-ui-with-html.md).

Een oud eind punt kan er als volgt uitzien:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

Een bijbehorend bijgewerkt eind punt ziet er als volgt uit:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Omleidings-Url's voor de ID-provider wijzigen

Wijzig op elke website van de identiteits provider waar u een toepassing hebt gemaakt, alle vertrouwde Url's om om te leiden in `your-tenant-name.b2clogin.com` plaats van *login.microsoftonline.com*.

Er zijn twee indelingen die u kunt gebruiken voor uw b2clogin.com-omleidings-Url's. De eerste biedt het voor deel dat ' micro soft ' nergens in de URL wordt weer gegeven met behulp van de Tenant-ID (een GUID) in plaats van de domein naam van de Tenant:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

De tweede optie maakt gebruik van de domein naam van uw Tenant in de vorm van `your-tenant-name.onmicrosoft.com` . Bijvoorbeeld:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Voor beide indelingen:

* Vervang `{your-tenant-name}` door de naam van uw Azure AD B2C-tenant.
* Verwijderen `/te` als deze bestaat in de URL.

## <a name="update-your-applications-and-apis"></a>Uw toepassingen en Api's bijwerken

De code in uw Azure AD B2C-toepassingen en Api's kunnen op `login.microsoftonline.com` verschillende locaties worden gebruikt. Uw code kan bijvoorbeeld verwijzingen bevatten naar gebruikers stromen en Token-eind punten. Werk het volgende bij in plaats daarvan `your-tenant-name.b2clogin.com` :

* Autorisatie-eind punt
* Token eindpunt
* Token Uitgever

Zo zou het CA-eind punt voor het aanmeldings-en aanmeldings beleid van Contoso nu het volgende zijn:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Voor informatie over het migreren van OWIN-webtoepassingen naar b2clogin.com, raadpleegt [u een op OWIN gebaseerde web-API migreren naar b2clogin.com](multiple-token-endpoints.md).

Zie de sectie [migreren naar b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) in [een Azure API Management-API beveiligen met Azure AD B2C](secure-api-management.md)voor meer informatie over het migreren van Azure API Management-api's die worden beveiligd door Azure AD B2C.

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>Eigenschap MSAL.NET ValidateAuthority

Als u [MSAL.net][msal-dotnet] v2 of eerder gebruikt, stelt u de eigenschap **ValidateAuthority** in `false` op client instantiëren om omleidingen naar *b2clogin.com* toe te staan. Het instellen van deze waarde op `false` is niet vereist voor MSAL.net v3 en hoger.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL voor Java script validateAuthority eigenschap

Als u MSAL gebruikt [voor Java script][msal-js] v 1.2.2 of eerder, stelt u de eigenschap **validateAuthority** in op `false` .

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

Als u `validateAuthority: true` in MSAL.js 1.3.0 + (de standaard instelling) hebt ingesteld, moet u ook een geldige token uitgever opgeven met `knownAuthorities` :

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Volgende stappen

Voor informatie over het migreren van OWIN-webtoepassingen naar b2clogin.com, raadpleegt [u een op OWIN gebaseerde web-API migreren naar b2clogin.com](multiple-token-endpoints.md).

Zie de sectie [migreren naar b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) in [een Azure API Management-API beveiligen met Azure AD B2C](secure-api-management.md)voor meer informatie over het migreren van Azure API Management-api's die worden beveiligd door Azure AD B2C.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
