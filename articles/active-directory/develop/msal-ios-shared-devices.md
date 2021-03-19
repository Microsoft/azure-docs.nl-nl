---
title: Modus voor gedeeld apparaat voor iOS-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over het inschakelen van de modus gedeeld apparaat zodat Frontline werk rollen een iOS-apparaat kunnen delen
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: c67c5d7b46c04e7f1aea020127ee798878c43d60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578784"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modus voor gedeeld apparaat voor iOS-apparaten

>[!IMPORTANT]
> Deze functie [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Frontline-werk rollen, zoals Retail Associates, leden van de vliegtuig bemanning en mede werkers van de Field service gebruiken vaak een gedeeld mobiel apparaat om hun werk uit te voeren. Deze gedeelde apparaten kunnen beveiligings Risico's opleveren als uw gebruikers hun wacht woord of pincode, opzettelijk of niet delen, om toegang te krijgen tot klant-en bedrijfs gegevens op het gedeelde apparaat.

In de modus gedeeld apparaat kunt u een apparaat met iOS 13 of hoger configureren zodat het eenvoudiger en veilig kan worden gedeeld door werk nemers. Werk nemers kunnen zich snel aanmelden en klant gegevens benaderen. Wanneer ze zijn voltooid met hun verschuiving of taak, kunnen ze zich afmelden bij het apparaat en het direct klaar voor gebruik door de volgende werk nemer.

De modus gedeeld apparaat biedt ook micro soft-beleid voor identiteits-back-ups van het apparaat.

Deze functie maakt gebruik van de [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md) voor het beheren van de gebruikers op het apparaat en voor het distribueren [van de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Een app voor een gedeelde apparaat modus maken

Als u een app voor gedeelde apparaten wilt maken, werken ontwikkel aars en beheerders van Cloud apparaten samen:

1. **Toepassings ontwikkelaars** schrijven een app met één account (apps met meerdere accounts worden niet ondersteund in de modus gedeeld apparaat) en schrijven code voor het afhandelen van dingen zoals het afmelden van gedeelde apparaten.

1. **Beheerders** van apparaten bereiden het apparaat voor met het delen van een Mobile Device Management (MDM)-provider, zoals Microsoft intune voor het beheren van de apparaten in hun organisatie. Het MDM duwt de Microsoft Authenticator-app naar de apparaten en schakelt gedeelde modus voor elk apparaat in via een profiel update op het apparaat. Met deze instelling voor de gedeelde modus wordt het gedrag van de ondersteunde apps op het apparaat gewijzigd. Deze configuratie van de MDM-provider stelt de modus voor gedeelde apparaten voor het apparaat in en schakelt de [micro soft Enter PRISE SSO-invoeg toepassing in voor Apple-apparaten](apple-sso-plugin.md) die vereist zijn voor de modus gedeeld apparaat.

1. [**Vereist tijdens alleen open bare preview**] Een gebruiker met de rol van beheerder van het [Cloud apparaat](../roles/permissions-reference.md#cloud-device-administrator) moet vervolgens de [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) starten en hun apparaat toevoegen aan de organisatie.

    Als u het lidmaatschap van uw organisatie rollen wilt configureren in de Azure portal: **Azure Active Directory**  >  **rollen en Administrators** van de  >  **Cloud apparaat-beheerder**

In de volgende secties wordt beschreven hoe u uw toepassing bijwerkt voor de ondersteuning van de modus gedeeld apparaat.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>InTune gebruiken om de modus gedeelde apparaten & SSO-extensie in te scha kelen

> [!NOTE]
> De volgende stap is alleen vereist tijdens de open bare preview-versie.

Het apparaat moet worden geconfigureerd voor de ondersteuning van de modus gedeelde apparaten. De app moet iOS 13 + geïnstalleerd hebben en MDM-registratie zijn. MDM-configuratie moet ook de [micro soft Enter PRISE SSO-invoeg toepassing inschakelen voor Apple-apparaten](apple-sso-plugin.md). Zie de [Apple-video](https://developer.apple.com/videos/play/tech-talks/301/)voor meer informatie over SSO-extensies.

1. Vertel in de intune-configuratie Portal het apparaat om de [micro soft Enter PRISE SSO-invoeg toepassing](apple-sso-plugin.md) in te scha kelen voor Apple-apparaten met de volgende configuratie:

    - **Type**: omleiden
    - **Extensie-id**: com. micro soft. azureauthenticator. ssoextension
    - **Team-ID**: (dit veld is niet nodig voor IOS)
    - **Url's**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **Aanvullende gegevens die moeten worden geconfigureerd**:
      - Sleutel: sharedDeviceMode
      - Type: Booleaans
      - Waarde: True

    Zie de [intune-configuratie documentatie](/intune/configuration/ios-device-features-settings)voor meer informatie over het configureren van met intune.

1. Configureer vervolgens uw MDM om de Microsoft Authenticator-app naar uw apparaat te pushen via een MDM-profiel.

    Stel de volgende configuratie opties in om de modus gedeeld apparaat in te scha kelen:

    - Configuratie 1:
      - Sleutel: sharedDeviceMode
      - Type: Booleaans
      - Waarde: True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Uw iOS-toepassing wijzigen ter ondersteuning van de modus gedeeld apparaat

Uw gebruikers zijn afhankelijk van u om ervoor te zorgen dat hun gegevens niet naar een andere gebruiker worden gelekt. De volgende secties geven nuttige signalen om aan te geven dat er een wijziging is opgetreden in uw toepassing en moet worden afgehandeld.

U bent verantwoordelijk voor het controleren van de status van de gebruiker op het apparaat telkens wanneer uw app wordt gebruikt, en vervolgens de gegevens van de vorige gebruiker te wissen. Dit geldt ook als het opnieuw wordt geladen vanaf de achtergrond in meerdere taken.

Bij wijziging van een gebruiker moet u ervoor zorgen dat de gegevens van de vorige gebruiker worden gewist en dat alle in de cache opgeslagen gegevens die in uw toepassing worden weer gegeven, worden verwijderd. U wordt ten zeerste aangeraden om en uw bedrijf een beveiligings beoordelings proces uit te voeren nadat u uw app hebt bijgewerkt om de modus voor gedeeld apparaat te ondersteunen.

### <a name="detect-shared-device-mode"></a>Modus gedeelde apparaten detecteren

Het detecteren van de modus gedeeld apparaat is belang rijk voor uw toepassing. Veel toepassingen vereisen een wijziging in hun gebruikers ervaring (UX) wanneer de toepassing wordt gebruikt op een gedeeld apparaat. Uw toepassing kan bijvoorbeeld een functie voor het registreren hebben die niet geschikt is voor een Frontline-werk nemer omdat deze waarschijnlijk al een account heeft. Mogelijk wilt u ook extra beveiliging toevoegen aan de verwerking van gegevens van uw toepassing als deze wordt weer gegeven in de modus gedeeld apparaat.

Gebruik de `getDeviceInformationWithParameters:completionBlock:` API in de `MSALPublicClientApplication` om te bepalen of een app wordt uitgevoerd op een apparaat in de modus gedeeld apparaat.

De volgende code fragmenten bevatten voor beelden van het gebruik van de `getDeviceInformationWithParameters:completionBlock:` API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>De aangemelde gebruiker ophalen en bepalen of een gebruiker op het apparaat heeft gewijzigd

Een ander belang rijk onderdeel van de ondersteunende modus voor gedeelde apparaten is het bepalen van de status van de gebruiker op het apparaat en het wissen van toepassings gegevens als een gebruiker heeft gewijzigd of als er op het apparaat geen gebruiker aanwezig is. U bent zelf verantwoordelijk voor het garanderen van gegevens die niet aan een andere gebruiker worden gelekt.

U kunt `getCurrentAccountWithParameters:completionBlock:` API gebruiken om een query uit te zoeken op het account dat momenteel is aangemeld op het apparaat.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globaal aanmelden als een gebruiker

Wanneer een apparaat is geconfigureerd als een gedeeld apparaat, kan uw toepassing de API aanroepen `acquireTokenWithParameters:completionBlock:` om het account aan te melden. Het account is wereld wijd beschikbaar voor alle apps die in aanmerking komen op het apparaat na de eerste app-ondertekening in het account.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globaal afmelden als een gebruiker

Met de volgende code wordt het aangemelde account verwijderd en worden de tokens in de cache gewist van niet alleen de app, maar ook van het apparaat dat zich in de modus gedeelde apparaten bevindt. Dit betekent echter niet dat de *gegevens* van uw toepassing worden gewist. U moet de gegevens uit uw toepassing wissen, maar ook de gegevens in de cache wissen die uw toepassing kan worden weer gegeven voor de gebruiker.

#### <a name="clear-browser-state"></a>Browser status wissen

> [!NOTE]
> De volgende stap is alleen vereist tijdens de open bare preview-versie.

In deze open bare preview-versie wordt in de [micro soft Enter PRISE SSO-invoeg toepassing voor Apple-apparaten](apple-sso-plugin.md) alleen status voor toepassingen gewist. De status wordt niet gewist in de Safari-browser. We raden u aan om de browser sessie hand matig te wissen om ervoor te zorgen dat er geen traceringen van de gebruikers status achterblijven. U kunt de optionele `signoutFromBrowser` eigenschap hieronder weer gegeven gebruiken om cookies te wissen. Dit zorgt ervoor dat de browser kort wordt gestart op het apparaat.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Volgende stappen

Als u de modus gedeeld apparaat in actie wilt zien, bevat het volgende code voorbeeld in GitHub een voor beeld van het uitvoeren van een Frontline worker-app op een iOS-apparaat in de modus gedeeld apparaat:

[Voor beeld van MSAL iOS Swift Microsoft Graph-API](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
