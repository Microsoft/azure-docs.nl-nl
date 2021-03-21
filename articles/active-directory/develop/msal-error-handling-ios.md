---
title: Fouten en uitzonderingen verwerken in MSAL voor iOS/macOS
titleSuffix: Microsoft identity platform
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, claim problemen voor voorwaardelijke toegang en nieuwe pogingen in MSAL voor iOS/macOS-toepassingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761101"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>Fouten en uitzonderingen verwerken in MSAL voor iOS/macOS

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>Fout afhandeling in MSAL voor iOS/macOS

De volledige lijst met MSAL voor iOS-en macOS-fouten wordt vermeld in [MSALError Enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Alle MSAL-gegenereerde fouten worden geretourneerd met het `MSALErrorDomain` domein.

Voor systeem fouten retourneert MSAL het origineel `NSError` van de systeem-API. Als bijvoorbeeld het verkrijgen van tokens mislukt vanwege een gebrek aan netwerk connectiviteit, retourneert MSAL een fout met het `NSURLErrorDomain` domein en de `NSURLErrorNotConnectedToInternet` code.

U wordt aangeraden ten minste de volgende twee MSAL-fouten aan de client zijde te verwerken:

- `MSALErrorInteractionRequired`: De gebruiker moet een interactieve aanvraag uitvoeren. Er zijn veel voor waarden die kunnen leiden tot deze fout, zoals een verlopen verificatie sessie of de nood zaak van aanvullende authenticatie vereisten. Roep de MSAL Interactive token Acquisition-API aan om te herstellen. 

- `MSALErrorServerDeclinedScopes`: Sommige of alle bereiken zijn afgewezen. Bepaal of u wilt door gaan met alleen de toegekende bereiken of stop het aanmeldings proces.

> [!NOTE]
> De `MSALInternalError` Enum mag alleen worden gebruikt voor naslag informatie en fout opsporing. Probeer deze fouten niet automatisch af te handelen tijdens runtime. Als uw app een van de fouten tegen komt die onder vallen `MSALInternalError` , wilt u mogelijk een algemeen bericht van de gebruiker weer geven waarin wordt uitgelegd wat er is gebeurd.

`MSALInternalErrorBrokerResponseNotReceived`Betekent bijvoorbeeld dat de gebruiker de verificatie niet heeft voltooid en hand matig is teruggekeerd naar de app. In dit geval moet uw app een algemeen fout bericht weer geven waarin wordt uitgelegd dat de verificatie is voltooid en er wordt voorgesteld dat ze opnieuw proberen te verifiëren.

De volgende voorbeeld code van de doel-C demonstreert de aanbevolen procedures voor het verwerken van enkele veelvoorkomende fout condities.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Met MSAL voor iOS en macOS kunt u specifieke claims aanvragen in zowel interactieve als Silent-verwervings scenario's.

Als u aangepaste claims wilt aanvragen, geeft u `claimsRequest` in `MSALSilentTokenParameters` of `MSALInteractiveTokenParameters` .

Zie [aangepaste claims aanvragen met MSAL voor IOS en macOS](request-custom-claims.md) voor meer informatie.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Volgende stappen

Overweeg [logboek registratie in MSAL in te scha kelen voor IOS/macOS](msal-logging-ios.md) om problemen op te sporen en op te lossen.
