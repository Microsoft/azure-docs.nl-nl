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
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: ab0113823bb5751828a71a9afd8c474091272e16
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724622"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Verbinding maken met Media Services v3 API-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt beschreven hoe u verbinding maakt met de Azure Media Services v3 node.js SDK met behulp van de aanmeldings methode voor de Service-Principal. U werkt met bestanden in de opslag plaats voor beelden van *Media-Services-v3-node-zelf studies* . Het voor beeld *HelloWorld-ListAssets* bevat de code voor verbinding maken en vervolgens lijst assets in het account.

## <a name="prerequisites"></a>Vereisten

- Een installatie van Visual Studio Code.
- [Node.js](https://nodejs.org/en/download/) installeren.
- Installeer [type script](https://www.typescriptlang.org/download).
- [Een Azure Media Services-account maken](./create-account-howto.md). Vergeet niet welke namen u gebruikt voor de resourcegroep en het Media Services-account.
- Maak een service-principal voor uw toepassing. Zie [toegangs-api's](./access-api-howto.md).<br/>**Pro-Tip.** Houd dit venster geopend of kopieer alles op het tabblad JSON naar Klad blok. 
- Zorg ervoor dat u de nieuwste versie van de [AZUREMEDIASERVICES SDK voor Java script](https://www.npmjs.com/package/@azure/arm-mediaservices)hebt.

> [!IMPORTANT]
> Bekijk de Azure Media Services [naam conventies](media-services-apis-overview.md#naming-conventions) om inzicht te krijgen in de belang rijke naamgevings beperkingen voor entiteiten.

## <a name="clone-the-nodejs-samples-repo"></a>De Node.JS-voor beelden opslag plaats klonen

U werkt met enkele bestanden in azure-voor beelden. Kloon de opslag plaats voor Node.JS-voor beelden.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>De pakketten installeren

### <a name="install-azurearm-mediaservices"></a>Vooraf @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Vooraf @azure/ms-rest-nodeauth

Installeer de minimale versie van ' @azure/ms-rest-nodeauth ":" ^ 3.0.0 ".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

In dit voor beeld gebruikt u de volgende pakketten in het `package.json` bestand.

|Pakket|Beschrijving|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Als u wilt controleren of u het meest recente Azure Media Services-pakket gebruikt, controleert u de [installatie @azure/arm-mediaservices van NPM ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Vereist voor AAD-verificatie met Service-Principal of beheerde identiteit|
|`@azure/storage-blob`|Opslag-SDK. Wordt gebruikt bij het uploaden van bestanden naar assets.|
|`@azure/ms-rest-js`| Wordt gebruikt om u aan te melden.|
|`@azure/storage-blob` | Wordt gebruikt om bestanden te uploaden en te downloaden naar assets in Azure Media Services voor code ring.|
|`@azure/abort-controller`| Gebruikt samen met de opslag-client tijd om langlopende Download bewerkingen uit te voeren|

### <a name="create-the-packagejson-file"></a>De package.jsmaken voor het bestand

1. Maak een `package.json` bestand met behulp van uw favoriete editor.
1. Open het bestand en plak de volgende code:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Verbinding maken met Node.js-client met behulp van type script



### <a name="sample-env-file"></a>Voor beeld *. env* -bestand

Kopieer de inhoud van dit bestand naar een bestand met de naam *. env*. Deze moet worden opgeslagen in de hoofdmap van uw werk plaats. Dit zijn de waarden die u hebt ontvangen op de pagina API-toegang voor uw Media Services-account in de portal.

Zodra u het *. env* -bestand hebt gemaakt, kunt u aan de slag gaan met de voor beelden.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>De voorbeeld toepassing *HelloWorld uitvoeren-ListAssets*

1. Wijzig de map in de map *AMSv3Samples*

```bash
cd AMSv3Samples
```

2. Installeer de pakketten die worden gebruikt in de *packages.jsin* het bestand.

```
npm install 
```

3. Wijzig de map in de map *HelloWorld-ListAssets* .

```bash
cd HelloWorld-ListAssets
```

4. Start Visual Studio code vanuit de map AMSv3Samples. Dit is vereist om te starten vanuit de map waar de map '. vscode ' en tsconfig.jsop bestanden zich bevinden

```dotnetcli
cd ..
code .
```

Open de map voor *HelloWorld-ListAssets* en open het bestand *index. TS* in de Visual Studio code-editor.

Druk in het bestand *index. TS* op F5 om de fout opsporing te starten. U ziet een lijst met assets die worden weer gegeven als u activa al in het account hebt. Als het account leeg is, wordt er een lege lijst weer geven.  

Als u weer gegeven assets snel wilt zien, gebruikt u de portal om enkele video bestanden te uploaden. Assets worden automatisch gemaakt en het script wordt opnieuw uitgevoerd en de namen worden vervolgens geretourneerd.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Meer kijken naar het *HelloWorld-ListAssets-* voor beeld

In het voor beeld *HelloWorld-ListAssets* ziet u hoe u verbinding maakt met de Media Services-client met een Service-Principal en een lijst met assets in het account. Bekijk de opmerkingen in de code voor een gedetailleerde uitleg van wat het doet.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Meer voor beelden

De volgende voor beelden zijn beschikbaar in de [opslag plaats](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Projectnaam|Toepassing|
|---|---|
|Live/index. TS| Basis voorbeeld van live streamen. **Waarschuwing**, Controleer of alle resources zijn opgeruimd en niet meer worden gefactureerd in de Portal wanneer u Live gebruikt|
|StreamFilesSample/index. TS| Basis voorbeeld voor het uploaden van een lokaal bestand of code ring vanuit een bron-URL. Voor beeld laat zien hoe u de Storage SDK gebruikt om inhoud te downloaden en hoe streamen naar een speler wordt weer gegeven |
|StreamFilesWithDRMSample/index. TS| Laat zien hoe u kunt coderen en streamen met behulp van Widevine en PlayReady DRM |
|VideoIndexerSample/index. TS| Voor beeld van het gebruik van de voor instellingen video en Audio Analyzer voor het genereren van meta gegevens en inzichten uit een video-of audio bestand |

## <a name="see-also"></a>Zie ook

- [Referentie documentatie voor Azure Media Services modules voor Node.js](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)
- [Azure voor Java script-& Node.js-ontwikkel aars](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services bron code in de @azure/azure-sdk-for-js Git-hub opslag plaats](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentatie voor het Azure-pakket voor Node.js-ontwikkel aars](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)
- [Media Services-concepts](concepts-overview.md)
- [NPM-installatie @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure voor Java script-& Node.js-ontwikkel aars](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services bron code in de @azure/azure-sdk-for-js opslag plaats](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Node.js-naslagdocumentatie](/javascript/api/overview/azure/mediaservices/management) van Media Services en bekijk [voorbeelden](https://github.com/Azure-Samples/media-services-v3-node-tutorials) waarin u ziet hoe u Media Services-API gebruikt met Node.js.
