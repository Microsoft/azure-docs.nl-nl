---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: c37637ba1cb5154bbd56f7fd22bbbca62b8c3438
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95096107"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md)
> * Een bot maken die verbonden is met het [Direct Line Speech-kanaal](/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Ervoor zorgen dat u toegang tot een microfoon hebt voor het vastleggen van audio
>
  > [!NOTE]
  > Raadpleeg [de lijst met ondersteunde regio's voor spraakassistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

## <a name="setup-your-environment"></a>Uw omgeving instellen

Werk het bestand go.mod bij met de nieuwste SDK-versie door deze regel toe te voegen
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.14.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode
Vervang de inhoud van het bronbestand (bijvoorbeeld `quickstart.go`) door de onderstaande, die het volgende bevat:

- definitie van het 'main'-pakket
- de benodigde modules importeren uit de Speech SDK
- variabelen voor het opslaan van de botgegevens die later in deze quickstart worden vervangen
- een eenvoudige implementatie waarin de microfoon wordt gebruikt voor audio-invoer
- gebeurtenis-handlers voor verschillende gebeurtenissen die plaatsvinden tijdens spraakinteractie

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Vervang de waarden `YOUR_SUBSCRIPTION_KEY` en `YOUR_BOT_REGION` door de werkelijke waarden van de Spraak-resource.

- Ga naar Azure Portal en open uw Spraak-resource
- Onder **Sleutels en Eindpunt** aan de linkerkant bevinden zich twee beschikbare abonnementssleutels
    - Gebruik een van beide als vervanging van de waarde `YOUR_SUBSCRIPTION_KEY`
- Noteer de regio onder **Overzicht** aan de linkerkant en wijs deze toe aan de regio-id
    - Gebruik de regio-id als vervanging van de waarde `YOUR_BOT_REGION`, bijvoorbeeld `"westus"` voor **US - west**

   > [!NOTE]
   > Raadpleeg [de lijst met ondersteunde regio's voor spraakassistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

   > [!NOTE]
   > Zie de Bot Framework-documentatie voor [het Direct Line Speech-kanaal](/azure/bot-service/bot-service-channel-connect-directlinespeech) voor meer informatie over het configureren van uw bot.

## <a name="code-explanation"></a>Uitleg bij de code
De sleutel en de regio voor het Spraak-abonnement zijn vereist voor het maken van een spraakconfiguratieobject. Het configuratieobject is vereist voor het instantiëren van een spraakherkenningsobject.

Het Recognizer-exemplaar biedt meerdere manieren om spraak te herkennen. In dit voorbeeld wordt spraak continu herkend. Door deze functionaliteit weet de Spraak-service dat u veel frasen ter herkenning verzendt, en dat het herkennen van spraak moet stoppen wanneer het programma wordt beëindigd. Wanneer er resultaten worden verkregen, worden deze door de code naar de console geschreven.

## <a name="build-and-run"></a>Bouwen en uitvoeren
U bent nu klaar om uw app te bouwen en uw aangepaste spraakassistent met behulp van de Spraakservice te testen.
1. Compileer uw project, bijvoorbeeld **'go-build'**
2. Voer de module uit en spreek enkele woorden of een zin in de microfoon van uw apparaat. De gesproken tekst wordt verzonden naar de Direct Line Speech-service en getranscribeerd naar tekst, die als uitvoer wordt weergegeven.


> [!NOTE]
> De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]