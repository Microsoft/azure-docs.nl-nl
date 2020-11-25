---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 2b5a34e8f3e7132a16ad3683b846d57e9ece2cb6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015466"
---
In deze quickstart leert u basispatronen voor het ontwerp van Speaker Recognition met behulp van de Speech SDK, met inbegrip van:

* Tekstafhankelijke en tekstonafhankelijke verificatie
* Sprekeridentificatie voor het identificeren van een spraaksample in een groep stemmen
* Spraakprofielen verwijderen

Zie het artikel [Overzicht](../../../speaker-recognition-overview.md) voor een diepgaande bespreking van de concepten van spraakherkenning.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition wordt momenteel *alleen* ondersteund in Azure Speech-resources die in de regio `westus` zijn gemaakt.

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
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) maken. In dit voorbeeld maakt u een [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet) met behulp van een abonnementssleutel en regio. U schrijft ook wat eenvoudige standaardcode voor gebruik in de rest van dit artikel. U gaat de code voor verschillende aanpassingen wijzigen.

De regio is ingesteld op `westus`, omdat dit de enige ondersteunde regio voor de service is.

```csharp
public class Program 
{
    static async Task Main(string[] args)
    {
        // replace with your own subscription key 
        string subscriptionKey = "YourSubscriptionKey";
        string region = "westus";
        var config = SpeechConfig.FromSubscription(subscriptionKey, region);
    }
}
```

## <a name="text-dependent-verification"></a>Tekstafhankelijke verificatie

Met Speaker Verification wordt bevestigd dat een spreker overeenkomt met een bekende of **geregistreerde** stem. De eerste stap bestaat uit het **registreren** van een spraakprofiel, zodat dit met latere spraaksamples kan worden vergeleken. In dit voorbeeld moet u het profiel registreren door middel van een **tekstafhankelijke** strategie, waarvoor een specifieke wachtwoordzin is vereist voor zowel de registratie als de verificatie. Zie het [referentiemateriaal ](/rest/api/speakerrecognition/) voor een lijst met ondersteunde wachtwoordenzinnen.

