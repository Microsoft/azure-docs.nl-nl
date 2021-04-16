---
title: Video's uploaden en indexeren met Video Indexer
titleSuffix: Azure Media Services
description: In dit onderwerp ziet u hoe u API's gebruikt om uw video's te uploaden en indexeren met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/04/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 90fca4342b1fe04adef97a1a4c1c2166ca7ec51e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532492"
---
# <a name="upload-and-index-your-videos"></a>Uw video's uploaden en indexeren  

Nadat uw video is geüpload, Video Indexer (optioneel) de video coderen (besproken in het artikel). Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij de gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met de betaalde versie maakt u een Video Indexer-account dat is [gekoppeld aan uw Azure-abonnement en een Azure Media Services-account](connect-to-azure.md). U betaalt voor geïndexeerde minuten. Zie [Prijzen van mediaservices](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie.

Wanneer u video's uploadt met de Video Indexer-API, hebt u de volgende opties voor uploaden: 

* uw video uploaden via een URL (aanbevolen),
* het videobestand verzenden als een bytematrix in de aanvraagbody.
* Bestaande Azure Media Services-assets gebruiken door de [asset-id](../latest/assets-concept.md) op te geven (alleen ondersteund in betaalde accounts).

In het artikel wordt beschreven hoe u uw video's uploadt en indexeert met de volgende opties:

* [De Video Indexer-website](#upload-and-index-a-video-using-the-video-indexer-website) 
* [De Video Indexer-API’s](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Ondersteunde bestandsindelingen voor Video Indexer

Zie het artikel [invoercontainer/bestandsindelingen](../latest/encode-media-encoder-standard-formats-reference.md) voor een lijst bestandsindelingen die u kunt gebruiken met Video Indexer.

## <a name="video-files-storage"></a>Opslag van videobestanden

- Met een betaald Video Indexer-account maakt u een Video Indexer-account dat is verbonden met uw Azure-abonnement en een Azure Media Services account. Zie Create a Video Indexer account connected to Azure (Een Video Indexer [met Azure verbonden) voor meer informatie.](connect-to-azure.md)
- Videobestanden worden opgeslagen in Azure Storage door Azure Media Services. Er is geen tijdslimiet.
- U kunt altijd uw video- en audiobestanden verwijderen, evenals alle metagegevens en inzichten die uit deze bestanden zijn geëxtraheerd door Video Indexer. Nadat u een bestand uit Video Indexer verwijderd, worden het bestand en de metagegevens en inzichten permanent verwijderd uit Video Indexer. Als u echter uw eigen back-upoplossing in Azure Storage hebt geïmplementeerd, blijft het bestand in uw Azure-opslag.
- De persistentie van een video is identiek, ongeacht of het uploaden is uitgevoerd op de Video Indexer website of met behulp van de Upload-API.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Een video uploaden en indexeren met behulp van de Video Indexer website

> [!NOTE]
> De naam van de video mag niet langer zijn dan 80 tekens.

1. Registreer u op de [Video Indexer](https://www.videoindexer.ai/)-website.
1. Als u een video wilt uploaden, drukt u op de knop of link **Uploaden**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Uploaden":::
1. Zodra uw video is geüpload, start Video Indexer met het indexeren en analyseren van de video.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Voortgang van het uploaden":::
1. Wanneer Video Indexer klaar is met analyseren, ontvangt u een e-mailbericht met een link naar uw video en een korte beschrijving van wat is gevonden in uw video. Bijvoorbeeld: mensen, onderwerpen, OCR's.

## <a name="upload-and-index-with-api"></a>Uploaden en indexeren met API

Gebruik de [VIDEO-API uploaden](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) om uw video's te uploaden en te indexeren op basis van een URL. Het volgende codevoorbeeld bevat de commentaarcode die laat zien hoe u de byte-matrix uploadt. 

### <a name="configurations-and-params"></a>Configuraties en parameters

Deze sectie beschrijft een aantal van de optionele parameters en wanneer u deze het best instelt. Zie video-API uploaden voor de meest recente [params-informatie.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

#### <a name="externalid"></a>externalID 

Met deze parameter kunt u een ID die is gekoppeld aan de video opgeven. De ID kan worden toegepast op externe VCM-systeemintegratie (Video Content Management). De video's die zich in de Video Indexer-portal bevinden, kunnen worden doorzocht met behulp van de opgegeven externe ID.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Andere overwegingen

- Video Indexer retourneert alle bestaande parameters die zijn opgegeven in de oorspronkelijke URL.
- De opgegeven URL moet worden gecodeerd.

#### <a name="indexingpreset"></a>indexingPreset

Gebruik deze parameter om de AI-bundel te definiëren die u wilt toepassen op uw audio- of videobestand. Deze parameter wordt gebruikt voor het configureren van het indexeringsproces. U kunt de volgende waarden opgeven:

- `AudioOnly` – Indexeren en inzichten extraheren met alleen audio (video negeren).
- `VideoOnly` - Indexeren en inzichten extraheren met alleen video (audio negeren).
- `Default` – Indexeren en inzichten extraheren met behulp van zowel audio als video.
- `DefaultWithNoiseReduction` – Indexeer en extraheer inzichten uit zowel audio als video, terwijl algoritmen voor ruisvermindering worden toegepast op audiostreams.

    De `DefaultWithNoiseReduction` waarde is nu ingesteld op de standaardvoorinstelling (afgeschaft).
- `BasicAudio` - Indexeren en inzichten extraheren met alleen audio (video negeren), met inbegrip van alleen basis audiofuncties (transcriptie, vertaling, uitvoerbijschriften en ondertiteling opmaken).
 - `AdvancedAudio` - Indexeren en inzichten extraheren met alleen audio (video negeren), inclusief geavanceerde audiofuncties (detectie van audiogebeurtenissen) naast de standaard audioanalyse.

> [!NOTE]
> Video Indexer bevat maximaal twee audionummers. Als er meer audionummers in het bestand staan, worden ze behandeld als één nummer.<br/>
Als u de sporen afzonderlijk wilt indexeren, moet u het relevante audiobestand extraheren en indexeren als `AudioOnly` .

De prijs is afhankelijk van de geselecteerde optie voor indexering. Raadpleeg prijzen voor Media Services [meer informatie.](https://azure.microsoft.com/pricing/details/media-services/)

#### <a name="priority"></a>priority

Video's worden geïndexeerd door Video Indexer op basis van de prioriteit. Gebruik de parameter **priority** voor het specificeren van de indexeringsprioriteit. De volgende waarden zijn geldig: **Laag**, **Normaal** (standaard) en **Hoog**.

De parameter **priority** wordt alleen ondersteund voor betaalde accounts.

#### <a name="streamingpreset"></a>streamingPreset

Zodra uw video is geüpload, codeert Video Indexer eventueel de video. Vervolgens gaat Video Indexer door naar het indexeren en analyseren van de video. Wanneer Video Indexer klaar is met analyseren, ontvangt u een melding met de video-ID.  

Wanneer u de API [Video uploaden](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) of [Video opnieuw indexeren](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) gebruikt, is een van de optionele parameters `streamingPreset`. Als u `streamingPreset` instelt op `Default`, `SingleBitrate` of `AdaptiveBitrate`, wordt het coderingsproces geactiveerd. Wanneer de indexerings- en coderingstaken klaar zijn, wordt de video gepubliceerd zodat u uw video ook kunt streamen. Het streaming-eindpunt vanwaar u de video wilt streamen, moet de status **Wordt uitgevoerd** hebben.

Voor SingleBitrate gelden de standaardkosten voor encoder per uitvoer. Als de hoogte van de video groter of gelijk is aan 720, Video Indexer codeert u deze als 1280x720. Anders als 640x468.
De standaardinstelling is [inhoudsbewuste codering.](../latest/encode-content-aware-concept.md)

Om de indexerings- en coderingstaken uit te voeren, heeft het [Azure Media Services-account dat is gekoppeld aan uw Video Indexer-account](connect-to-azure.md) gereserveerde eenheden nodig. Zie [Mediaverwerking schalen](../previous/media-services-scale-media-processing-overview.md) voor meer informatie. Aangezien deze rekentaken intensief zijn, wordt eenheidstype S3 aanbevolen. Het aantal gereserveerde eenheden bepaalt het maximale aantal taken dat parallel kan worden uitgevoerd. De aanbevolen basislijn is 10 gereserveerde S3-eenheden. 

Als u uw video alleen wilt indexeren, maar deze niet wilt coderen, stelt u `streamingPreset` in op `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Een URL van het video-/audiobestand dat moet worden geïndexeerd. De URL moet verwijzen naar een mediabestand (HTML-pagina's worden niet ondersteund). Het bestand kan worden beveiligd door een toegangstoken dat is geleverd als onderdeel van de URI en het eindpunt voor het bestand moet worden beveiligd met TLS 1.2 of hoger. De URL moet worden gecodeerd. 

Als de `videoUrl` niet is opgegeven, verwacht de Video Indexer dat u het bestand als inhoud met meerdere delen of formulier opgeeft.

### <a name="code-sample"></a>Codevoorbeeld

Het volgende C#-codefragment toont het gebruik van alle Video Indexer-API's samen.

**Instructies voor het uitvoeren van het volgende codevoorbeeld**

Nadat u deze code naar uw ontwikkelplatform hebt kopieert, moet u twee parameters opgeven: API Management verificatiesleutel en video-URL.

* API-sleutel: API-sleutel is uw persoonlijke API Management-abonnementssleutel, waarmee u een toegangs token kunt krijgen om bewerkingen uit te voeren op uw Video Indexer account. 

    Als u uw API-sleutel wilt op halen, gaat u door deze stroom:

    * Ga naar https://api-portal.videoindexer.ai/
    * Aanmelden
    * Ga naar **Autorisatie-abonnement**  ->  **voor**  ->  **productenautorisatie**
    * De primaire **sleutel kopiëren**
* Video-URL: een URL van het video-/audiobestand dat moet worden geïndexeerd. De URL moet verwijzen naar een mediabestand (HTML-pagina's worden niet ondersteund). Het bestand kan worden beveiligd door een toegangstoken dat is geleverd als onderdeel van de URI en het eindpunt voor het bestand moet worden beveiligd met TLS 1.2 of hoger. De URL moet worden gecodeerd.

Het resultaat van het uitvoeren van het codevoorbeeld bevat een URL van de widget inzicht en een widget-URL voor de speler waarmee u respectievelijk de inzichten en video kunt onderzoeken die zijn geüpload. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Algemene fouten

De statuscodes in de volgende tabel kunnen worden geretourneerd door de uploadbewerking.

|Statuscode|ErrorType (in hoofdtekst van antwoord)|Description|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Dezelfde video wordt al verwerkt in het opgegeven account.|
|400|VIDEO_ALREADY_FAILED|Dezelfde video kon minder dan twee uur geleden niet worden verwerkt in het opgegeven account. API-clients moeten ten minste twee uur wachten voordat ze een video opnieuw uploaden.|
|429||Proefaccounts zijn 5 uploads per minuut toegestaan. Betaalde accounts zijn 50 uploads per minuut toegestaan.|

## <a name="uploading-considerations-and-limitations"></a>Overwegingen en beperkingen uploaden
 
- De naam van de video mag niet langer zijn dan 80 tekens.
- Bij het uploaden van uw video op basis van de URL (aanbevolen) moet het eindpunt worden beveiligd met TLS 1.2 (of hoger).
- De uploadgrootte met de URL-optie is beperkt tot 30 GB.
- De lengte van de aanvraag-URL is beperkt tot 6144 tekens waarbij de lengte van de querytekenreeks-URL is beperkt tot 4096 tekens.
- De uploadgrootte met de bytematrixoptie is beperkt tot 2 GB.
- Na 30 minuten treedt een time-out op voor de bytematrixoptie.
- De URL in de `videoURL` -param moet worden gecodeerd.
- Het indexeren van Media Services-assets heeft dezelfde beperking als het indexeren via een URL.
- Video Indexer heeft een maximale duurlimiet van vier uur voor één bestand.
- De URL moet toegankelijk zijn (bijvoorbeeld een openbare URL). 

    Als het een privé-URL is, moet het toegangstoken in de aanvraag worden opgegeven.
- De URL moet verwijzen naar een geldig mediabestand en niet naar een webpagina, zoals een koppeling naar de `www.youtube.com` pagina.
- In een betaald account kunt u maximaal 50 films per minuut uploaden, en in een proefaccount maximaal 5 films per minuut.

> [!Tip]
> Het verdient aanbeveling om .NET Framework versie 4.6.2 of hoger te gebruiken omdat oudere versies van .NET Framework niet standaard gebruikmaken van TLS 1.2.
>
> Als u een oudere versie van .NET Framework moet gebruiken, voegt u deze regel toe aan uw code voordat u de REST API-aanroept:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>Volgende stappen

[De Uitvoer van Azure Video Indexer die wordt geproduceerd door de API](video-indexer-output-json-v2.md)
