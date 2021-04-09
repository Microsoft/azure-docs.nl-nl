---
title: De portal gebruiken om inhoud te uploaden, coderen en te streamen
description: In deze quickstart ziet u hoe u de portal kunt gebruiken om inhoud te uploaden, coderen en streamen met Azure Media Services.
ms.topic: quickstart
ms.date: 08/31/2020
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.openlocfilehash: 3f175ff8e7c809032f35cdea9dc3cffa8345b82c
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106820"
---
# <a name="quickstart-upload-encode-and-stream-content-with-portal"></a>Quickstart: Inhoud uploaden, coderen en streamen met de portal

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In deze quickstart ziet u hoe u Azure Portal kunt gebruiken om inhoud te uploaden, coderen en streamen met Azure Media Services.
  
## <a name="overview"></a>Overzicht

* Als u wilt beginnen met het versleutelen, coderen, analyseren en streamen van media-inhoud in Azure, moet u een Media Services-account maken en uw digitale mediabestand van hoge kwaliteit uploaden naar een **asset**. 
    
    > [!NOTE]
    > Als uw video eerder is geüpload naar het Media Services-account met behulp van de Media Services v3-API of als de inhoud is gegenereerd op basis van live-uitvoer, zijn de knoppen **Coderen**, **Analyseren** of **Versleutelen** niet zichtbaar in Azure Portal. Gebruik de Media Services v3-API's om deze taken uit te voeren.

    Bekijk het volgende: 

  * [Uploaden naar en opslaan in de cloud](storage-account-concept.md)
  * [Het concept van assets](assets-concept.md)
