---
title: Een mobiele app bouwen die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept (overzicht)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f60e21f98092df65ef01e68fe9c2e64833a24855
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063209"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Een mobiele app die web-API's aanroept

Meer informatie over het bouwen van een mobiele app die web-Api's aanroept.

## <a name="getting-started"></a>Aan de slag

Als u dat nog niet hebt gedaan, maakt u uw eerste toepassing door een Snelstartgids te volt ooien:

- [Snelstartgids: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Android-app](./quickstart-v2-android.md)
- [Snelstartgids: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een iOS-app](./quickstart-v2-ios.md)
- [Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Xamarin IOS-en Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) (github)

## <a name="overview"></a>Overzicht

Een persoonlijke, naadloze gebruikers ervaring is essentieel voor mobiele apps.  Met micro soft Identity platform kunnen mobiele ontwikkel aars die ervaring voor iOS-en Android-gebruikers maken. Uw toepassing kan zich aanmelden Azure Active Directory-gebruikers (Azure AD), persoonlijke Microsoft-account gebruikers en Azure AD B2C gebruikers. Het kan ook tokens verkrijgen om namens u een web-API aan te roepen. Voor het implementeren van deze stromen gebruikt u de micro soft Authentication Library (MSAL). MSAL implementeert de industrie standaard [OAuth 2.0-autorisatie code stroom](v2-oauth2-auth-code-flow.md).

![Daemon-apps](./media/scenarios/mobile-app.svg)

Overwegingen voor mobiele apps:

- **Gebruikers ervaring is sleutel**: Hiermee staat u gebruikers toe de waarde van uw app te zien voordat u zich aanmeldt. Vraag alleen de vereiste machtigingen aan.
- **Ondersteuning voor alle gebruikers configuraties**: veel mobiele zakelijke gebruikers moeten voldoen aan beleid voor voorwaardelijke toegang en nalevings beleid voor apparaten. Zorg ervoor dat u deze belang rijke scenario's ondersteunt.
- **Eenmalige aanmelding (SSO) implementeren**: met behulp van MSAL en het micro soft-identiteits platform kunt u eenmalige aanmelding inschakelen via de browser van het apparaat of Microsoft Authenticator (en intune-bedrijfsportal op Android).
- **Modus voor gedeeld apparaat implementeren**: u kunt uw toepassing gebruiken in scenario's voor gedeelde apparaten, bijvoorbeeld zieken huizen, productie, detail handel en Financiën. [Meer informatie over de ondersteuning van de modus gedeelde apparaten](msal-shared-devices.md).

## <a name="specifics"></a>Opsporingsgegevens

Houd rekening met de volgende overwegingen wanneer u een mobiele app bouwt op het micro soft Identity-platform:

- Afhankelijk van het platform is het mogelijk dat de gebruiker zich voor de eerste keer aanmeldt dat gebruikers zich moeten aanmelden. IOS vereist bijvoorbeeld dat Apps gebruikers interactie laten zien wanneer ze voor de eerste keer SSO gebruiken via Microsoft Authenticator (en Intune-bedrijfsportal op Android).
- Op iOS en Android kan MSAL een externe browser gebruiken om gebruikers aan te melden. De externe browser wordt mogelijk boven op de app weer gegeven.
- Gebruik nooit een geheim in een mobiele toepassing. In deze toepassingen zijn geheimen toegankelijk voor alle gebruikers.

## <a name="recommended-reading"></a>Aanbevolen Lees bewerkingen

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [app-registratie](scenario-mobile-app-registration.md).
