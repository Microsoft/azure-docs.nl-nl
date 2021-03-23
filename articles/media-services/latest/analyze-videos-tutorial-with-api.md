---
title: Video's analyseren met Media Services v3
description: Leer hoe u met behulp van Azure Media Services video's kunt analyseren.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: f9c52544160e52c29b2d0e53aa957b40017dc8df
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870477"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Zelfstudie: Video's analyseren met Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In deze zelfstudie ziet u hoe u video's kunt analyseren met Azure Media Services. Er zijn veel scenario's waarin u misschien inzicht wilt krijgen in opgenomen video's of audio-inhoud. Voor het bereiken van een hogere klanttevredenheid kunnen organisaties bijvoorbeeld spraak-naar-tekstverwerking uitvoeren om opnamen van de klantenondersteuning om te zetten in een catalogus van zoekmachines, met indexen en dashboards.

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Download de voorbeeld-app, zoals beschreven in het onderwerp.
> * De code kunt onderzoeken waarmee de opgegeven video wordt geanalyseerd.
> * Voer de app uit.
> * De uitvoer kunt onderzoeken.
> * Resources opschonen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Naleving, privacy en beveiliging
 
Als belang rijke herinnering moet u zich houden aan alle toepasselijke wetten in uw gebruik van Video Indexer. U moet Video Indexer of een andere Azure-service niet gebruiken op een manier die de rechten van anderen schendt. Voordat u Video's, met inbegrip van biometrische gegevens, naar de Video Indexer-service voor verwerking en opslag uploadt, moet u alle juiste rechten hebben, inclusief alle toepasselijke mede werkers, van de personen in de video. Voor meer informatie over naleving, privacy en beveiliging in Video Indexer, de Azure [Cognitive Services-voor waarden](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Raadpleeg de [privacyverklaring](https://privacy.microsoft.com/PrivacyStatement)van micro soft, het OST-bericht ( [Online Services-voor waarden](https://www.microsoft.com/licensing/product-licensing/products) ) en de [addendum op gegevens verwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") voor meer informatie over de privacy van micro soft en het afhandelen van uw gegevens. Meer informatie over privacy, waaronder het bewaren van gegevens, verwijderen/vernietigen, is beschikbaar in de OST en [hier](../video-indexer/faq.md). Door Video Indexer te gebruiken, gaat u akkoord met de Cognitive Services voor waarden, de OST, DPA en de privacyverklaring.

## <a name="prerequisites"></a>Vereisten

- Download [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) als u Visual Studio nog niet hebt geïnstalleerd.
- [Een Azure Media Services-account maken](./create-account-howto.md).<br/>Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.
- Volg de stappen in [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Toegang tot de Azure Media Services-API met de Azure CLI) en sla de referenties op. U hebt deze nodig voor toegang tot de API.

## <a name="download-and-configure-the-sample"></a>Het voorbeeld downloaden en configureren

Kloon met de volgende opdracht een GitHub-opslagplaats met het .NET-voorbeeld op de computer:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Het voorbeeld bevindt zich in de map [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos).

