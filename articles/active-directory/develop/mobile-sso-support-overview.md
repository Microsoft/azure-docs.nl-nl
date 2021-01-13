---
title: Ondersteuning voor eenmalige aanmelding en app-beveiligings beleid in mobiele apps die u ontwikkelt | Azure
titleSuffix: Microsoft identity platform
description: Uitleg en overzicht van het bouwen van mobiele toepassingen die ondersteuning bieden voor eenmalige aanmelding en app-beveiligings beleid met behulp van het micro soft-identiteits platform en integreren met Azure Active Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165175"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Ondersteuning voor eenmalige aanmelding en app-beveiligings beleid in mobiele apps die u ontwikkelt

Eenmalige aanmelding (SSO) is een belang rijke aanbieding van het micro soft Identity platform en Azure Active Directory, waarmee u eenvoudig en veilig aanmeldingen voor gebruikers van uw app kunt leveren. Daarnaast wordt door app-beveiligings beleid (APP) ondersteuning geboden voor het belangrijkste beveiligings beleid waarmee de gegevens van uw gebruiker veilig blijven. Met deze functies worden de beveiligde gebruikers aanmeldingen en het beheer van de gegevens van uw app ingeschakeld.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

In dit artikel wordt uitgelegd waarom SSO en APP belang rijk zijn en biedt de richt lijnen op hoog niveau voor het bouwen van mobiele toepassingen die ondersteuning bieden voor deze functies. Dit geldt voor telefoon-en Tablet-apps. Als u een IT-beheerder bent die SSO wil implementeren in de Azure Active Directory Tenant van uw organisatie, raadpleegt u onze [richt lijnen voor het plannen van de implementatie van eenmalige aanmelding](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>Informatie over eenmalige aanmelding en app-beveiligings beleid

Met [eenmalige aanmelding (SSO)](../manage-apps/plan-sso-deployment.md) kan een gebruiker zich eenmalig aanmelden en toegang krijgen tot andere toepassingen zonder referenties opnieuw in te voeren. Hierdoor is het eenvoudiger om apps te openen en hoeven gebruikers geen lange lijsten met gebruikers namen en wacht woorden te onthouden. Als u het implementeert in uw app, is toegang tot en gebruik van uw app eenvoudiger.

Wanneer u eenmalige aanmelding inschakelt in uw app, worden nieuwe verificatie mechanismen die worden geleverd met moderne authenticatie, zoals [wacht woord-aanmeldingen](../authentication/concept-authentication-passwordless.md), ontgrendeld. Gebruikers namen en wacht woorden zijn een van de meest voorkomende aanvals vectoren tegen toepassingen, en het inschakelen van EENMALIGe aanmelding biedt u de mogelijkheid om dit risico te beperken door voorwaardelijke toegang of wacht woordloze aanmeldingen af te dwingen die extra beveiliging bieden of gebruikmaken van veiligere verificatie mechanismen. Ten slotte is het inschakelen van eenmalige aanmelding ook mogelijk voor [eenmalige afmelding](v2-protocols-oidc.md#single-sign-out). Dit is handig in situaties zoals werk toepassingen die worden gebruikt op gedeelde apparaten.

[App-beveiligings beleid (app)](/mem/intune/apps/app-protection-policy) zorgt ervoor dat de gegevens van een organisatie veilig en aanwezig blijven. Ze kunnen bedrijven hun gegevens in een app beheren en beveiligen en kunnen bepalen wie toegang heeft tot de app en de gegevens. Door app-beveiligings beleid te implementeren, kan uw app gebruikers verbinden met bronnen die worden beveiligd door beleid voor voorwaardelijke toegang en gegevens veilig overdragen van en naar andere beveiligde apps. Scenario's die zijn ontgrendeld door app-beveiligings beleid, zijn het vereisen van een pincode voor het openen van een app, het delen van gegevens tussen apps en het voor komen van het opslaan van app-gegevens op persoonlijke opslag locaties.

## <a name="implementing-single-sign-on"></a>Eenmalige aanmelding implementeren

We raden u aan het volgende te doen om ervoor te zorgen dat uw app kan profiteren van eenmalige aanmelding.

### <a name="use-the-microsoft-authentication-library-msal"></a>De micro soft Authentication Library (MSAL) gebruiken

De beste optie voor het implementeren van eenmalige aanmelding in uw toepassing is het gebruik van [de micro soft Authentication Library (MSAL)](msal-overview.md). Door MSAL te gebruiken, kunt u verificatie toevoegen aan uw app met minimale code-en API-aanroepen, de volledige functies van het [micro soft-identiteits platform](./index.yml)ophalen en micro soft de handhaving van een veilige verificatie oplossing laten afhandelen. MSAL voegt standaard SSO-ondersteuning toe voor uw toepassing. Daarnaast is het gebruik van MSAL een vereiste als u ook van plan bent om app-beveiligings beleid te implementeren.

> [!NOTE]
> Het is mogelijk om MSAL te configureren voor het gebruik van een Inge sloten webweergave. Hiermee voor komt u eenmalige aanmelding. Gebruik het standaard gedrag (dat wil zeggen, de webbrowser van het systeem) om er zeker van te zijn dat SSO werkt.

Als u momenteel de ADAL- [bibliotheek](../azuread-dev/active-directory-authentication-libraries.md) in uw toepassing gebruikt, raden we u ten zeerste [aan om deze te migreren naar MSAL](msal-migration.md), omdat [ADAL wordt afgeschaft](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

Voor iOS-toepassingen hebben we een [Snelstartgids](quickstart-v2-ios.md) waarin u kunt zien hoe u aanmeldingen kunt instellen met behulp van MSAL, evenals [richt lijnen voor het configureren van MSAL voor verschillende SSO-scenario's](single-sign-on-macos-ios.md).

Voor Android-toepassingen hebben we een [Snelstartgids](quickstart-v2-android.md) waarin u kunt zien hoe u aanmeldingen kunt instellen met behulp van MSAL en richt lijnen voor [het inschakelen van SSO voor meerdere apps op Android met behulp van MSAL](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>De systeem webbrowser gebruiken

Voor interactieve verificatie is een webbrowser vereist. Voor mobiele apps die gebruikmaken van moderne verificatie bibliotheken dan MSAL (dat wil zeggen, andere OpenID connect-verbinding of SAML-bibliotheken) of als u uw eigen verificatie code implementeert, moet u de systeem browser gebruiken als verificatie-Opper vlak om SSO in te scha kelen.

Google bevat richt lijnen voor het uitvoeren van dit in Android-toepassingen: [aangepaste Chrome-tabbladen-Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Apple bevat richt lijnen voor het uitvoeren van deze service in iOS-toepassingen: [een gebruiker verifiëren via een webservice | Apple-documentatie voor ontwikkel aars](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> De [SSO-invoeg toepassing voor Apple-apparaten](apple-sso-plugin.md) biedt SSO voor IOS-apps die gebruikmaken van Inge sloten webweergaven op beheerde apparaten met intune. We raden MSAL en System browser aan als de beste optie voor het ontwikkelen van apps die SSO inschakelen voor alle gebruikers, maar Hiermee wordt SSO mogelijk in sommige scenario's waarin dit niet mogelijk is.

## <a name="enable-app-protection-policies"></a>Beveiligings beleid voor apps inschakelen

Als u beveiligings beleid voor Apps wilt inschakelen, gebruikt u de [micro soft Authentication Library (MSAL)](msal-overview.md). MSAL is de verificatie-en autorisatie bibliotheek van het micro soft-identiteits platform en de intune SDK is ontwikkeld om samen met IT te werken.

Daarnaast moet u een Broker-app gebruiken voor verificatie. De Broker vereist dat de app toepassings-en apparaatgegevens verstrekt om de compatibiliteit van de app te garanderen. iOS-gebruikers gebruiken de [Microsoft Authenticator app](../user-help/user-help-auth-app-sign-in.md) en Android-gebruikers gebruiken de Microsoft Authenticator-app of de [bedrijfsportal-app](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) voor [brokered-verificatie](./msal-android-single-sign-on.md). MSAL gebruikt standaard een Broker als de eerste keuze voor het uitvoeren van een verificatie aanvraag, dus als u de Broker voor verificatie gebruikt, wordt deze automatisch ingeschakeld voor uw app wanneer MSAL out-of-the-box wordt gebruikt.

Voeg tot slot [de intune-SDK](/mem/intune/developer/app-sdk-get-started) toe aan uw app om beveiligings beleid voor apps in te scha kelen. De SDK voor het meest deel maakt gebruik van een interceptie model en past automatisch beleids regels voor app-beveiliging toe om te bepalen of acties die door de app worden uitgevoerd, zijn toegestaan. Er zijn ook Api's die u hand matig kunt aanroepen om de app te laten weten of er beperkingen gelden voor bepaalde acties.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Een Azure Active Directory implementatie van eenmalige aanmelding plannen](../manage-apps/plan-sso-deployment.md)
- [Procedure: SSO configureren in macOS en iOS](single-sign-on-macos-ios.md)
- [Micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten (preview-versie)](apple-sso-plugin.md)
- [Brokered-verificatie in Android](./msal-android-single-sign-on.md)
- [Autorisatie agenten en hoe ze kunnen worden ingeschakeld](./msal-android-single-sign-on.md)
- [Aan de slag met de Microsoft Intune App SDK](/mem/intune/developer/app-sdk-get-started)
- [De instellingen van de Intune App SDK configureren](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Met Microsoft Intune beveiligde apps](/mem/intune/apps/apps-supported-intune-apps)
