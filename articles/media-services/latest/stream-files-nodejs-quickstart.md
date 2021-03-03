---
title: Video bestanden coderen en streamen met Node.JS
description: Video bestanden streamen met Node.JS. Volg de stappen in deze zelfstudie om een nieuw Azure Media Services-account te maken, een bestand te coderen en dit vervolgens te streamen naar Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 566a7a978a08bf7e37b6c4ab8b0609222b34e549
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724520"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Video bestanden coderen en streamen met Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Deze snelstartgids laat zien hoe u met Azure Media Services eenvoudig video's kunt coderen en streamen naar allerlei verschillende browsers en apparaten. Een invoer video bestand kan worden opgegeven met behulp van HTTPS-Url's, SAS-Url's of paden naar bestanden die zich in Azure Blob Storage bevinden.

Aan het einde van deze Snelstartgids weet u het volgende:

- Coderen met Node.JS
- Streamen met Node.JS
- Een bestand uploaden vanuit een HTTPS-URL met Node.JS
- Een HLS-of DASH-client speler gebruiken met Node.JS

Met het voorbeeld in dit artikel wordt inhoud gecodeerd die u toegankelijk maakt via een HTTPS-URL. Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's.

![De video afspelen](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://nodejs.org/en/download/) installeren
- [Een Azure Media Services-account maken](./create-account-howto.md).<br/>Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U hebt deze nodig voor toegang tot de API.
- Door loop de [Configure and Connect with Node.js](./configure-connect-nodejs-howto.md) How-to-to-to-to-out hoe u de Node.js client-SDK kunt gebruiken

## <a name="download-and-configure-the-sample"></a>Het voorbeeld downloaden en configureren

Gebruik de volgende opdracht om een GitHub-opslagplaats te klonen op uw computer die het Node.js-voorbeeld voor het streamen van video bevat:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Het voorbeeld bevindt zich in de map [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Open [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) in het gedownloade project. Werk het bestand *sample. env* in de hoofdmap bij met de waarden en referenties die u hebt gekregen van [toegang tot api's](./access-api-howto.md). Wijzig de naam van het bestand *sample. env* in *. env* (Ja, alleen de extensie).

In het voorbeeld worden de volgende acties uitgevoerd:

1. Hiermee maakt u een **trans formatie** met een [vooraf ingestelde code ring voor inhoud](./content-aware-encoding.md). Eerst wordt gecontroleerd of de opgegeven trans formatie bestaat.
1. Hiermee maakt u een uitvoer **activum** dat door de coderings **taak** wordt gebruikt om de uitvoer te bevatten
1. Hiermee wordt een lokaal bestand optioneel geüpload met de opslag-BLOB SDK
1. Hiermee wordt de **taak** invoer gemaakt die is gebaseerd op een HTTPS-URL of een geüpload bestand
1. Hiermee wordt de coderings **taak** verzonden met de invoer en uitvoer die eerder zijn gemaakt
1. Hiermee wordt de status van de taak gecontroleerd
1. Hiermee wordt de uitvoer van de coderings taak gedownload naar een lokale map
1. Hiermee maakt u een **streaming-Locator** voor gebruik in de speler
1. Maakt streaming-Url's voor HLS en streepje
1. Hiermee wordt de inhoud weer gegeven in een Player-toepassing-Azure Media Player

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. De toepassing downloadt de versleutelde bestanden. Maak een map waar de uitvoer bestanden naartoe moeten gaan en werk de waarde van de variabele **outputFolder** in het bestand [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) bij. Deze is standaard ingesteld op ' temp '.
1. Open een **opdracht prompt** en blader naar de map van het voor beeld.
1. Wijzig de map in de map AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Installeer de pakketten die worden gebruikt in de *packages.jsin* het bestand.

    ```bash
    npm install 
    ```

1. Wijzig de map naar de map *StreamFilesSample* .

    ```bash
    cd StreamFilesSample
    ```

1. Start Visual Studio code vanuit de map *AMSv3Samples* . (Dit is vereist om te starten vanuit de map waarin de map *. vscode* en *tsconfig.jsop* bestanden zich bevinden.)

    ```bash
    cd ..
    code .
    ```

Open de map voor *StreamFilesSample* en open het bestand *index. TS* in de Visual Studio code-editor.
Druk in het bestand *index. TS* op F5 om de fout opsporing te starten.

## <a name="test-with-azure-media-player"></a>Testen met Azure Media Player

Gebruik Azure Media Player om de stroom te testen. U kunt ook een HLS-of DASH Board compatibele speler gebruiken, zoals Shaka-speler, HLS.js, Dash.js of anderen.

U moet op de koppeling kunnen klikken die in het voor beeld is gegenereerd en de AMP-speler starten met het DASH Board-manifest dat al is geladen.

> [!NOTE]
> Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

1. Open een browser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in het vak **URL:** een van de waarden van de streaming-URL die u hebt ontvangen tijdens het uitvoeren van de toepassing. U kunt de URL in HLS, Dash of Smooth plakken en Azure Media Player overschakelen naar een geschikt streaming-protocol dat automatisch op het apparaat wordt afgespeeld.
3. Klik op **Update Player**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources in de resourcegroep niet meer nodig hebt, met inbegrip van het Media Services-account en de opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Meer documentatie voor ontwikkel aars voor Node.js op Azure

- [Azure voor Java script-& Node.js-ontwikkel aars](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services bron code in de @azure/azure-sdk-for-js Git-hub opslag plaats](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Documentatie voor het Azure-pakket voor Node.js-ontwikkel aars](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="see-also"></a>Zie ook

- [Taakfoutcodes](/rest/api/media/jobs/get#joberrorcode).
- [NPM-installatie @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure voor Java script-& Node.js-ontwikkel aars](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services bron code in de @azure/azure-sdk-for-js opslag plaats](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Volgende stappen

> [Media Services-concepts](concepts-overview.md)
