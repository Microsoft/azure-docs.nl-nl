---
title: Micro soft-identiteits platform verificatie bibliotheken | Azure
description: Lijst met client bibliotheken en middleware die compatibel zijn met het micro soft Identity-platform. Gebruik deze bibliotheken om ondersteuning toe te voegen voor gebruikers aanmelding (verificatie) en beveiligde web API-toegang (autorisatie) voor uw toepassingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 590e57d587c8e6e254811892b5c5e740b511c302
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104690650"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Micro soft Identity platform-verificatie bibliotheken

De volgende tabellen tonen ondersteuning van micro soft-verificatie bibliotheek voor verschillende toepassings typen. Deze bevatten koppelingen naar de bron code van de bibliotheek, waar u het pakket voor het project van uw app ophaalt, en of de bibliotheek gebruikers aanmelding (verificatie) ondersteunt, toegang tot beveiligde web-Api's (autorisatie) of beide.

Het micro soft-identiteits platform is gecertificeerd door de OpenID Connect-basis als een [gecertificeerde OpenID Connect-provider](https://openid.net/certification/). Als u liever een andere bibliotheek dan de micro soft Authentication Library (MSAL) of een andere door micro soft ondersteunde bibliotheek gebruikt, kiest u er een met een [gecertificeerde OpenID Connect Connect-implementatie](https://openid.net/developers/certified/).

Als u uw eigen implementatie op protocol niveau van [OAuth 2,0 of OpenID Connect Connect 1,0](active-directory-v2-protocols.md)hand matig wilt uitvoeren, moet u rekening houden met de beveiligings overwegingen voor elke standaard specificatie en een SDL-methodologie (Software Development Lifecycle) volgen, zoals [micro soft sdl][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Toepassing met één pagina (SPA)

Een toepassing met één pagina wordt volledig uitgevoerd op het browser oppervlak en de pagina gegevens (HTML, CSS en Java script) worden dynamisch of op basis van de laad tijd van de toepassing opgehaald. Het kan Web-Api's aanroepen om te communiceren met back-end-gegevens bronnen.

Omdat de code van een beveiligd-wachtwoord verificatie volledig in de browser wordt uitgevoerd, wordt dit beschouwd als een *open bare client* waarop geheimen niet veilig kunnen worden opgeslagen.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Webtoepassing

Een webtoepassing voert code uit op een server waarmee HTML, CSS en Java script worden gegenereerd en verzonden naar de webbrowser van een gebruiker. De identiteit van de gebruiker wordt behouden als een sessie tussen de browser van de gebruiker (de front-end) en de webserver (de back-end).

Omdat de code van een webtoepassing wordt uitgevoerd op de webserver, wordt deze beschouwd als een *vertrouwelijke client* waarop geheimen veilig kunnen worden opgeslagen.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Bureaublad toepassing

Een bureaublad toepassing is doorgaans binaire (gecompileerde) code die een gebruikers interface bedient en die is bedoeld om te worden uitgevoerd op het bureau blad van een gebruiker.

Omdat een bureaublad toepassing op het bureau blad van de gebruiker wordt uitgevoerd, wordt deze beschouwd als een *open bare client* waarop geheimen niet veilig kunnen worden opgeslagen.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Mobiele toepassing

Een mobiele toepassing is doorgaans een binaire (gecompileerde) code die een gebruikers interface oppereert en die is bedoeld om te worden uitgevoerd op het mobiele apparaat van een gebruiker.

Omdat een mobiele toepassing wordt uitgevoerd op het mobiele apparaat van de gebruiker, wordt deze beschouwd als een *open bare client* waarop geheimen niet veilig kunnen worden opgeslagen.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Service/daemon

Services en daemons worden vaak gebruikt voor server-naar-server en andere communicatie zonder toezicht (ook wel *headless*). Omdat er geen gebruiker is op het toetsen bord om referenties in te voeren of toestemming te geven voor toegang tot resources, verifiëren deze toepassingen zichzelf, niet als gebruiker, bij het aanvragen van geautoriseerde toegang tot de resources van een web-API.

Een service of daemon die op een server wordt uitgevoerd, wordt beschouwd als een *vertrouwelijke client* waarop de geheimen veilig kunnen worden opgeslagen.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de micro soft Authentication Library (MSAL)](msal-overview.md)voor meer informatie over de micro soft-verificatie bibliotheek.

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
