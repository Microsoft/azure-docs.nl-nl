---
title: Cookiedefinities
titleSuffix: Azure AD B2C
description: Bevat definities voor de cookies die in Azure Active Directory B2C worden gebruikt.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e44e2e1eb37e808e60134a6fba5051552e84029
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85389340"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Cookie definities voor Azure AD B2C

De volgende secties bevatten informatie over de cookies die in Azure Active Directory B2C (Azure AD B2C) worden gebruikt.

## <a name="samesite"></a>SameSite

De B2C-service van Microsoft Azure AD is compatibel met SameSite-browser configuraties, inclusief ondersteuning voor `SameSite=None` met het- `Secure` kenmerk.

Webbrowsers kunnen de toegang tot sites beveiligen door een nieuw beveiligd standaard model toe te passen dat alle cookies moet worden beschermd tegen externe toegang tenzij anders wordt aangegeven. De Chrome-browser is de eerste voor het implementeren van deze wijziging, vanaf [Chrome 80 in februari 2020](https://www.chromium.org/updates/same-site). Voor meer informatie over het voorbereiden van de wijziging in Chrome raadpleegt u [ontwikkel aars: bereid u voor op nieuwe SameSite = geen; Beveiligde cookie-instellingen](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) in de chroom-blog.

Ontwikkel aars moeten de nieuwe cookie-instelling gebruiken `SameSite=None` om cookies voor toegang tussen sites aan te wijzen. Wanneer het `SameSite=None` kenmerk aanwezig is, moet er een extra `Secure` kenmerk worden gebruikt, zodat cookies voor meerdere locaties alleen toegankelijk zijn via HTTPS-verbindingen. Valideer en test al uw toepassingen, met inbegrip van de toepassingen die gebruikmaken van Azure AD B2C.

Zie voor meer informatie:

* [SameSite-cookiewijzigingen in Chrome-browser verwerken](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Effect op websites van de klant en micro soft-Services en-producten in Chrome versie 80 of hoger](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

De volgende tabel geeft een lijst van de cookies die worden gebruikt in Azure AD B2C.

| Name | Domain | Verloopdatum | Doel |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Einde van [browser sessie](session-behavior.md) | Bevat gegevens van het lidmaatschap van gebruikers via tenants. De tenants een gebruiker is lid van en het niveau van lidmaatschap (beheerder of gebruiker). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het routeren van aanvragen naar het geschikte productie-exemplaar. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het bijhouden van de trans acties (aantal authenticatie aanvragen naar Azure AD B2C) en de huidige trans actie. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het onderhouden van de SSO-sessie. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md), geslaagde verificatie | Wordt gebruikt voor het onderhouden van de status van de aanvraag. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Token voor aanvraag vervalsing op meerdere sites gebruikt voor CRSF-beveiliging. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor Azure AD B2C netwerk routering. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Gebruikt voor het opslaan van lidmaatschaps gegevens voor de resource provider Tenant. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het opslaan van de relay-cookie. |
