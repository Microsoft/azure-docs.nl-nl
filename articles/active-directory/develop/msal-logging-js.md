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
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954814"
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