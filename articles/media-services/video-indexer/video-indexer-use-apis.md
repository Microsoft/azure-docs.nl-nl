---
title: De Video Indexer-API gebruiken
titleSuffix: Azure Media Services
description: In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Media Services Video Indexer-API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: fcd194e2503610db314f6a975a4afb1d27962f8c
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028177"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Zelfstudie: de Video Indexer-API gebruiken

Video Indexer consolideert diverse AI-technologieën (audio en video kunst matige intelligentie) die door micro soft worden aangeboden in één geïntegreerde service, waardoor ontwikkeling eenvoudiger wordt. De Api's zijn zodanig ontworpen dat ontwikkel aars zich kunnen richten op het gebruik van media AI-technologieën zonder dat u zich zorgen hoeft te maken over schaal, wereld wijd bereik, Beschik baarheid en betrouw baarheid van Cloud platforms. U kunt de API gebruiken om uw bestanden te uploaden, gedetailleerde video inzichten te verkrijgen, Url's van een Inge sloten Insight-en speler-widgets op te halen en meer.

Wanneer u een Video Indexer-account maakt, kunt u kiezen uit een gratis proefversie (waarmee u een bepaald aantal gratis minuten indexering krijgt) of een betaalde optie (zonder quotumlimiet). Bij een gratis proefversie biedt Video Indexer websitegebruikers maximaal 600 minuten aan gratis indexering en API-gebruikers maximaal 2400 minuten gratis indexering. Met een betaalde optie maakt u een Video Indexer-account dat is [verbonden met uw Azure-abonnement en een Azure Media Services-account](connect-to-azure.md). U betaalt voor geïndexeerde minuten. Zie [Prijzen van mediaservices](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie.

In dit artikel wordt uitgelegd hoe ontwikkelaars kunnen profiteren van de [Video Indexer-API](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Abonneren op de API

1. Meld u aan bij de [Video Indexer-ontwikkelaarsportal](https://api-portal.videoindexer.ai/).

    Bekijk een release opmerking over [aanmeldings gegevens](release-notes.md#october-2020).
    
     ![Aanmelden bij Video Indexer ontwikkelaars Portal](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * U moet dezelfde provider gebruiken als voor het aanmelden bij Video Indexer.
   > * Persoonlijke Google-en micro soft-accounts (Outlook/Live) kunnen alleen worden gebruikt voor proef accounts. Accounts die gekoppeld zijn met Azure vereisen Azure Active Directory.
   > * Er kan slechts één actief account per e-mail zijn. Als een gebruiker zich probeert aan te melden bij user@gmail.com voor LinkedIn en later met user@gmail.com voor Google, wordt er een fout pagina weer gegeven met de melding dat de gebruiker al bestaat.
2. Abonneer u.

    Selecteer het tabblad [producten](https://api-portal.videoindexer.ai/products) . Selecteer vervolgens autorisatie en abonneren.
    
    ![Het tabblad producten in Video Indexer ontwikkelaars Portal](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > Nieuwe gebruikers worden automatisch geabonneerd op Autorisatie.
    
    Nadat u zich hebt geabonneerd, kunt u uw abonnement vinden onder de autorisatie van **producten**  ->  . Op de pagina abonnement vindt u de primaire en secundaire sleutels. De sleutels moeten beveiligd zijn. De sleutels mogen alleen door uw servercode gebruikt worden. Ze zijn niet beschikbaar voor de client zijde (. js,. html, enzovoort).

    ![Abonnementen en sleutels in Video Indexer ontwikkelaars Portal](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Een gebruiker van Video Indexer kan met één abonnementssleutel verbinding maken met meerdere Video Indexer-accounts. U kunt deze Video Indexer-accounts vervolgens koppelen aan verschillende Media Services-accounts.

## <a name="obtain-access-token-using-the-authorization-api"></a>Tokens verkrijgen met de Authorization-API

Zodra u zich hebt geabonneerd op de autorisatie-API, kunt u toegangs tokens verkrijgen. Deze toegangstokens worden gebruikt voor verificatie op basis van de Operations-API.

Elke aanroep naar de Operations-API moet worden gekoppeld aan een toegangstoken dat overeenkomt met het autorisatiebereik van de aanroep.

- Gebruikers niveau: toegangs tokens op gebruikers niveau bieden u de mogelijkheid om bewerkingen op het **gebruikers** niveau uit te voeren. Hiermee kunt u bijvoorbeeld gekoppelde accounts ophalen.
- Account niveau: toegangs tokens op account niveau bieden u de mogelijkheid om bewerkingen uit te voeren op het niveau van de **account** of het **video** niveau. Upload bijvoorbeeld video, vermeld alle Video's, haal video inzichten op, enzovoort.
- Video niveau: toegangs tokens op video niveau bieden u de mogelijkheid om bewerkingen uit te voeren op een specifieke **video**. U kunt bijvoorbeeld video inzichten verkrijgen, bijschriften downloaden, widgets ophalen, enzovoort.

U kunt bepalen of deze tokens alleen-lezen zijn of dat ze het bewerken toestaan door **allowEdit = True/False** op te geven.

Voor de meeste server-naar-server-scenario's gebruikt u waarschijnlijk hetzelfde **account** token, omdat de **account bewerkingen en** **video** bewerkingen worden behandeld. Als u echter van plan bent om aan client zijde aanroepen naar Video Indexer te maken (bijvoorbeeld van Java script), zou u een **video** toegangs token willen gebruiken om te voor komen dat clients toegang krijgen tot het hele account. Dit is ook de reden dat bij het insluiten van Video Indexer client code in uw client (bijvoorbeeld met behulp van de **widget Get Insights** of het ophalen van een **speler**), een token voor **video** toegang moet worden verstrekt.

Om het eenvoudiger te maken, kunt u **Authorization-API** > **GetAccounts** gebruiken om uw accounts op te halen zonder eerst een gebruikerstoken te verkrijgen. U kunt verzoeken om de accounts op te halen met behulp van geldige tokens, zodat u een extra aanroep om een accounttoken op te halen kunt overslaan.

Toegangstokens verlopen na één uur. Zorg ervoor dat uw token geldig is voordat u de Operations-API gebruikt. Als deze is verlopen, roept u de verificatie-API opnieuw op om een nieuw toegangs token op te halen.

U bent klaar om te beginnen met de integratie met de API. Zoek [de gedetailleerde beschrijving van elke Video Indexer-REST API](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Account-id

De parameter Account-id is vereist in alle operationele API-aanroepen. Account-id is een GUID die kan worden verkregen op een van de volgende manieren:

* Gebruik de **Video Indexer-website** om de account-id op te halen:

    1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
    2. Ga naar de pagina **Instellingen**.
    3. Kopieer de account-id.

        ![Video Indexer instellingen en account-ID](./media/video-indexer-use-apis/account-id.png)

* Gebruik de **Video Indexer-ontwikkelaarsportal** om de account-id programmatisch op te halen.

    Gebruik de [Get account](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) API.

    > [!TIP]
    > U kunt de toegangstokens voor de accounts genereren door `generateAccessTokens=true` te definiëren.

* Haal de account-id op uit de URL van een pagina van de speler in uw account.

    Wanneer u een video bekijkt, wordt de id weergegeven na de sectie `accounts` en voor de sectie `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Aanbevelingen

In deze sectie vindt u enkele aanbevelingen bij het gebruik van de Video Indexer-API.

- Als u van plan bent een video te uploaden, is het raadzaam om het bestand in een open bare netwerk locatie te plaatsen (bijvoorbeeld een Azure Blob Storage-account). Haal de link naar de video op en geef de URL op als de parameter van het uploadbestand.

    De URL die is opgegeven met Video Indexer moet verwijzen naar een mediabestand (audio of video). Een eenvoudige verificatie voor de URL (of SAS-URL) is om deze in een browser te plakken als het bestand wordt afgespeeld of gedownload, is dit waarschijnlijk een goede URL. Als de browser een bepaalde visualisatie weergeeft, is het waarschijnlijk geen koppeling naar een bestand maar naar een HTML-pagina.

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

Wanneer u klaar bent met deze zelf studie, verwijdert u de resources die u niet wilt gebruiken.

## <a name="see-also"></a>Zie tevens

- [Overzicht van Video Indexer](video-indexer-overview.md)
- [Regio's](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Volgende stappen

- [Details van de JSON van de uitvoer bekijken](video-indexer-output-json-v2.md)
- Bekijk de [voorbeeld code](https://github.com/Azure-Samples/media-services-video-indexer) die belang rijk aspect van het uploaden en indexeren van een video toont. Met de volgende code krijgt u een goed idee van het gebruik van onze API voor basis functionaliteit. Lees de opmerkingen in inline en noteer de aanbevelingen voor best practices.

