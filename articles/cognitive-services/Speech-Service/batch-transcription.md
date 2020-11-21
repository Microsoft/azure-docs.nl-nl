---
title: Batch transcriptie-Speech Service gebruiken
titleSuffix: Azure Cognitive Services
description: Batch transcriptie is ideaal als u een grote hoeveelheid audio in de opslag ruimte wilt transcriberen, zoals Azure-blobs. U kunt met behulp van de toegewezen REST API naar audio bestanden verwijzen met een SAS-URI (Shared Access Signature) en transcripties asynchroon ontvangen.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: b9ed43019e7af0cb810c3e0fc849281a458a43e1
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023630"
---
# <a name="how-to-use-batch-transcription"></a>Batch-transcriptie gebruiken

Batch-transcriptie is een reeks REST API bewerkingen waarmee u een grote hoeveelheid audio in de opslag kunt transcriberen. U kunt naar audio bestanden verwijzen met behulp van een typische URI of een SAS-URI (Shared Access Signature) en transcriptie-resultaten asynchroon ontvangen. Met de v 3.0 API kunt u een of meer audio bestanden transcriberen of een hele opslag container verwerken.

U kunt batch-transcriptie REST-Api's gebruiken om de volgende methoden aan te roepen:

|    Batch transcriptie-bewerking                                             |    Methode    |    REST API-aanroep                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Hiermee maakt u een nieuwe transcriptie.                                              |    POST      |    speechtotext/v 3.0/transcripties            |
|    Hiermee wordt een lijst met transcripties voor het geverifieerde abonnement opgehaald.    |    GET       |    speechtotext/v 3.0/transcripties            |
|    Hiermee wordt een lijst met ondersteunde land instellingen voor offline-transcripties opgehaald.              |    GET       |    speechtotext/v 3.0/transcripties/land instellingen    |
|    Hiermee worden de onveranderlijke gegevens van de transcriptie die door de ID worden geïdentificeerd, bijgewerkt.    |    VERZENDEN     |    speechtotext/v 3.0/transcripties/{id}       |
|    Hiermee wordt de opgegeven transcriptie-taak verwijderd.                                 |    DELETE    |    speechtotext/v 3.0/transcripties/{id}       |
|    Hiermee wordt de transcriptie opgehaald die wordt geïdentificeerd door de opgegeven ID.                        |    GET       |    speechtotext/v 3.0/transcripties/{id}       |
|    Hiermee worden de resultaat bestanden opgehaald van de transcriptie die wordt geïdentificeerd door de opgegeven ID.    |    GET       |    speechtotext/v 3.0/transcripties/{id}/bestanden |

