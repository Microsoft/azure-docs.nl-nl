---
title: Een web-API aanroepen vanuit een daemon-app | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een daemon-app die een web-API aanroept.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4b223c9195168b445b7eb81c2709a61807fddac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578393"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-app die web-Api's aanroept-een web-API van de app aanroepen

.NET daemon-apps kunnen een web-API aanroepen. .NET daemon-apps kunnen ook verschillende vooraf goedgekeurde Web-Api's aanroepen.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Een web-API aanroepen vanuit een daemon-toepassing

Hier volgt een beschrijving van het gebruik van het token voor het aanroepen van een API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Met een HTTP-client, zoals [Axios](https://www.npmjs.com/package/axios), roept u de API-EINDPUNT-URI aan met een toegangs token als de *autorisatie-Bearer*.

```JavaScript
const axios = require('axios');

async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="calling-several-apis"></a>Meerdere Api's aanroepen

Voor daemon-apps moet u de Web-Api's die u aanroept vooraf moeten worden goedgekeurd. Er is geen incrementele toestemming met daemon-apps. (Er is geen tussen komst van de gebruiker.) De Tenant beheerder moet vooraf toestemming geven voor de toepassing en alle API-machtigingen. Als u meerdere Api's wilt aanroepen, moet u voor elke resource een token verkrijgen, elke keer dat u aanroept `AcquireTokenForClient` . MSAL maakt gebruik van de toepassings token cache om onnodige service aanroepen te voor komen.

## <a name="next-steps"></a>Volgende stappen

# <a name="net"></a>[.NET](#tab/dotnet)

Ga naar het volgende artikel in dit scenario en [Ga naar productie](./scenario-daemon-production.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Ga naar het volgende artikel in dit scenario en [Ga naar productie](./scenario-daemon-production.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ga naar het volgende artikel in dit scenario en [Ga naar productie](./scenario-daemon-production.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Ga naar het volgende artikel in dit scenario en [Ga naar productie](./scenario-daemon-production.md?tabs=python).

---
