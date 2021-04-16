---
title: De Video Indexer-API gebruiken
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Media Services Video Indexer API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a445e9869b0cd9928d95364f39e60fc892214b9a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532459"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Zelfstudie: de Video Indexer-API gebruiken

Video Indexer voegt verschillende ai-technologieën (kunstmatige intelligentie) voor audio en video die door Microsoft worden aangeboden samen in één geïntegreerde service, waardoor de ontwikkeling eenvoudiger wordt. De API's zijn ontworpen om ontwikkelaars in staat te stellen zich te richten op het gebruik van Media AI-technologieën zonder dat ze zich zorgen hoeven te maken over de schaal, het wereldwijde bereik, de beschikbaarheid en de betrouwbaarheid van cloudplatforms. U kunt de API gebruiken om uw bestanden te uploaden, gedetailleerde video-inzichten te verkrijgen, URL's van insluitbare inzichten en spelerwidgets op te halen, en meer.

Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij een gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met een betaalde optie maakt u een Video Indexer-account dat is verbonden met uw Azure-abonnement en een [Azure Media Services account.](connect-to-azure.md) U betaalt voor geïndexeerde minuten. Zie [Prijzen van mediaservices](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie.

In dit artikel wordt uitgelegd hoe ontwikkelaars kunnen profiteren van de [Video Indexer-API](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Abonneren op de API

1. Meld u aan bij de [Video Indexer-ontwikkelaarsportal](https://api-portal.videoindexer.ai/).

    Bekijk een release-opmerking met betrekking [tot aanmeldingsgegevens](release-notes.md#october-2020).
    
     ![Meld u aan bij Video Indexer Developer Portal](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * U moet dezelfde provider gebruiken als voor het aanmelden bij Video Indexer.
   > * Persoonlijke Google- en Microsoft-accounts (Outlook/Live) kunnen alleen worden gebruikt voor proefaccounts. Accounts die gekoppeld zijn met Azure vereisen Azure Active Directory.
   > * Er kan slechts één actief account per e-mail zijn. Als een gebruiker zich probeert aan te melden met voor LinkedIn en later met voor Google, wordt in het laatste geval een foutpagina weergegeven met de naam dat user@gmail.com user@gmail.com de gebruiker al bestaat.
2. Abonneer u.

    Selecteer het [tabblad](https://api-portal.videoindexer.ai/products) Producten. Selecteer vervolgens Autorisatie en abonneer u.
    
    ![Tabblad Producten in Video Indexer Developer Portal](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > Nieuwe gebruikers worden automatisch geabonneerd op Autorisatie.
    
    Nadat u zich hebt geabonneerd, kunt u uw abonnement vinden onder **Autorisatie**  ->  **van producten.** Op de abonnementspagina vindt u de primaire en secundaire sleutels. De sleutels moeten beveiligd zijn. De sleutels mogen alleen door uw servercode gebruikt worden. Ze mogen niet beschikbaar zijn aan de clientzijde (.js, .html, etc.).

    ![Abonnement en sleutels in Video Indexer Developer Portal](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Een gebruiker van Video Indexer kan met één abonnementssleutel verbinding maken met meerdere Video Indexer-accounts. U kunt deze Video Indexer-accounts vervolgens koppelen aan verschillende Media Services-accounts.

## <a name="obtain-access-token-using-the-authorization-api"></a>Tokens verkrijgen met de Authorization-API

Zodra u zich hebt geabonneerd op de Autorisatie-API, kunt u toegangstokens verkrijgen. Deze toegangstokens worden gebruikt voor verificatie op basis van de Operations-API.

Elke aanroep naar de Operations-API moet worden gekoppeld aan een toegangstoken dat overeenkomt met het autorisatiebereik van de aanroep.

- Gebruikersniveau: Met toegangstokens op gebruikersniveau kunt u bewerkingen uitvoeren op **gebruikersniveau.** Hiermee kunt u bijvoorbeeld gekoppelde accounts ophalen.
- Accountniveau: met toegangstokens op accountniveau kunt u bewerkingen uitvoeren op **accountniveau** of **videoniveau.** U kunt bijvoorbeeld een video uploaden, een lijst met alle video's maken, inzichten in video's verkrijgen, en meer.
- Videoniveau: met toegangstokens op videoniveau kunt u bewerkingen uitvoeren op een specifieke **video.** U kunt bijvoorbeeld video-inzichten verkrijgen, bijschriften downloaden, widgets verkrijgen, en meer.

U kunt bepalen of deze tokens alleen-lezen zijn of dat ze bewerking toestaan door **allowEdit=true/false op te geven.**

Voor de meeste server-naar-server-scenario's  gebruikt u waarschijnlijk hetzelfde account-token, omdat dit zowel **accountbewerkingen** als **videobewerkingen dekt.** Als u echter van plan bent om aanroepen aan de clientzijde uit te voeren naar Video Indexer (bijvoorbeeld vanuit JavaScript), kunt u het beste een token voor **videotoegang** gebruiken om te voorkomen dat clients toegang krijgen tot het hele account. Dat is ook de reden waarom u bij het insluiten van Video Indexer-clientcode in uw client (bijvoorbeeld met behulp van de widget Inzichten verkrijgen of Spelerwidget verkrijgen) een toegangsken voor **video's** moet leveren.  

Om het eenvoudiger te maken, kunt u **Authorization-API** > **GetAccounts** gebruiken om uw accounts op te halen zonder eerst een gebruikerstoken te verkrijgen. U kunt verzoeken om de accounts op te halen met behulp van geldige tokens, zodat u een extra aanroep om een accounttoken op te halen kunt overslaan.

Toegangstokens verlopen na één uur. Zorg ervoor dat uw token geldig is voordat u de Operations-API gebruikt. Als deze is verlopen, roept u de Autorisatie-API opnieuw aan om een nieuw toegang token op te halen.

U bent klaar om te beginnen met de integratie met de API. Zoek [de gedetailleerde beschrijving van elke Video Indexer-REST API](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Account-id

De parameter Account-id is vereist in alle operationele API-aanroepen. Account-id is een GUID die kan worden verkregen op een van de volgende manieren:

* Gebruik de **Video Indexer-website** om de account-id op te halen:

    1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
    2. Ga naar de pagina **Instellingen**.
    3. Kopieer de account-id.

        ![Video Indexer-instellingen en account-id](./media/video-indexer-use-apis/account-id.png)

* Gebruik de **Video Indexer-ontwikkelaarsportal** om de account-id programmatisch op te halen.

    Gebruik de [API Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) maken.

    > [!TIP]
    > U kunt de toegangstokens voor de accounts genereren door `generateAccessTokens=true` te definiëren.

* Haal de account-id op uit de URL van een pagina van de speler in uw account.

    Wanneer u een video bekijkt, wordt de id weergegeven na de sectie `accounts` en voor de sectie `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Aanbevelingen

In deze sectie vindt u enkele aanbevelingen bij het gebruik van de Video Indexer-API.

- Als u van plan bent om een video te uploaden, is het raadzaam om het bestand op een openbare netwerklocatie te plaatsen (bijvoorbeeld een Azure Blob Storage account). Haal de link naar de video op en geef de URL op als de parameter van het uploadbestand.

    De URL die is opgegeven met Video Indexer moet verwijzen naar een mediabestand (audio of video). Een eenvoudige verificatie voor de URL (of SAS-URL) is om deze in een browser te plakken. Als het bestand begint te afspelen/downloaden, is dit waarschijnlijk een goede URL. Als de browser een visualisatie we weergeven, is dit waarschijnlijk geen koppeling naar een bestand, maar naar een HTML-pagina.

- Als u de API die video-inzichten ophaalt voor de opgegeven video aanroept, krijgt u gedetailleerde JSON-uitvoer als resultaat. [Informatie over de geretourneerde JSON in dit onderwerp](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Codevoorbeeld

Het volgende C#-codefragment toont het gebruik van alle Video Indexer-API's samen.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u klaar bent met deze zelfstudie, verwijdert u resources die u niet van plan bent te gebruiken.

## <a name="see-also"></a>Zie tevens

- [Overzicht van Video Indexer](video-indexer-overview.md)
- [Regio's](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Volgende stappen

- [Details van de uitvoer-JSON bekijken](video-indexer-output-json-v2.md)
- Bekijk de [voorbeeldcode die](https://github.com/Azure-Samples/media-services-video-indexer) een belangrijk aspect van het uploaden en indexeren van een video laat zien. Als u de code volgt, krijgt u een goed idee van het gebruik van onze API voor basisfunctionaliteiten. Lees de inline-opmerkingen en bekijk onze adviezen voor best practices.