Begin met het maken van de volgende functie in uw `Program`-klasse om een spraakprofiel te registreren.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextDependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsCount > 0)
            {
                Console.WriteLine("Speak the passphrase, \"My voice is my passport, verify me.\"");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollments needed: {result.RemainingEnrollmentsCount}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

In deze functie wordt met `await client.CreateProfileAsync()` feitelijk het nieuwe spraakprofiel gemaakt. Nadat dit is gemaakt, geeft u op hoe u audiosamples wilt invoeren. Dat doet u in dit voorbeeld met `AudioConfig.FromDefaultMicrophoneInput()` om audio op te nemen van uw standaardinvoerapparaat. Vervolgens registreert u audiosamples in een `while`-lus, waarmee het aantal voor de registratie resterende en vereiste samples wordt gevolgd. In elke iteratie vraagt `client.EnrollProfileAsync(profile, audioInput)` u om de wachtwoordzin in uw microfoon in te spreken en voegt u de sample aan het spraakprofiel toe.

Nadat de sample is geregistreerd, roept u `await SpeakerVerify(config, profile, profileMapping)` aan als verificatie tegen het profiel dat u zojuist hebt gemaakt. Voeg een andere functie toe om `SpeakerVerify` te definiëren.

```csharp
public static async Task SpeakerVerify(SpeechConfig config, VoiceProfile profile, Dictionary<string, string> profileMapping)
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerVerificationModel.FromProfile(profile);

    Console.WriteLine("Speak the passphrase to verify: \"My voice is my passport, please verify me.\"");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"Verified voice profile for speaker {profileMapping[result.ProfileId]}, score is {result.Score}");
}
```

Met deze functie geeft u het `VoiceProfile`-object door dat u zojuist hebt gemaakt om een model te initialiseren waartegen de verificatie moet worden uitgevoerd. `await speakerRecognizer.RecognizeOnceAsync(model)` vraagt u vervolgens de wachtwoordzin opnieuw uit te spreken, maar deze keer wordt de zin gevalideerd tegen uw spraakprofiel en wordt er een vergelijkingsscore geretourneerd. Deze loopt van 0,0 tot 1.0. Het `result`-object retourneert ook `Accept` of `Reject`, op basis van het feit of de wachtwoordzin al dan niet overeenkomt.

Wijzig vervolgens de `Main()`-functie om de nieuwe functies aan te roepen die u hebt gemaakt. Houd er tevens rekening mee dat u een `Dictionary<string, string>` maakt, die als verwijzing via de functieaanroepen moet worden doorgegeven. De reden hiervoor is dat de service geen door de mens leesbare naam met een gemaakt `VoiceProfile` mag opslaan en vanwege privacydoeleinden alleen een id-nummer opslaat. In de `VerificationEnroll`-functie voegt u aan deze woordenlijst een item met de zojuist gemaakte id toe, samen met een tekstnaam. In scenario's voor het ontwikkelen van toepassingen waarbij u een leesbare naam moet weergeven, **moet u deze toewijzing ergens opslaan. Dat kan niet met de service**.

```csharp
static async Task Main(string[] args)
{
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    await VerificationEnroll(config, profileMapping);

    Console.ReadLine();
}
```

Voer het script uit en u wordt gevraagd om de volgende zin driemaal voor de registratie uit te spreken en nogmaals ter verificatie: *Mijn stem is mijn paspoort, verifieer mij*. Het resultaat dat wordt geretourneerd, is de vergelijkingsscore. Deze kunt u kunt gebruiken om uw eigen drempelwaarden voor de verificatie te maken.

```shell
Enrolling profile id 87-2cef-4dff-995b-dcefb64e203f.
Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 2

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 1

Speak the passphrase, "My voice is my passport, verify me."
Remaining enrollments needed: 0

Speak the passphrase to verify: "My voice is my passport, verify me."
Verified voice profile for speaker Your Name, score is 0.915581
```

## <a name="text-independent-verification"></a>Tekstonafhankelijke verificatie

In tegenstelling tot **tekstafhankelijke** verificatie, geldt voor **tekstonafhankelijke** verificatie:

* Vereist niet dat een bepaalde wachtwoordzin wordt uitgesproken; alles mag worden uitgesproken
* Vereist geen drie audiosamples, maar vereist in totaal *wel* twintig seconden aan audio

Breng enkele eenvoudige wijzigingen aan uw `VerificationEnroll`-functie aan om over te schakelen op **tekstonafhankelijke** verificatie. Eerst wijzigt u het verificatietype in `VoiceProfileType.TextIndependentVerification`. Wijzig vervolgens de `while`-lus om `result.RemainingEnrollmentsSpeechLength` te volgen, waarna u net zolang wordt gevraagd te spreken totdat er twintig seconden aan audio is vastgelegd.

```csharp
public static async Task VerificationEnroll(SpeechConfig config, Dictionary<string, string> profileMapping)
{
    using (var client = new VoiceProfileClient(config))
    using (var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentVerification, "en-us"))
    {
        using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
        {
            Console.WriteLine($"Enrolling profile id {profile.Id}.");
            // give the profile a human-readable display name
            profileMapping.Add(profile.Id, "Your Name");

            VoiceProfileEnrollmentResult result = null;
            while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
            {
                Console.WriteLine("Continue speaking to add to the profile enrollment sample.");
                result = await client.EnrollProfileAsync(profile, audioInput);
                Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                Console.WriteLine("");
            }
            
            if (result.Reason == ResultReason.EnrolledVoiceProfile)
            {
                await SpeakerVerify(config, profile, profileMapping);
            }
            else if (result.Reason == ResultReason.Canceled)
            {
                var cancellation = VoiceProfileEnrollmentCancellationDetails.FromResult(result);
                Console.WriteLine($"CANCELED {profile.Id}: ErrorCode={cancellation.ErrorCode} ErrorDetails={cancellation.ErrorDetails}");
            }
        }
    }
}
```

Voer het programma opnieuw uit en spreek iets uit tijdens de verificatiefase omdat er geen wachtwoordzin is vereist. De vergelijkingsscore wordt ook hier geretourneerd.

```shell
Enrolling profile id 4tt87d4-f2d3-44ae-b5b4-f1a8d4036ee9.
Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:15.3200000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:09.8100008

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:05.1900000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00.8700000

Continue speaking to add to the profile enrollment sample.
Remaining enrollment audio time needed: 00:00:00

Speak the passphrase to verify: "My voice is my passport, please verify me."
Verified voice profile for speaker Your Name, score is 0.849409
```

## <a name="speaker-identification"></a>Sprekeridentificatie

Speaker Identification wordt gebruikt om vast te stellen **wie** er spreekt uit een bepaalde groep geregistreerde stemmen. Het proces is vergelijkbaar met **tekstonafhankelijke verificatie**, waarbij het belangrijkste verschil is dat tegelijkertijd verificatie tegen meerdere spraakprofielen kan worden uitgevoerd in plaats van te verifiëren tegen één profiel.

Maak een `IdentificationEnroll`-functie om meerdere spraakprofielen te registreren. Het registratieproces voor elk profiel is hetzelfde als het registratieproces voor **tekstonafhankelijke verificatie** en vereist twintig seconden aan audio voor elk profiel. Deze functie accepteert een lijst tekenreeksen, `profileNames`, en maakt een nieuw spraakprofiel voor elke naam in de lijst. De functie retourneert een lijst `VoiceProfile`-objecten, die u in de volgende functie gebruikt om een spreker te identificeren.

```csharp
public static async Task<List<VoiceProfile>> IdentificationEnroll(SpeechConfig config, List<string> profileNames, Dictionary<string, string> profileMapping)
{
    List<VoiceProfile> voiceProfiles = new List<VoiceProfile>();
    using (var client = new VoiceProfileClient(config))
    {
        foreach (string name in profileNames)
        {
            using (var audioInput = AudioConfig.FromDefaultMicrophoneInput())
            {
                var profile = await client.CreateProfileAsync(VoiceProfileType.TextIndependentIdentification, "en-us");
                Console.WriteLine($"Creating voice profile for {name}.");
                profileMapping.Add(profile.Id, name);

                VoiceProfileEnrollmentResult result = null;
                while (result is null || result.RemainingEnrollmentsSpeechLength > TimeSpan.Zero)
                {
                    Console.WriteLine($"Continue speaking to add to the profile enrollment sample for {name}.");
                    result = await client.EnrollProfileAsync(profile, audioInput);
                    Console.WriteLine($"Remaining enrollment audio time needed: {result.RemainingEnrollmentsSpeechLength}");
                    Console.WriteLine("");
                }
                voiceProfiles.Add(profile);
            }
        }
    }
    return voiceProfiles;
}
```

Maak de volgende `SpeakerIdentification`-functie om een identificatieverzoek in te dienen. Het belangrijkste verschil in deze functie vergeleken met een verzoek tot **sprekercontrole** is het gebruik van `SpeakerIdentificationModel.FromProfiles()`, waarmee een lijst met `VoiceProfile`-objecten wordt geaccepteerd. 

```csharp
public static async Task SpeakerIdentification(SpeechConfig config, List<VoiceProfile> voiceProfiles, Dictionary<string, string> profileMapping) 
{
    var speakerRecognizer = new SpeakerRecognizer(config, AudioConfig.FromDefaultMicrophoneInput());
    var model = SpeakerIdentificationModel.FromProfiles(voiceProfiles);

    Console.WriteLine("Speak some text to identify who it is from your list of enrolled speakers.");
    var result = await speakerRecognizer.RecognizeOnceAsync(model);
    Console.WriteLine($"The most similar voice profile is {profileMapping[result.ProfileId]} with similarity score {result.Score}");
}
```

Wijzig de `Main()`-functie in de volgende. U maakt een lijst met tekenreeksen, `profileNames`, die u doorgeeft aan de `IdentificationEnroll()`-functie. Hiermee wordt u gevraagd om voor elke naam in deze lijst een nieuw spraakprofiel te maken, zodat u meer namen kunt toevoegen om extra profielen voor vrienden of collega's te maken.

```csharp
static async Task Main(string[] args)
{
    // replace with your own subscription key 
    string subscriptionKey = "YourSubscriptionKey";
    string region = "westus";
    var config = SpeechConfig.FromSubscription(subscriptionKey, region);

    // persist profileMapping if you want to store a record of who the profile is
    var profileMapping = new Dictionary<string, string>();
    var profileNames = new List<string>() { "Your name", "A friend's name" };
    
    var enrolledProfiles = await IdentificationEnroll(config, profileNames, profileMapping);
    await SpeakerIdentification(config, enrolledProfiles, profileMapping);

    foreach (var profile in enrolledProfiles)
    {
        profile.Dispose();
    }
    Console.ReadLine();
}
```

Voer het script uit en u wordt gevraagd om te spreken om spraakvoorbeelden voor het eerste profiel te registreren. Nadat de registratie is voltooid, wordt u gevraagd dit proces te herhalen voor elke naam in lijst `profileNames`. Nadat alle registraties zijn voltooid, wordt u gevraagd **iemand** te laten spreken. De service probeert deze persoon te identificeren uit de geregistreerde spraakprofielen.

In dit voorbeeld worden alleen de vergelijkingsscore en het resultaat dat het meest overeenkomt geretourneerd. U kunt het volledige antwoord krijgen met de vijf hoogste vergelijkingsscores door `string json = result.Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)` aan de `SpeakerIdentification`-functie toe te voegen.

## <a name="changing-audio-input-type"></a>Type audio-invoer wijzigen

In de voorbeelden in dit artikel wordt de standaardmicrofoon van het apparaat gebruikt als invoer voor audiosamples. In scenario's waarin u echter audiobestanden moet gebruiken in plaats van de microfooninvoer, wijzigt u een willekeurige instantie van `AudioConfig.FromDefaultMicrophoneInput()` in `AudioConfig.FromWavFileInput(path/to/your/file.wav)` om over te schakelen naar een bestandsinvoer. Gemengde invoer is ook mogelijk, met behulp van bijvoorbeeld een microfoon voor de inschrijving en bestanden voor de verificatie.

## <a name="deleting-voice-profile-enrollments"></a>Registraties van spraakprofielen verwijderen

Als u een geregistreerd profiel wilt verwijderen, gebruikt u de `DeleteProfileAsync()`-functie voor het `VoiceProfileClient`-object. In de volgende voorbeeldfunctie ziet u hoe u een spraakprofiel van een bekende spraakprofiel-id verwijdert.

```csharp
public static async Task DeleteProfile(SpeechConfig config, string profileId) 
{
    using (var client = new VoiceProfileClient(config))
    {
        var profile = new VoiceProfile(profileId);
        await client.DeleteProfileAsync(profile);
    }
}
```