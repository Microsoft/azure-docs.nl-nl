---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 502729e4c90a6d6ffa424ea7f379e2b426b3e3e0
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445502"
---
## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech-SDK installeren voor JavaScript</a>. Gebruik de volgende instructies, afhankelijk van uw platform:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser </a>

## <a name="create-voice-signatures"></a>Spraak handtekeningen maken

De eerste stap is het maken van gesp roken hand tekeningen voor de deel nemers aan de conversatie zodat ze kunnen worden geïdentificeerd als unieke luid sprekers. Het `.wav` audio bestand voor invoer voor het maken van spraak handtekeningen moet 16-bits, 16 kHz sample frequentie en een mono-indeling (Single Channel) zijn. De aanbevolen lengte voor elk audio voorbeeld ligt tussen dertig seconden en twee minuten. Het `.wav` bestand moet een voor beeld zijn van de stem van **één persoon** , zodat er een uniek spraak profiel wordt gemaakt.

In het volgende voor beeld ziet u hoe u een spraak handtekening maakt met [behulp van de rest API](https://aka.ms/cts/signaturegenservice) in Java script. Houd er rekening mee dat u de werkelijke gegevens voor uw `subscriptionKey` , `region` en het pad naar een voorbeeld `.wav` bestand moet vervangen.

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

Als u dit script uitvoert, wordt een teken reeks voor spraak ondertekening geretourneerd in de variabele `voiceSignatureString` . Voer de functie twee maal uit zodat u twee teken reeksen kunt gebruiken als invoer voor de variabelen `voiceSignatureStringUser1` en de `voiceSignatureStringUser2` onderstaande.

> [!NOTE]
> Spraak handtekeningen kunnen **alleen** worden gemaakt met behulp van de rest API.

## <a name="transcribe-conversations"></a>Conversaties transcriberen

De volgende voorbeeld code laat zien hoe u gesp rekken in realtime kunt transcriberen voor twee luid sprekers. Hierbij wordt ervan uitgegaan dat u voor elke spreker al gesp roken teken reeksen voor spraak handtekening hebt gemaakt zoals hierboven wordt weer gegeven. Vervang de werkelijke gegevens voor `subscriptionKey` , `region` en het pad `filepath` voor de audio die u wilt transcriberen.

Deze voorbeeld code doet het volgende:

* Hiermee maakt u een push-stream die moet worden gebruikt voor transcriptie en schrijft u het voorbeeld `.wav` bestand naar het.
* Hiermee maakt `Conversation` u een using `createConversationAsync()` .
* Hiermee maakt `ConversationTranscriber` u een met de constructor.
* Deel nemers toevoegen aan de conversatie. De teken reeksen `voiceSignatureStringUser1` en `voiceSignatureStringUser2` moeten als uitvoer worden opgehaald uit de bovenstaande stappen.
* Registreert op gebeurtenissen en begint transcriptie.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```