U kunt de gedetailleerde API bekijken en testen, die beschikbaar is als [Swagger-document](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Batch transcriptie-taken worden gepland op basis van de beste inspanningen.
U kunt niet schatten wanneer een taak wordt gewijzigd in de actieve status, maar deze moet binnen enkele minuten plaatsvinden bij normale systeem belasting. Zodra de status wordt uitgevoerd, treedt de transcriptie sneller op dan de afspeel snelheid van de audio-runtime.

## <a name="prerequisites"></a>Vereisten

Net als bij alle functies van de speech-service maakt u een abonnements sleutel van de [Azure Portal](https://portal.azure.com) door de [aan de slag-hand leiding](overview.md#try-the-speech-service-for-free)te volgen.

>[!NOTE]
> Voor de speech-service is een Standard-abonnement (S0) vereist om batch-transcriptie te gebruiken. Sleutels voor gratis abonnementen (F0) werken niet. Zie [prijzen en limieten](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie.

Volg de stappen in [akoestische aanpassing](./how-to-custom-speech-train-model.md) en [taal aanpassing](./how-to-custom-speech-train-model.md)als u van plan bent om modellen aan te passen. Als u de gemaakte modellen in batch transcriptie wilt gebruiken, hebt u hun model locatie nodig. U kunt de model locatie ophalen wanneer u de details van het model ( `self` eigenschap) inspecteert. Een geïmplementeerd aangepast eind punt is *niet nodig* voor de batch transcriptie-service.

>[!NOTE]
> Als onderdeel van de REST API heeft batch transcriptie een set [quota en limieten](speech-services-quotas-and-limits.md#batch-transcription), die we aanmoedigen te beoordelen. Om optimaal te profiteren van de mogelijkheid van batch transcriptie om een groot aantal audio bestanden efficiënt te transcriberen, raden we u aan om altijd meerdere bestanden per aanvraag te verzenden of een Blob Storage-container aan te wijzen met de audio bestanden om te transcriberen. Met de service worden de bestanden gelijktijdig getranscribeerd, waardoor de verlever tijd is verminderd. Het gebruik van meerdere bestanden in één aanvraag is zeer eenvoudig en eenvoudig: Zie de [configuratie](#configuration) sectie. 

## <a name="batch-transcription-api"></a>Transcriptie-API voor batch

De batch transcriptie-API ondersteunt de volgende indelingen:

| Indeling | Videocodec | Bits per voor beeld | Sample frequentie             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bits  | 8 kHz of 16 kHz, mono of stereo |
| MP3    | PCM   | 16-bits  | 8 kHz of 16 kHz, mono of stereo |
| OGG    | OPUS  | 16-bits  | 8 kHz of 16 kHz, mono of stereo |

Voor audio stromen met stereo worden de linker-en rechter kanalen gesplitst tijdens de transcriptie. Er wordt een JSON-resultaat bestand gemaakt voor elk kanaal.
Als u een geordende definitieve transcriptie wilt maken, gebruikt u de tijds tempels die per utterance zijn gegenereerd.

### <a name="configuration"></a>Configuratie

Configuratie parameters worden als JSON opgegeven.

**Een of meer afzonderlijke bestanden transcriberen.** Als u meer dan één bestand wilt transcriberen, raden we u aan om meerdere bestanden in één aanvraag te verzenden. In het onderstaande voor beeld worden drie bestanden gebruikt:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**Een hele opslag container verwerken:**

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**Gebruik een aangepast getraind model in een batch-transcriptie.** In het voor beeld worden drie bestanden gebruikt:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Configuratie-eigenschappen

Gebruik deze optionele eigenschappen om transcriptie te configureren:

:::row:::
   :::column span="1":::
      **Parameter**
   :::column-end:::
   :::column span="2":::
      **Beschrijving**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Optioneel, standaard ingesteld op `Masked` . Hiermee geeft u op hoe scheld woorden in de herkennings resultaten moet worden afgehandeld. Geaccepteerde waarden zijn `None` het uitschakelen van het filteren van grove woorden, `Masked` het vervangen van woorden met sterretjes, `Removed` om alle woorden van het resultaat te verwijderen, of `Tags` om "Gods gang" tags toe te voegen.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Optioneel, standaard ingesteld op `DictatedAndAutomatic` . Hiermee wordt aangegeven hoe interpunctie in herkennings resultaten moet worden afgehandeld. Geaccepteerde waarden zijn `None` om Lees tekens uit te scha kelen, `Dictated` zodat er expliciete (gesp roken) Lees tekens worden `Automatic` gebruikt, zodat de decoder met interpunctie kan omgaan of `DictatedAndAutomatic` gedicteerde en automatische interpunctie mag gebruiken.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Standaard optioneel `false` . Hiermee wordt aangegeven of Time Stamps op woord niveau moeten worden toegevoegd aan de uitvoer.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Standaard optioneel `false` . Hiermee geeft u op dat diarization analyse moet worden uitgevoerd op de invoer, die naar verwachting mono-kanaal met twee stemmen is. Opmerking: moet `wordLevelTimestampsEnabled` worden ingesteld op `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Optioneel `0` en is `1` standaard transcribed. Een matrix met kanaal nummers die moeten worden verwerkt. Hier kunt u een subset van de beschik bare kanalen in het audio bestand opgeven die moeten worden verwerkt (bijvoorbeeld `0` alleen).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Optioneel, niet standaard verwijderd. Een duur om transcripties automatisch te verwijderen nadat de transcriptie is voltooid. Het `timeToLive` is handig in transcripties voor massa verwerking om ervoor te zorgen dat ze uiteindelijk worden verwijderd (bijvoorbeeld `PT12H` gedurende 12 uur).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Optionele URL met [service ad-hoc SAS](../../storage/common/storage-sas-overview.md) naar een Beschrijf bare container in Azure. Het resultaat wordt opgeslagen in deze container. SAS met beleid voor opgeslagen toegang wordt **niet** ondersteund. Wanneer deze niet is opgegeven, slaat micro soft de resultaten op in een opslag container die door micro soft wordt beheerd. Wanneer de transcriptie wordt verwijderd door het aanroepen van [Delete transcriptie](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), worden de resultaat gegevens ook verwijderd.
:::row-end:::

### <a name="storage"></a>Storage

Batch transcriptie kan audio lezen van een open bare Internet-URI en kan audio lezen of transcripties schrijven met behulp van een SAS-URI met [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md).

## <a name="batch-transcription-result"></a>Resultaten batch-transcriptie

Voor elke audio-invoer wordt één transcriptie-resultaat bestand gemaakt.
De bewerking [transcripties-bestanden ophalen](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) retourneert een lijst met resultaten bestanden voor deze transcriptie. Als u het transcriptie-bestand voor een specifiek invoer bestand wilt zoeken, filtert u alle geretourneerde bestanden met `kind`  ==  `Transcription` en `name`  ==  `{originalInputName.suffix}.json` .

Elk transcriptie-resultaat bestand heeft de volgende indeling:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "speaker": 1,                 // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]    
    }
  ]
}
```

Het resultaat bevat de volgende velden:

:::row:::
   :::column span="1":::
      **Veld**
   :::column-end:::
   :::column span="2":::
      **Inhoud**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      De werkelijke woorden die worden herkend.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Inverse-text-genormaliseerde vorm van de herkende tekst. Afkortingen ("Doctor Smith" naar "Dr Smith"), telefoon nummers en andere trans formaties worden toegepast.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      Het formulier ITN waarin de maskering voor scheld woorden is toegepast.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      De weergave vorm van de herkende tekst. Er zijn toegevoegde interpunctie en hoofdletter gebruik opgenomen.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Schei ding van de spreker (diarization)

Diarization is het proces waarbij de luid sprekers in een audio fragment worden gescheiden. De batch pijplijn ondersteunt diarization en kan twee luid sprekers herkennen aan mono-kanaal opnamen. De functie is niet beschikbaar op stereo-opnamen.

De uitvoer van transcriptie met diarization ingeschakeld bevat een `Speaker` vermelding voor elke getranscribeerde woord groep. Als diarization niet wordt gebruikt, `Speaker` is de eigenschap niet aanwezig in de JSON-uitvoer. Voor diarization ondersteunen we twee stemmen, waardoor de luid sprekers worden aangeduid als `1` of `2` .

Als u diarization wilt aanvragen, voegt `diarizationEnabled` u de eigenschap instellen op `true` zoals de HTTP-aanvraag hieronder weer gegeven.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Tijds tempels op woord niveau moeten zijn ingeschakeld als de para meters in de bovenstaande aanvraag aangeven.

## <a name="best-practices"></a>Aanbevolen procedures

De batch transcriptie-service kan een groot aantal verzonden transcripties verwerken. U kunt de status van uw transcripties opvragen bij [Get transcripties](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions).
Roep het [verwijderen van transcriptie](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) regel matig aan bij de service zodra u de resultaten hebt opgehaald. Stel `timeToLive` de eigenschap ook in om ervoor te zorgen dat de resultaten uiteindelijk worden verwijderd.

## <a name="sample-code"></a>Voorbeeldcode

Volledige voor beelden zijn beschikbaar in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples) in de `samples/batch` submap.

Werk de voorbeeld code bij met de abonnements gegevens, de service regio, de URI die naar het audio bestand verwijst en model locatie als u een aangepast model gebruikt.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

De voorbeeld code stelt de-client in en verzendt de transcriptie-aanvraag. Vervolgens wordt een poll uitgevoerd voor de status informatie en worden details over de voortgang van de transcriptie afgedrukt.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

Zie het [Swagger-document](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)voor volledige informatie over de voor gaande aanroepen. Voor het volledige voor beeld dat hier wordt weer gegeven, gaat u naar [github](https://aka.ms/csspeech/samples) in de `samples/batch` submap.

In dit voor beeld wordt een asynchrone installatie gebruikt voor het plaatsen van audio en het ontvangen van transcriptie-status.
De- `PostTranscriptions` methode verzendt de details van het audio bestand en de- `GetTranscriptions` methode ontvangt de statussen.
`PostTranscriptions` retourneert een ingang en `GetTranscriptions` gebruikt deze om een ingang te maken om de status van transcriptie op te halen.

In deze voorbeeld code wordt geen aangepast model opgegeven. De service gebruikt het basis model voor het transcriberen van het bestand of de bestanden. Als u het model wilt opgeven, kunt u de model verwijzing voor het aangepaste model door geven aan dezelfde methode.

> [!NOTE]
> Voor basislijn transcripties hoeft u de ID voor het basis model niet te declareren.

## <a name="next-steps"></a>Volgende stappen

- [Spraak naar tekst v3 API-referentie](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)