---
title: Bestanden uploaden naar een Media Services-account via Azure Portal | Microsoft Docs
description: In deze zelfstudie wordt stapsgewijs uitgelegd hoe u bestanden uploadt naar een Media Services-account via Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c2dc193d65ff1c85837477c0a8fd345f11d59bcd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009742"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Bestanden uploaden naar een Media Services-account via Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. Voor de bijgewerkte Upload bestanden met de portal raadpleegt u [Portal gebruiken om inhoud te uploaden, coderen en streamen](../latest/manage-assets-quickstart.md).<br/>Kijk ook eens naar: [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In Azure Media Services uploadt u de digitale bestanden naar een asset. De asset kan video, audio, afbeeldingen, verzamelingen van miniaturen, tekstsporen en ondertitelingsbestanden (en de metagegevens voor deze bestanden) bevatten. Nadat de bestanden zijn geüpload, wordt uw inhoud veilig opgeslagen in de cloud voor verdere verwerking en streaming.

Media Services heeft een maximale bestandsgrootte voor het verwerken van bestanden. Zie [Media Services quotas and limitations](media-services-quotas-and-limitations.md) (Quota en beperkingen voor Media Services) voor meer informatie over maximale bestandsgrootte.

U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie. 

## <a name="upload-files"></a>Bestanden uploaden
1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **Instellingen** > **Assets**. Selecteer de knop **Uploaden**.
   
    ![Bestanden uploaden](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Het venster **Videoasset uploaden** wordt weergegeven.
   
   > [!NOTE]
   > Media Services heeft geen maximale bestandsgrootte voor het uploaden van video's.
 
3. Ga op uw computer naar de video die u wilt uploaden. Selecteer de video en selecteer vervolgens **OK**.  
   
    Het uploaden wordt gestart. U kunt de voortgang onder de bestandsnaam bekijken.  

Wanneer het uploaden is voltooid, wordt de nieuwe asset in het deelvenster **Assets** weergegeven. 

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [coderen van de geüploade assets](media-services-portal-encode.md).

* U kunt ook Azure Functions gebruiken om een coderingstaak te activeren wanneer er een bestand binnenkomt in de geconfigureerde container. Bekijk voor meer informatie het voorbeeld in [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Media Services: Azure Media Services integreren met Azure Functions en Logic Apps).
