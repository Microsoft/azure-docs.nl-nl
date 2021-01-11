---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: c62fd0d946d53244809cca3b77496ffa6f3379d2
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97731879"
---
In deze quickstart maakt u kennis met algemene ontwerppatronen voor het uitvoeren van een spraak-naar-tekstsynthese met behulp van de Speech-SDK. Eerst voert u een basisconfiguratie en -synthese uit en gaat u verder met geavanceerdere voorbeelden voor aangepaste toepassingsontwikkeling zoals:

* Antwoorden krijgen als stromen in het geheugen
* De uitgevoerde frequentie- en bitsnelheid aanpassen
* Synthese-aanvragen indienen met behulp van SSML (Speech Synthesis Markup Language)
* Neurale stemmen gebruiken

## <a name="skip-to-samples-on-github"></a>Naar voorbeelden op GitHub

Zie de [C#-quickstartvoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/text-to-speech) op GitHub als u direct naar voorbeeldcode wilt gaan.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit artikel wilt uitvoeren, voegt u boven aan het script de volgende `using`-instructies toe.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) kunt initialiseren:

* Met een abonnement: geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

In dit voorbeeld maakt u een [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) met behulp van een abonnementssleutel en regio. U kunt deze referenties ophalen door de stappen te volgen in [De Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free). U schrijft ook wat eenvoudige standaardcode voor gebruik in de rest van dit artikel. U gaat de code voor verschillende aanpassingen wijzigen.

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Vervolgens maakt u een [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-dotnet)-object, waarmee tekst-naar-spraakconversies en uitvoer naar luidsprekers, bestanden of andere uitvoerstromen worden uitgevoerd. De [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-dotnet) accepteert als parameters het [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet)-object dat in de vorige stap is gemaakt en tevens een [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?preserve-view=true&view=azure-dotnet)-object dat aangeeft hoe uitvoerresultaten moeten worden verwerkt.

Eerst maakt u een `AudioConfig` om de uitvoer automatisch naar een `.wav` bestand te schrijven, met behulp van de functie `FromWavFileOutput()` en voert u een exemplaar uit met een `using`-instructie. Met een `using`-instructie in deze context worden niet-beheerde resources automatisch verwijderd en wordt het object na verwijdering buiten gebruik gesteld.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Vervolgens maakt u een `SpeechSynthesizer` met een andere `using`-instructie. Geef uw `config`-object en het `audioConfig`-object als parameters door. Daarna is het uitvoeren van spraaksynthese en het schrijven naar een bestand net zo eenvoudig als het uitvoeren van `SpeakTextAsync()` met een regel tekst.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Voer het programma uit. Er wordt een gesynthetiseerd `.wav`-bestand geschreven naar de locatie die u hebt opgegeven. Dit is een goed voorbeeld van het eenvoudigste gebruik, maar hierna gaat u de uitvoer aanpassen en de uitvoerrespons verwerken als een in-memory stroom om met aangepaste scenario's te kunnen werken.

## <a name="synthesize-to-speaker-output"></a>Synthetiseren naar de uitvoer van de luidspreker

In sommige gevallen kunt u gesynthetiseerde spraak rechtstreeks naar een luidspreker uitvoeren. U kunt dit doen door de parameter `AudioConfig` weg te laten bij het maken van de `SpeechSynthesizer` in bovenstaand voorbeeld. Hiermee wordt de uitvoer naar het huidige, actieve uitvoerapparaat gestuurd.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat ophalen als een in-memory stroom

Voor veel scenario's in het ontwikkelen van spraaktoepassingen hebt u waarschijnlijk de resulterende audiogegevens als een in-memory stroom nodig, in plaats dat u rechtstreeks naar een bestand gaat schrijven. Zo kunt u aangepast gedrag maken, waaronder:

* Vat de resulterende bytematrix samen als een doorzoekbare stroom voor aangepaste downstreamservices.
* Integreer het resultaat met andere API's of services.
* Wijzig de audiogegevens, schrijf aangepaste `.wav`-headers, enzovoort.

U kunt deze wijziging eenvoudig met behulp van het voorgaande voorbeeld uitvoeren. Verwijder eerst het `AudioConfig`-blok, omdat u het uitvoergedrag vanaf dit punt handmatig gaat beheren voor een betere controle. Geef vervolgens `null` door voor de `AudioConfig` in de `SpeechSynthesizer`-constructor. 

> [!NOTE]
> Als `null` voor de `AudioConfig` wordt doorgegeven in plaats van dat deze wordt weggelaten, zoals in het bovenstaande voorbeeld met de luidspreker, wordt de audio niet standaard afgespeeld op het huidige actieve uitvoerapparaat.

Deze keer slaat u het resultaat op in een [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?preserve-view=true&view=azure-dotnet)-variabele. De eigenschap `AudioData` bevat een `byte []` van de uitvoergegevens. U kunt handmatig met deze `byte []` werken, of u kunt de [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-dotnet)-klasse gebruiken om de stroom in het geheugen te beheren. In dit voorbeeld gebruikt u de statische functie `AudioDataStream.FromResult()` om een stroom van het resultaat op te halen.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Hierna kunt u aangepast gedrag implementeren met behulp van het resulterende `stream`-object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u kenmerken van audio-uitvoer kunt aanpassen, zoals:

* Audiobestandstype
* Samplefrequentie
* Bitdiepte

Als u de audio-indeling wilt wijzigen, gebruikt u de functie `SetSpeechSynthesisOutputFormat()` op het `SpeechConfig`-object. Deze functie verwacht een `enum` van het type [`SpeechSynthesisOutputFormat`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-dotnet), die u gebruikt om de uitvoerindeling te selecteren. Zie de naslagdocumenten voor een [lijst met audio-indelingen](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-dotnet) die beschikbaar zijn.

Voor verschillende bestandstypen zijn er diverse opties mogelijk, afhankelijk van uw vereisten. Houd er rekening mee dat raw-indelingen als `Raw24Khz16BitMonoPcm` geen audio-headers bevatten. Gebruik raw-indelingen alleen als u weet dat uw stroomafwaartse implementatie een onbewerkte bitstream kan decoderen of als u van plan bent om headers handmatig te bouwen op basis van bitdiepte, samplefrequentie, aantal kanalen, enzovoort.

> [!NOTE]
> Stemmen voor **en-US-AriaRUS** en **en-US-GuyRUS** worden gemaakt uit voorbeelden die zijn gecodeerd in de samplefrequentie `Riff24Khz16BitMonoPcm`.

In dit voorbeeld geeft u een RIFF-indeling met hoge kwaliteit `Riff24Khz16BitMonoPcm` op door `SpeechSynthesisOutputFormat` in te stellen voor het `SpeechConfig`-object. Net als in het voorbeeld in de vorige sectie, gebruikt u [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?preserve-view=true&view=azure-dotnet) om een stroom in het geheugen van het resultaat te verkrijgen en vervolgens naar een bestand te schrijven.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Als u het programma opnieuw uitvoert, wordt een `.wav`-bestand naar het opgegeven pad geschreven.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML gebruiken om spraakkenmerken aan te passen

Met SSML (Speech Synthesis Markup Language) kunt u de toonhoogte, de uitspraak, de spreeksnelheid, het volume en meer van tekst-naar-spraakuitvoer nauwkeuriger instellen door uw aanvragen vanaf een XML-schema te verzenden. In deze sectie vindt u enkele praktische gebruik voorbeelden, maar voor een meer gedetailleerde handleiding raadpleegt u het [Instructieartikel over SSML](../../../speech-synthesis-markup.md).

Als u SSML voor aanpassing wilt gaan gebruiken, brengt u een eenvoudige wijziging aan die de stem wisselt.
Maak eerst een nieuw XML-bestand voor de SSML-configuratie in de hoofdmap van het project, in dit voorbeeld `ssml.xml`. Het hoofdelement is altijd `<speak>`, en wanneer u de tekst in een `<voice>`-element verpakt, kunt u de stem wijzigen met behulp van de parameter `name`. In dit voorbeeld wordt de stem gewijzigd in een Engelse (UK), mannelijke stem. Merk op dat deze stem een **standaardstem** is, waarvoor andere prijzen en beschikbaarheid gelden dan **neurale** stemmen. Zie de [volledige lijst](../../../language-support.md#standard-voices) met ondersteunde **standaardstemmen**.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Vervolgens moet u de aanvraag voor spraaksynthese wijzigen om te verwijzen naar uw XML-bestand. De aanvraag is doorgaans hetzelfde, maar in plaats van de functie `SpeakTextAsync()` gebruikt u `SpeakSsmlAsync()`. Deze functie verwacht een XML-tekenreeks, dus u moet uw SSML-configuratie eerst laden als een tekenreeks met behulp van `File.ReadAllText()`. Hier is het resultaatobject precies hetzelfde als in de vorige voorbeelden.

> [!NOTE]
> Als u Visual Studio gebruikt, wordt uw XML-bestand waarschijnlijk niet standaard door uw build-configuratie gevonden. Als u dit wilt oplossen, klikt u met de rechter muisknop op het XML-bestand en selecteert u **eigenschappen**. Wijzig **Build-actie** naar *Inhoud* en wijzig **Kopiëren naar uitvoermap** naar *Altijd kopiëren*.

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

De uitvoer werkt, maar er zijn enkele eenvoudige wijzigingen die u kunt aanbrengen om het natuurlijker te laten klinken. De gemiddelde spreeksnelheid is iets te snel, dus we voegen een `<prosody>`-tag toe en verminderen de snelheid tot **90%** van de standaardsnelheid. Bovendien is de pauze na de komma in de zin een beetje te kort en klinkt onnatuurlijk. U kunt dit probleem oplossen door een `<break>`-tag toe te voegen om de spraaksnelheid te vertragen en de tijdparameter in te stellen op **200 ms**. Voer de synthese opnieuw uit om te zien hoe deze aanpassingen van invloed zijn op de uitvoer.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neurale stemmen

Neurale stemmen zijn spraaksynthesealgoritmen die worden aangestuurd door Deep Neural Networks. Als u een neurale stem gebruikt, is de gesynthetiseerde spraak bijna niet van de menselijke opnamen te onderscheiden. Dankzij de menselijke, natuurlijke prosodie en duidelijke articulatie verminderen neurale stemmen de bij het luisteren optredende vermoeidheid wanneer gebruikers met AI-systemen communiceren.

Als u wilt overschakelen naar een neurale stem, wijzigt u de `name` in een van de [opties voor neurale stemmen](../../../language-support.md#neural-voices). Voeg vervolgens een XML-naamruimte voor `mstts` toe en verpak uw tekst in de `<mstts:express-as>`-tag. Gebruik parameter `style` om de spreekstijl aan te passen. In dit voorbeeld wordt `cheerful` gebruikt, maar u kunt deze aanpassen naar `customerservice` of `chat` om het verschil in spreekstijl te horen.

> [!IMPORTANT]
> Neurale stemmen worden **alleen** ondersteund voor spraakbronnen die zijn gemaakt in de regio's *US - oost*, *Azië - zuidoost* en *Europa - west*.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```