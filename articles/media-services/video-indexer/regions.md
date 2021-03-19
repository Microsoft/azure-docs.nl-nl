---
title: Regio's waar Video Indexer beschikbaar is-Azure
titleSuffix: Azure Media Services
description: Dit artikel spreekt over Azure-regio's waarin Azure Media Services Video Indexer beschikbaar is.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530931"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-regio's waarin Video Indexer bestaat

Video Indexer-Api's bevatten een **locatie** parameter die u moet instellen op de Azure-regio waarin de aanroep moet worden gerouteerd. Dit moet een Azure-regio zijn waarin [video indexer beschikbaar is](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Locaties

De `location` para meter moet de naam van de Azure-regio code krijgen als waarde. Als u Video Indexer in de preview-modus gebruikt, moet u `"trial"` deze als waarde invoeren. `trial` is de standaard waarde voor de `location` para meter. Als u de code naam wilt ophalen van de Azure-regio waarin uw account zich bevindt en dat uw oproep moet worden doorgestuurd, kunt u de Azure Portal gebruiken of een [Azure cli](/cli/azure) -opdracht uitvoeren.

### <a name="azure-portal"></a>Azure Portal

1. Registreer u op de [Video Indexer](https://www.videoindexer.ai/)-website.
1. Selecteer **gebruikers accounts** in de rechter bovenhoek van de pagina.
1. Zoek de locatie van uw account in de rechter bovenhoek.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Locatie":::
    
###  <a name="cli-command"></a>CLI-opdracht

```azurecli-interactive
az account list-locations
```

Wanneer u de hierboven weer gegeven regel hebt uitgevoerd, krijgt u een lijst met alle Azure-regio's. Ga naar de Azure-regio met de *DisplayName* die u zoekt en gebruik de *naam* waarde voor de **locatie** parameter.

Voor de Azure-regio vs-West 2 (hieronder weer gegeven) gebruikt u bijvoorbeeld ' westus2 ' voor de **locatie** parameter.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Volgende stappen

- [Taal model aanpassen met behulp van Api's](customize-language-model-with-api.md)
- [Het merk model aanpassen met behulp van Api's](customize-brands-model-with-api.md)
- [Persoonlijk model aanpassen met behulp van Api's](customize-person-model-with-api.md)
