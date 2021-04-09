---
title: Clouds en regio's voor Azure Media Services v3
description: In dit artikel vindt u informatie over de Url's die worden gebruikt voor eind punten en code voor regio's.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954324"
---
# <a name="regional-code-names-and-endpoints"></a>Regionale codenamen en eindpunten

### <a name="region-code-name"></a>Regio code naam

Wanneer de **locatie** parameter wordt gebruikt in een opdracht of aanvraag, moet u de regio code naam opgeven als **locatie** waarde. Als u de code naam wilt ophalen van de regio waarin uw account zich bevindt en de aanroep moet worden doorgestuurd naar, kunt u de volgende regel uitvoeren in azure CLI.

```azurecli-interactive
az account list-locations
```

Wanneer u de hierboven weer gegeven regel hebt uitgevoerd, krijgt u een lijst met alle Azure-regio's. Ga naar de Azure-regio met de *DisplayName* die u zoekt en gebruik de *naam* waarde voor de **locatie** parameter.

Voor de Azure-regio vs-West 2 (hieronder weer gegeven) gebruikt u bijvoorbeeld ' westus2 ' voor de **locatie** parameter.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Eindpunten  

De volgende eind punten zijn belang rijk om te weten wanneer u verbinding maakt met Media Services accounts van verschillende nationale Azure-Clouds.

### <a name="global-azure"></a>Wereld wijd Azure

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Verificatie | `https://login.microsoftonline.com/` |
| Token doelgroep | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Verificatie | `https://login.microsoftonline.us/` |
| Token doelgroep | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Duitsland

> [!NOTE]
> De Azure Duitsland-eind punten zijn alleen van toepassing op de soevereine Clouds in Duitsland.

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Verificatie | `https://login.microsoftonline.de/` |
| Token doelgroep | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Service | Eindpunt |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Verificatie | `https://login.chinacloudapi.cn/` |
| Token doelgroep |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Zie ook

* [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)
* [Regionale codenamen en eindpunten](azure-regions-code-names.md)
* [Geografische gebieden in Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services v3](media-services-overview.md)
