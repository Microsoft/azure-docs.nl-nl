---
title: Aan de slag met het leveren van video on demand met Azure Portal | Microsoft Docs
description: In deze zelfstudie wordt u begeleid bij het implementeren van een basisservice voor levering van video on demand-inhoud met een Azure Media Services-toepassing via Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: fc908f909cdb64e968db78b2c98b8f0edf1c61dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009688"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Aan de slag met het leveren van inhoud op aanvraag via Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In deze zelfstudie wordt u begeleid bij het implementeren van een basisservice voor levering van video on demand-inhoud met een Azure Media Services-toepassing via Azure Portal.

## <a name="prerequisites"></a>Vereisten
Hieronder wordt aangegeven wat de vereisten zijn om de zelfstudie te voltooien:

* Een Azure-account. Zie [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie. 
* Een Media Services-account. Zie [een Media Services account maken](media-services-portal-create-account.md)als u een Media Services account wilt maken.

Deze zelfstudie bevat de volgende taken:

1. Het streaming-eindpunt starten.
2. Een videobestand uploaden.
3. Het bronbestand coderen in een set Adaptive Bitrate MP4-bestanden.
4. De asset publiceren en URL's voor streamen en progressief downloaden ophalen.  
5. Uw inhoud afspelen.

## <a name="start-the-streaming-endpoint"></a>Het streaming-eindpunt starten

Bij het werken met Azure Media Services wordt video meestal via Adaptive Bitrate Streaming geleverd. Media Services biedt dynamische pakketten. Met dynamische pakketten kunt u uw Adaptive Bitrate MP4-inhoud leveren in Just-In-Time-streaming-indelingen die worden ondersteund door Media Services. Voorbeelden zijn HTTP Live Streaming (HLS) van Apple, Smooth Streaming van Microsoft en Dynamic Adaptive Streaming via HTTP (DASH, ook wel MPEG-DASH genoemd). Met behulp van Adaptive Bitrate Streaming van Media Services, kunt u uw video's leveren zonder voorverpakte versies van elk van deze streaming-indelingen op te slaan.

> [!NOTE]
> Wanneer u een Media Services-account maakt, wordt er een standaardstreaming-eindpunt met de status **Gestopt** aan uw account toegevoegd. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 

Het streaming-eindpunt starten:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **instellingen**  >  **streaming-eind punten**. 
3. Selecteert het standaardstreaming-eindpunt. Het venster **standaard gegevens van het streaming-eind punt** wordt weer gegeven.
4. Selecteer het pictogram **Start**.
5. Selecteer de knop **Opslaan**.

## <a name="upload-files"></a>Bestanden uploaden
Als u video's wilt streamen met Media Services, moet u de bronvideo's uploaden, ze coderen in meerdere bitsnelheden en vervolgens het resultaat publiceren. De eerste stap wordt in deze sectie beschreven. 

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **Instellingen** > **Assets**. Selecteer de knop **Uploaden**.
   
    ![Bestanden uploaden](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Het venster **Videoasset uploaden** wordt weergegeven.
   
   > [!NOTE]
   > Media Services heeft geen maximale bestandsgrootte voor het uploaden van video's.
   > 
   > 
3. Ga op uw computer naar de video die u wilt uploaden. Selecteer de video en selecteer vervolgens **OK**.  
   
    Het uploaden wordt gestart. U kunt de voortgang onder de bestandsnaam bekijken.  

Wanneer het uploaden is voltooid, wordt de nieuwe asset in het deelvenster **Assets** weergegeven. 

## <a name="encode-assets"></a>Assets coderen
U moet het bronbestand coderen in een set multi-bitrate MP4-bestanden om van dynamische pakketten gebruik te maken. De coderingsstappen worden in deze sectie uitgelegd.

### <a name="encode-assets-in-the-portal"></a>Assets in de portal coderen
Uw inhoud coderen met behulp van Media Encoder Standard in Azure Portal:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **Instellingen** > **Assets**. Selecteer de asset die u wilt coderen.
3. Selecteer de knop **Coderen**.
4. Selecteer in het deelvenster **Een asset coderen** de processor **Media Encoder Standard** en een standaardinstelling. Zie [automatisch een bitrate ladder genereren](media-services-autogen-bitrate-ladder-with-mes.md) en [Standaardinstellingen voor taken in Media Encoder Standard](media-services-mes-presets-overview.md) voor informatie over standaardinstellingen. Het is belangrijk om de standaardinstelling te kiezen die het meest geschikt is voor uw invoervideo. Als u bijvoorbeeld weet dat uw invoervideo een resolutie van 1920 x 1080 pixels heeft, kunt u de standaardinstelling **H264 Multiple Bitrate 1080p** gebruiken. Als u een video met lage resolutie hebt (640 x 360), kunt u beter niet de standaardinstelling **H264 Multiple Bitrate 1080p** gebruiken.
   
   U kunt de naam van de uitvoerasset en de naam van de taak bewerken, zodat u uw resources beter kunt beheren.
   
   ![Assets coderen](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecteer **Maken**.

### <a name="monitor-encoding-job-progress"></a>De voortgang van het codering van de taak bewaken
Als u de voortgang van de coderingstaak wilt controleren, klikt u bovenaan de pagina op **Instellingen** en selecteert u vervolgens **Taken**.

![Taken](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Inhoud publiceren
Als u aan uw gebruikers een URL wilt leveren die ze kunnen gebruiken om uw inhoud te streamen of te downloaden, moet u uw asset eerst publiceren door een locator te maken. Locators bieden toegang tot bestanden in de asset. Azure Media Services ondersteunt twee typen locators: 

* **Streaming-locators (OnDemandOrigin)**. Streaming-locators worden gebruikt voor adaptief streamen. Voorbeelden van adaptief streamen zijn HLS, Smooth Streaming en MPEG-DASH. Als u een streaming-locator wilt maken, moet uw asset een ISM-bestand bevatten. 
* **Progressieve locators (Shared Access Signature)**. Progressieve locators worden gebruikt voor het leveren van video via progressief downloaden.

Als u een HLS streaming-URL wilt maken, voegt u *(Format = M3U8-AAPL)* toe aan de URL:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)`

Gebruik de volgende URL-indeling om een streaming-URL te maken voor het afspelen van Smooth Streaming-assets:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest`

Als u een streaming-URL voor MPEG-DASH wilt maken, voegt u *(format=mpd-time-csf)* toe aan de URL:

`{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)`

Een Shared Access Signature-URL heeft de volgende indeling:

`{blob container name}/{asset name}/{file name}/{shared access signature}`

> [!NOTE]
> Locators die vóór maart 2015 in Azure Portal zijn gemaakt, hebben een vervaldatum over twee jaar.  
> 
> 

Als u een vervaldatum van een locator wilt bijwerken, kunt u een [REST API](/rest/api/media/operations/locator#update_a_locator) of een [.NET API](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator) gebruiken. 

> [!NOTE]
> Wanneer u de vervaldatum van een Shared Access Signature-locator bijwerkt, wordt de URL gewijzigd.

### <a name="to-use-the-portal-to-publish-an-asset"></a>De portal gebruiken om een asset te publiceren
1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **Instellingen** > **Assets**. Selecteer de asset die u wilt publiceren.
3. Selecteer de knop **Publiceren**.
4. Selecteer het type locator.
5. Selecteer **Toevoegen**.
   
    ![De video publiceren](./media/media-services-portal-vod-get-started/media-services-publish1.png)

De URL wordt toegevoegd aan de lijst met **gepubliceerde URL's**.

## <a name="play-content-from-the-portal"></a>Inhoud afspelen vanuit de portal
U kunt uw video testen in een speler in Azure Portal.

Selecteer de video en selecteer vervolgens de knop **Afspelen**.

![De video afspelen in Azure Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Hierbij geldt het volgende:

* Als u wilt beginnen met streamen, activeert u het standaardstreaming-eindpunt.
* De video moet zijn gepubliceerd.
* Met de mediaspeler in Azure Portal wordt inhoud afgespeeld vanaf het standaardstreaming-eindpunt. Als u inhoud vanaf een ander streaming-eindpunt wilt afspelen, selecteert en kopieert u de URL en plak u deze in een andere speler. U kunt uw video bijvoorbeeld in de [Azure Media Player](https://aka.ms/azuremediaplayer) testen.

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]