---
title: Vraag gedrag interactieve aanvraag (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanpassen van prompt gedrag in interactieve aanroepen met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477546"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Vraag gedrag in MSAL.js interactieve aanvragen

Wanneer een gebruiker een actieve Azure AD-sessie met meerdere gebruikers accounts heeft gemaakt, wordt de gebruiker door de aanmeldings pagina van Azure AD standaard gevraagd een account te selecteren voordat u zich aanmeldt. Gebruikers zien geen account selectie-ervaring als er slechts één geverifieerde sessie is met Azure AD.

De MSAL.js-bibliotheek (vanaf v 0.2.4) verzendt geen prompt parameter tijdens de interactieve aanvragen ( `loginRedirect` , `loginPopup` `acquireTokenRedirect` en `acquireTokenPopup` ) en dwingt daarom geen vraag gedrag af. `acquireTokenSilent`MSAL.js wordt een prompt para meter ingesteld op voor Silent token aanvragen met de-methode `none` .

Op basis van uw toepassings scenario kunt u het prompt gedrag voor de interactieve aanvragen bepalen door de para meter prompt in te stellen in de aanvraag parameters die aan de methoden zijn door gegeven. Als u bijvoorbeeld de account selectie wilt aanroepen:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


De volgende prompt waarden kunnen worden door gegeven bij de verificatie met Azure AD:

**aanmelding:** Met deze waarde wordt de gebruiker gedwongen om referenties in te voeren voor de verificatie aanvraag.

**select_account:** Met deze waarde krijgt de gebruiker een account selectie-ervaring met een lijst met alle accounts in de sessie.

**toestemming:** Met deze waarde wordt het dialoog venster OAuth-toestemming aangeroepen waarmee gebruikers machtigingen voor de app kunnen verlenen.

**geen:** Met deze waarde wordt ervoor gezorgd dat de gebruiker geen interactieve prompt ziet. Het wordt aanbevolen deze waarde niet door te geven aan interactieve methoden in MSAL.js, omdat deze mogelijk onverwachte gedragingen kunnen ondergaan. Gebruik in plaats daarvan de methode voor het bezorgen van `acquireTokenSilent` Silent-aanroepen.

## <a name="next-steps"></a>Volgende stappen

Lees meer over de `prompt` para meter in het [OAuth 2,0 impliciet granting](v2-oauth2-implicit-grant-flow.md) -protocol dat MSAL.js-bibliotheek gebruikt.
