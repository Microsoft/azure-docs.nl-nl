---
title: Logboek registratie van fouten en uitzonde ringen in MSAL.js
titleSuffix: Microsoft identity platform
description: Meer informatie over het vastleggen van fouten en uitzonde ringen in MSAL.js
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
ms.openlocfilehash: 8604a38bc310cc884c2b5e99efe7a47ae5e787d7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763404"
---
# <a name="logging-in-msaljs"></a>Logboekregistratie in MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Logboek registratie in MSAL.js configureren

Schakel logboek registratie in MSAL.js (Java script) in door een traceer object door te geven tijdens de configuratie voor het maken van een `UserAgentApplication` exemplaar. Dit logger object heeft de volgende eigenschappen:

- `localCallback`: een call back-instantie die kan worden geleverd door de ontwikkelaar om logboeken op een aangepaste manier te gebruiken en publiceren. Implementeer de localCallback-methode, afhankelijk van hoe u logboeken wilt omleiden.
- `level` (optioneel): het Configureer bare logboek niveau. De ondersteunde logboek niveaus zijn: `Error` , `Warning` , en `Info` `Verbose` . De standaardwaarde is `Info`.
- `piiLoggingEnabled` (optioneel): als deze optie is ingesteld op waar, worden persoonlijke en organisatie gegevens vastgelegd in een logboek. Standaard is dit false, zodat uw toepassing geen persoonlijke gegevens kan registreren. Persoonlijke gegevens logboeken worden nooit geschreven naar de standaard uitvoer, zoals console, Logcat of NSLog.
- `correlationId` (optioneel): een unieke id, die wordt gebruikt om de aanvraag toe te wijzen met het antwoord op fout opsporing. De standaard instelling is RFC4122-versie 4 GUID (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor meer code voorbeelden.