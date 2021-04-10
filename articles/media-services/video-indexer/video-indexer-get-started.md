---
title: Registreren voor Video Indexer en uw eerste video uploaden - Azure
titleSuffix: Azure Media Services
description: Leer hoe u zich registreert en uw eerste video met behulp van de Video Indexer-portal uploadt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 5b38c731db141052e6700472020cd60b6a4d13a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797806"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Quickstart: Registreren en uw eerste video uploaden

In deze quickstart leert u zich aanmelden bij de Video Indexer-website en hoe u uw eerste video kunt uploaden.

Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij de gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met de betaalde versie maakt u een Video Indexer-account dat is [gekoppeld aan uw Azure-abonnement en een Azure Media Services-account](connect-to-azure.md). U betaalt voor geïndexeerde minuten. Zie [Prijzen van mediaservices](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie. 

## <a name="sign-up-for-video-indexer"></a>Registreren voor Video Indexer

Als u wilt gaan ontwikkelen met Video Indexer, gaat u naar de website van [Video Indexer](https://www.videoindexer.ai/) en registreert u zich.

Als u eenmaal begint met het gebruik van Video Indexer, worden al uw opgeslagen gegevens en geüploade inhoud at-rest gecodeerd met een door Microsoft beheerde sleutel.

> [!NOTE]
> Bekijk de [geplande verificatiewijzigingen voor de Video Indexer-website](release-notes.md#planned-video-indexer-website-authenticatication-changes).

## <a name="upload-a-video-using-the-video-indexer-website"></a>Een video met behulp van de Video Indexer-website uploaden

### <a name="supported-file-formats-for-video-indexer"></a>Ondersteunde bestandsindelingen voor Video Indexer

Zie het artikel [invoercontainer/bestandsindelingen](../latest/media-encoder-standard-formats.md#input-containerfile-formats) voor een lijst bestandsindelingen die u kunt gebruiken met Video Indexer.

### <a name="upload-a-video"></a>Een video uploaden

1. Registreer u op de [Video Indexer](https://www.videoindexer.ai/)-website.
1. Als u een video wilt uploaden, drukt u op de knop of link **Uploaden**.

    > [!NOTE]
    > De naam van de video mag niet langer zijn dan 80 tekens.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Uploaden":::
1. Zodra uw video is geüpload, start Video Indexer met het indexeren en analyseren van de video. U ziet de voortgang. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Voortgang van het uploaden":::
1. Wanneer Video Indexer klaar is met analyseren, ontvangt u een e-mailbericht met een link naar uw video en een korte beschrijving van wat is gevonden in uw video. Bijvoorbeeld: personen, gesproken en geschreven woorden, onderwerpen en benoemde entiteiten.
1. U kunt uw video later vinden in de bibliotheeklijst en verschillende bewerkingen uitvoeren. Bijvoorbeeld: zoeken, opnieuw indexeren, bewerken.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Het uploaden is klaar":::

## <a name="supported-browsers"></a>Ondersteunde browsers

Zie [ondersteunde browsers](video-indexer-overview.md#supported-browsers)voor meer informatie.

## <a name="see-also"></a>Zie ook

Zie [Video's uploaden en indexeren](upload-index-videos.md) voor meer informatie.

Nadat u een video hebt geüpload en geïndexeerd, kunt u de [Video Indexer](video-indexer-view-edit.md)-website of [Video Indexer Developer Portal](video-indexer-use-apis.md) gebruiken om de inzichten van de video te bekijken. 

[Starten met het gebruik van API's](video-indexer-use-apis.md)

## <a name="next-steps"></a>Volgende stappen

Ga voor een gedetailleerde introductie naar ons [introductielab](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Aan het einde van de workshop hebt u een beter begrip van het soort informatie dat uit video- en audiocontent kan worden gehaald, bent u beter in staat mogelijkheden te identificeren met betrekking tot content intelligence, video AI op Azure te presenteren en verschillende scenario's op Video Indexer te tonen.