Open [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) in het project dat u hebt gedownload. Vervang de waarden door de referenties die u hebt verkregen via [toegang tot API's](./access-api-howto.md).

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>De code onderzoeken die de opgegeven video analyseert

In dit gedeelte worden de functies bekeken die zijn gedefinieerd in het bestand [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) van het project *AnalyzeVideos*.

Door het voorbeeld worden de volgende acties voltooid:

1. Een **Transformatie** en een **Taak** maken voor het analyseren van de video.
2. Een invoer **asset** maken en de video uploaden naar deze asset. De asset wordt gebruikt als de invoer voor de taak.
3. Een uitvoerasset maken waarin de uitvoer van de taak wordt opgeslagen.
4. De taak verzenden.
5. De status van de taak controleren.
6. De bestanden downloaden die het resultaat zijn van het uitvoeren van de taak.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Starten met het gebruik van Media Services API's met .NET SDK

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de code die u aan het begin van het artikel hebt gekloond, wordt met de functie **GetCredentialsAsync** het object ServiceClientCredentials gemaakt op basis van de referenties die zijn opgegeven in het lokale configuratiebestand. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Een invoerasset maken en er een lokaal bestand in uploaden 

Met de functie **CreateInputAsset** wordt een nieuwe [invoerasset](/rest/api/media/assets) gemaakt en het opgegeven lokale videobestand wordt hierin geladen. Deze asset wordt gebruikt als invoer voor uw coderingstaak. In Media Services-v3 kan de invoer voor een taak een asset zijn of inhoud die u beschikbaar maakt voor uw Media Services-account via HTTPS-URL's. Zie [dit](job-input-from-http-how-to.md) artikel als u wilt weten hoe u een HTTPS-URL kunt coderen.  

In Media Services v3 kunt u Azure Storage-API's gebruiken om bestanden te uploaden. Het volgende .NET-fragment laat zien hoe.

Met de volgende functie worden de volgende acties voltooid:

* Er wordt een asset gemaakt.
* Er wordt een schrijfbare [SAS-URL](../../storage/common/storage-sas-overview.md) opgehaald voor de [container in opslag](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) van de asset.

    Als u de functie [ListContainerSas](/rest/api/media/assets/listcontainersas) van de asset gebruikt om SAS-URL's op te halen, moet u er rekening mee houden dat met deze functie meerdere SAS-URL's worden geretourneerd, aangezien er voor elk opslagaccount twee opslagaccountsleutels zijn. Een opslagaccount heeft twee sleutels omdat dit naadloze rotatie van opslagaccountsleutels mogelijk maakt (bijvoorbeeld als u een van de sleutels wilt wijzigen terwijl u de andere gebruikt en vervolgens de nieuwe sleutel wilt gebruiken en de andere roteert). De eerste SAS-URL staat voor opslagsleutel 1 en een tweede voor opslagsleutel 2.
* Uploadt het bestand naar de container in opslag met behulp van de SAS-URL.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Een uitvoerasset maken voor het opslaan van het resultaat van de taak

In de [uitvoerasset](/rest/api/media/assets) wordt het resultaat van de taak opgeslagen. Het project definieert de functie **DownloadResults** die de resultaten van deze uitvoerasset naar de uitvoermap downloadt zodat u kunt zien wat u hebt gekregen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Een transformatie en een taak maken die video's analyseert

Bij het coderen of verwerken van inhoud in Media Services is het gebruikelijk om de coderingsinstellingen als recept in te stellen. U dient vervolgens een **taak** in te dienen om het recept toe te passen op een video. Door voor elke nieuwe video nieuwe taken in te dienen, past u dat recept toe op alle video's in de bibliotheek. Een recept in Media Services wordt een **transformatie** genoemd. Zie voor meer informatie [Transformaties en taken](./transforms-jobs-concept.md). Met het voorbeeld dat wordt beschreven in deze zelfstudie wordt een recept gedefinieerd waarmee de opgegeven video wordt geanalyseerd.

#### <a name="transform"></a>Transformeren

Bij het maken van een nieuw [transformatie](/rest/api/media/transforms)-exemplaar, moet u opgeven wat u als uitvoer wilt maken. **TransformOutput** is een vereiste parameter. Elke **transformatie-uitvoer** bevat een **voorinstelling**. **Voorinstelling** bevat stapsgewijze instructies van de video- en/of audioverwerkingen die moeten worden gebruikt voor het genereren van de gewenste **TransformOutput**. In dit voorbeeld wordt de voorinstelling **VideoAnalyzerPreset** gebruikt en de taal ('en-US') wordt doorgegeven aan de bijbehorende constructor (`new VideoAnalyzerPreset("en-US")`). Met deze voorinstelling kunt u meerdere audio- en video-inzichten uit een video extraheren. U kunt de voorinstelling **AudioAnalyzerPreset** gebruiken als u meerdere audio-inzichten wilt extraheren uit een video.

Bij het maken van een **transformatie** moet u eerst met de methode **Ophalen** controleren of er al een bestaat, zoals weergegeven in de code die erop volgt. In Media Services-v3 retourneert de methode **Ophalen** van entiteiten **null** als de entiteit (een hoofdlettergevoelige controle van de naam).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Taak

Zoals eerder vermeld, is het [transformatie](/rest/api/media/transforms)-object het recept en is de [taak](/rest/api/media/jobs) de werkelijke aanvraag bij Media Services om deze **transformatie** toe te passen op een bepaalde invoervideo of audio-inhoud. De **taak** bevat informatie zoals de locatie van de invoervideo en de locatie voor de uitvoer. U kunt de locatie van uw video opgeven met behulp van: HTTPS-URL's, SAS-URL's of activa die zich in uw Media Services-account bevinden.

In dit voorbeeld is de taakinvoer een lokale video.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Wacht tot de taak is voltooid

Het duurt even voordat de taak is voltooid. Wanneer dit is gebeurd, wilt u daarover een melding ontvangen. U kunt op verschillende manieren een melding ontvangen over de voltooiing van de [taak](/rest/api/media/jobs). De eenvoudigste optie (die hier wordt getoond) is het gebruik van polling.

Navragen is geen aanbevolen best practice voor productie-apps vanwege mogelijke latentie. Polling kan worden beperkt bij een te intensief gebruik op een account. Ontwikkelaars moeten in plaats daarvan Event Grid gebruiken.

Event Grid is ontworpen voor hoge beschikbaarheid, consistente prestaties en dynamisch schalen. Met Event Grid kunnen uw apps luisteren naar en reageren op gebeurtenissen uit vrijwel alle Azure-services, evenals aangepaste bronnen. Eenvoudige, op HTTP gebaseerde reactieve gebeurtenisafhandeling maakt het mogelijk om efficiënte oplossingen te bouwen met intelligente filtering en routering van gebeurtenissen. Zie [Gebeurtenissen routeren naar een aangepast webeindpunt](monitoring/job-state-events-cli-how-to.md) voor meer informatie.

De **taak** doorloopt meestal de volgende statussen: **Gepland**, **In de wachtrij geplaatst**, **Verwerken**, **Voltooid** (de eindstatus). Als bij de taak een fout is opgetreden, krijgt u de status **Fout**. Als de taak wordt geannuleerd, krijgt u de melding **Wordt geannuleerd** en vervolgens **Geannuleerd** wanneer het annuleren is voltooid.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>Het resultaat van de taak downloaden

Met de volgende functie worden de resultaten van de [uitvoerasset](/rest/api/media/assets) gedownload naar de uitvoermap, zodat u de resultaten van de taak kunt bekijken.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Resources in uw Media Services-account opschonen

[!INCLUDE [clean-up-warning](includes/clean-up-warning.md)]

Over het algemeen moet u alles opschonen, behalve objecten die u van plan bent opnieuw te gebruiken (meestal gebruikt u transformaties opnieuw en behoudt u StreamingLocators). Als u wilt dat uw account na het experiment is opgeschoond, moet u de resources verwijderen die u niet van plan bent opnieuw te gebruiken. Met de volgende code wordt bijvoorbeeld de uitvoerasset verwijderd:

### <a name="delete-resources-with-code"></a>Resources met code verwijderen

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

U kunt ook de CLI gebruiken.

[!INCLUDE [clean-up-resources-cli](includes/clean-up-resources-cli.md)]

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Druk op Ctrl+F5 om de *AnalyzeVideos*-app uit te voeren.

Wanneer we het programma uitvoeren, produceert de taak miniaturen voor elk gezicht dat in de video wordt gevonden. Het produceert ook het insights.json-bestand.

## <a name="examine-the-output"></a>De uitvoer controleren

Het uitvoerbestand van het analyseren van video's heet insights.json. Dit bestand bevat informatie over uw video. U vindt de beschrijving van elementen die in het json-bestand zijn gevonden in het artikel [Media intelligence](./analyzing-video-audio-files-concept.md).

## <a name="multithreading"></a>Multithreading

> [!WARNING]
> De SDK's van Azure Media Services v3 zijn niet thread-safe. Als u werkt met een app met meerdere threads, moet u per thread een nieuw AzureMediaServicesClient-object genereren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Bestanden uploaden, coderen en streamen](stream-files-tutorial-with-api.md)
