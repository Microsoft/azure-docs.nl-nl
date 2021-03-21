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
ms.openlocfilehash: 3a3c2812a4ecfa1a80539804122042bc2dc2f3a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199183"
---
# <a name="upload-and-index-your-videos"></a>Uw video's uploaden en indexeren  

Als uw video eenmaal is geüpload, wordt de video door Video Indexer (optioneel) codeert (eventueel beschreven in het artikel). Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij de gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met de betaalde versie maakt u een Video Indexer-account dat is [gekoppeld aan uw Azure-abonnement en een Azure Media Services-account](connect-to-azure.md). U betaalt voor geïndexeerde minuten. Zie [Prijzen van mediaservices](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie.

Wanneer u video's uploadt met de Video Indexer-API, hebt u de volgende opties voor uploaden: 

* uw video uploaden via een URL (aanbevolen),
* het videobestand verzenden als een bytematrix in de aanvraagbody.
* Bestaande Azure Media Services-assets gebruiken door de [asset-id](../latest/assets-concept.md) op te geven (alleen ondersteund in betaalde accounts).

In dit artikel wordt beschreven hoe u Video's uploadt en indexeert met de volgende opties:

* [De Video Indexer-website](#upload-and-index-a-video-using-the-video-indexer-website) 
* [De Video Indexer-API’s](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Ondersteunde bestandsindelingen voor Video Indexer

Zie het artikel [invoercontainer/bestandsindelingen](../latest/media-encoder-standard-formats.md#input-containerfile-formats) voor een lijst bestandsindelingen die u kunt gebruiken met Video Indexer.

## <a name="video-files-storage"></a>Video bestanden opslag

- Met een betaald Video Indexer account maakt u een Video Indexer-account dat is verbonden met uw Azure-abonnement en een Azure Media Services-account. Zie [een video indexer-account maken dat is verbonden met Azure](connect-to-azure.md)voor meer informatie.
- Video bestanden worden opgeslagen in azure Storage door Azure Media Services. Er is geen beperking van de tijd.
- U kunt altijd uw video-en audio bestanden verwijderen, evenals alle meta gegevens en inzichten die door Video Indexer worden geëxtraheerd. Wanneer u een bestand verwijdert uit Video Indexer, worden het bestand en de bijbehorende meta gegevens en inzichten permanent verwijderd uit Video Indexer. Als u echter uw eigen back-upoplossing hebt geïmplementeerd in azure Storage, blijft het bestand in uw Azure-opslag.
- De opereren van een video is identiek, ongeacht of het uploaden is voltooid, de Video Indexer website of het gebruik van de upload-API.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Een video uploaden en indexeren met behulp van de Video Indexer-website

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

Gebruik de [video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) -API uploaden om uw Video's te uploaden en indexeren op basis van een URL. In het volgende code voorbeeld wordt de code voor commentaar opgenomen, die laat zien hoe u de byte matrix uploadt. 

### <a name="configurations-and-params"></a>Configuraties en parameters

Deze sectie beschrijft een aantal van de optionele parameters en wanneer u deze het best instelt. Zie de video-API [uploaden](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) voor meer informatie over de meest recente para meters.

#### <a name="externalid"></a>externalID 

Met deze parameter kunt u een ID die is gekoppeld aan de video opgeven. De ID kan worden toegepast op externe VCM-systeemintegratie (Video Content Management). De video's die zich in de Video Indexer-portal bevinden, kunnen worden doorzocht met behulp van de opgegeven externe ID.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Andere overwegingen

- Video Indexer retourneert alle bestaande parameters die zijn opgegeven in de oorspronkelijke URL.
- De opgegeven URL moet worden gecodeerd.

#### <a name="indexingpreset"></a>indexingPreset

Gebruik deze para meter om de AI-bundel te definiëren die u wilt Toep assen op uw audio-of video bestand. Deze parameter wordt gebruikt voor het configureren van het indexeringsproces. U kunt de volgende waarden opgeven:

- `AudioOnly` – Indexeer en extraheer inzichten alleen met behulp van audio (video wordt genegeerd).
- `VideoOnly` -Alleen inzichten indexeren en uitpakken met alleen video (audio wordt genegeerd).
- `Default` – Indexeer en extraheer inzichten met behulp van audio en video.
- `DefaultWithNoiseReduction` – Indexeer en extraheer inzichten van audio en video, terwijl er ruis reductie algoritmen worden toegepast op een audio stroom.

    De `DefaultWithNoiseReduction` waarde is nu toegewezen aan de standaard voorinstelling (afgeschaft).
- `BasicAudio` -U kunt inzichten alleen indexeren en uitpakken met alleen audio (video wordt genegeerd), met inbegrip van alleen basis audio functies (transcriptie, vertaling, opmaak van uitvoer bijschriften en ondertiteling).
 - `AdvancedAudio` -U kunt inzichten alleen indexeren en uitpakken met alleen audio (video wordt genegeerd), inclusief geavanceerde audio functies (detectie van audio gebeurtenissen) naast de standaard audio analyse.

> [!NOTE]
> Video Indexer omvat Maxi maal twee sporen van audio. Als het bestand meer audio sporen bevat, worden deze behandeld als één spoor.<br/>
Als u de nummers afzonderlijk wilt indexeren, moet u het relevante audio bestand extra heren en indexeren als `AudioOnly` .

De prijs is afhankelijk van de geselecteerde optie voor indexering. Raadpleeg voor meer informatie [Media Services prijzen](https://azure.microsoft.com/pricing/details/media-services/).

#### <a name="priority"></a>priority

Video's worden geïndexeerd door Video Indexer op basis van de prioriteit. Gebruik de parameter **priority** voor het specificeren van de indexeringsprioriteit. De volgende waarden zijn geldig: **Laag**, **Normaal** (standaard) en **Hoog**.

De parameter **priority** wordt alleen ondersteund voor betaalde accounts.

#### <a name="streamingpreset"></a>streamingPreset

Zodra uw video is geüpload, codeert Video Indexer eventueel de video. Vervolgens gaat Video Indexer door naar het indexeren en analyseren van de video. Wanneer Video Indexer klaar is met analyseren, ontvangt u een melding met de video-ID.  

Wanneer u de API [Video uploaden](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) of [Video opnieuw indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) gebruikt, is een van de optionele parameters `streamingPreset`. Als u `streamingPreset` instelt op `Default`, `SingleBitrate` of `AdaptiveBitrate`, wordt het coderingsproces geactiveerd. Wanneer de indexerings- en coderingstaken klaar zijn, wordt de video gepubliceerd zodat u uw video ook kunt streamen. Het streaming-eindpunt vanwaar u de video wilt streamen, moet de status **Wordt uitgevoerd** hebben.

Voor SingleBitrate zijn de kosten van Standard encoder van toepassing op de uitvoer. Als de video hoogte groter is dan of gelijk is aan 720, Video Indexer deze als 1280x720 codeert. Anders als 640x468.
De standaard instelling is [code ring met inhoud](../latest/content-aware-encoding.md).

Om de indexerings- en coderingstaken uit te voeren, heeft het [Azure Media Services-account dat is gekoppeld aan uw Video Indexer-account](connect-to-azure.md) gereserveerde eenheden nodig. Zie [Mediaverwerking schalen](../previous/media-services-scale-media-processing-overview.md) voor meer informatie. Aangezien deze rekentaken intensief zijn, wordt eenheidstype S3 aanbevolen. Het aantal gereserveerde eenheden bepaalt het maximale aantal taken dat parallel kan worden uitgevoerd. De aanbevolen basislijn is 10 gereserveerde S3-eenheden. 

Als u uw video alleen wilt indexeren, maar deze niet wilt coderen, stelt u `streamingPreset` in op `NoStreaming`.

#### <a name="videourl"></a>videoUrl

Een URL van het video-/audiobestand dat moet worden geïndexeerd. De URL moet verwijzen naar een mediabestand (HTML-pagina's worden niet ondersteund). Het bestand kan worden beveiligd door een toegangstoken dat is geleverd als onderdeel van de URI en het eindpunt voor het bestand moet worden beveiligd met TLS 1.2 of hoger. De URL moet worden gecodeerd. 

Als de `videoUrl` niet is opgegeven, verwacht de Video Indexer dat u het bestand als inhoud met meerdere delen of formulier opgeeft.

### <a name="code-sample"></a>Codevoorbeeld

Het volgende C#-codefragment toont het gebruik van alle Video Indexer-API's samen.

**Instructies voor het uitvoeren van het volgende code voorbeeld**

Nadat u deze code naar uw ontwikkel platform hebt gekopieerd, moet u twee para meters opgeven: API Management verificatie sleutel en video-URL.

* API-sleutel – API-sleutel is uw persoonlijke API Management-abonnements sleutel, waarmee u een toegangs token kunt ophalen om bewerkingen uit te voeren op uw Video Indexer-account. 

    Als u uw API-sleutel wilt ophalen, gaat u door deze stroom:

    * Ga naar https://api-portal.videoindexer.ai/
    * Aanmelden
    * Naar het   ->  abonnement voor **verificatie**  ->  **autorisatie** van producten
    * De **primaire sleutel** kopiëren
* Video-URL: een URL van het video-of audio bestand dat moet worden geïndexeerd. De URL moet verwijzen naar een mediabestand (HTML-pagina's worden niet ondersteund). Het bestand kan worden beveiligd door een toegangstoken dat is geleverd als onderdeel van de URI en het eindpunt voor het bestand moet worden beveiligd met TLS 1.2 of hoger. De URL moet worden gecodeerd.

Het resultaat van het uitvoeren van het code voorbeeld bevat een Insight-widget-URL en een URL van een speler-widget waarmee u de inzichten en video die respectievelijk worden geüpload, kunt onderzoeken. 


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

|Statuscode|ErrorType (in hoofdtekst van antwoord)|Beschrijving|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Dezelfde video wordt al verwerkt in het opgegeven account.|
|400|VIDEO_ALREADY_FAILED|Dezelfde video kon minder dan twee uur geleden niet worden verwerkt in het opgegeven account. API-clients moeten ten minste twee uur wachten voordat ze een video opnieuw uploaden.|
|429||Proef accounts zijn vijf uploads per minuut toegestaan. Betaalde accounts zijn toegestaan 50 uploads per minuut.|

## <a name="uploading-considerations-and-limitations"></a>Overwegingen en beperkingen uploaden
 
- De naam van de video mag niet langer zijn dan 80 tekens.
- Bij het uploaden van uw video op basis van de URL (aanbevolen) moet het eindpunt worden beveiligd met TLS 1.2 (of hoger).
- De uploadgrootte met de URL-optie is beperkt tot 30 GB.
- De lengte van de aanvraag-URL is beperkt tot 6144 tekens waarbij de lengte van de querytekenreeks-URL is beperkt tot 4096 tekens.
- De uploadgrootte met de bytematrixoptie is beperkt tot 2 GB.
- Na 30 minuten treedt een time-out op voor de bytematrixoptie.
- De URL die is opgenomen in de `videoURL` para meter moet worden gecodeerd.
- Het indexeren van Media Services-assets heeft dezelfde beperking als het indexeren via een URL.
- Video Indexer heeft een maximale duurlimiet van vier uur voor één bestand.
- De URL moet toegankelijk zijn (bijvoorbeeld een openbare URL). 

    Als het een privé-URL is, moet het toegangstoken in de aanvraag worden opgegeven.
- De URL moet verwijzen naar een geldig Media bestand en niet naar een webpagina, zoals een koppeling naar de `www.youtube.com` pagina.
- In een betaald account kunt u maximaal 50 films per minuut uploaden, en in een proefaccount maximaal 5 films per minuut.

> [!Tip]
> Het verdient aanbeveling om .NET Framework versie 4.6.2 of hoger te gebruiken omdat oudere versies van .NET Framework niet standaard gebruikmaken van TLS 1.2.
>
> Als u een oudere versie van .NET Framework moet gebruiken, voegt u deze regel toe aan uw code voordat u de REST API-aanroept:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>Volgende stappen

[De Azure Video Indexer-uitvoer controleren die door de API is geproduceerd](video-indexer-output-json-v2.md)
