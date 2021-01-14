---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 08679bac77121ee2d276f3d2854e0b119c769582
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109491"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">Installeer de Speech-SDK voor uw ontwikkelomgeving en maak een leeg voorbeeldproject<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor intentieherkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Open uw project in Visual Studio

Open vervolgens uw project in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project en open `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen. Houd er rekening mee dat u een asynchrone methode met de naam `recognizeIntent()` hebt gemaakt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de sleutel en de locatie voor uw LUIS-voorspellingsresource.

> [!IMPORTANT]
> Uw startsleutel- en ontwerpsleutels werken niet. U moet uw voorspellingssleutel en -locatie gebruiken die u eerder hebt gemaakt. Voor meer informatie, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

Voeg deze code toe in de methode `recognizeIntent()`. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door de LUIS-voorspellingssleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door de locatie van uw LUIS.  Gebruik **Regio-id** uit [regio](../../../../regions.md).

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

In dit voorbeeld wordt de methode `FromSubscription()` gebruikt om de `SpeechConfig` te maken. Zie [SpeechConfig Class](/cpp/cognitive-services/speech/speechconfig) voor een volledige lijst met beschikbare methoden.

De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Laten we nu een `IntentRecognizer` maken. Voeg deze code toe in de methode `recognizeIntent()`, recht onder uw Speech-configuratie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en Intents toevoegen

U moet een `LanguageUnderstandingModel` koppelen aan de intentieherkenning en de intenties toevoegen die u wilt laten herkennen. We gaan de intenties van het vooraf ontwikkelde domein voor woningautomatisering gebruiken.

Voeg deze code toe onder uw `IntentRecognizer`. Zorg ervoor dat u `"YourLanguageUnderstandingAppId"` vervangt door de id van uw LUIS-app.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

In dit voorbeeld wordt de functie `AddIntent()` gebruikt om intenties afzonderlijk toe te voegen. Als u alle intenties uit een model wilt toevoegen, gebruikt u `AddAllIntents(model)` en geeft u het model door.

> [!NOTE]
> Speech SDK ondersteunt alleen LUIS v2.0-eindpunten.
> U moet de URL van het v3.0-eindpunt in het veld van de voorbeeldquery handmatig wijzigen om een v2.0 URL-patroon te gebruiken.
> LUIS v2.0-eindpunten volgen altijd een van deze twee patronen:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de methode `RecognizeOnceAsync()` aan. Met deze methode laat u de Speech-service weten dat u één woordgroep verstuurt voor herkenning en dat er kan worden gestopt met het herkennen van spraak zodra de woordgroep is geïdentificeerd. Voor de eenvoud zullen we wachten tot de toekomstige retournering voltooid is.

Voeg deze code toe onder uw model:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten (of fouten) weergeven

Wanneer het herkenningsresultaat wordt geretourneerd door de Speech-service, wilt u daar iets mee doen. We zullen het eenvoudig houden en het resultaat afdrukken naar de console.

Voeg deze code toe onder `auto result = recognizer->RecognizeOnceAsync().get();`:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Uw code controleren

Op dit punt moet uw code er als volgt uitzien:

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

U bent nu klaar om uw app te bouwen en de spraakherkenning te testen met behulp van de Speech-service.

1. **De code compileren**: kies in de menubalk van Visual Studio **Build** > **Build Solution**.
2. **Start uw app**: kies in de menubalk **Debug** > **Start Debugging** of druk op <kbd>F5</kbd>.
3. **Herkenning starten**: u wordt gevraagd om een woordgroep uit te spreken in het Engels. Uw spraak wordt verzonden naar de Speech-service, getranscribeerd als tekst en weergegeven in de console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]