* Wanneer u uw digitale mediabestand van hoge kwaliteit hebt geüpload naar een asset (een invoerasset), kunt u het verwerken (coderen of analyseren). De verwerkte inhoud gaat naar een andere asset (uitvoerasset). 
    * [Codeer](encode-concept.md) uw geüploade bestand in indelingen die kunnen worden afgespeeld via een groot aantal verschillende browsers en apparaten.
    * [Analyseer](analyze-video-audio-files-concept.md) uw geüploade bestand. 

        Momenteel kunt u bij gebruik van Azure Portal het volgende doen: TTML- en WebVTT-ondertitelingsbestanden genereren. Bestanden in deze indelingen kunnen worden gebruikt om audio- en videobestanden toegankelijk te maken voor mensen met een gehoorbeperking. U kunt ook trefwoorden uit uw inhoud extraheren.

        Voor een uitgebreide ervaring waarmee u inzichten kunt extraheren uit uw video- en audiobestanden gebruikt u Media Services v3-standaardinstellingen (zoals beschreven in [Zelfstudie: Video's analyseren met Media Services v3](analyze-videos-tutorial.md)). <br/>Als u gedetailleerdere inzichten wilt, gebruikt u [Video Indexer](../video-indexer/index.yml) rechtstreeks.    
* Zodra uw inhoud is verwerkt, kunt u media-inhoud leveren aan clientspelers. Om video's in de uitvoerasset beschikbaar te maken voor clients om af te spelen, moet u een **streaming-locator** maken. Bij het maken van de **streaming-locator** moet u een **beleid voor streaming** opgeven. Met **beleid voor streaming** kunt u streamingprotocollen en versleutelingsopties voor uw **streaming-locators** definiëren (indien aanwezig).
    
    Bekijk:

    * [Streaming-locators](streaming-locators-concept.md)
    * [Beleid voor streaming](streaming-policy-concept.md)
    * [Verpakking en levering](encode-dynamic-packaging-concept.md)
    * [Filters](filters-concept.md)
* U kunt uw inhoud beveiligen door deze te versleutelen met Advanced Encryption Standard (AES-128) of/en een van de drie belangrijkste DRM-systemen: Microsoft PlayReady, Google Widevine en Apple FairPlay. In de quickstart [Inhoud versleutelen met Azure Portal](drm-encrypt-content-how-to.md) ziet u hoe u inhoudsbeveiliging kunt configureren.
        
## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[Een Azure Media Services-account maken](account-create-how-to.md)

## <a name="upload"></a>Uploaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zoek uw Media Services-account en klik erop.
1. Selecteer **Assets (nieuw)** .
1. Druk boven in het venster op **Uploaden**. 
1. Sleep een bestand en zet het neer of blader naar een bestand dat u wilt uploaden.

Als u naar uw assetsvenster gaat, ziet u dat er een nieuwe asset is toegevoegd aan de lijst:

![Schermopname van de Azure Portal die de weergave van het activavenster toont dat is geopend door assets te selecteren (nieuw), en een nieuwe asset die is toegevoegd door de knop uploaden te selecteren.](./media/asset-create-asset-upload-portal-quickstart/upload.png)

## <a name="encode"></a>Coderen

1. Selecteer **Assets (nieuw)** .
1. Selecteer uw nieuwe asset (toegevoegd in de laatste stap).
1. Klik boven in het venster op **Coderen**.

    Door op deze knop te klikken wordt de coderingstaak gestart. Wanneer het proces is voltooid, is er een uitvoerasset gegenereerd dat de gecodeerde inhoud bevat.

Als u naar uw assetsvenster gaat, ziet u dat de uitvoerasset is toegevoegd aan de lijst:

![Schermopname van het venster Assets in Azure Portal waarin de asset ignite.mp4 Media Encoded Standard aan de lijst met assets wordt toegevoegd.](./media/asset-create-asset-upload-portal-quickstart/encode.png)

## <a name="monitor-the-job-progress"></a>De voortgang van de taak controleren

Navigeer naar **Taken** om de status van de taak weer te geven. De taak doorloopt meestal de volgende statussen: Gepland, In wachtrij, Verwerken, Voltooid (de eindstatus). Als bij de taak een fout is opgetreden, krijgt u de status Fout.

![Status](./media/asset-create-asset-upload-portal-quickstart/job-status.png)

## <a name="publish-and-stream"></a>Publiceren en streamen

Als u een asset wilt publiceren, moet u nu een streaming-locator toevoegen aan uw asset.

### <a name="streaming-locator"></a>Streaming-locator 

1. Druk in de sectie **Streaming-locator** op **+ Een streaming-locator toevoegen**.
    Hiermee wordt de asset gepubliceerd en worden de streaming-URL's gegenereerd.

    > [!NOTE]
    > Als u de stream wilt versleutelen, moet u beleid voor inhoudssleutels maken en dit instellen op de streaming-locator. Zie [Inhoud versleutelen met Azure Portal](drm-encrypt-content-how-to.md) voor meer informatie.
1. In het venster **Streaming-locator toevoegen** kiest u vooraf gedefinieerd beleid voor streaming. Zie [Beleid voor streaming](streaming-policy-concept.md) voor gedetailleerde informatie

    ![Streaming-locator](./media/asset-create-asset-upload-portal-quickstart/streaming-locator.png)

Zodra de asset is gepubliceerd, kunt u deze rechtstreeks naar de portal streamen. 

![Afspelen](./media/asset-create-asset-upload-portal-quickstart/publish.png)

Of kopieer de streaming-URL en gebruik deze in uw clientspeler.

> [!NOTE]
> Zorg ervoor dat uw [streaming-eindpunt](streaming-endpoint-concept.md) wordt uitgevoerd. Wanneer u voor het eerst een Media Service-account maakt, wordt het standaardstreaming-eindpunt gemaakt en heeft het de status Gestopt. U moet het dus starten voordat u uw inhoud kunt streamen.<br/>U wordt alleen gefactureerd wanneer uw streaming-eindpunt wordt uitgevoerd.

## <a name="cleanup-resources"></a>Resources opruimen

Als u de andere quickstarts wilt proberen, moet u de gemaakte resources bewaren. Anders gaat u naar de Azure-portal, bladert u naar de resourcegroepen, selecteert u de resourcegroep waaronder u deze quickstart hebt uitgevoerd en verwijdert u alle resources.

## <a name="next-steps"></a>Volgende stappen

[De portal gebruiken om inhoud te versleutelen](drm-encrypt-content-how-to.md)
