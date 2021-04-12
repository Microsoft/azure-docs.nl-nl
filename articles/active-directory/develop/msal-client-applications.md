---
title: Open bare en vertrouwelijke client-apps (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over open bare client-en vertrouwelijke client toepassingen in de micro soft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2ef29f6fe3403809d01fcea382474c514319b7c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063753"
---
# <a name="public-client-and-confidential-client-applications"></a>Open bare client-en vertrouwelijke client toepassingen
De micro soft Authentication Library (MSAL) definieert twee typen clients: open bare clients en vertrouwelijke clients. De twee client typen zijn herkenbaar aan de mogelijkheid om veilig te verifiëren met de autorisatie server en de vertrouwelijkheid van hun client referenties te hand haven. Azure AD Authentication Library (ADAL) gebruikt daarentegen de zogenaamde *verificatie context* (een verbinding met Azure AD).

- **Vertrouwelijke client toepassingen** zijn apps die worden uitgevoerd op servers (Web apps, Web API-apps of zelfs service/daemon-apps). Ze worden beschouwd als moeilijk te benaderen en daarom kan een toepassings geheim worden bewaard. Vertrouwelijke clients kunnen configuratie-en tijd geheimen bevatten. Elk exemplaar van de client heeft een afzonderlijke configuratie (inclusief client-ID en client geheim). Deze waarden zijn moeilijk te extra heren voor eind gebruikers. Een web-app is de meest voorkomende vertrouwelijke client. De client-ID wordt weer gegeven via de webbrowser, maar het geheim wordt alleen door gegeven in het back-upkanaal en nooit rechtstreeks zichtbaar.

    Vertrouwelijke client-apps: <BR>
    ![Web ](media/msal-client-applications/web-app.png) ![ -app Web API ](media/msal-client-applications/web-api.png) ![ daemon/service](media/msal-client-applications/daemon-service.png)

- **Open bare client toepassingen** zijn apps die worden uitgevoerd op apparaten of desktop computers of in een webbrowser. Ze worden niet vertrouwd om toepassings geheimen veilig te blijven, zodat ze alleen toegang hebben tot Web-Api's namens de gebruiker. (Alleen open bare client stromen worden ondersteund.) Open bare clients kunnen geen configuratie-tijd geheimen bevatten, zodat ze geen client geheimen hebben.

    Open bare client-apps: <BR>
    ![](media/msal-client-applications/desktop-app.png) ![ ](media/msal-client-applications/browserless-app.png) Mobiele app browserloze API voor desktop-apps ![](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> In MSAL.js zijn geen schei ding van open bare en vertrouwelijke client-apps.  MSAL.js vertegenwoordigt client-apps als apps op basis van gebruikers agent, open bare clients waarin de client code wordt uitgevoerd in een gebruikers agent zoals een webbrowser. Deze clients slaan geen geheimen op omdat de browser context geopend is.

## <a name="comparing-the-client-types"></a>De client typen vergelijken
Hier volgen enkele overeenkomsten en verschillen tussen open bare client-en vertrouwelijke client-apps:

- In beide soorten apps wordt een token cache voor gebruikers bijgehouden en kan een token op de achtergrond worden verkregen (wanneer het token al in de token cache staat). Vertrouwelijke client-apps hebben ook een app-token cache voor tokens die voor de app zelf zijn.
- Beide typen apps beheren gebruikers accounts en kunnen een account ophalen uit de gebruikers token cache, een account ophalen uit de id of een account verwijderen.
- Open bare client-apps hebben vier manieren om een token (vier verificatie stromen) te verkrijgen. Vertrouwelijke client-apps hebben drie manieren om een token te verkrijgen (en een manier om de URL van het eind punt van de identiteits provider te bemachtigen). Zie [tokens ophalen](msal-acquire-cache-tokens.md)voor meer informatie.

Als u ADAL hebt gebruikt, ziet u mogelijk dat, in tegens telling tot de verificatie context van ADAL, in MSAL de client-ID (ook wel de *toepassings-id* of *App-ID* genoemd) wordt door gegeven na de constructie van de toepassing. Het hoeft niet opnieuw te worden door gegeven wanneer de app een token ophaalt. Dit geldt voor zowel voor open bare als vertrouwelijke client-apps. Voor constructors van vertrouwelijke client-apps worden ook client referenties door gegeven: het geheim dat ze delen met de ID-provider.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over:
- [Configuratie opties voor client toepassing](msal-client-application-configuration.md)
- [Client toepassingen instantiëren met behulp van MSAL.NET](msal-net-initializing-client-applications.md)
- [Client toepassingen instantiëren met behulp van MSAL.js](msal-js-initializing-client-applications.md)
