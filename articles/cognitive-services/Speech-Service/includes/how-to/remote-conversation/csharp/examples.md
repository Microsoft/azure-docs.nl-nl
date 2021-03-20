---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.custom: devx-track-csharp
ms.openlocfilehash: 51b919c97a15946f57211cf8fe12d7c5efe435bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934634"
---
## <a name="upload-the-audio"></a>De audio uploaden

Voordat asynchrone transcriptie kunnen worden uitgevoerd, moet u de audio verzenden naar de conversatie transcriptie-service met behulp van de micro soft cognitieve speech client SDK (versie 1.13.0 of hoger).

Deze voorbeeld code laat zien hoe u een conversatie transcriber maakt voor de modus alleen asynchroon. Als u audio wilt streamen naar de transcriber, voegt u code voor audio-streaming toe die is afgeleid van het verzetten van [conversaties in realtime met de spraak-SDK](../../../../how-to-use-conversation-transcription.md). 

```csharp
async Task CompleteContinuousRecognition(ConversationTranscriber recognizer, string conversationId)
{
    recognizer.SessionStopped += (s, e) =>
    {
        m_taskCompletionSource.TrySetResult(0);
    };
    string canceled = string.Empty;

    recognizer.Canceled += (s, e) => {
        canceled = e.ErrorDetails;
        if (e.Reason == CancellationReason.Error)
        {
            m_taskCompletionSource.TrySetResult(0);
        }
    };

    await recognizer.StartTranscribingAsync().ConfigureAwait(false);
    await Task.WhenAny(m_taskCompletionSource.Task, Task.Delay(TimeSpan.FromSeconds(10)));
    await recognizer.StopTranscribingAsync().ConfigureAwait(false);
}

async Task<List<string>> GetRecognizerResult(ConversationTranscriber recognizer, string conversationId)
{
    List<string> recognizedText = new List<string>();
    recognizer.Transcribed += (s, e) =>
    {
        if (e.Result.Text.Length > 0)
        {
            recognizedText.Add(e.Result.Text);
        }
    };

    await CompleteContinuousRecognition(recognizer, conversationId);

    recognizer.Dispose();
    return recognizedText;
}

async Task UploadAudio()
{
    // Create the speech config object
    // Substitute real information for "YourSubscriptionKey" and "Region"
    SpeechConfig speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "Region");
    speechConfig.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");

    // Set the property for asynchronous transcription
    speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

    // Set the property for real-time plus asynchronous transcription
    //speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

    // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
    // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
    PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
    // Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
    AudioStreamFormat audioStreamFormat = AudioStreamFormat.GetWaveFormatPCM((long)16000, (short)16,(short)8);
    // Create an input stream
    AudioInputStream audioStream = AudioInputStream.CreatePullStream(wavfilePullStreamCallback, audioStreamFormat);

    // pick a conversation Id that is a GUID.
    String conversationId = Guid.NewGuid().ToString();

    // Create a Conversation
    using (var conversation = await Conversation.CreateConversationAsync(speechConfig, conversationId))
    {
        using (var conversationTranscriber = TrackSessionId(new ConversationTranscriber(AudioConfig.FromStreamInput(audioStream))))
        {
            await conversationTranscriber.JoinConversationAsync(conversation);
            // Helper function to get the real time transcription results
            var result = await GetRecognizerResult(conversationTranscriber, conversationId);
        }
    }
}
```

Als u real-time _plus_ asynchroon en commentaar wilt toevoegen aan de juiste regels code, volgt u de volgende stappen:

```csharp
// Set the property for asynchronous transcription
//speechConfig.SetServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.SetServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Transcriptie-resultaten ophalen

Installeer de **micro soft. CognitiveServices. speech. Remoteconversation-versie 1.13.0 of hoger** via NuGet.

### <a name="sample-transcription-code"></a>Voorbeeld code transcriptie

Nadat u de hebt `conversationId` gemaakt, maakt u een externe conversatie transcriptie-client **RemoteConversationTranscriptionClient** op de client toepassing om de status van de asynchrone transcriptie op te vragen. Maak een  **RemoteConversationTranscriptionOperation** -object om een lang uitgevoerd [bewerkings](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/core/Azure.Core#consuming-long-running-operations-using-operationt) object op te halen. U kunt de status van de bewerking controleren of wachten totdat deze is voltooid. 

```csharp
// Create the speech config
SpeechConfig config = SpeechConfig.FromSubscription("YourSpeechKey", "YourSpeechRegion");
// Create the speech client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(config);
// Create the remote operation
RemoteConversationTranscriptionOperation operation = 
                            new RemoteConversationTranscriptionOperation(conversationId, client);

// Wait for operation to finish
await operation.WaitForCompletionAsync(TimeSpan.FromSeconds(10), CancellationToken.None);
// Get the result of the long running operation
var val = operation.Value.ConversationTranscriptionResults;
// Print the fields from the results
foreach (var item in val)
{
    Console.WriteLine($"{item.Text}, {item.ResultId}, {item.Reason}, {item.UserId}, {item.OffsetInTicks}, {item.Duration}");
    Console.WriteLine($"{item.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)}");
}
Console.WriteLine("Operation completed");

```
