---
title: Aanmeldings stroom van de app met het micro soft-identiteits platform | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de aanmeldings stroom van web-, desktop-en Mobile-apps in het micro soft Identity-platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1f9f330ab140fa66b5a66a112c47ca2a68ba56bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755694"
---
# <a name="app-sign-in-flow-with-the-microsoft-identity-platform"></a>Aanmeldings flow voor apps met het micro soft Identity-platform

In dit onderwerp wordt de basis-aanmeldings stroom voor web-, desktop-en Mobile-apps beschreven met behulp van micro soft Identity platform. Zie [verificatie stromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over aanmeldings scenario's die worden ondersteund door het micro soft Identity-platform.

## <a name="web-app-sign-in-flow"></a>Aanmeldings stroom voor web-apps

Wanneer een gebruiker in de browser naar een web-app navigeert, gebeurt het volgende:

* De web-app bepaalt of de gebruiker is geverifieerd.
* Als de gebruiker niet is geverifieerd, wordt de web-app gemachtigd om zich aan te melden bij de gebruiker in azure AD. Deze aanmelding voldoet aan het beleid van de organisatie. Dit kan ertoe leiden dat de gebruiker de referenties invoert met behulp van [multi-factor Authentication](../authentication/concept-mfa-howitworks.md) (ook wel twee ledige verificatie of twee ledige), of dat er geen wacht woord wordt gebruikt (bijvoorbeeld met Windows hello).
* De gebruiker wordt gevraagd toestemming te geven voor de toegang die de client-app nodig heeft. Daarom moeten client-apps worden geregistreerd bij Azure AD, zodat het micro soft Identity-platform tokens kan leveren die de toegang hebben die de gebruiker heeft ingestemd.

Wanneer de gebruiker is geverifieerd:

* Het micro soft Identity-platform verzendt een token naar de web-app.
* Er wordt een cookie opgeslagen dat is gekoppeld aan het domein van Azure AD. Dit bevat de identiteit van de gebruiker in het cookie jar van de browser. De volgende keer dat een app de browser gebruikt om te navigeren naar het micro soft Identity platform Authorization-eind punt, geeft de browser de cookie weer zodat de gebruiker zich niet opnieuw hoeft aan te melden. Dit is ook de manier waarop SSO wordt behaald. De cookie wordt gemaakt door Azure AD en kan alleen worden geïnterpreteerd door Azure AD.
* De web-app valideert vervolgens het token. Als de validatie slaagt, wordt de beveiligde pagina weer gegeven in de web-app en wordt een sessie cookie in het cookie jar van de browser opgeslagen. Wanneer de gebruiker naar een andere pagina navigeert, weet de web-app dat de gebruiker is geverifieerd op basis van de sessie cookie.

In het volgende sequentie diagram wordt deze interactie samenvatten:

![verificatie proces van de web-app](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hoe een web-app bepaalt of de gebruiker is geverifieerd

Web app-ontwikkel aars kunnen aangeven of voor alle of alleen bepaalde pagina's verificatie is vereist. In ASP.NET/ASP.NET core wordt dit bijvoorbeeld gedaan door het kenmerk toe te voegen `[Authorize]` aan de controller acties.

Met dit kenmerk wordt ASP.NET gecontroleerd op de aanwezigheid van een sessie cookie met de identiteit van de gebruiker. Als er geen cookie aanwezig is, stuurt ASP.NET de verificatie door naar de opgegeven id-provider. Als de ID-provider Azure AD is, wordt de verificatie door de web-app omgeleid naar `https://login.microsoftonline.com` , waardoor een dialoog venster voor aanmelden wordt weer gegeven.

### <a name="how-a-web-app-delegates-sign-in-to-the-microsoft-identity-platform-and-obtains-a-token"></a>Hoe een web-app zich aanmeldt bij het micro soft Identity-platform en een token verkrijgt

Gebruikers verificatie gebeurt via de browser. Het OpenID Connect-protocol gebruikt standaard berichten van het HTTP-protocol.

* De web-app stuurt een HTTP 302 (omleiding) naar de browser om het micro soft Identity-platform te gebruiken.
* Wanneer de gebruiker is geverifieerd, verzendt het micro soft Identity-platform het token naar de web-app met behulp van een omleiding via de browser.
* De omleiding wordt verzorgd door de web-app in de vorm van een omleidings-URI. Deze omleidings-URI is geregistreerd bij het Azure AD-toepassings object. Er kunnen verschillende omleidings-Uri's zijn omdat de toepassing kan worden geïmplementeerd op verschillende Url's. De web-app moet dus ook de omleidings-URI opgeven die moet worden gebruikt.
* Azure AD controleert of de omleidings-URI die door de web-app is verzonden, een van de geregistreerde omleidings-Uri's voor de app is.

## <a name="desktop-and-mobile-app-sign-in-flow"></a>Aanmeldings stroom voor Desk tops en mobiele apps

De hierboven beschreven stroom geldt, met kleine verschillen, voor desktop-en mobiele toepassingen.

Desktop-en mobiele toepassingen kunnen een Inge sloten Webbe sturings element of een systeem browser gebruiken voor verificatie. In het volgende diagram ziet u hoe een bureau blad of een mobiele app de micro soft Authentication Library (MSAL) gebruikt om toegangs tokens te verkrijgen en Web-Api's aan te roepen.

![Bureau blad-app hoe deze eruitziet](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL maakt gebruik van een browser om tokens op te halen. Net als bij Web-apps wordt de verificatie overgedragen aan het micro soft Identity-platform.

Omdat Azure AD dezelfde identiteits cookie in de browser opslaat als voor web-apps, wordt door de systeem eigen of de mobiele app, direct SSO met de bijbehorende Web-app, gebruikt.

MSAL maakt standaard gebruik van de systeem browser. De uitzonde ring is .NET Framework desktop toepassingen waarbij een Inge sloten besturings element wordt gebruikt om een meer geïntegreerde gebruikers ervaring te bieden.

## <a name="next-steps"></a>Volgende stappen

Voor andere onderwerpen met betrekking tot de basis van verificatie en autorisatie:

* Zie [Verificatie versus autorisatie](authentication-vs-authorization.md) voor meer informatie over de basis concepten van verificatie en autorisatie in het micro soft Identity-platform.
* Zie [beveiligings tokens](security-tokens.md) voor meer informatie over het gebruik van toegangs tokens, het vernieuwen van tokens en id-tokens in verificatie en autorisatie.
* Zie [toepassings model](application-model.md) voor meer informatie over het proces van het registreren van uw toepassing, zodat deze kan worden geïntegreerd met het micro soft Identity-platform.

Meer informatie over de aanmeldings flow voor apps:

* Zie [verificatie stromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over andere scenario's voor het verifiëren van gebruikers die worden ondersteund door het micro soft Identity-platform.
* Zie [MSAL-bibliotheken](msal-overview.md) voor meer informatie over de micro soft-bibliotheken die u helpen bij het ontwikkelen van toepassingen die werken met micro soft-accounts, Azure AD-accounts en Azure AD B2C gebruikers in één gestroomlijnd programmeer model.
