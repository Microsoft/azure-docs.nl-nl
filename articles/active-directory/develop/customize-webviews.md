---
title: Browsers aanpassen & webweergaven (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanpassen van de MSAL iOS/macOS-browser ervaring om gebruikers aan te melden.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85478124"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Procedure: browsers en webweergaven voor iOS/macOS aanpassen

Voor interactieve verificatie is een webbrowser vereist. Op iOS en macOS 10.15 + gebruikt de micro soft Authentication Library (MSAL) standaard de systeem webbrowser (die mogelijk boven op uw app wordt weer gegeven) om interactieve verificatie uit te voeren voor gebruikers die zich aanmelden. Het gebruik van de systeem browser is het voor deel van het delen van de status voor eenmalige aanmelding (SSO) met andere toepassingen en met webtoepassingen.

U kunt de ervaring wijzigen door de configuratie aan te passen aan andere opties voor het weer geven van webinhoud, zoals:

Alleen voor iOS:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Voor iOS en macOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL voor macOS ondersteunt alleen de `WKWebView` oudere versies van het besturings systeem. `ASWebAuthenticationSession` wordt alleen ondersteund voor macOS 10,15 en hoger. 

## <a name="system-browsers"></a>Systeem browsers

Voor iOS, `ASWebAuthenticationSession` , `SFAuthenticationSession` , en `SFSafariViewController` worden beschouwd als systeem browsers. Voor macOS is alleen `ASWebAuthenticationSession` beschikbaar. Over het algemeen delen systeem browsers cookies en andere website gegevens met de Safari-browser toepassing.

Standaard detecteert MSAL de iOS-versie dynamisch en selecteert de aanbevolen systeem browser die beschikbaar is op die versie. Op iOS 12 + is dit `ASWebAuthenticationSession` . 

### <a name="default-configuration-for-ios"></a>Standaard configuratie voor iOS

| Versie | Webbrowser |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Standaard configuratie voor macOS

| Versie | Webbrowser |
|:-------------:|:-------------:|
| macOS 10.15 + | ASWebAuthenticationSession |
| andere versies | WKWebView |

Ontwikkel aars kunnen ook een andere systeem browser voor MSAL-apps selecteren:

- `SFAuthenticationSession` is de iOS 11-versie van `ASWebAuthenticationSession` .
- `SFSafariViewController` is een meer algemeen doel en biedt een interface voor het surfen op internet en kan ook worden gebruikt voor aanmeldings doeleinden. In iOS 9 en 10 worden cookies en andere website gegevens gedeeld met Safari, maar niet in iOS 11 en hoger.

## <a name="in-app-browser"></a>Browser in-app

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) is een in-app browser die webinhoud weergeeft. U kunt geen cookies of website gegevens delen met andere **WKWebView** -instanties of met de Safari-browser. WKWebView is een platformoverschrijdende browser die beschikbaar is voor zowel iOS als macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Implicaties voor het delen van cookies en het gebruik van eenmalige aanmelding (SSO)

De browser die u gebruikt, is van invloed op de SSO-ervaring vanwege hoe ze cookies delen. In de volgende tabellen vindt u een overzicht van de SSO-ervaringen per browser.

| Technologie    | Browser type  | iOS-Beschik baarheid | macOS-Beschik baarheid | Cookies en andere gegevens delen  | MSAL-Beschik baarheid | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Systeem | iOS12 en up | macOS 10,15 en up | Ja | iOS-en macOS-10.15 + | exemplaren van w/Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Systeem | iOS11 en up | N.v.t. | Ja | Alleen voor iOS |  exemplaren van w/Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Systeem | iOS11 en up | N.v.t. | Nee | Alleen voor iOS | Geen * *
| **SFSafariViewController** | Systeem | iOS10 | N.v.t. | Ja | Alleen voor iOS |  exemplaren van w/Safari
| **WKWebView**  | In-app | iOS8 en up | macOS 10,10 en up | Nee | iOS en macOS | Geen * *

* * Voor het werken met SSO moeten tokens worden gedeeld tussen apps. Hiervoor is een token cache of een Broker-toepassing vereist, zoals Microsoft Authenticator voor iOS.

## <a name="change-the-default-browser-for-the-request"></a>De standaard browser voor de aanvraag wijzigen

U kunt een in-app-browser of een specifieke systeem browser gebruiken, afhankelijk van uw UX-vereisten, door de volgende eigenschap te wijzigen in `MSALWebviewParameters` :

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Wijziging per interactieve aanvraag

Elke aanvraag kan worden geconfigureerd voor het overschrijven van de standaard browser door de `MSALInteractiveTokenParameters.webviewParameters.webviewType` eigenschap te wijzigen voordat deze wordt door gegeven aan de `acquireTokenWithParameters:completionBlock:` API.

Daarnaast biedt MSAL ondersteuning voor het door geven van een aangepast `WKWebView` door de eigenschap in te stellen `MSALInteractiveTokenParameters.webviewParameters.customWebView` .

Bijvoorbeeld:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Als u een aangepaste Webweergave gebruikt, worden meldingen gebruikt om de status aan te geven van de webinhoud die wordt weer gegeven, zoals:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opties

Alle MSAL ondersteunde webbrowser typen worden gedeclareerd in de [MSALWebviewType-Enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)
