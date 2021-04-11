---
title: CLI gebruiken om filters te maken met Azure Media Services
description: In dit artikel wordt beschreven hoe u CLI gebruikt om filters te maken met Azure Media Services v3.
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
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c3b58981c3b76f86397b7f2724ac5da71c7ad3dc
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960211"
---
# <a name="creating-filters-with-cli"></a>Filters maken met CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bij het leveren van uw inhoud aan klanten (het streamen van Live-gebeurtenissen of video op aanvraag), heeft uw client mogelijk meer flexibiliteit nodig dan is beschreven in het manifest bestand van het standaard activum. Met Azure Media Services kunt u account filters en activa filters definiëren voor uw inhoud.

Zie [dynamische manifesten](filters-dynamic-manifest-overview.md) en [filters](filters-concept.md)voor een gedetailleerde beschrijving van deze functie en scenario's waarin deze wordt gebruikt.

In dit onderwerp wordt beschreven hoe u een filter configureert voor een video op aanvraag en gebruikt u CLI voor Media Services v3 om [account filters](/cli/azure/ams/account-filter) en [activa filters](/cli/azure/ams/asset-filter)te maken.

> [!NOTE]
> Zorg ervoor dat u de [presentationTimeRange](filters-concept.md#presentationtimerange)controleert.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](./account-create-how-to.md). Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet.

## <a name="define-a-filter"></a>Een filter definiëren

In het volgende voor beeld worden de voor waarden voor het bijhouden van selecties gedefinieerd die worden toegevoegd aan het definitieve manifest. Dit filter bevat audio tracks die EC-3 zijn en video tracks met bitsnelheid in het 0-1000000-bereik.

> [!TIP]
> Als u van plan bent om **filters** in rest te definiëren, moet u er rekening mee houden dat u het JSON-object ' Eigenschappen ' kunt toevoegen.  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>Account filters maken

Met de volgende [AZ AMS account-filter](/cli/azure/ams/account-filter) opdracht maakt u een account filter met selecties voor het bijhouden van filters die [eerder zijn gedefinieerd](#define-a-filter).

Met de opdracht kunt u een optionele `--tracks` para meter door geven die JSON bevat die de selecties van het bijhouden vertegenwoordigt.  Gebruik @ {file} om JSON vanuit een bestand te laden. Als u de Azure CLI lokaal gebruikt, geeft u het hele bestandspad op:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Zie ook [JSON-voor beelden voor filters](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Activa filters maken

Met de volgende opdracht [AZ AMS Asset-filter](/cli/azure/ams/asset-filter) Command maakt u een activa filter met selecties voor het bijhouden van filters die [eerder zijn gedefinieerd](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Zie ook [JSON-voor beelden voor filters](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Filters koppelen aan streaming-Locator

U kunt een lijst opgeven met activa of account filters die van toepassing zijn op uw streaming-Locator. Met de [dynamische pakket (streaming-eind punt)](dynamic-packaging-overview.md) wordt deze lijst met filters toegepast, samen met de gegevens die door uw client zijn opgegeven in de URL. Deze combi natie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md)dat is gebaseerd op filters in de URL + filters die u opgeeft in de streaming-Locator. U wordt aangeraden deze functie te gebruiken als u filters wilt Toep assen, maar niet de filter namen in de URL wilt weer geven.

De volgende CLI-code laat zien hoe u een streaming-Locator maakt en opgeeft `filters` . Dit is een optionele eigenschap die een door spaties gescheiden lijst met namen van activa filters en/of account filter namen neemt.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Streamen met filters

Zodra u filters hebt gedefinieerd, kunnen uw clients deze gebruiken in de streaming-URL. Filters kunnen worden toegepast op Adaptive Bitrate Streaming protocollen: Apple HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming.

In de volgende tabel ziet u enkele voor beelden van Url's met filters:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Volgende stap

[Video streamen](stream-files-tutorial-with-api.md)

## <a name="see-also"></a>Zie ook

[Azure-CLI](/cli/azure/ams)
