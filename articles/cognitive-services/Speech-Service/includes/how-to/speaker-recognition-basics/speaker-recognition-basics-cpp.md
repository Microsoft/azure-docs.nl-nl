---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: f8b29da09f5da4a30378cc0228fecad63d196162
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445038"
---
In deze quickstart leert u basispatronen voor het ontwerp van Speaker Recognition met behulp van de Speech SDK, met inbegrip van:

* Tekstafhankelijke en tekstonafhankelijke verificatie
* Sprekeridentificatie voor het identificeren van een spraaksample in een groep stemmen
* Spraakprofielen verwijderen

Zie het artikel [Overzicht](../../../speaker-recognition-overview.md) voor een diepgaande bespreking van de concepten van spraakherkenning.

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [C++-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition wordt momenteel *alleen* ondersteund in Azure Speech-resources die in de regio `westus` zijn gemaakt.

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows </a>

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit artikel wilt uitvoeren, voegt u boven aan het cpp-bestand de volgende instructies toe.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Tekstafhankelijke verificatie

Met Speaker Verification wordt bevestigd dat een spreker overeenkomt met een bekende of **geregistreerde** stem. De eerste stap bestaat uit het **registreren** van een spraakprofiel, zodat dit met latere spraaksamples kan worden vergeleken. In dit voorbeeld moet u het profiel registreren door middel van een **tekstafhankelijke** strategie, waarvoor een specifieke wachtwoordzin is vereist voor zowel de registratie als de verificatie. Zie het [referentiemateriaal ](/rest/api/speakerrecognition/) voor een lijst met ondersteunde wachtwoordzinnen.

### <a name="textdependentverification-function"></a>De functie TextDependentVerification

Begin door de functie `TextDependentVerification` te maken.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Deze functie maakt een [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-object met de methode [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync). Houd er rekening mee dat er drie [typen](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) van `VoiceProfile` zijn:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

In dit geval geeft u `VoiceProfileType::TextDependentVerification` door aan `CreateProfileAsync`.

Vervolgens roept u twee hulpfuncties aan die u hierna definieert, `AddEnrollmentsToTextDependentProfile` en `SpeakerVerify`. Ten slotte roept u [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) aan om het profiel op te schonen.

### <a name="addenrollmentstotextdependentprofile-function"></a>De functie AddEnrollmentsToTextDependentProfile

Definieer de volgende functie om een spraakprofiel in te schrijven.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

In deze functie registreert u audiosamples in een `while`-lus, waarmee het aantal voor de registratie resterende en vereiste samples wordt gevolgd. In elke iteratie vraagt [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) u om de wachtwoordzin in uw microfoon in te spreken en voegt u de sample aan het spraakprofiel toe.

### <a name="speakerverify-function"></a>De functie SpeakerVerify

Definieer `SpeakerVerify` als volgt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

In deze functie maakt u een [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel)-object met de methode [SpeakerVerificationModel::FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile), waarbij u het [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-object dat u eerder hebt gemaakt, doorgeeft.

Vervolgens vraagt [SpeechRecognizer:: RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) u om de wachtwoordzin opnieuw te spreken, maar deze keer wordt deze gecontroleerd aan de hand van uw spraakprofiel en wordt een vergelijkingsscore van 0,0 -1,0 geretourneerd. Het [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult)-object retourneert ook `Accept` of `Reject`, afhankelijk van of de wachtwoordzin overeenkomt.

## <a name="text-independent-verification"></a>Tekstonafhankelijke verificatie

In tegenstelling tot **tekstafhankelijke** verificatie, geldt voor **tekstonafhankelijke** verificatie:

* Vereist niet dat een bepaalde wachtwoordzin wordt uitgesproken. Alles mag worden uitgesproken
* Vereist niet drie audiovoorbeelden, maar vereist in totaal *wel* twintig seconden aan audio

### <a name="textindependentverification-function"></a>De functie TextIndependentVerification

Begin door de functie `TextIndependentVerification` te maken.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Net als de functie `TextDependentVerification` maakt deze functie een [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-object met de methode [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync).

In dit geval geeft u `VoiceProfileType::TextIndependentVerification` door aan `CreateProfileAsync`.

Vervolgens roept u twee hulpfuncties aan: `AddEnrollmentsToTextIndependentProfile`, die u hierna definieert, en `SpeakerVerify`, die u al hebt gedefinieerd. Ten slotte roept u [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) aan om het profiel op te schonen.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definieer de volgende functie om een spraakprofiel in te schrijven.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

In deze functie registreert u audiosamples in een `while`-lus, waarmee het aantal voor de registratie resterende en vereiste seconden audio wordt gevolgd. In elke iteratie vraagt [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) u om in uw microfoon te spreken en voegt de sample aan het spraakprofiel toe.

## <a name="speaker-identification"></a>Sprekeridentificatie

Speaker Identification wordt gebruikt om vast te stellen **wie** er spreekt uit een bepaalde groep geregistreerde stemmen. Het proces is vergelijkbaar met **tekstonafhankelijke verificatie**, waarbij het belangrijkste verschil is dat tegelijkertijd verificatie tegen meerdere spraakprofielen kan worden uitgevoerd in plaats van te verifiëren tegen één profiel.

### <a name="textindependentidentification-function"></a>De functie TextIndependentIdentification

Begin door de functie `TextIndependentIdentification` te maken.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Net als de functies `TextDependentVerification` en `TextIndependentVerification` maakt deze functie een [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-object met de methode [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync).

In dit geval geeft u `VoiceProfileType::TextIndependentIdentification` door aan `CreateProfileAsync`.

Vervolgens roept u twee hulpfuncties aan: `AddEnrollmentsToTextIndependentProfile`, die u als hebt gedefinieerd, en `SpeakerIdentify`, die u hierna definieert. Ten slotte roept u [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) aan om het profiel op te schonen.

### <a name="speakeridentify-function"></a>De functie SpeakerIdentify

Definieer de functie `SpeakerIdentify` als volgt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

In deze functie maakt u een [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel)-object met de methode [SpeakerIdentificationModel::FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles). `SpeakerIdentificationModel::FromProfiles` accepteert een lijst van [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile)-objecten. In dit geval geeft u alleen het object `VoiceProfile` dat u eerder hebt gemaakt door. Als u wilt, kunt u echter meerdere `VoiceProfile`-objecten doorgeven, die allemaal zijn ingeschreven met audiosamples van een andere stem.

Vervolgens vraagt [SpeechRecognizer::RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) u om opnieuw te spreken. Nu vergelijkt deze uw stem met de ingeschreven spraakprofielen en wordt het meest vergelijkbare spraakprofiel geretourneerd.

## <a name="main-function"></a>Hoofdfunctie

Definieer tenslotte de functie `main` als volgt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Deze functie roept gewoon de functies aan die u eerder hebt gedefinieerd. Er wordt echter eerst een [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient)-object en een [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer)-object gemaakt.

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

De `VoiceProfileClient` wordt gebruikt voor het maken, inschrijven en verwijderen van spraakprofielen. De `SpeakerRecognizer` wordt gebruikt om spraakvoorbeelden te valideren op basis van een of meer ingeschreven spraakprofielen.

## <a name="changing-audio-input-type"></a>Type audio-invoer wijzigen

In de voorbeelden in dit artikel wordt de standaardmicrofoon van het apparaat gebruikt als invoer voor audiosamples. In scenario's waarin u echter audiobestanden moet gebruiken in plaats van de microfooninvoer, wijzigt u de volgende regel:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

in:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Of u vervangt het gebruik van `audio_config` door [Audio::AudioConfig::FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Gemengde invoer is ook mogelijk, met behulp van bijvoorbeeld een microfoon voor de inschrijving en bestanden voor de verificatie.