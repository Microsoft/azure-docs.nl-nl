---
title: Aanroepen opnemen en downloaden met Event Grid- een Azure Communication Services quickstart
titleSuffix: An Azure Communication Services quickstart
description: In deze quickstart leert u hoe u aanroepen kunt opnemen en downloaden met behulp van Event Grid.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730567"
---
# <a name="record-and-download-calls-with-event-grid"></a>Aanroepen opnemen en downloaden met Event Grid

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ga aan de slag met Azure Communication Services door uw Communication Services op te nemen met behulp Azure Event Grid.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een actieve Communication Services-resource. [Een Communication Services maken](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Het [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) NuGet-pakket.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Een webhook maken en u abonneren op de opnamegebeurtenissen
We gebruiken *webhooks*  en gebeurtenissen om het opnemen van aanroepen en downloaden van mediabestand mogelijk te maken. 

Eerst maken we een webhook. Uw Communication Services-resource gebruikt Event Grid om deze webhook te waarschuwen wanneer de gebeurtenis wordt geactiveerd en vervolgens opnieuw wanneer opgenomen media gereed zijn om `recording` te worden gedownload.

U kunt uw eigen aangepaste webhook schrijven om deze gebeurtenismeldingen te ontvangen. Het is belangrijk dat deze webhook reageert op inkomende berichten met de validatiecode om de webhook te abonneren op de gebeurtenisservice.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


De bovenstaande code is afhankelijk van het `Microsoft.Azure.EventGrid` NuGet-pakket. Voor meer informatie over het valideren Event Grid eindpunt, gaat u naar de [documentatie voor eindpuntvalidatie](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation)

Vervolgens abonneren we deze webhook op de `recording` gebeurtenis:

1. Selecteer de `Events` blade in uw Azure Communication Services resource.
2. Selecteer `Event Subscription` zoals hieronder wordt weergegeven.
![Schermopname van de gebruikersinterface van Event Grid](./media/call-recording/image1-event-grid.png)
3. Configureer het gebeurtenisabonnement en selecteer `Call Recording File Status Update` als `Event Type` de . Selecteer `Webhook` als `Endpoint type` de .
![Gebeurtenisabonnement maken](./media/call-recording/image2-create-subscription.png)
4. Voer de URL van uw webhook `Subscriber Endpoint` in.
![Abonneren op gebeurtenis](./media/call-recording/image3-subscribe-to-event.png)

Uw webhook wordt nu op de hoogte gesteld wanneer uw Communication Services resource wordt gebruikt om een aanroep te registreren.

## <a name="notification-schema"></a>Meldingsschema
Wanneer de opname beschikbaar is om te downloaden, Communication Services resource een melding met het volgende gebeurtenisschema. De document-ID's voor de opname kunnen worden opgehaald uit de `documentId` velden van elke `recordingChunk` .

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>De opgenomen mediabestanden downloaden

Zodra we de document-id voor het bestand hebben dat we willen downloaden, roepen we de onderstaande Azure Communication Services API's aan om de opgenomen media en metagegevens te downloaden met behulp van HMAC-verificatie.

De maximale grootte van het opnamebestand is 1,5 GB. Wanneer deze bestandsgrootte wordt overschreden, splitst de recorder automatisch opgenomen media in meerdere bestanden.

De client moet alle mediabestanden met één aanvraag kunnen downloaden. Als er een probleem is, kan de client het opnieuw proberen met een bereikheader om te voorkomen dat segmenten die al zijn gedownload opnieuw worden gedownload.

Opgenomen media downloaden: 
- Methode: `GET` 
- Url: https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Vastgelegde mediametagegevens downloaden: 
- Methode: `GET` 
- Url: https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Verificatie
Als u opgenomen media en metagegevens wilt downloaden, gebruikt u HMAC-verificatie om de aanvraag te verifiëren Azure Communication Services API's.

Maak een `HttpClient` en voeg de benodigde headers toe met behulp van de `HmacAuthenticationUtils` onderstaande:

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
De onderstaande hulpprogramma's kunnen worden gebruikt voor het beheren van uw HMAC-werkstroom.

**Hash van inhoud maken**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**HMAC-headers toevoegen**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Resources opschonen
Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).


## <a name="next-steps"></a>Volgende stappen
Raadpleeg voor meer informatie de volgende artikelen:

- Bekijk ons voorbeeld [van webbellen](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Meer informatie over [mogelijkheden voor het aanroepen van de SDK](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Meer informatie over [de werking van aanroepen](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
