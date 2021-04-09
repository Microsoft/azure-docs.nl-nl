---
title: Id-providers configureren (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het gebruik van verschillende instanties, zoals B2C, soevereine Clouds en gast gebruikers, met MSAL voor iOS en macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96169440"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Procedure: MSAL voor iOS en macOS configureren voor het gebruik van verschillende id-providers

In dit artikel wordt uitgelegd hoe u uw micro soft-verificatie bibliotheek-app voor iOS en macOS (MSAL) kunt configureren voor verschillende instanties, zoals Azure Active Directory (Azure AD), Business-to-consumer (B2C), soevereine Clouds en gast gebruikers.  In dit artikel kunt u het algemeen beschouwen als een id-provider.

## <a name="default-authority-configuration"></a>Configuratie van de standaard instantie

`MSALPublicClientApplication` is geconfigureerd met een standaard instantie-URL van `https://login.microsoftonline.com/common` , die geschikt is voor de meeste Azure Active Directory Aad-scenario's. Tenzij u geavanceerde scenario's zoals nationale Clouds implementeert, of als u werkt met B2C, hoeft u deze niet te wijzigen.

> [!NOTE]
> Moderne authenticatie met Active Directory Federation Services als id-provider (ADFS) wordt niet ondersteund (Zie [ADFS voor ontwikkel aars](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) voor meer informatie). ADFS wordt ondersteund via Federatie.

## <a name="change-the-default-authority"></a>De standaard instantie wijzigen

In sommige scenario's, zoals Business-to-consumer (B2C), moet u mogelijk de standaard instantie wijzigen.

### <a name="b2c"></a>B2C

Voor het werken met B2C is voor de [micro soft Authentication Library (MSAL)](reference-v2-libraries.md) een andere certificerings instantie geconfigureerd. MSAL herkent één instantie-URL-indeling als B2C. De herkende indeling voor de B2C-instantie is `https://<host>/tfp/<tenant>/<policy>` bijvoorbeeld `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` . U kunt echter ook andere ondersteunde B2C ca-Url's gebruiken door instanties expliciet te declareren als B2C-instantie.

Als u een wille keurige URL-indeling voor B2C wilt ondersteunen, `MSALB2CAuthority` kunt u als volgt een wille keurige URL instellen:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Alle B2C-instanties die geen gebruik maken van de standaard indeling voor B2C-instanties, moeten worden gedeclareerd als bekende instanties.

Voeg elke andere B2C-instantie toe aan de lijst met bekende instanties, zelfs als de autoriteiten van het beleid verschillen.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Als uw app een nieuw beleid aanvraagt, moet de URL van de instantie worden gewijzigd omdat de URL van de autoriteit voor elk beleid verschillend is. 

Als u een B2C-toepassing wilt configureren, stelt u `@property MSALAuthority *authority` een exemplaar van `MSALB2CAuthority` in `MSALPublicClientApplicationConfig` voordat u maakt `MSALPublicClientApplication` , bijvoorbeeld:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Onafhankelijke clouds

Als uw app wordt uitgevoerd in een soevereine Cloud, moet u mogelijk de URL van de autoriteit wijzigen in de `MSALPublicClientApplication` . In het volgende voor beeld wordt de URL van de instantie ingesteld voor gebruik met de Duitse AAD-Cloud:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Mogelijk moet u verschillende bereiken door geven aan elke soevereine Cloud. Welke bereiken moeten worden verzonden, is afhankelijk van de resource die u gebruikt. U kunt bijvoorbeeld gebruiken in de `"https://graph.microsoft.com/user.read"` wereld wijde Cloud en `"https://graph.microsoft.de/user.read"` in de Duitse Cloud.

### <a name="signing-a-user-into-a-specific-tenant"></a>Een gebruiker ondertekenen in een specifieke Tenant

Wanneer de CA-URL is ingesteld op `"login.microsoftonline.com/common"` , wordt de gebruiker aangemeld bij hun thuis Tenant. Sommige apps moeten de gebruiker echter mogelijk ondertekenen in een andere Tenant en sommige apps werken alleen met één Tenant.

Als u de gebruiker wilt aanmelden bij een specifieke Tenant, configureert u `MSALPublicClientApplication` met een specifieke instantie. Bijvoorbeeld:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Hieronder ziet u hoe u een gebruiker aanmeldt bij een specifieke Tenant:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Ondersteunde instanties

### <a name="msalauthority"></a>MSALAuthority

De `MSALAuthority` klasse is de basis abstracte klasse voor de MSAL-instantie klassen. Probeer er geen instantie van te maken met `alloc` of `new` . In plaats daarvan maakt u een van de subklassen rechtstreeks ( `MSALAADAuthority` , `MSALB2CAuthority` ) of gebruikt u de fabrieks methode `authorityWithURL:error:` om subklassen te maken met behulp van een CA-URL.

Gebruik de `url` eigenschap om een genormaliseerde URL op te halen. Extra para meters en padcomponenten of fragmenten die geen deel uitmaken van de instantie, worden niet weer gegeven in de genormaliseerde URL.

Hier volgen enkele subklassen van `MSALAuthority` die u kunt instantiëren, afhankelijk van de instantie die u wilt gebruiken.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` vertegenwoordigt een AAD-instantie. De CA-URL moet de volgende indeling hebben, waar `<port>` optioneel is: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` vertegenwoordigt een B2C-instantie. Standaard moet de URL van de B2C-instantie de volgende indeling hebben, waar `<port>` optioneel is: `https://<host>:<port>/tfp/<tenant>/<policy>` . MSAL ondersteunt echter ook andere wille keurige B2C-instantie-indelingen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)