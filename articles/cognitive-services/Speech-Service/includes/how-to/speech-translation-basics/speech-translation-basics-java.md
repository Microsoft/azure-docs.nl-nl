---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: e030e6466570d3fc2abf809ca09d46c9f596b765
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105371"
---
Een van de belangrijkste functies van de speech-service is de mogelijkheid om menselijke spraak te herkennen en te vertalen naar andere talen. In deze Snelstartgids leert u hoe u de Speech SDK in uw apps en producten kunt gebruiken om spraak vertalingen van hoge kwaliteit uit te voeren. In deze Quick Start vindt u onderwerpen, waaronder:

* Omzetten van spraak naar tekst
* Spraak omzetten naar meerdere doel talen
* Direct omzetten van spraak naar spraak uitvoeren

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Raadpleeg de [Java-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies in de sectie <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">de SDK voor spraak ophalen</a> van het artikel _over Speech SDK_ .

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voor beelden in dit artikel wilt uitvoeren, moet u de volgende `import` instructies toevoegen boven aan het **. Java* -code bestand.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevings variabelen

De voorbeeld bron code in dit artikel is afhankelijk van omgevings variabelen voor het opslaan van gevoelige gegevens, zoals de sleutel van het abonnement voor spraak bronnen en de regio. Het Java-code bestand bevat twee `static final String` waarden die zijn toegewezen uit de omgevings variabelen van de hostcomputer, te weten `SPEECH__SUBSCRIPTION__KEY` en `SPEECH__SERVICE__REGION` . Beide velden bevinden zich in het bereik klasse, waardoor ze toegankelijk zijn in methode-instanties van de-klasse. Zie [omgevings variabelen en toepassings configuratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevings variabelen.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
```

## <a name="create-a-speech-translation-configuration"></a>Een configuratie voor spraak omzetting maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechTranslationConfig`][config] maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

> [!TIP]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechTranslationConfig`][config] kunt initialiseren:

* Met een abonnement: geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken hoe een [`SpeechTranslationConfig`][config] wordt gemaakt met behulp van een sleutel en regio. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak van spraak omzetting is het opgeven van de invoer-(of bron-) taal. Laten we eens kijken hoe u de invoertaal wijzigt in Italiaans. In uw code communiceert u met het [`SpeechTranslationConfig`][config] exemplaar en roept u de- `setSpeechRecognitionLanguage` methode aan.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

De [`setSpeechRecognitionLanguage`][recognitionlang] functie verwacht een indelings teken reeks met taal instellingen. U kunt in de lijst met ondersteunde [Landinstellingen en talen](../../../language-support.md) een willekeurige waarde opgeven in de kolom **Landinstelling**.

## <a name="add-translation-language"></a>Vertaal taal toevoegen

Een andere veelvoorkomende taak van spraak omzetting is het opgeven van talen voor doel omzetting, ten minste één is vereist, maar meerdere worden ondersteund. In het volgende code fragment worden zowel Frans als Duits ingesteld als Vertaal taal doelen.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Bij elke aanroep van [`addTargetLanguage`][addlang] wordt een nieuwe doel taal voor vertalen opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de bron taal, is elke doel omzetting beschikbaar als onderdeel van de resulterende Vertaal bewerking.

## <a name="initialize-a-translation-recognizer"></a>Een omzettings herkenning initialiseren

Nadat u een [`SpeechTranslationConfig`][config] hebt gemaakt, is de volgende stap het initialiseren van een [`TranslationRecognizer`][recognizer]. Wanneer u een [`TranslationRecognizer`][recognizer] initialiseert, moet u er uw `translationConfig` aan doorgeven. Het configuratie object bevat de referenties die de speech-service nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaardmicrofoon van uw apparaat, ziet u hier hoe de [`TranslationRecognizer`][recognizer] eruit moet zien:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Als u het audio-invoerapparaat wilt opgeven, moet u een [`AudioConfig`][audioconfig] maken en de parameter `audioConfig` opgeven bij het initialiseren van uw [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u `AudioConfig` als volgt naar het object:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Als u een audiobestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig` opgeven. Wanneer u echter een [`AudioConfig`][audioconfig] maakt in plaats van `fromDefaultMicrophoneInput` aan te roepen, roept u `fromWavFileInput` aan en geeft u de parameter `filename` door.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Spraak vertalen

Om spraak te kunnen vertalen, is de spraak-SDK afhankelijk van een microfoon of een audio bestand invoer. Spraak herkenning vindt plaats voordat spraak wordt vertaald. Wanneer alle objecten zijn geïnitialiseerd, roept u de functie recognize Once aan en haalt u het resultaat op.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Zie [basis beginselen van spraak herkenning](../../../get-started-speech-to-text.md)voor meer informatie over spraak naar tekst.

## <a name="synthesize-translations"></a>Vertalingen voor synthesizer

Na een geslaagde spraak herkenning en-omzetting bevat het resultaat alle vertalingen in een woorden lijst. De [`getTranslations`][translations] functie retourneert een woorden lijst met de sleutel als de doel taal voor vertalen en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens in een andere taal (spraak naar spraak) worden gesynthesizerd.

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object toont een `synthesizing` gebeurtenis. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme voor het ophalen van de gesynthesizerde audio uit het resultaat van de vertalings herkenning. Zie [hand matige synthese](#manual-synthesis)als u naar meerdere talen wilt vertalen. Geef de synthese stem op door een [`setVoiceName`][voicename] gebeurtenis-handler toe te wijzen en de `synthesizing` audio op te halen. In het volgende voor beeld wordt de vertaalde audio opgeslagen als *WAV* -bestand.

> [!IMPORTANT]
> De op gebeurtenissen gebaseerde synthese werkt alleen met één vertaling, Voeg **geen** meerdere talen voor doel omzetting toe. Daarnaast moet het [`setVoiceName`][voicename] dezelfde taal zijn als die van de doel taal van de vertaling, bijvoorbeeld; `"de"` kan worden toegewezen aan `"de-DE-Hedda"` .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Hand matige synthese

De [`getTranslations`][translations] functie retourneert een woorden lijst die kan worden gebruikt voor het bekunsten van audio uit de tekst van de vertaling. Herhaal elke vertaling en synthesizer de vertaling. Bij het maken van een `SpeechSynthesizer` exemplaar `SpeechConfig` moet de eigenschap van het object [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] op de gewenste stem zijn ingesteld. In het volgende voor beeld worden de vijf talen omgezet en elke vertaling wordt vervolgens op een audio bestand in de bijbehorende Neural-taal gesynthesizerd.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Zie [basis beginselen van spraak synthese](../../../get-started-text-to-speech.md)voor meer informatie over spraak synthese.

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename