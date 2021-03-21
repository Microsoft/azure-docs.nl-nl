---
title: Wijzigingen in SameSite-cookies afhandelen in de Chrome-browser | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verwerken van wijzigingen in SameSite-cookies in de Chrome-browser.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94444908"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>SameSite-cookiewijzigingen in Chrome-browser verwerken

## <a name="what-is-samesite"></a>Wat is SameSite?

`SameSite` is een eigenschap die kan worden ingesteld in HTTP-cookies om CSRF-aanvallen (cross-site request vervalsing) in webtoepassingen te voor komen:

- Wanneer `SameSite` is ingesteld op **slordig**, wordt de cookie verzonden in aanvragen binnen dezelfde site en in Get-aanvragen van andere sites. Het wordt niet verzonden in GET-aanvragen die meerdere domeinen zijn.
- Een waarde van **strikt** zorgt ervoor dat de cookie alleen binnen dezelfde site in aanvragen wordt verzonden.

De `SameSite` waarde is standaard niet ingesteld in browsers en daarom zijn er geen beperkingen voor cookies die in aanvragen worden verzonden. Een toepassing moet zich bij de CSRF-beveiliging aanmelden door het instellen van **slordig** of **strikt** volgens hun vereisten.

## <a name="samesite-changes-and-impact-on-authentication"></a>Wijzigingen in SameSite en invloed op verificatie

Recente [updates van de standaarden op SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) stellen het beveiligen van apps voor, door het standaard gedrag van `SameSite` te maken wanneer er geen waarde is ingesteld op slordig. Deze beperking betekent dat cookies worden beperkt voor HTTP-aanvragen, behalve het ophalen van andere sites. Daarnaast wordt er geen waarde van **geen** geïntroduceerd om beperkingen te verwijderen voor het verzenden van cookies. Deze updates worden binnenkort uitgebracht in een toekomstige versie van de Chrome-browser.

Wanneer Web-apps met het micro soft-identiteits platform worden geverifieerd met de antwoord modus ' form_post ', reageert de aanmeldings server op de toepassing met behulp van een HTTP POST om de tokens of de verificatie code te verzenden. Omdat deze aanvraag een aanvraag voor meerdere domeinen is (van `login.microsoftonline.com` naar het exemplaar van uw domein `https://contoso.com/auth` ), vallen cookies die door uw app zijn ingesteld nu onder de nieuwe regels in Chrome. De cookies die moeten worden gebruikt in scenario's met meerdere sites zijn cookies die de *status* en *nonce* -waarden bevatten, die ook in de aanmeldings aanvraag worden verzonden. Er zijn andere cookies verwijderd door Azure AD om de sessie te bewaren.

Als u uw web-apps niet bijwerkt, zal dit nieuwe gedrag leiden tot mislukte verificaties.

## <a name="mitigation-and-samples"></a>Risico beperking en voor beelden

Om de verificatie fouten op te lossen, kunnen Web-apps die met het micro soft Identity-platform worden geverifieerd, de `SameSite` eigenschap instellen op `None` cookies die worden gebruikt in scenario's met meerdere domeinen wanneer ze worden uitgevoerd in de Chrome-browser.
Andere browsers (Zie [hier](https://www.chromium.org/updates/same-site/incompatible-clients) voor een volledige lijst) Volg het vorige gedrag van `SameSite` en bevat geen cookies als `SameSite=None` is ingesteld.
Daarom moet de waarde alleen op Chrome worden ingesteld voor het ondersteunen van authenticatie op meerdere web-apps, `SameSite` `None` en laat de waarde leeg in andere browsers.

Deze benadering wordt uitgelegd in onze code voorbeelden hieronder.

# <a name="net"></a>[.NET](#tab/dotnet)

In de onderstaande tabel ziet u de pull-aanvragen die hebben gewerkt aan de SameSite-wijzigingen in onze ASP.NET en ASP.NET Core-voor beelden.

| Voorbeeld | Pull-aanvraag |
| ------ | ------------ |
|  [Incrementele zelf studie voor de web-app ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Dezelfde site cookie oplossen #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [Voor beeld van ASP.NET MVC-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Dezelfde site cookie oplossen #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-DotNet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Dezelfde site cookie oplossen #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

Zie ook voor meer informatie over het afhandelen van SameSite-cookies in ASP.NET en ASP.NET Core:

- [Werken met SameSite-cookies in ASP.net core](/aspnet/core/security/samesite) .
- [ASP.NET-blog op SameSite-probleem](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Voorbeeld |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Voorbeeld | Pull-aanvraag |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Dezelfde site cookie oplossen #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Dezelfde site cookie oplossen #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over SameSite en het scenario voor de web-app:

- [Veelgestelde vragen over Google Chrome op SameSite](https://www.chromium.org/updates/same-site/faq)

- [Pagina chroom SameSite](https://www.chromium.org/updates/same-site)

- [Scenario: Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)