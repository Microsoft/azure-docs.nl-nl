---
title: Filters maken met Azure Media Services v3 REST API
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stroom te streamen. Media Services v3-REST API maakt dynamische manifesten om deze selectief streaming te verzorgen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c1d7bf933b487c40d571f1912341b5ef771e4e67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90527323"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Filters maken met Media Services REST API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bij het leveren van uw inhoud aan klanten (het streamen van Live-gebeurtenissen of video op aanvraag), heeft uw client mogelijk meer flexibiliteit nodig dan is beschreven in het manifest bestand van het standaard activum. Met Azure Media Services kunt u account filters en activa filters definiëren voor uw inhoud. 

Zie [dynamische manifesten](filters-dynamic-manifest-overview.md) en [filters](filters-concept.md)voor een gedetailleerde beschrijving van deze functie en scenario's waarin deze wordt gebruikt.

In dit onderwerp wordt uitgelegd hoe u een filter definieert voor een video op aanvraag-Asset en REST-Api's gebruikt om [account filters](/rest/api/media/accountfilters) en- [activa filters](/rest/api/media/assetfilters)te maken. 

> [!NOTE]
> Zorg ervoor dat u de [presentationTimeRange](filters-concept.md#presentationtimerange)controleert.

## <a name="prerequisites"></a>Vereisten 

Voor het uitvoeren van de stappen die in dit onderwerp worden beschreven, moet u het volgende doen:

- Controleer [filters en dynamische manifesten](filters-dynamic-manifest-overview.md).
- [Postman configureren voor Azure Media Services rest API-aanroepen](media-rest-apis-with-postman.md).

    Zorg ervoor dat u de laatste stap in het onderwerp [Azure AD-Token ophalen](media-rest-apis-with-postman.md#get-azure-ad-token)volgt. 

## <a name="define-a-filter"></a>Een filter definiëren  

Hieronder ziet u het voor beeld van een **aanvraag tekst** waarin de selectie voorwaarden voor het volgen worden gedefinieerd die worden toegevoegd aan het manifest. Dit filter bevat audio tracks die EC-3 zijn en video tracks met bitsnelheid in het 0-1000000-bereik.

```json
{
    "properties": {
        "tracks": [
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
                        "operation": "Equal"
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
     }
}
```

## <a name="create-account-filters"></a>Account filters maken

Selecteer in de verzameling die u hebt gedownload van de Postman, **account filters** -> **een account filter maken of bijwerken**.

De methode voor het **opnemen** van HTTP-aanvragen is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecteer het tabblad **hoofd tekst** en plak de JSON-code die u [eerder hebt gedefinieerd](#define-a-filter).

Selecteer **Verzenden**. 

Het filter is gemaakt.

Zie [maken of bijwerken](/rest/api/media/accountfilters/createorupdate)voor meer informatie. Zie ook [JSON-voor beelden voor filters](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Activa filters maken  

Selecteer in de Postman-verzameling Media Services v3 die u hebt gedownload, **activa** -> **filter maken of bijwerken**.

De methode voor het **opnemen** van HTTP-aanvragen is vergelijkbaar met:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecteer het tabblad **hoofd tekst** en plak de JSON-code die u [eerder hebt gedefinieerd](#define-a-filter).

Selecteer **Verzenden**. 

Het activa filter is gemaakt.

Zie [maken of bijwerken](/rest/api/media/assetfilters/createorupdate)voor meer informatie over het maken of bijwerken van Asset-filters. Zie ook [JSON-voor beelden voor filters](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Filters koppelen aan streaming-Locator

U kunt een lijst opgeven met activa of account filters die van toepassing zijn op uw streaming-Locator. Met de [dynamische pakket (streaming-eind punt)](dynamic-packaging-overview.md) wordt deze lijst met filters toegepast, samen met de gegevens die door uw client zijn opgegeven in de URL. Deze combi natie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md)dat is gebaseerd op filters in de URL + filters die u opgeeft in de streaming-Locator. U wordt aangeraden deze functie te gebruiken als u filters wilt Toep assen, maar niet de filter namen in de URL wilt weer geven.

Als u filters met een streaming-Locator wilt maken en koppelen met behulp van REST, gebruikt u de [streams](/rest/api/media/streaminglocators/create) voor het maken van een API en geeft u `properties.filters` in de [aanvraag tekst](/rest/api/media/streaminglocators/create#request-body)op.
                                
## <a name="stream-using-filters"></a>Streamen met filters

Zodra u filters hebt gedefinieerd, kunnen uw clients deze gebruiken in de streaming-URL. Filters kunnen worden toegepast op Adaptive Bitrate Streaming protocollen: Apple HTTP Live Streaming (HLS), MPEG-DASH en Smooth Streaming.

In de volgende tabel ziet u enkele voor beelden van Url's met filters:

|Protocol|Voorbeeld|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Volgende stappen

[Video streamen](stream-files-tutorial-with-rest.md) 
