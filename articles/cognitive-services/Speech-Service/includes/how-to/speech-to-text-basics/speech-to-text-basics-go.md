---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/15/2020
ms.author: trbye
ms.openlocfilehash: bced384e8ba88fb83499e78c4e0d60e811ae32df
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214258"
---
Een van de belangrijkste functies van de Speech-service is de mogelijkheid om menselijke spraak te herkennen en te transcriberen (ook wel spraak-naar-tekst genoemd). In deze quickstart leert u meer over het gebruik van de Speech-SDK in uw apps en producten om spraak-naar-tekst-conversie van hoge kwaliteit uit te voeren.

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [Go-quickstart-voorbeelden](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/recognizer) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de [Speech-SDK voor Go](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser) installeren.

## <a name="speech-to-text-from-microphone"></a>Spraak naar tekst vanuit een microfoon

Gebruik het volgende codevoorbeeld om spraakherkenning uit te voeren met de standaardmicrofoon van het apparaat. Vervang de variabelen `subscription` en `region` door de sleutels voor uw abonnement en regio. Wanneer het script wordt uitgevoerd, wordt er een herkenningssessie gestart op de standaardmicrofoon en wordt er tekst uitgevoerd.

```go
package main

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func sessionStartedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
}

func sessionStoppedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
}

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(sessionStartedHandler)
    speechRecognizer.SessionStopped(sessionStoppedHandler)
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Voer de volgende opdrachten uit om een go.mod-bestand te maken dat is gekoppeld aan onderdelen die worden gehost op Github.

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Bouw de code en voer deze uit.

```cmd
go build
go run quickstart
```

Zie de referentiedocumenten voor gedetailleerde informatie over de klassen [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechConfig) en [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechRecognizer).

## <a name="speech-to-text-from-audio-file"></a>Spraak naar tekst vanuit een audiobestand

Gebruik het volgende voorbeeld om spraakherkenning uit te voeren met een audiobestand. Vervang de variabelen `subscription` en `region` door de sleutels voor uw abonnement en regio. Vervang ook de variabele `file` door een pad naar een WAV-bestand. Als u het script uitvoert, wordt spraak uit het bestand herkend en het tekstresultaat uitgevoerd.

```go
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"
    file := "path/to/file.wav"

    audioConfig, err := audio.NewAudioConfigFromWavFileInput(file)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Started (ID=", event.SessionID, ")")
    })
    speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Stopped (ID=", event.SessionID, ")")
    })

    task := speechRecognizer.RecognizeOnceAsync()
    var outcome speech.SpeechRecognitionOutcome
    select {
    case outcome = <-task:
    case <-time.After(5 * time.Second):
        fmt.Println("Timed out")
        return
    }
    defer outcome.Close()
    if outcome.Error != nil {
        fmt.Println("Got an error: ", outcome.Error)
    }
    fmt.Println("Got a recognition!")
    fmt.Println(outcome.Result.Text)
}
```

Voer de volgende opdrachten uit om een go.mod-bestand te maken dat is gekoppeld aan onderdelen die worden gehost op Github.

```cmd
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

Bouw de code en voer deze uit.

```cmd
go build
go run quickstart
```

Zie de referentiedocumenten voor gedetailleerde informatie over de klassen [`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechConfig) en [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.15.0/speech#SpeechRecognizer).