---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 0cb27a8dc5685ce295c2ce30820734c4301e9dc6
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109372"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Installeer de Speech-SDK voor uw ontwikkelomgeving en maak een leeg voorbeeldproject<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor intentieherkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Open uw project

1. Open de IDE van uw voorkeur.
2. Maak een nieuw project en maak een bestand met de naam `quickstart.py` en open het.

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de sleutel en de locatie voor uw LUIS-voorspellingsresource.

Voeg deze code toe in `quickstart.py`. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door de LUIS-voorspellingssleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door de locatie van uw LUIS. Gebruik **Regio-id** uit [regio](../../../../regions.md)

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Met dit voorbeeld wordt het object `SpeechConfig` gebouwd met behulp van de LUIS-sleutel en-regio. Zie [SpeechConfig Class](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) voor een volledige lijst met beschikbare methoden.

De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Laten we nu een `IntentRecognizer` maken. Voeg deze code toe onder uw Speech-configuratie.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en Intents toevoegen

U moet een `LanguageUnderstandingModel` koppelen aan de intentieherkenning en de intenties toevoegen die u wilt laten herkennen. We gaan de intenties van het vooraf ontwikkelde domein voor woningautomatisering gebruiken.

Voeg deze code toe onder uw `IntentRecognizer`. Zorg ervoor dat u `"YourLanguageUnderstandingAppId"` vervangt door de id van uw LUIS-app. 

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

In dit voorbeeld wordt de functie `add_intents()` gebruikt om een lijst met expliciet gedefinieerde intenties toe te voegen. Als u alle intenties uit een model wilt toevoegen, gebruikt u `add_all_intents(model)` en geeft u het model door.

> [!NOTE]
> Speech SDK ondersteunt alleen LUIS v2.0-eindpunten.
> U moet de URL van het v3.0-eindpunt in het veld van de voorbeeldquery handmatig wijzigen om een v2.0 URL-patroon te gebruiken.
> LUIS v2.0-eindpunten volgen altijd een van deze twee patronen:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de methode `recognize_once()` aan. Met deze methode laat u de Speech-service weten dat u één woordgroep verstuurt voor herkenning en dat er kan worden gestopt met het herkennen van spraak zodra de woordgroep is geïdentificeerd.

Voeg deze code toe onder uw model.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten (of fouten) weergeven

Wanneer het herkenningsresultaat wordt geretourneerd door de Speech-service, wilt u daar iets mee doen. We zullen het eenvoudig houden en het resultaat afdrukken naar de console.

Voeg deze code toe onder de aanroep van `recognize_once()`.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Uw code controleren

Op dit punt moet uw code er als volgt uitzien.

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

Voer het voorbeeld uit vanaf de console of in uw IDE:

```
python quickstart.py
```

De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]