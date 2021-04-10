---
title: SSO tussen ADAL & MSAL-apps (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het delen van SSO tussen ADAL-en MSAL-apps
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584244"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Procedure: SSO tussen ADAL-en MSAL-apps in macOS en iOS

De micro soft Authentication Library (MSAL) voor iOS kan SSO-status delen met [ADAL doelstelling-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) tussen toepassingen. U kunt uw apps in uw eigen tempo migreren naar MSAL, zodat uw gebruikers nog steeds profiteren van SSO op basis van meerdere apps, zelfs met een combi natie van ADAL-en MSAL-apps.

Zie [Silent SSO tussen meerdere apps](single-sign-on-macos-ios.md#silent-sso-between-apps)als u hulp nodig hebt bij het instellen van SSO tussen apps met behulp van de MSAL-SDK. Dit artikel richt zich op SSO tussen ADAL en MSAL.

De specifieke informatie voor het implementeren van SSO is afhankelijk van de ADAL-versie die u gebruikt.

## <a name="adal-27x"></a>ADAL 2.7. x

In deze sectie worden de SSO-verschillen tussen MSAL en ADAL 2.7 beschreven. x

### <a name="cache-format"></a>Cache-indeling

ADAL 2.7. x kan de MSAL-cache-indeling lezen. U hoeft niets speciaal te doen voor SSO van cross-app met versie ADAL 2.7. x. Houd echter rekening met verschillen in account-id's die door deze twee bibliotheken worden ondersteund.

### <a name="account-identifier-differences"></a>Verschillen tussen account-id's

MSAL en ADAL gebruiken verschillende account-id's. ADAL maakt gebruik van UPN als de primaire account-id. MSAL maakt gebruik van een niet-Bewaak bare account-id die is gebaseerd op een object-ID en een Tenant-ID voor AAD-accounts en een `sub` claim voor andere typen accounts.

Wanneer u een `MSALAccount` object ontvangt in het MSAL resultaat, bevat het een account-id in de `identifier` eigenschap. De toepassing moet deze id voor volgende stille aanvragen gebruiken.

Daarnaast `identifier` `MSALAccount` bevat het object een id met de naam unplayable `username` . Die wordt omgezet `userId` in ADAL. `username` wordt niet beschouwd als een unieke id en kan op elk gewenst moment worden gewijzigd, zodat deze alleen mag worden gebruikt voor compatibiliteits scenario's met eerdere versies van ADAL. MSAL ondersteunt cache query's met ofwel `username` of `identifier` , waarbij query's `identifier` worden aanbevolen.

De volgende tabel bevat een overzicht van de verschillen tussen de account-id's tussen ADAL en MSAL:

| Account-id                | MSAL                                                         | ADAL 2.7. x      | Oudere ADAL (vóór ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| id die kan worden afgespeeld            | `username`                                                   | `userId`        | `userId`                       |
| unieke id die niet kan worden afgespeeld | `identifier`                                                 | `homeAccountId` | N.v.t.                            |
| Geen account-id bekend               | Query's uitvoeren op alle accounts via `allAccounts:` API in `MSALPublicClientApplication` | N.v.t.             | N.v.t.                            |

Dit is de `MSALAccount` interface die die id's biedt:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO van MSAL naar ADAL

Als u een MSAL-app en een ADAL-app hebt en de gebruiker zich voor het eerst aanmeldt bij de MSAL-app, kunt u SSO ophalen in de ADAL-app door het `username` van het object op te slaan `MSALAccount` en dit door te geven aan uw op ADAL gebaseerde app als `userId` . ADAL kan vervolgens de account gegevens op de achtergrond vinden met de `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API.

### <a name="sso-from-adal-to-msal"></a>SSO van ADAL naar MSAL

Als u een MSAL-app en een ADAL-app hebt en de gebruiker zich voor het eerst aanmeldt bij de ADAL-app, kunt u ADAL-gebruikers-id's gebruiken voor het opzoeken van accounts in MSAL. Dit geldt ook voor de migratie van ADAL naar MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId van ADAL

ADAL 2.7. x retourneert de `homeAccountId` in het `ADUserInformation` object in het resultaat via deze eigenschap:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` in ADAL is equivalent van `identifier` in MSAL. U kunt deze id opslaan voor gebruik in MSAL voor het opzoeken van accounts met de `accountForIdentifier:error:` API.

#### <a name="adals-userid"></a>Van ADAL `userId`

Als `homeAccountId` niet beschikbaar is of als u alleen de niet-berekenings-id hebt, kunt u ADAL gebruiken `userId` om het account in MSAL te zoeken.

Zoek in MSAL eerst naar een account met `username` of `identifier` . Gebruik altijd `identifier` voor het uitvoeren van query's als u deze gebruikt en alleen `username` als terugval. Als het account is gevonden, gebruikt u het account in de- `acquireTokenSilent` aanroepen.

Doel-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL ondersteunde account lookup-Api's:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2. x-2.6.6

In deze sectie worden de SSO-verschillen tussen MSAL en ADAL 2. x-2.6.6 besproken.

Oudere ADAL-versies bieden geen systeem eigen ondersteuning voor de MSAL-cache-indeling. Om te zorgen voor een soepele migratie van ADAL naar MSAL, kan MSAL echter de oudere ADAL-cache-indeling lezen zonder dat de gebruiker om referenties wordt gevraagd.

Omdat `homeAccountId` deze niet beschikbaar is in oudere versies van ADAL, moet u accounts opzoeken met behulp van `username` :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Bijvoorbeeld:

Doel-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



U kunt ook alle accounts lezen, waardoor ook account gegevens van ADAL worden gelezen:

Doel-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)
