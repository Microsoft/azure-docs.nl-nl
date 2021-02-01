---
title: Toepassingen met één pagina (SPA) registreren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een toepassing met één pagina (app-registratie)
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 39a675ff4947e7eca64298e1e68160cd6149f081
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226335"
---
# <a name="single-page-application-app-registration"></a>Toepassing met één pagina: app-registratie

Voer de volgende stappen uit als u een toepassing met één pagina (SPA) wilt registreren in het micro soft-identiteits platform. De registratie stappen verschillen van MSAL.js 1,0, die ondersteuning biedt voor de impliciete toekennings stroom en MSAL.js 2,0, waarmee de autorisatie code stroom met PKCE wordt ondersteund.

## <a name="create-the-app-registration"></a>De app-registratie maken

Voor zowel MSAL.js 1,0-als 2,0-toepassingen moet u eerst de volgende stappen uitvoeren om de eerste app-registratie te maken.

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Kies de **ondersteunde account typen** voor de toepassing. Voer **geen** **omleidings-URI** in. Zie [een toepassing registreren](quickstart-register-app.md)voor een beschrijving van de verschillende typen accounts.
1. Selecteer **registreren** om de app-registratie te maken.

Configureer vervolgens de app-registratie met een **omleidings-URI** om op te geven waar het micro soft Identity-platform de client moet omleiden met alle beveiligings tokens. Volg de stappen voor de versie van MSAL.js die u in uw toepassing gebruikt:

- [MSAL.js 2,0 met verificatie code stroom](#redirect-uri-msaljs-20-with-auth-code-flow) (aanbevolen)
- [MSAL.js 1,0 met impliciete stroom](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Omleidings-URI: [MSAL.js 2,0 met verificatie code stroom](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Volg deze stappen om een omleidings-URI toe te voegen voor een app die gebruikmaakt van MSAL.js 2,0 of hoger. MSAL.js 2.0 + ondersteunt de autorisatie code stroom met PKCE en CORS als reactie op [Cookie beperkingen van derden](reference-third-party-cookies-spas.md). De impliciete toekennings stroom wordt niet ondersteund in MSAL.js 2.0 +.

1. Selecteer in de Azure Portal de app-registratie die u eerder hebt gemaakt in [de app-registratie maken](#create-the-app-registration).
1. Selecteer onder **Beheren** achtereenvolgens **Verificatie** > **Een platform toevoegen**.
1. Selecteer onder **webtoepassingen** de tegel **toepassing met één pagina** .
1. Voer bij **omleidings-uri's** een [omleidings-URI](reply-url.md)in. Schakel **geen** van beide selectie vakjes in onder **impliciete toekenning en hybride stromen**.
1. Selecteer **configureren** om het toevoegen van de omleidings-URI te volt ooien.

U hebt nu de registratie van uw toepassing met één pagina (SPA) voltooid en een omleidings-URI geconfigureerd waarnaar de client wordt omgeleid en eventuele beveiligings tokens worden verzonden. Door de omleidings-URI te configureren met behulp van de tegel **toepassing met één pagina** in het deel venster **een platform toevoegen** , wordt de registratie van de toepassing geconfigureerd ter ondersteuning van de autorisatie code stroom met PKCE en CORS.

Volg de [zelf studie](tutorial-v2-javascript-auth-code.md) voor meer informatie.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Omleidings-URI: [MSAL.js 1,0 met impliciete stroom](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Volg deze stappen om een omleidings-URI toe te voegen voor een app met één pagina die gebruikmaakt van MSAL.js 1,3 of eerder en de impliciete toekennings stroom. Toepassingen die gebruikmaken van MSAL.js 1,3 of eerder, bieden geen ondersteuning voor de verificatie code stroom.

1. Selecteer in de Azure Portal de app-registratie die u eerder hebt gemaakt in [de app-registratie maken](#create-the-app-registration).
1. Selecteer onder **Beheren** achtereenvolgens **Verificatie** > **Een platform toevoegen**.
1. Selecteer onder **webtoepassingen** de tegel **toepassing met één pagina** .
1. Voer bij **omleidings-uri's** een [omleidings-URI](reply-url.md)in.
1. De **impliciete toekenning en hybride stromen** inschakelen:
    - Als uw toepassing zich aanmeldt bij gebruikers, selecteert u **id-tokens**.
    - Als uw toepassing ook een beveiligde web-API moet aanroepen, selecteert u **toegangs tokens**. Zie [id-tokens](id-tokens.md) en [toegangs tokens](access-tokens.md)voor meer informatie over deze token typen.
1. Selecteer **configureren** om het toevoegen van de omleidings-URI te volt ooien.

U hebt nu de registratie van uw toepassing met één pagina (SPA) voltooid en een omleidings-URI geconfigureerd waarnaar de client wordt omgeleid en eventuele beveiligings tokens worden verzonden. Als u een of beide **id-tokens** en **toegangs tokens** selecteert, hebt u de impliciete toekennings stroom ingeschakeld.

Volg de [zelf studie](tutorial-v2-javascript-spa.md) voor meer informatie.

## <a name="note-about-authorization-flows"></a>Opmerking over autorisatie stromen

Standaard maakt een registratie van een app die is gemaakt met behulp van de configuratie van een toepassings platform met één pagina, de autorisatie code stroom. Uw toepassing moet MSAL.js 2,0 of hoger gebruiken om gebruik te kunnen maken van deze stroom.

Zoals eerder vermeld, worden toepassingen met één pagina die gebruikmaken van MSAL.js 1,3 beperkt tot de impliciete toekennings stroom. De huidige [OAuth 2,0 best practices](v2-oauth2-auth-code-flow.md) raden het gebruik van de autorisatie code stroom in plaats van de impliciete stroom voor Spas. Met beperkte levens duur vernieuwings tokens kan uw toepassing ook worden aangepast aan de privacy-beperkingen van de [moderne browser cookie](reference-third-party-cookies-spas.md), zoals Safari ITP.

Wanneer al uw productie-apps met één pagina die worden vertegenwoordigd door een app-registratie, gebruikmaken van MSAL.js 2,0 en de autorisatie code stroom, schakelt u het deel venster **verificatie** van de impliciete toestemming in het Azure portal van de app-registratie uit. Toepassingen die MSAL.js 1. x gebruiken en de impliciete stroom kunnen blijven functioneren, maar als u de impliciete stroom ingeschakeld houdt (ingeschakeld).

## <a name="next-steps"></a>Volgende stappen

Configureer de code van uw app om de app-registratie te gebruiken die u in de vorige stappen hebt gemaakt: de [code configuratie van de app](scenario-spa-app-configuration.md).
