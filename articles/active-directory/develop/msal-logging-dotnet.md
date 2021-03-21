---
title: Logboek registratie van fouten en uitzonde ringen in MSAL.NET
titleSuffix: Microsoft identity platform
description: Meer informatie over het vastleggen van fouten en uitzonde ringen in MSAL.NET
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
ms.openlocfilehash: da36ce0a956e0c3ed369a676960bdb9b5c5b1199
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954831"
---
# <a name="logging-in-msalnet"></a>Logboekregistratie in MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Logboek registratie in MSAL.NET configureren

In MSAL 3. x wordt logboek registratie per toepassing ingesteld bij het maken van de app met behulp van de `.WithLogging` opbouw functie voor Builder. Deze methode accepteert optionele para meters:

- `Level` Hiermee kunt u bepalen welk registratie niveau u wilt. Als u deze instelt op fouten, worden er alleen fouten opgehaald
- `PiiLoggingEnabled` Hiermee kunt u persoonlijke en organisatie gegevens registreren indien ingesteld op waar. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens registreert.
- `LogCallback` is ingesteld op een gemachtigde die de logboek registratie doet. Als `PiiLoggingEnabled` True is, ontvangt deze methode de berichten twee keer: wanneer de `containsPii` para meter is ingesteld op False en het bericht zonder persoons gegevens, en een tweede keer met de `containsPii` para meter is ingesteld op True en het bericht persoons gegevens kan bevatten. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), is het bericht hetzelfde.
- `DefaultLoggingEnabled` Hiermee schakelt u de standaard logboek registratie in voor het platform. De standaard instelling is False. Als u deze instelt op True, wordt gebeurtenis tracering gebruikt in Desktop/UWP-toepassingen, NSLog op iOS en logcat op Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

> [!TIP]
 > Zie de [MSAL.net-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) voor voor beelden van MSAL.net-logboek registratie en meer.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor meer code voorbeelden.