---
title: Video bestanden streamen met Azure Media Services CLI
description: Volg de stappen in deze zelfstudie om Azure CLI te gebruiken om een nieuw Azure Media Services-account te maken, een bestand te coderen en dit vervolgens te streamen naar Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, streamen
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c78205d7e2b41628de9e8b92c9fa5506e82158cb
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954493"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Zelfstudie: Extern bestand coderen op basis van URL en video streamen - Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Deze zelfstudie laat zien hoe u met Azure Media Services en de Azure CLI eenvoudig video's kunt coderen en streamen naar verschillende browsers en apparaten. U kunt invoerinhoud opgeven met HTTPS-URL's, SAS-URL's of paden naar bestanden in Azure Blob-opslag.

Met het voorbeeld in dit artikel wordt inhoud gecodeerd die u toegankelijk maakt via een HTTPS-URL. Op dit moment biedt Media Services v3 geen ondersteuning voor gesegmenteerde overdrachtscodering via HTTPS-URL's.

Als u deze zelfstudie hebt voltooid, weet u hoe u een video kunt streamen.  

![De video afspelen](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

Voordat u media-inhoud in Azure kunt opslaan, coderen, analyseren, versleutelen, beheren en streamen, moet u een Media Services-account maken. Dat account moet worden gekoppeld aan een of meer opslagaccounts.

Uw Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. We raden u aan om opslagaccounts te gebruiken die zich op dezelfde locatie bevinden als het Media Services-account om de latentie en kosten voor gegevensuitvoer te beperken.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

In dit voorbeeld maakt u een Standard LRS-account voor algemeen gebruik (v2).

Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Als u een SKU voor productie selecteert, kunt u overwegen om `--sku Standard_RAGRS` te gebruiken. Deze biedt geografische replicatie voor bedrijfscontinuïteit. Zie [Opslagaccounts](/cli/azure/storage/account) voor meer informatie.

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Een Azure Media Services-account maken

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

U krijgt een antwoord zoals dit:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Het streaming-eindpunt starten

Met de volgende Azure CLI-opdracht wordt het standaard-**streaming-eindpunt** gestart.

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

U krijgt een antwoord zoals dit:

```
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Als het streaming-eindpunt al wordt uitgevoerd, wordt dit bericht weergegeven:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Een transformatie maken voor adaptieve bitratecodering

Maak een **Transformatie** om veelvoorkomende taken voor het coderen of analyseren van video's te configureren. In dit voorbeeld gaan we een adaptieve bitratecodering uitvoeren. Vervolgens verzenden we een taak onder de transformatie die we hebben gemaakt. De taak is de aanvraag aan Media Services om de transformatie toe te passen op de inhoud van de gegeven invoervideo of -audio.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

U krijgt een antwoord zoals dit:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Een uitvoeractivum maken

Maak een **uitvoerasset** die wordt gebruikt als uitvoer van de coderingstaak.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

U krijgt een antwoord zoals dit:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Een taak starten met behulp van HTTPS-invoer

Wanneer u taken verzendt voor het verwerken van video's, moet u aan Media Services de locatie van de invoervideo doorgeven. Een optie is dat u een HTTPS-URL als taakinvoer opgeeft, zoals in dit voorbeeld.

Wanneer u `az ams job start` uitvoert, kunt u een label instellen op de uitvoer van de taak. U kunt vervolgens het label gebruiken om aan te geven waarvoor het uitvoeractivum bedoeld is.

- Als u een waarde aan het label toewijst, stelt u '--output-assets' in op 'assetname=label'.
- Als u geen waarde aan het label toewijst, stelt u '--output-assets' in op 'assetname='.

  Merk op dat we '=' toevoegen aan `output-assets`.

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

U krijgt een antwoord zoals dit:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Status controleren

Controleer na ongeveer vijf minuten de status van de taak. Deze moet 'Voltooid' zijn. Als deze niet 'Voltooid' is, wacht u enkele minuten en controleert u de status opnieuw. Als deze 'Voltooid' is, gaat u naar de volgende stap en maakt u een **streaming-locator**.

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Een streaming-locator maken en een pad verkrijgen

Wanneer de codering is voltooid, bestaat de volgende stap eruit om de video in de uitvoerasset beschikbaar te maken voor weergave door clients. Hiertoe maakt u eerst een streaming-locator. Vervolgens bouwt u streaming-URL's die clients kunnen gebruiken.

### <a name="create-a-streaming-locator"></a>Een streaming-locator te maken

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

U krijgt een antwoord zoals dit:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Streaming-locatorpaden verkrijgen

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

U krijgt een antwoord zoals dit:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Kopieer het HLS-pad (HTTP Live Streaming). In dit geval is dat `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-the-url"></a>De URL bouwen

### <a name="get-the-streaming-endpoint-host-name"></a>De hostnaam van het streaming-eindpunt ophalen

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Kopieer de waarde van `hostName`. In dit geval is dat `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-the-url"></a>De URL samenstellen

'https://' + &lt;waarde van hostnaam&gt; + &lt;padwaarde Hls&gt;

Hier volgt een voorbeeld:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Afspelen testen met behulp van Azure Media Player

> [!NOTE]
> Als een speler wordt gehost op een HTTPS-site, moet de URL beginnen met 'https'.

1. Open een webbrowser en ga naar [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Plak in het vak **URL** de URL die u in de vorige sectie hebt samengesteld. U kunt de URL plakken in de indeling HLS, Dash of Smooth. In Azure Media Player wordt automatisch een geschikt streaming-protocol gebruikt voor uw apparaat.
3. Selecteer **Speler bijwerken**.

>[!NOTE]
>Azure Media Player kan worden gebruikt voor testdoeleinden, maar mag niet worden gebruikt in een productieomgeving.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources in de resourcegroep niet meer nodig hebt, met inbegrip van het Media Services-account en de opslagaccounts die u hebt gemaakt voor deze zelfstudie, verwijdert u de resourcegroep.

Voer deze Azure CLI-opdracht uit:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services](media-services-overview.md)
