---
title: Ondersteuning voor verificatie zonder wacht woord met FIDO2-sleutels in apps die u ontwikkelt | Azure
titleSuffix: Microsoft identity platform
description: In deze implementatie handleiding wordt uitgelegd hoe u verificatie zonder wacht woord ondersteunt met FIDO2-beveiligings sleutels in de toepassingen die u ontwikkelt
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: f63d7aed75b14f5f008a639d667d8806b233b9fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174595"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Ondersteuning voor verificatie zonder wacht woord met FIDO2-sleutels in apps die u ontwikkelt

Deze configuraties en aanbevolen procedures helpen u bij het voor komen van veelvoorkomende scenario's waarin FIDO2 met een [wacht woord zonder authenticatie](../../active-directory/authentication/concept-authentication-passwordless.md) beschikbaar wordt gesteld aan gebruikers van uw toepassingen.

## <a name="general-best-practices"></a>Algemene best practices

### <a name="domain-hints"></a>Domein hints

Gebruik geen domein hint om de detectie van de [Home-realm](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)over te slaan. Deze functie is bedoeld om aanmeldingen meer gestroomlijnd te maken, maar de federatieve id-provider biedt mogelijk geen ondersteuning voor verificatie zonder wacht woord.

### <a name="requiring-specific-credentials"></a>Specifieke referenties vereisen

Als u SAML gebruikt, geeft u niet op dat een wacht woord vereist is [met behulp van het RequestedAuthnContext-element](single-sign-on-saml-protocol.md#requestauthncontext).

Het RequestedAuthnContext-element is optioneel. om dit op te lossen, kunt u het verwijderen uit uw SAML-verificatie aanvragen. Dit is een algemene best practice, omdat het gebruik van dit element ook kan verhinderen dat andere verificatie opties, zoals multi-factor Authentication, goed werken.

### <a name="using-the-most-recently-used-authentication-method"></a>De meest recent gebruikte verificatie methode gebruiken

De aanmeldings methode die het meest recent door een gebruiker is gebruikt, wordt eerst weer gegeven. Dit kan leiden tot Verwar ring wanneer gebruikers van mening zijn dat ze de eerste optie moeten gebruiken. Ze kunnen echter een andere optie kiezen door ' andere manieren om u aan te melden ' te selecteren, zoals hieronder wordt weer gegeven.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Afbeelding van de gebruikers authenticatie-ervaring met markering van de knop waarmee de gebruiker de verificatie methode kan wijzigen.":::

## <a name="platform-specific-best-practices"></a>Platformspecifieke aanbevolen procedures

### <a name="desktop"></a>Desktop

De aanbevolen opties voor het implementeren van verificatie zijn in volg orde:

- .NET-desktop toepassingen die gebruikmaken van de micro soft Authentication Library (MSAL), moeten gebruikmaken van de Windows Authentication Manager (WAM). Deze integratie en de voor delen ervan zijn [beschreven op github](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- Gebruik [WebView2](/microsoft-edge/webview2/) om FIDO2 te ondersteunen in een Inge sloten browser.
- Gebruik de systeem browser. De MSAL-bibliotheken voor desktop platforms gebruiken deze methode standaard. U kunt onze pagina in FIDO2 browser compatibiliteit raadplegen om te controleren of de browser die u gebruikt FIDO2-verificatie ondersteunt.

### <a name="mobile"></a>Mobiel

Vanaf februari 2021 wordt FIDO2 momenteel niet ondersteund voor systeem eigen iOS-of Android-apps, maar dit is in ontwikkeling.

Om toepassingen voor te bereiden voor de beschik baarheid, en als algemeen best practice, moeten iOS-en Android-toepassingen MSAL gebruiken met de standaard configuratie van de webbrowser van het systeem.

Als u geen gebruik maakt van MSAL, moet u nog steeds de systeem webbrowser voor verificatie gebruiken. Functies als eenmalige aanmelding en voorwaardelijke toegang zijn afhankelijk van een gedeeld weboppervlak dat wordt meegeleverd door de webbrowser van het systeem. Dit betekent dat het gebruik van [Chrome aangepaste tabbladen](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) of [het verifiëren van een gebruiker via een webservice | Apple-documentatie voor ontwikkel aars](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (Ios).

### <a name="web-and-single-page-apps"></a>Webtoepassingen en apps met één pagina

De beschik baarheid van FIDO2 met een wacht woordloze verificatie voor toepassingen die worden uitgevoerd in een webbrowser is afhankelijk van de combi natie van browser en platform. U kunt de [FIDO2-compatibiliteits matrix](../authentication/fido2-compatibility.md) raadplegen om te controleren of de combi natie die uw gebruikers ondervindt, wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

[Verificatie opties met een wacht woord voor Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)