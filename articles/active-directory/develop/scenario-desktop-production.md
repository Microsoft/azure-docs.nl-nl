---
title: De bureau blad-app voor het aanroepen van web-Api's naar productie verplaatsen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verplaatsen van een bureau blad-app die web-Api's aanroept voor productie
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 22e61ea767d781dc9da54d61143c1b2524e06e94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584411"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Bureau blad-app voor het aanroepen van web-Api's: verplaatsen naar productie

In dit artikel leert u hoe u uw bureau blad-app kunt verplaatsen die web-Api's aanroept voor productie.

## <a name="handle-errors-in-desktop-applications"></a>Fouten in bureaublad toepassingen afhandelen

In de verschillende stromen hebt u geleerd hoe u de fouten voor de Stille stromen kunt afhandelen, zoals wordt weer gegeven in de code fragmenten. U hebt ook gezien dat er sprake is van gevallen waarin interactie nodig is, zoals in incrementele toestemming en voorwaardelijke toegang.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>De gebruiker vooraf toestemming geven voor verschillende bronnen

> [!NOTE]
> Het verkrijgen van toestemming voor verschillende bronnen werkt voor het micro soft Identity-platform, maar niet voor Azure Active Directory (Azure AD) B2C. Azure AD B2C ondersteunt alleen beheerders toestemming en geen toestemming van de gebruiker.

U kunt geen token voor meerdere resources tegelijk ophalen met het micro soft Identity-platform. De `scopes` para meter kan alleen bereiken voor één resource bevatten. U kunt ervoor zorgen dat de gebruiker vooraf toestuurt naar verschillende bronnen met behulp van de `extraScopesToConsent` para meter.

U kunt bijvoorbeeld twee resources hebben met twee bereiken:

- `https://mytenant.onmicrosoft.com/customerapi` met de scopes `customer.read` en `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` met de scopes `vendor.read` en `vendor.write`

In dit voor beeld gebruikt u de `.WithExtraScopesToConsent` modificator met de `extraScopesToConsent` para meter.

Bijvoorbeeld:

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopesToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doel-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Met deze aanroep krijgt u een toegangs token voor de eerste web-API.

Roep de API aan bij het aanroepen van de tweede Web-API `AcquireTokenSilent` .

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Voor een persoonlijk micro soft-account moet u elke keer dat de app wordt uitgevoerd, toestemming geven

Voor gebruikers van een persoonlijk micro soft-account vraagt de vraag om toestemming op elke systeem eigen client (desktop of mobiele app) om toestemming te verlenen is het beoogde gedrag. De systeem eigen client identiteit is inherent onveilig, wat in strijd is met de id van de vertrouwelijke client toepassing. Vertrouwelijke client toepassingen wisselen een geheim uit met het micro soft-identiteits platform om hun identiteit te bewijzen. Het micro soft Identity-platform heeft ervoor gekozen om deze inbeveiliging te beperken voor consumenten Services door de gebruiker te vragen om toestemming telkens wanneer de toepassing wordt geautoriseerd.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Desktop en mobiele open bare client-apps](sample-v2-code.md#desktop-and-mobile-public-client-apps)voor meer voor beelden.



