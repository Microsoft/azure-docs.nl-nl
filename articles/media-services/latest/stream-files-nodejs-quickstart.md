---
title: Video bestanden streamen met Azure Media Services-Node.js
description: Volg de stappen in deze zelfstudie om een nieuw Azure Media Services-account te maken, een bestand te coderen en dit vervolgens te streamen naar Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, streamen
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 711db5135345fd9b69da9aaea5bc4dbb929d27c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895133"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Zelfstudie: Extern bestand coderen op basis van URL en video streamen - Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Deze zelfstudie laat zien hoe u met Azure Media Services eenvoudig video's kunt coderen en streamen naar allerlei verschillende browsers en apparaten. De invoerinhoud kan worden opgegeven met HTTPS-URL's, SAS-URL's of paden naar bestanden in Azure Blob-opslag.

Met het voorbeeld in dit artikel wordt inhoud gecodeerd die u toegankelijk maakt via een HTTPS-URL. Op dit moment biedt AMS v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's.

Als u deze zelfstudie hebt voltooid, weet u hoe u een video kunt streamen.  

![De video afspelen](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://nodejs.org/en/download/) installeren
- [Een Azure Media Services-account maken](./create-account-howto.md).<br/>Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U hebt deze nodig voor toegang tot de API.

## <a name="download-and-configure-the-sample"></a>Het voorbeeld downloaden en configureren

Gebruik de volgende opdracht om een GitHub-opslagplaats te klonen op uw computer die het Node.js-voorbeeld voor het streamen van video bevat:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Het voorbeeld bevindt zich in de map [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Open [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) in het gedownloade project. Vervang de `endpoint config`-waarden door referenties die u hebt verkregen via [toegang tot API's](./access-api-howto.md).

In het voorbeeld worden de volgende acties uitgevoerd:

1. Er wordt een **transformatie** gemaakt (eerst wordt gecontroleerd of de opgegeven transformatie bestaat). 
2. Er wordt een uitvoer **asset** gemaakt die wordt gebruikt als uitvoer van de coderings **taak**.
3. De invoer van de **taak** wordt gemaakt en is gebaseerd op een HTTPS-URL.
4. De coderings **taak** wordt met behulp van de eerder gemaakte invoer en uitvoer verzonden.
5. De status van de taak wordt gecontroleerd.
6. Er wordt een **streaming-locator** gemaakt.
7. Er worden streaming-URL's samengesteld.

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

1. De app downloadt gecodeerde bestanden. Maak een map op de locatie waar u de uitvoerbestanden wilt opslaan en werk de waarde van de variabele **outputFolder** in het bestand [index. js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) bij.
1. Open de **opdrachtprompt**, blader naar de map van het voorbeeld en voer de volgende opdrachten uit.

    ```
    npm install 
    node index.js
    ```

Nadat deze is uitgevoerd, ziet u vergelijkbare uitvoer:

![Schermopname van een opdrachtvenster met uitvoer van de voorbeeld-app StreamFileSample met de URL's van drie bestanden die naar de lokale map zijn gedownload.](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testen met Azure Media Player

In dit artikel gebruiken we Azure Media Player om de stream te testen. 

> [!NOTE]
> Als een speler wordt gehost op een https-site, moet u de URL bijwerken naar 'https'.

1. Open een browser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in het vak **URL:** een van de streaming URL's die u hebt verkregen door het uitvoeren van de toepassing. 
 
     U kunt de URL plakken in de HLS-, Dash-, of Smooth-indeling. Azure Media Player schakelt over op naar een geschikt streaming-protocol zodat de stream automatisch op uw apparaat wordt afgespeeld.
3. Klik op **Update Player**.

Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources in de resourcegroep niet meer nodig hebt, met inbegrip van het Media Services-account en de opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep.

Voer de volgende CLI-opdracht uit:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Zie ook

[Taakfoutcodes](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Media Services-concepts](concepts-overview.md)
