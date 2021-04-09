---
title: Een overlay met Media Encoder Standard maken
description: Meer informatie over het maken van een overlay met Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 69930e06e2ce7f2679feec74ca8ccbc93bdb8d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721120"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Een overlay met Media Encoder Standard maken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met de Media Encoder Standard kunt u een afbeelding, audio bestand of andere video naar een andere video bedekken. In de invoer moet precies één bestand worden opgegeven. U kunt een afbeeldings bestand in JPG-, PNG-, GIF-of BMP-indeling opgeven, of een audio bestand (zoals een WAV-, MP3-, WMA-of M4A-bestand) of een video bestand.


## <a name="prerequisites"></a>Vereisten

* Verzamel de account gegevens die u nodig hebt om de *appsettings.js* in het bestand te configureren in het voor beeld. Als u niet zeker weet hoe u dit moet doen, raadpleegt u [Quick Start: een toepassing registreren bij het micro soft Identity-platform](../../active-directory/develop/quickstart-register-app.md). De volgende waarden worden verwacht in de *appsettings.jsvoor* het bestand.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Als u nog niet bekend bent met trans formaties, is het raadzaam om de volgende activiteiten uit te voeren:

* [Video en audio coderen met behulp van Media Services](encoding-concept.md)
* Lees [hoe u codeert met een aangepaste trans formatie-.net](customize-encoder-presets-how-to.md). Volg de stappen in dit artikel om de .NET in te stellen die nodig zijn om met trans formaties te werken en ga vervolgens hier terug om een voor beeld van een overlay voor te bereiden.
* Bekijk het [referentie document transformeert](/rest/api/media/transforms).

Wanneer u bekend bent met trans formaties, downloadt u het voor beeld van de overlays.

## <a name="overlays-preset-sample"></a>Voor beeld van overlays vooraf ingesteld

Down load het voor [beeld van Media Services-overlay](https://github.com/Azure-Samples/media-services-overlays) om aan de slag te gaan met overlays.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
