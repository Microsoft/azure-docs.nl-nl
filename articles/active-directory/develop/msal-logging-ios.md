---
title: Logboek registratie van fouten en uitzonde ringen in MSAL voor iOS/macOS
titleSuffix: Microsoft identity platform
description: Meer informatie over het vastleggen van fouten en uitzonde ringen in MSAL voor iOS/macOS
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763401"
---
# <a name="logging-in-msal-for-iosmacos"></a>Logboekregistratie in MSAL voor iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL voor iOS-en macOS-logboek registratie-ObjC

Stel een call back in om MSAL-logboek registratie vast te leggen en deze op te nemen in de logboek registratie van uw eigen toepassing. De hand tekening voor de retour aanroep ziet er als volgt uit:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Bijvoorbeeld:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Persoonsgegevens

MSAL worden standaard geen persoonlijke gegevens vastgelegd of geregistreerd. Met de bibliotheek kunnen app-ontwikkel aars dit inschakelen via een eigenschap in de MSALLogger-klasse. Als u inschakelt `pii.Enabled` , wordt de app verantwoordelijk voor het veilig verwerken van uiterst gevoelige gegevens en de volgende regelgevings vereisten.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Registratie niveaus

Gebruik een van de volgende waarden om het logboek registratie niveau in te stellen wanneer u zich aanmeldt met behulp van MSAL voor iOS en macOS:

|Niveau  |Beschrijving |
|---------|---------|
| `MSALLogLevelNothing`| Alle logboek registratie uitschakelen |
| `MSALLogLevelError` | Standaard niveau, alleen informatie afdrukken wanneer er fouten optreden |
| `MSALLogLevelWarning` | Waarschuwingen |
| `MSALLogLevelInfo` |  Bibliotheek ingangs punten, met para meters en verschillende sleutel hanger bewerkingen |
|`MSALLogLevelVerbose`     |  API-tracering |

Bijvoorbeeld:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Indeling van logboek bericht

Het bericht gedeelte van MSAL-logboek berichten heeft de indeling `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Bijvoorbeeld:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Het opgeven van correlatie-Id's en tijds tempels is handig voor het volgen van problemen. Informatie over tijds tempel en correlatie-ID is beschikbaar in het logboek bericht. De enige betrouw bare plaats om deze op te halen, is afkomstig uit MSAL-logboek berichten.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL voor iOS-en macOS-logboek registratie-Swift

Stel een call back in om MSAL-logboek registratie vast te leggen en deze op te nemen in de logboek registratie van uw eigen toepassing. De hand tekening (vertegenwoordigd in doel-C) voor de retour aanroep ziet er als volgt uit:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Bijvoorbeeld:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Persoonsgegevens

MSAL worden standaard geen persoonlijke gegevens vastgelegd of geregistreerd. Met de bibliotheek kunnen app-ontwikkel aars dit inschakelen via een eigenschap in de MSALLogger-klasse. Als u inschakelt `pii.Enabled` , wordt de app verantwoordelijk voor het veilig verwerken van uiterst gevoelige gegevens en de volgende regelgevings vereisten.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Registratie niveaus

Gebruik een van de volgende waarden om het logboek registratie niveau in te stellen wanneer u zich aanmeldt met behulp van MSAL voor iOS en macOS:

|Niveau  |Beschrijving |
|---------|---------|
| `MSALLogLevelNothing`| Alle logboek registratie uitschakelen |
| `MSALLogLevelError` | Standaard niveau, alleen informatie afdrukken wanneer er fouten optreden |
| `MSALLogLevelWarning` | Waarschuwingen |
| `MSALLogLevelInfo` |  Bibliotheek ingangs punten, met para meters en verschillende sleutel hanger bewerkingen |
|`MSALLogLevelVerbose`     |  API-tracering |

Bijvoorbeeld:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Indeling van logboek bericht

Het bericht gedeelte van MSAL-logboek berichten heeft de indeling `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Bijvoorbeeld:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Het opgeven van correlatie-Id's en tijds tempels is handig voor het volgen van problemen. Informatie over tijds tempel en correlatie-ID is beschikbaar in het logboek bericht. De enige betrouw bare plaats om deze op te halen, is afkomstig uit MSAL-logboek berichten.

---

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor meer code voorbeelden.