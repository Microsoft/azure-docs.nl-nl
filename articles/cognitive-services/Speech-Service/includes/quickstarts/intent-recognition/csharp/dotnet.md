---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c14dfadfb7e8724c196b125e9b93c439fac249c3
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109479"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Installeer de Speech-SDK voor uw ontwikkelomgeving en maak een leeg voorbeeldproject<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor intentieherkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Open uw project in Visual Studio

Open vervolgens uw project in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project en open `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen. Houd er rekening mee dat u een asynchrone methode met de naam `RecognizeIntentAsync()` hebt gemaakt.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de sleutel en de locatie voor uw LUIS-voorspellingsresource.

> [!IMPORTANT]
> Uw startsleutel- en ontwerpsleutels werken niet. U moet uw voorspellingssleutel en -locatie gebruiken die u eerder hebt gemaakt. Voor meer informatie, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

Voeg deze code toe in de methode `RecognizeIntentAsync()`. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door de LUIS-voorspellingssleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door de locatie van uw LUIS. Gebruik **Regio-id** uit [regio](../../../../regions.md).

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

In dit voorbeeld wordt de methode `FromSubscription()` gebruikt om de `SpeechConfig` te maken. Zie [SpeechConfig Class](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) voor een volledige lijst met beschikbare methoden.

De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Laten we nu een `IntentRecognizer` maken. Dit object wordt in een using-instructie gemaakt om de juiste vrijgave van niet-beheerde resources te verzekeren. Voeg deze code toe in de methode `RecognizeIntentAsync()`, recht onder uw Speech-configuratie.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet een `LanguageUnderstandingModel` koppelen aan de intentieherkenning en de intenties toevoegen die u wilt laten herkennen. We gaan de intenties van het vooraf ontwikkelde domein voor woningautomatisering gebruiken. Voeg deze code toe in de using-instructie uit de vorige sectie. Zorg ervoor dat u `"YourLanguageUnderstandingAppId"` vervangt door de id van uw LUIS-app.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

In dit voorbeeld wordt de functie `AddIntent()` gebruikt om intenties afzonderlijk toe te voegen. Als u alle intenties uit een model wilt toevoegen, gebruikt u `AddAllIntents(model)` en geeft u het model door. 

> [!NOTE]
> Speech SDK ondersteunt alleen LUIS v 2.0-eindpunten.
> U moet de URL van het v3.0-eindpunt in het veld van de voorbeeldquery handmatig wijzigen om een v2.0 URL-patroon te gebruiken.
> LUIS v2.0-eindpunten volgen altijd een van deze twee patronen:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de methode `RecognizeOnceAsync()` aan. Met deze methode laat u de Speech-service weten dat u één woordgroep verstuurt voor herkenning en dat er kan worden gestopt met het herkennen van spraak zodra de woordgroep is geïdentificeerd.

In de using-instructie voegt u deze code toe onder uw model.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Herkenningsresultaten (of fouten) weergeven

Wanneer het herkenningsresultaat wordt geretourneerd door de Speech-service, wilt u daar iets mee doen. We zullen het eenvoudig houden en het resultaten afdrukken naar de console.

In de using-instructie voegt u onder `RecognizeOnceAsync()` deze code toe:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Uw code controleren

Op dit punt moet uw code er als volgt uitzien:

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

U bent nu klaar om uw app te bouwen en de spraakherkenning te testen met behulp van de Speech-service.

1. **De code compileren**: kies in de menubalk van Visual Studio **Build** > **Build Solution**.
2. **Start uw app**: kies in de menubalk **Debug** > **Start Debugging** of druk op <kbd>F5</kbd>.
3. **Herkenning starten**: u wordt gevraagd om een woordgroep uit te spreken in het Engels. Uw spraak wordt verzonden naar de Speech-service, getranscribeerd als tekst en weergegeven in de console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]