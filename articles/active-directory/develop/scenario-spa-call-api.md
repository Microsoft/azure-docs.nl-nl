---
title: Een app met één pagina bouwen die een web-API aanroept
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een toepassing met één pagina die een web-API aanroept
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4dd4a40233fc1c030581fd1ae2827061435a0ab3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753534"
---
# <a name="single-page-application-call-a-web-api"></a>Toepassing met één pagina: een web-API aanroepen

U wordt aangeraden de `acquireTokenSilent` methode voor het verkrijgen of vernieuwen van een toegangs token aan te roepen voordat u een web-API aanroept. Nadat u een token hebt, kunt u een beveiligde web-API aanroepen.

## <a name="call-a-web-api"></a>Een web-API aanroepen

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gebruik het verkregen toegangs token als een Bearer in een HTTP-aanvraag om een web-API, zoals Microsoft Graph-API, aan te roepen. Bijvoorbeeld:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

De MSAL-hoek wrapper maakt gebruik van de HTTP-Interceptor om automatisch toegangs tokens op de achtergrond te verkrijgen en deze te koppelen aan de HTTP-aanvragen aan Api's. Zie [een token verkrijgen voor het aanroepen van een API](scenario-spa-acquire-token.md)voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Ga naar productie](scenario-spa-production.md).