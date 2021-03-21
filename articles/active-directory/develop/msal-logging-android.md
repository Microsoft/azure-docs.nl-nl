---
title: Logboek registratie van fouten en uitzonde ringen in MSAL voor Android.
titleSuffix: Microsoft identity platform
description: Meer informatie over het vastleggen van fouten en uitzonde ringen in MSAL voor Android.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954881"
---
# <a name="logging-in-msal-for-android"></a>Logboekregistratie in MSAL voor Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="logging-in-msal-for-android-using-java"></a>Logboek registratie in MSAL voor Android met behulp van Java

Schakel logboek registratie in bij het maken van een app door het maken van een logboek registratie. De retour aanroep heeft de volgende para meters:

- `tag` is een teken reeks die door de bibliotheek wordt door gegeven aan de call back. Het is gekoppeld aan de logboek vermelding en kan worden gebruikt om logboek registratie berichten te sorteren.
- `logLevel` Hiermee kunt u bepalen welk registratie niveau u wilt. De ondersteunde logboek niveaus zijn: `Error` , `Warning` , en `Info` `Verbose` .
- `message` is de inhoud van de logboek vermelding.
- `containsPII` Hiermee wordt aangegeven of berichten met persoonlijke gegevens of organisatie gegevens worden geregistreerd. Deze instelling is standaard ingesteld op ONWAAR, zodat uw toepassing geen persoonlijke gegevens kan registreren. Als `containsPII` `true` dat het geval is, ontvangt deze methode de berichten twee keer: eenmaal waarvoor de `containsPII` para meter is ingesteld op `false` en het `message` zonder persoons gegevens, en een tweede keer waarbij de `containsPii` para meter is ingesteld op `true` en het bericht persoonlijke gegevens kan bevatten. In sommige gevallen (wanneer het bericht geen persoonlijke gegevens bevat), is het bericht hetzelfde.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Standaard worden door de MSAL-logger geen persoonlijke Identificeer bare gegevens of informatie over de organisatie vastgelegd.
De logboek registratie van persoons gegevens of door de organisatie geïdentificeerde informatie inschakelen:

```java
Logger.getInstance().setEnablePII(true);
```

Logboek registratie van persoonlijke gegevens en organisatie gegevens uitschakelen:

```java
Logger.getInstance().setEnablePII(false);
```

Logboek registratie in logcat is standaard uitgeschakeld. In te scha kelen:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor meer code voorbeelden.