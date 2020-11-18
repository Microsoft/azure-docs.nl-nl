---
title: Verbinding maken met Azure Media Services v3 API-Node.js
description: In dit artikel wordt beschreven hoe u verbinding maakt met Media Services v3 API met Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 1fc9d38827b8c00a6c90280a89e520e28f1763b2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844298"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Verbinding maken met Media Services v3 API-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt beschreven hoe u verbinding maakt met de Azure Media Services v3 node.js SDK met behulp van de aanmeldings methode voor de Service-Principal.

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://nodejs.org/en/download/) installeren.
- [Een Azure Media Services-account maken](./create-account-howto.md). Vergeet niet welke namen u gebruikt voor de resourcegroep en het Media Services-account.

> [!IMPORTANT]
> Bekijk [naamconventies](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>package.jsmaken op

1. Maak een package.jsin het bestand met behulp van uw favoriete editor.
1. Open het bestand en plak de volgende code:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

De volgende pakketten moeten worden opgegeven:

|Pakket|Beschrijving|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Om ervoor te zorgen dat u het meest recente Azure Media Services-pakket gebruikt, controleert u [NPM Azure-arm-Media Services installeren](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Opslag-SDK. Wordt gebruikt bij het uploaden van bestanden naar assets.|
|`ms-rest-azure`| Wordt gebruikt om u aan te melden.|

U kunt de volgende opdracht uitvoeren om ervoor te zorgen dat u het meest recente pakket gebruikt:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Verbinding maken met Node.js-client

1. Maak een JS-bestand met behulp van uw favoriete editor.
1. Open het bestand en plak de volgende code.
1. Stel de waarden in de sectie endpoint config in op de waarden die u hebt ontvangen van [Access-api's](./access-api-howto.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Voer uw app uit

Open een opdrachtprompt. Blader naar de map van het voor beeld en voer de volgende opdrachten uit:

```
npm install 
node index.js
```

## <a name="see-also"></a>Zie ook

- [Media Services-concepts](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Node.js-naslagdocumentatie](/javascript/api/overview/azure/mediaservices/management) van Media Services en bekijk [voorbeelden](https://github.com/Azure-Samples/media-services-v3-node-tutorials) waarin u ziet hoe u Media Services-API gebruikt met Node.js.
