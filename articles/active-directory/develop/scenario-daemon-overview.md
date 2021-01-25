---
title: Een daemon-app bouwen die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 45af940382a261f1793583e471871b9cceed0a57
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753836"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: Een daemon-app die web-API's aanroept

Meer informatie over wat u nodig hebt om een daemon-toepassing te bouwen die web-Api's aanroept.

## <a name="overview"></a>Overzicht

Uw toepassing kan een token verkrijgen om een web-API namens zichzelf aan te roepen (niet namens een gebruiker). Dit scenario is nuttig voor daemon-toepassingen. De standaard OAuth 2,0- [client referenties](v2-oauth2-client-creds-grant-flow.md) worden verleend.

![Daemon-apps](./media/scenario-daemon-app/daemon-app.svg)

Hier volgen enkele voor beelden van gebruiks voorbeelden voor daemon-apps:

- Webtoepassingen die worden gebruikt voor het inrichten of beheren van gebruikers of het uitvoeren van batch processen in een directory
- Bureaublad toepassingen (zoals Windows-Services in Windows-of daemon-processen in Linux) die batch-taken uitvoeren, of een besturingssysteem service die op de achtergrond wordt uitgevoerd
- Web-Api's die directory's moeten bewerken, niet specifieke gebruikers

Er is een andere veelvoorkomende situatie waarbij niet-daemon-toepassingen gebruikmaken van client referenties: zelfs wanneer ze namens gebruikers handelen, moeten ze om technische redenen toegang hebben tot een web-API of een resource onder hun eigen identiteit. Een voor beeld is toegang tot geheimen in Azure Key Vault of Azure SQL Database voor een cache.

Toepassingen die een token voor hun eigen identiteiten verkrijgen:

- Zijn vertrouwelijke client toepassingen. Deze apps, gezien de toegang tot bronnen onafhankelijk van gebruikers, moeten hun identiteit bewijzen. Ze zijn ook in plaats daarvan gevoelige apps. Ze moeten worden goedgekeurd door de Tenant beheerders van de Azure Active Directory (Azure AD).
- Een geheim (toepassings wachtwoord of-certificaat) hebt geregistreerd bij Azure AD. Dit geheim wordt door gegeven tijdens het aanroepen van Azure AD om een token op te halen.

## <a name="specifics"></a>Opsporingsgegevens

> [!IMPORTANT]
>
> - Gebruikers kunnen niet communiceren met een daemon-toepassing. Een daemon-toepassing vereist een eigen identiteit. Met dit type toepassing wordt een toegangs token aangevraagd met behulp van de toepassings-ID en worden de toepassings-id's, referenties (wacht woord of certificaat) en de URI van de toepassings-ID naar Azure AD gepresenteerd. Na een geslaagde verificatie ontvangt de daemon een toegangs token (en een vernieuwings token) van het micro soft Identity-platform. Dit token wordt vervolgens gebruikt om de Web-API aan te roepen (en wordt naar behoefte vernieuwd).
> - Omdat gebruikers niet kunnen communiceren met daemon-toepassingen, is het niet mogelijk om een incrementele toestemming te nemen. Alle vereiste API-machtigingen moeten worden geconfigureerd bij de registratie van de toepassing. De code van de toepassing verzoekt alleen statisch gedefinieerde machtigingen. Dit betekent ook dat daemon-toepassingen geen ondersteuning bieden voor incrementele toestemming.

Voor ontwikkel aars heeft de end-to-end-ervaring voor dit scenario de volgende aspecten:

- Daemon-toepassingen kunnen alleen worden gebruikt in azure AD-tenants. Het kan zinvol zijn om een daemon-toepassing te maken die probeert persoonlijke micro soft-accounts te bewerken. Als u een LOB-app (line-of-Business) bent, maakt u uw daemon-app in uw Tenant. Als u een ISV bent, wilt u mogelijk een multi tenant-daemon-toepassing maken. Elke Tenant beheerder moet toestemming geven.
- Tijdens de registratie van de [toepassing](./scenario-daemon-app-registration.md)is de antwoord-URI niet nodig. U moet geheimen of certificaten of ondertekende verklaringen delen met Azure AD. U moet ook toepassings machtigingen aanvragen en toestemming van de beheerder verlenen om deze app-machtigingen te gebruiken.
- De [configuratie](./scenario-daemon-app-configuration.md) van de toepassing moet client referenties opgeven die worden gedeeld met Azure ad tijdens de registratie van de toepassing.
- Het [bereik](scenario-daemon-acquire-token.md#scopes-to-request) dat wordt gebruikt om een token met de client referentie stroom te verkrijgen, moet een statisch bereik zijn.

## <a name="recommended-reading"></a>Aanbevolen Lees bewerkingen

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [app-registratie](./scenario-daemon-app-registration.md).
