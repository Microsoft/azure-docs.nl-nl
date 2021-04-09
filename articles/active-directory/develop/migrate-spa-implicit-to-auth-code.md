---
title: JavaScript-app met één pagina migreren van impliciete toekenning naar autorisatiecodestroom | Azure
titleSuffix: Microsoft identity platform
description: Een JavaScript-toepassing met één pagina bijwerken met MSAL.js1.x en de impliciete toekenningsstroom naar MSAL.js2.x en de autorisatiecodestroom met PKCE- en CORS-ondersteuning.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 3c11334fe1b4d77be6e64febfc1d3de6efa302c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365937"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Een JavaScript-app met één pagina migreren van impliciete toekenning naar autorisatiecodestroom

Met de micro soft-verificatie bibliotheek voor Java script (MSAL.js) v 2.0 wordt ondersteuning geboden voor de autorisatie code stroom met PKCE en CORS voor toepassingen met één pagina op het micro soft Identity-platform. Volg de stappen in de onderstaande secties om de MSAL.js 1.x-toepassing met impliciete toekenning te migreren naar MSAL.js 2.0+ (hierna *2. x*) en de authenticatiecodestroom.

MSAL.js 2.x verbetert MSAL.js 1.x door de autorisatiecodestroom in de browser te ondersteunen in plaats van de impliciete toekenningsstroom. MSAL.js 2.x biedt **GEEN** ondersteuning voor de impliciete stroom.

## <a name="migration-steps"></a>Migratiestappen

Als u uw toepassing wilt bijwerken naar MSAL.js 2.x en de authenticatiecodestroom, moet u drie primaire stappen uitvoeren:

1. Wijzig de omleidings-URL(s) van uw [app-registratie](#switch-redirect-uris-to-spa-platform) van **Web**-platform naar **SPA**-platform.
1. Werk de [code](#switch-redirect-uris-to-spa-platform) bij van MSAL.js 1.x naar **2.x**.
1. Schakel de [impliciete toekenning](#disable-implicit-grant-settings) in de app-registratie uit wanneer alle toepassingen die de registratie delen, zijn bijgewerkt naar MSAL.js 2.x en de authenticatiecodestroom.

In de volgende secties wordt elke stap uitgebreid beschreven.

## <a name="switch-redirect-uris-to-spa-platform"></a>Omleidings-URL's naar SPA-platform doorschakelen

Als u uw bestaande app-registratie voor uw toepassingen wilt blijven gebruiken, moet u in de Azure-portal de omleidings-URL's van de registratie bijwerken naar het SPA-platform. Hierdoor wordt de autorisatiecodestroom met PKCE- en CORS-ondersteuning ingeschakeld voor apps die gebruikmaken van de registratie (de code van uw toepassing moet nog wel worden bijgewerkt naar MSAL.js v2.x).

Volg deze stappen voor app-registraties die momenteel zijn geconfigureerd met omleidings-URL’s voor het **Web**-platform:

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a> en selecteer uw **Azure Active Directory**-tenant.
1. Selecteer uw toepassing in **App-registraties** en klik vervolgens op **Verificatie**.
1. Selecteer in de tegel **Web**-platform onder **Omleidings-URL's** de waarschuwingsbanner die aangeeft dat u de URLS's moet migreren.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Waarschuwingsbanner voor impliciete stroom in de webapp-tegel in de Azure-portal":::
1. Selecteer *alleen* de omleidings-URL's waarvan de toepassingen MSAL.js 2.x gebruiken en selecteer vervolgens **Configureren**.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Selecteer het deelvenster URL omleiden in het deelvenster SPA in de Azure-portal":::

De omleidings-URLS's zouden nu moeten worden weergegeven in de **SPA**-platformtegel, wat betekent dat CORS-ondersteuning met de autorisatiecodestroom en PKCE is ingeschakeld voor deze URL's.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="De SAP-tegel in de app-registratie in de Azure-portal":::

U kunt ook [een nieuwe app-registratie maken](scenario-spa-app-registration.md) in plaats van de omleidings-URL's in uw bestaande registratie bij te werken.

## <a name="update-your-code-to-msaljs-2x"></a>Werk uw code bij naar MSAL.js 2.x

In MSAL 1.x hebt u een toepassingsexemplaar gemaakt door een [UserAgentApplication][msal-js-useragentapplication] als volgt te initialiseren:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

In MSAL 2.x initialiseert u in plaats daarvan een [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Ga voor een volledige uitleg over het toevoegen van MSAL 2.x aan uw toepassing naar de [Zelfstudie: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een JavaScript-app met één pagina met behulp van autorisatiecodestroom](tutorial-v2-javascript-auth-code.md).

Zie de [migratiehandleiding](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) op GitHub voor aanvullende wijzigingen die u mogelijk in uw code moet aanbrengen.

## <a name="disable-implicit-grant-settings"></a>Impliciete toekenningsinstellingen uitschakelen

Nadat u de productietoepassingen die deze app-registratie en de bijbehorende client-ID gebruiken, hebt bijgewerkt naar MSAL 2.x en de autorisatiecodestroom, moet u de impliciete toekenningsinstellingen in de app-registratie uitschakelen.

Wanneer u de impliciete toekenningsinstellingen in de app-registratie uitschakelt, wordt de impliciete stroom uitgeschakeld voor alle toepassingen die de registratie en de bijbehorende client-ID gebruiken.

Schakel de impliciete toekenningsstroom **niet** uit voordat u al uw toepassingen hebt bijgewerkt naar MSAL.js 2.x en de [PublicClientApplication][msal-js-publicclientapplication].

## <a name="next-steps"></a>Volgende stappen

Zie de [micro soft Identity platform and OAuth 2,0 Authorization code flow](v2-oauth2-auth-code-flow.md)(Engelstalig) voor meer informatie over de autorisatie code stroom, met inbegrip van de verschillen tussen de impliciete en auth-code stromen.

Als u meer wilt weten over het ontwikkelen van toepassingen met één pagina op het Microsoft-identiteitsplatform, is de reeks artikelen [Scenario: Een toepassing met één pagina](scenario-spa-overview.md)-serie artikelen is nuttig om mee aan de slag te gaan.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
