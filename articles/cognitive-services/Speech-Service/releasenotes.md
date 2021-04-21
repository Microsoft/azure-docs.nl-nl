---
title: Opmerkingen bij de release - Speech Service
titleSuffix: Azure Cognitive Services
description: Een logboek met releases van Speech Service-functies, verbeteringen, oplossingen voor fouten en bekende problemen.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: f97ecedd4088a825b9ec5a076f4da70df92a3269
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775399"
---
# <a name="speech-service-release-notes"></a>Opmerkingen bij de release van Speech Service

## <a name="text-to-speech-2021-april-release"></a>Release van tekst-naar-spraak 2021-april

**Neurale TTS is beschikbaar in 21 regio's**

- **Twaalf nieuwe regio's** toegevoegd - Neurale TTS is nu beschikbaar in deze nieuwe 12 regio's: `Japan East` , , , , , , , , `Japan West` , , `Korea Central` , , `North Central US` `North Europe` `South Central US` `Southeast Asia` `UK South` `west Central US` `West Europe` `West US` . `West US 2` Bekijk [hier](regions.md#text-to-speech) de volledige lijst met 21 ondersteunde regio's.

## <a name="text-to-speech-2021-march-release"></a>Release van tekst-naar-spraak 2021-maart

**Nieuwe talen en stemmen toegevoegd voor neurale TTS**

-  Er zijn zes nieuwe talen geïntroduceerd- 12 nieuwe stemmen in 6 nieuwe landinstellingen worden toegevoegd aan de neurale TTS-taallijst: Nia in `cy-GB` Het Verenigd Koninkrijk, Aled in `cy-GB` Transactie (Verenigd Koninkrijk), Keer in Engels `en-PH` (Keers), James in Engels (Engelstalig), Charline in het Frans (Frankrijk), Moet in het Frans (Zij), Dena in het Nederlands (Zij), Arnaud in het Nederlands `en-PH` `fr-BE` `fr-BE` `nl-BE` `nl-BE` (Lands), Polina in `uk-UA` HetEns (Rusland), Ostap in Het Frans `uk-UA` (Örisch), Uzma in `ur-PK` Urdu (Nudu), Asad in `ur-PK` Urdu (Fraud).

- Vijf talen van preview tot **GA** : 10 stemmen in 5 landtalen die zijn geïntroduceerd in 2020-november zijn nu GA: Kert in het Estisch `et-EE` (Engelstalig), Colm in `ga-IE` Ierland (Ierland), Nils in Lets (Lets) Enaas in HetEns `lv-LV` `lt-LT` (Veens), Joseph in `mt-MT` Nowen (Laten) .

- **Nieuwe mannenstem toegevoegd voor Frans (Canada)** - Er is een nieuwe stem beschikbaar voor Frans `fr-CA` (Canada).

- **Kwaliteitsverbetering:** vermindering van het aantal uitspraakfouten op `hu-HU` Hongaars - 48,17%, Noors `nb-NO` - 52,76%, `nl-NL` Nederlands (Nederland) - 22,11%.

Met deze release ondersteunen we nu in totaal 142 neurale stemmen in 60 talen/landtalen. Daarnaast zijn er meer dan 70 standaardstemmen beschikbaar in 49 talen/talen. Ga [naar Taalondersteuning](language-support.md#text-to-speech) voor de volledige lijst.

**Get facial pose events to animate characters (Gebeurtenissen van gezichtshoudingen tot animatie van tekens)**

De [Viseme-gebeurtenis](how-to-speech-synthesis-viseme.md) wordt toegevoegd aan Neural TTS, waarmee gebruikers de gezichtshoudingsreeks en de duur van gesynthetiseerde spraak kunnen krijgen. Viseme kan worden gebruikt om de verplaatsing van 2D- en 3D-avatarmodellen te bepalen, perfect overeenkomende mondbewegingen met gesynthetiseerde spraak. Viseme werkt nu alleen voor de stem en-US-AriaNeural.

**Het bladwijzerelement toevoegen in Speech Synthesis Markup Language (SSML)**

Met [het element bladwijzer](speech-synthesis-markup.md#bookmark-element) kunt u aangepaste markeringen invoegen in SSML om de offset van elke markering in de audiostroom op te halen. Deze kan worden gebruikt om te verwijzen naar een specifieke locatie in de tekst- of tagreeks.

## <a name="speech-sdk-1160-2021-march-release"></a>Speech SDK 1.16.0: release van 2021-maart

> [!NOTE]
> De Speech SDK in Windows is afhankelijk van het gedeelde Microsoft Visual C++ Redistributable voor Visual Studio 2015, 2017 en 2019. Download deze [hier.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

#### <a name="new-features"></a>Nieuwe functies

- **C++/C#/Java/Python:** is verplaatst naar de nieuwste versie van GStreamer (1.18.3) om ondersteuning toe te voegen voor het transcriberen van media-indelingen in Windows, Linux en Android. Zie de documentatie [hier.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)
- **C++/C#/Java/Objective-C/Python:** Ondersteuning toegevoegd voor het decoderen van gecomprimeerde TTS/gesynthetiseerde audio naar de SDK. Als u de audio-uitvoerindeling in uw systeem in stelt op PCM en GStreamer beschikbaar is, vraagt de SDK automatisch gecomprimeerde audio aan bij de service om bandbreedte te besparen en de audio op de client te decoderen. U kunt instellen `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` op om deze functie uit te `false` schakelen. Details voor [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable), [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid) [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python).
- **JavaScript:** Node.js kunnen nu de [ `AudioConfig.fromWavFileInput` API gebruiken.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_) Hiermee wordt het [GitHub-probleem #252.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252)
- **C++/C#/Java/Objective-C/Python:** Methode toegevoegd voor TTS om alle `GetVoicesAsync()` beschikbare synthesestemmen te retourneren. Details voor [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods), [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)en [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods).
- **C++/C#/Java/JavaScript/Objective-C/Python:** Gebeurtenis toegevoegd voor `VisemeReceived` TTS/spraaksynthese om synchrone viseme-animatie te retourneren. Zie de documentatie [hier.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme)
- **C++/C#/Java/JavaScript/Objective-C/Python:** `BookmarkReached` Gebeurtenis toegevoegd voor TTS. U kunt bladwijzers instellen in de SSML-invoer en de audio-offsets voor elke bladwijzer op halen. Zie de documentatie [hier.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element)
- **Java:** Er is ondersteuning toegevoegd voor SPEAKER Recognition-API's. Hier [kunt u meer informatie vinden.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/JavaScript/Objective-C/Python:** Er zijn twee nieuwe audio-indelingen voor uitvoer toegevoegd met WebM-container voor TTS (Webm16Khz16BitMonoOpus en Webm24Khz16BitMonoOpus). Dit zijn betere indelingen voor het streamen van audio met de Opus-codec. Details voor [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable), [JavaScript,](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat) [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python).
- **C++/C#/Java:** Er is ondersteuning toegevoegd voor het ophalen van spraakprofiel voor het scenario voor sprekerherkenning. Details voor [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)en [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable).
- **C++/C#/Java/Objective-C/Python:** Er is ondersteuning toegevoegd voor afzonderlijke gedeelde bibliotheek voor audiomicrofoon en sprekerbesturing. Hierdoor kan de SDK worden gebruikt in omgevingen die geen vereiste audiobibliotheekafhankelijkheden hebben.
- **Objective-C/Swift:** Er is ondersteuning toegevoegd voor het module-framework met een koptekst met een overkoepelende titel. Hiermee kunt u de Speech-SDK als module importeren in iOS/Mac Objective-C/Swift-apps. Hiermee wordt het [GitHub-probleem #452.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452)
- **Python:** ondersteuning toegevoegd voor [Python 3.9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) en ondersteuning voor Python 3.5 per [Python's end-of-life voor 3.5 is gestopt.](https://devguide.python.org/devcycle/#end-of-life-branches)

**Bekende problemen**

- **C++/C#/Java:** kan geen toegang krijgen tot een aangepaste opdrachten toepassing en er wordt in plaats `DialogServiceConnector` `CustomCommandsConfig` daarvan een verbindingsfout weergegeven. Dit kan worden omwerkt door handmatig uw toepassings-id toe te voegen aan de aanvraag met `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)` . Het verwachte gedrag van `CustomCommandsConfig` wordt in de volgende release hersteld.

#### <a name="improvements"></a>Verbeteringen

- Als onderdeel van onze inspanningen voor meerdere versies om het geheugengebruik en de schijfvoetafdruk van de Speech SDK te verminderen, zijn de binaire Android-bestanden nu 3% tot 5% kleiner.
- Verbeterde nauwkeurigheid, leesbaarheid en ook secties van onze C#-referentiedocumentatie [hier](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet).

#### <a name="bug-fixes"></a>Opgeloste fouten

- **JavaScript:** grote WAV-bestandsheaders worden nu correct geparseerd (verhoogt het headersegment naar 512 bytes). Hiermee wordt het [GitHub-probleem #962.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962)
- **JavaScript:** Probleem met timing van microfoon gecorrigeerd als de microfoonstream eindigt voordat de herkenning wordt gestopt, en er een probleem wordt opgelost met spraakherkenning dat niet werkt in Firefox.
- **JavaScript:** we verwerken de initialisatie-promise nu correct wanneer de browser microfoon afdeert voordat inschakelen is voltooid.
- **JavaScript:** We hebben de URL-afhankelijkheid vervangen door url-parse. Hiermee wordt het [GitHub-probleem #264.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264)
- **Android:** vaste callbacks werken niet wanneer `minifyEnabled` is ingesteld op true.
- **C++/C#/Java/Objective-C/Python:** wordt correct ingesteld op onderliggende `TCP_NODELAY` socket-IO voor TTS om de latentie te verminderen.
- **C++/C#/Java/Python/Objective-C/Go:** er is een incidentele crash opgelost toen de herkenning werd vernietigd net na het starten van een herkenning.
- **C++/C#/Java:** Er is een incidentele crash opgelost in de vernietigende speaker recognizer.

#### <a name="samples"></a>Voorbeelden

- **JavaScript:** [voor browservoorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) is het downloaden van afzonderlijke JavaScript-bibliotheekbestand niet langer vereist.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Speech CLI (ook wel bekend als SPX): release van 2021-maart

> [!NOTE]
> Ga hier aan de slag met de opdrachtregelinterface (CLI) van de Azure [Speech-service.](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics) Met de CLI kunt u de Azure Speech-service gebruiken zonder code te schrijven.

#### <a name="new-features"></a>Nieuwe functies

- Er `spx intent` is een opdracht toegevoegd voor intentieherkenning, die `spx recognize intent` vervangt.
- Recognize en intentie kunnen nu Azure-functies gebruiken om het foutpercentage van woorden te berekenen met behulp van `spx recognize --wer url <URL>` .
- Recognize kan nu resultaten als VTT-bestanden met `spx recognize --output vtt file <FILENAME>` .
- Gevoelige sleutelgegevens zijn nu verborgen in foutopsporing/uitgebreide uitvoer.
- URL-controle en foutbericht toegevoegd voor inhoudsveld in batchtranscriptie maken.

**COVID-19 verkort testen:**

Omdat onze technici nog steeds thuis moeten werken, is het aantal handmatige verificatiescripts die vooraf gaan, aanzienlijk verminderd. We testen op minder apparaten met minder configuraties en de kans dat omgevingsspecifieke fouten worden opgelost, kan toenemen. We valideren nog steeds grondig met een grote set automatisering. Laat het ons weten op GitHub in het onwaarschijnlijke geval dat we iets [hebben gemist.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Blijf in orde!

## <a name="text-to-speech-2021-february-release"></a>Release van tekst-naar-spraak 2021-februari

**Aangepaste neurale stem GA**

Aangepaste neurale stem is ga in februari in 13 talen: Chinees (Mandarijn, Vereenvoudigd), Engels (Australië), Engels (India), Engels (Verenigd Koninkrijk), Engels (Verenigde Staten), Frans (Canada), Frans (Frankrijk), Duits (Duitsland), Italiaans (Italië), Japans (Japan), Koreaans (Korea), Portugees (Brazilië), Spaans (Mexico) en Spaans (Spanje). Meer informatie over [wat er Aangepaste neurale stem](custom-neural-voice.md) en hoe u dit op verantwoorde wijze kunt [gebruiken.](concepts-guidelines-responsible-deployment-synthetic.md) Aangepaste neurale stem vereist registratie en Microsoft kan de toegang beperken op basis van de geschiktheidscriteria van Microsoft. Meer informatie over de [beperkte toegang](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context).  

## <a name="speech-sdk-1150-2021-january-release"></a>Speech SDK 1.15.0: release van 2021-januari

> [!NOTE]
> De Speech SDK in Windows is afhankelijk van het gedeelde Microsoft Visual C++ Redistributable voor Visual Studio 2015, 2017 en 2019. Download deze [hier.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Samenvatting van highlights**
- Kleiner geheugen en een kleinere schijfvoetafdruk maken de SDK efficiënter.
- Uitvoerindelingen met een hogere betrouwbaarheid die beschikbaar zijn voor de persoonlijke preview van aangepaste neurale spraak.
- Intent Recognizer kan nu meer dan de belangrijkste intentie retourneren, zodat u een afzonderlijke evaluatie kunt maken over de intentie van uw klant.
- Uw spraakassistent of bot is nu eenvoudiger in te stellen en u kunt ervoor zorgen dat deze onmiddellijk niet meer luistert en meer controle krijgen over hoe deze reageert op fouten.
- Verbeterde apparaatprestaties door compressie optioneel te maken.
- Gebruik de Speech SDK in Windows ARM/ARM64.
- Verbeterde debuggen op laag niveau.
- De functie beoordeling van uitspraak is nu algemeen beschikbaar.
- Er zijn verschillende oplossingen voor problemen die u, onze waardevolle klanten, hebben gemarkeerd op GitHub. Dank u! Houd de feedback binnen!

**Verbeteringen**
- De Speech SDK is nu efficiënter en lichtgewicht. We hebben meerdere release-inspanningen gestart om het geheugengebruik en de schijfvoetafdruk van de Speech SDK te verminderen. Als eerste stap hebben we aanzienlijke bestandsgrootteverminderingen aangebracht in gedeelde bibliotheken op de meeste platforms. Vergeleken met versie 1.14:
  - 64-bits UWP-compatibele Windows-bibliotheken zijn ongeveer 30% kleiner.
  - 32-bits Windows-bibliotheken zien nog geen grootteverbeteringen.
  - Linux-bibliotheken zijn 20-25% kleiner.
  - Android-bibliotheken zijn 3-5% kleiner.

**Nieuwe functies**
- **Alle:** nieuwe 48 KHz-uitvoerindelingen beschikbaar voor de privépreview van aangepaste neurale spraak via de TTS-spraaksynthese-API: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48 khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Bitsf48Khz16BitoPcm, rawf-48khz-16bit-mono-pcm.
- **Alle:** Aangepaste spraak is ook eenvoudiger te gebruiken. Ondersteuning toegevoegd voor het instellen van aangepaste spraak via `EndpointId` ([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). Vóór deze wijziging moesten custom voice-gebruikers de eindpunt-URL instellen via de `FromEndpoint` methode . Klanten kunnen nu de methode `FromSubscription` gebruiken, net als openbare stemmen, en vervolgens de implementatie-id verstrekken door in te `EndpointId` stellen. Dit vereenvoudigt het instellen van aangepaste stemmen. 
- **C++/C#/Java/Objective-C/Python:** haal meer op dan de belangrijkste intentie van `IntentRecognizer` . Het biedt nu ondersteuning voor het configureren van het JSON-resultaat met alle intenties en niet alleen de best scorende intentie via de methode met behulp van de `LanguageUnderstandingModel FromEndpoint` `verbose=true` URI-parameter. Hiermee wordt het [GitHub-probleem #880.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880) Zie hier bijgewerkte [documentatie.](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)
- **C++/C#/Java:** zorg ervoor dat uw spraakassistent of bot onmiddellijk niet meer luistert. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) heeft nu `StopListeningAsync()` een -methode om bij te `ListenOnceAsync()` komen. Hierdoor wordt audio-opname onmiddellijk gestopt en wordt er zonder al te veel tijd gewacht op een resultaat, waardoor het perfect is voor gebruik met 'nu stoppen' scenario's voor het indrukken van knoppen.
- **C++/C#/Java/JavaScript:** ervoor zorgen dat uw spraakassistent of bot beter reageert op onderliggende systeemfouten. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) heeft nu een nieuwe `TurnStatusReceived` gebeurtenis-handler. Deze optionele gebeurtenissen komen overeen met elke oplossing van de bot en rapporteren uitvoeringsfouten wanneer deze optreden, bijvoorbeeld als gevolg van een onverhandelde uitzondering, time-out of netwerkuitval tussen Direct Line Speech en de [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) bot. `TurnStatusReceived` maakt het gemakkelijker om te reageren op foutomstandigheden. Als een bot bijvoorbeeld te lang duurt voor een back-updatabasequery (bijvoorbeeld om een product op te zoeken), kan de client het opnieuw proberen met de vraag 'Sorry, ik heb dat niet helemaal door, kunt u het opnieuw proberen' of iets `TurnStatusReceived` vergelijkbaars.
- **C++/C#**: Gebruik de Speech SDK op meer platforms. Het [Speech SDK NuGet-pakket](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) ondersteunt nu binaire Windows ARM/ARM64-desktopcomputers (UWP werd al ondersteund) om de Speech SDK nuttiger te maken voor meer computertypen.
- **Java:** heeft nu een methode die onbedoeld is [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) uitgesloten van de taal die eerder is `setSpeechActivityTemplate()` gebruikt. Dit is gelijk aan het instellen van de eigenschap en vraagt alle toekomstige Bot Framework-activiteiten die afkomstig zijn van de Direct Line Speech-service, de geleverde inhoud samen te voegen in hun `Conversation_Speech_Activity_Template` JSON-nettoladingen.
- **Java:** verbeterde debuggen op laag niveau. De [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) klasse heeft nu een `MessageReceived` gebeurtenis, vergelijkbaar met andere programmeertalen (C++, C#). Deze gebeurtenis biedt toegang op laag niveau tot binnenkomende gegevens van de service en kan nuttig zijn voor diagnostische gegevens en debuggen.
- **JavaScript:** eenvoudigere installatie voor spraakassistenten en bots via , dat nu - en -factorymethoden heeft die het gebruik van aangepaste servicelocaties vereenvoudigen en het handmatig [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) `fromHost()` instellen van `fromEndpoint()` eigenschappen. We hebben ook optionele specificatie van gestandaardiseerd `botId` voor het gebruik van een niet-standaardbot in de configuratiefabrieken.
- **JavaScript:** verbeterde apparaatprestaties door de toegevoegde eigenschap voor tekenreeksbesturingselementen voor websocket-compressie. Uit prestatieoverwegingen is standaard websocket-compressie uitgeschakeld. Dit kan opnieuw worden in te zetten voor scenario's met lage bandbreedte. Meer informatie [kunt u hier vinden.](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid) Hiermee wordt het [GitHub-probleem #242.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242)
- **JavaScript:** er is ondersteuning toegevoegd voor beoordeling van uitspraak om de spraakuitspraak te evalueren. Zie de snelstart [hier.](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)

**Opgeloste fouten**
- **Alle** (behalve JavaScript): er is een regressie opgelost in versie 1.14, waarbij te veel geheugen werd toegewezen door de kiezer.
- **C++**: Er is een probleem opgelost met garbage collection met `DialogServiceConnector` , dat het [GitHub-probleem #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C#**: Er is een probleem opgelost met het afsluiten van de thread waardoor objecten ongeveer een seconde werden geblokkeerd bij verwijdering.
- **C++/C#/Java:** Er is een uitzondering opgelost waardoor een toepassing geen spraakautorisatie-token of activiteitensjabloon meer dan één keer kan instellen op een `DialogServiceConnector` .
- **C++/C#/Java:** er is een herkennende crash opgelost vanwege een racevoorwaarde in de noodtoestand.
- **JavaScript:** heeft de optionele parameter die is opgegeven [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) in `botId` `BotFrameworkConfig` 's factories niet eerder in ere houden. Hierdoor moest de queryreeksparameter handmatig worden ingesteld `botId` om een niet-standaardbot te gebruiken. De fout is gecorrigeerd en de waarden die aan de fabrieken worden geleverd, worden gehonoreerd en gebruikt, met inbegrip van `botId` `BotFrameworkConfig` de nieuwe en `fromHost()` `fromEndpoint()` toevoegingen. Dit geldt ook voor de `applicationId` parameter voor `CustomCommandsConfig` .
- **JavaScript:** [GitHub-probleem opgelost #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), waardoor recognizer-objecten opnieuw kunnen worden gebruikt.
- **JavaScript:** er is een probleem opgelost waarbij de SKU meerdere keren in één `speech.config` TTS-sessie werd verzonden, wat bandbreedte verspilde.
- **JavaScript:** vereenvoudigde foutafhandeling voor microfoonautorisatie, waardoor een beschrijvend bericht kan worden weergegeven wanneer de gebruiker geen microfooninvoer in de browser heeft toegestaan.
- **JavaScript:** [GitHub-probleem opgelost #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) typefouten in en een compilatiefout `ConversationTranslator` veroorzaakt voor `ConversationTranscriber` TypeScript-gebruikers.
- **Objective-C:** Er is een probleem opgelost waarbij de GStreamer-build is mislukt voor iOS op Xcode 11.4, waarbij het [GitHub-probleem #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python:** [GitHub-probleem met #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870)opgelost, waardoor 'DeprecationWarning: the imp module is deprecated in favor of importlib' wordt verwijderd.

**Voorbeelden**
- [Voorbeeld van bestand voor JavaScript-browser gebruikt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) nu bestanden voor spraakherkenning. Hiermee wordt het [GitHub-probleem #884.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884)

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Speech CLI (ook wel bekend als SPX): release van 2021-januari

**Nieuwe functies**
- Speech CLI is nu beschikbaar als [een NuGet-pakket](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) en kan worden geïnstalleerd via .NET CLI als een globaal .NET-hulpprogramma dat u kunt aanroepen vanaf de shell/opdrachtregel.
- De [Custom Speech DevOps Template-repo](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) is bijgewerkt voor het gebruik van Speech CLI voor de custom speech-werkstromen.

**COVID-19** verkorte tests: Omdat de voortdurende inding van de vraag blijft of onze technici thuis moeten werken, is het aantal handmatige verificatiescripts die vooraf gaan, aanzienlijk verminderd. We testen op minder apparaten met minder configuraties en de kans dat omgevingsspecifieke fouten worden opgelost, kan toenemen. We valideren nog steeds grondig met een grote set automatisering. Laat het ons weten op GitHub in het onwaarschijnlijke geval dat we iets [hebben gemist.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Blijf in orde!

## <a name="text-to-speech-2020-december-release"></a>Release van tekst-naar-spraak 2020-december

**Nieuwe neurale stemmen in ga en preview**

Er zijn 51 nieuwe stemmen uitgebracht voor in totaal 129 neurale stemmen in 54 talen/landtalen:

- **46** nieuwe stemmen in GA-land: Shakir in Arabisch `ar-EG` (Schrift), Hamed in Arabisch `ar-SA` (Arabische Republiek),Keringlav in `bg-BG` HetEns (Rusland), Moeta in Spanje (Spanje), Alsn in Deens `ca-ES` `cs-CZ` (Tsjechische Republiek), Jeppe in `da-DK` Deens (Tsjechisch), Duits (Zwitserland), Jan in het Duits `de-AT` `de-CH` (Zwitserland), Nestoras in Grieks `el-GR` `en-CA` (Engelstalig), Engelstalige (Canada), `en-IE` Engelstalige (Ierland), Britkas in `en-IN` Hindi (India), Mohan in `en-IN` Telugu (India), Prabhat in het `en-IN` Engels (India), Valluvar in `en-IN` Tamil (India), Enric inPt (Spanje), Kert in Het Frans (Spanje), Kert in Het Fins `es-ES` `et-EE` (Estisch), Harri in Fins `fi-FI` (Lakens), Fabrice in Frans `fi-FI` `fr-CH` (Zwitserland), Colm in `ga-IE` Navigeer (Ierland), Avri in `he-IL` Hebreeuws (Hebreeuws) Rusland), Srecko in het Frans `hr-HR` (Hongaars), Tamas in Hongaars `hu-HU` (Maleis), Gadis in `id-ID` Hongaars (Argentinië), Euas in `lt-LT` HetEns (Rusland), Nils in Het Lets `lv-LV` (Lets), Osman in Maleis `ms-MY` (Maleis), Joseph in `mt-MT` , Finn in `nb-NO` Noors, Bokmål (Rusland), Pernille in `nb-NO` Noors, Bokmål (Rusland), Fenna in Het Nederlands `nl-NL` (Nederland), Tekst in Nederlands `nl-NL` (Nederland), Agnieszka in `pl-PL` Pools (Tsjechisch), Marek in Pools (Duitsland), Duarte in Het Portugees `pl-PL` (Brazilië), Radome in het Portugees `pt-BR` `pt-PT` (Potugal), `ro-RO` Roemeense (Tsjechische), Dmitry in `ru-RU` Russisch (Rusland), Sålana in `ru-RU` Russisch (Rusland), Lukas in `sk-SK` Slowaaks (Slowaaks),Auts in `sl-SI` Den Haagn (Sloveens), Mattieën in Zweeds `sv-SE` (Rusland), Sofie in Zweeds `sv-SE` (Rusland), Niwat in `th-TH` Thai (Vak), Ahmet in `tr-TR` het Turks (Hongië), NamMinh in `vi-VN` Vietnam (Vietnam), HmateoLeten in Chinese Mandarijn `zh-TW` (Taiwan), YunJhe in `zh-TW` Mandarijn (Taiwan), HiuMaan in `zh-HK` Chinees Cantonese (Hongkong), WanHef in `zh-HK` Chinese Cantonese (Hongkong).

- 5 nieuwe stemmen **in de preview-versie:** Kert in `et-EE` het Letse (Estisch), Colm in `ga-IE` Italië (Ierland), Nils in `lv-LV` Het Lets (Lets), Alsa's in `lt-LT` HetEns (Rusland), Joseph in `mt-MT` Kanten (Kanten).

Met deze release ondersteunen we nu in totaal 129 neurale stemmen in 54 talen/landtalen. Daarnaast zijn er meer dan 70 standaardstemmen beschikbaar in 49 talen/talen. Ga [naar Taalondersteuning](language-support.md#text-to-speech) voor de volledige lijst.

**Updates voor audio-inhoud maken**
- Verbeterde gebruikersinterface voor spraakselectie met spraakcategorieën en gedetailleerde spraakbeschrijvingen. 
- Ingeschakelde afstemming van de innatie voor alle neurale stemmen in verschillende talen.
- Lokalisatie van de gebruikersinterface is geautomatiseerd op basis van de taal van de browser.
- Ingeschakelde `StyleDegree` besturingselementen voor alle `zh-CN` neurale stemmen.
Ga naar [audio-inhoud maken hulpprogramma om](https://speech.microsoft.com/audiocontentcreation) de nieuwe functies te zien. 

**Updates voor zh-CN-stemmen**
- Alle `zh-CN` neurale stemmen zijn bijgewerkt ter ondersteuning van Engels spreken.
- Schakel alle neurale `zh-CN` stemmen in om aanpassing van de intonatie te ondersteunen. SSML of audio-inhoud maken kan worden gebruikt om aan te passen voor de beste intonatie.
- Alle `zh-CN` neurale stemmen in meerdere stijlen zijn bijgewerkt om controle te `StyleDegree` ondersteunen. De intensiteit van de emotie (zacht of sterk) is aanpasbaar.
- Bijgewerkt `zh-CN-YunyeNeural` om ondersteuning te bieden voor meerdere stijlen die verschillende emoties kunnen uitvoeren.

## <a name="text-to-speech-2020-november-release"></a>Release van tekst-naar-spraak 2020-november

**Nieuwe locales en stemmen in preview**
- **Er zijn vijf nieuwe stemmen en talen** geïntroduceerd in de portfolio neurale TTS. Ze zijn: Grace in Voorkomen (Zij), Ona in Het Lets (Rusland), Anu in Het lets (Rusland), Orla in Eues (Ierland) en Everita in Het Lets (Lets).
- **Er zijn `zh-CN` vijf nieuwe stemmen met meerdere stijlen** en rollen die ondersteuning bieden voor: Moetenhan, Contactmo,Functiesrui, Nuxxia en Yunxi.

> Deze stemmen zijn beschikbaar in openbare preview in drie Azure-regio's: EastUS, SouthEastAsia en WestEurope.

**Neurale TTS-container GA**
- Met neurale TTS-containers kunnen ontwikkelaars spraaksynthese uitvoeren met de meest natuurlijke digitale stemmen in hun eigen omgeving voor specifieke vereisten voor beveiliging en gegevensbeheer. Controleer [hoe u Spraakcontainers installeert.](speech-container-howto.md) 

**Nieuwe functies**
- **Custom Voice:** gebruikers in staat stellen om een spraakmodel van de ene regio naar de andere te kopiëren; ondersteunde opzegging en hervatting van eindpunten. Ga hier naar [de portal.](https://speech.microsoft.com/customvoice)
- [Ondersteuning voor SSML-stiltetags.](speech-synthesis-markup.md#add-silence) 
- Algemene verbeteringen in de spraakkwaliteit van TTS: Verbeterde uitspraaknauwkeurigheid op woordniveau in nb-NO. Gereduceerde uitspraakfout van 53%.

> Lees meer op [dit techblog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604).

## <a name="text-to-speech-2020-october-release"></a>Release van tekst-naar-spraak 2020-oktober

**Nieuwe functies**
- Door Haar wordt een nieuwe stijl `newscast` ondersteund. Zie [de spreekstijlen in SSML gebruiken.](speech-synthesis-markup.md#adjust-speaking-styles)
- **Neurale stemmen zijn bijgewerkt naar HiFiNet vocoder, met een hogere audiokwaliteit en een snellere synthesesnelheid.** Dit is een voordeel voor klanten van wie het scenario afhankelijk is van hi-fi audio of lange interacties, waaronder videosynchronisatie, audioboeken of onlinemateriaal voor onderwijs. [Lees meer over het verhaal en de stemvoorbeelden op onze blog van de tech-community](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Custom Voice](https://speech.microsoft.com/customvoice)  &  [audio-inhoud maken Studio](https://speech.microsoft.com/audiocontentcreation) gelokaliseerd op 17 locales.** Gebruikers kunnen de gebruikersinterface eenvoudig overschakelen naar een lokale taal voor een meer gebruiksvriendelijke ervaring.   
- **audio-inhoud maken:** Er is een besturingselement voor een stijlgraad toegevoegd voor ManifestxiaoNeural; De aangepaste onderbrekingsfunctie is verfijnd met incrementele onderbrekingen van 50 ms. 

**Algemene verbeteringen in de spraakkwaliteit van TTS**
- Verbeterde uitspraaknauwkeurigheid op woordniveau in `pl-PL` (foutfrequentievermindering: 51%) en `fi-FI` (foutfrequentievermindering: 58%)
- Verbeterd `ja-JP` lezen van één woord voor het woordenlijstscenario. Gereduceerde uitspraakfout met 80%.
- `zh-CN-XiaoxiaoNeural`: Verbeterde spraakkwaliteit in sentiment/CustomerService/Newscast/Naar/Huisstijl.
- `zh-CN`: Verbeterde uitspraak en lichte toon en verfijnde ruimte-prosody, waardoor de begrijpelijkheid aanzienlijk wordt verbeterd. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0: release van 2020-oktober

> [!NOTE]
> De Speech SDK in Windows is afhankelijk van het gedeelde Microsoft Visual C++ Redistributable voor Visual Studio 2015, 2017 en 2019. Download deze [hier.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Nieuwe functies**
- **Linux:** ondersteuning toegevoegd voor Debian 10 en Ubuntu 20.04 LTS.
- **Python/Objective-C:** Er is ondersteuning toegevoegd voor de `KeywordRecognizer` API. Documentatie vindt u [hier.](./custom-keyword-basics.md)
- **C++/Java/C#**: Ondersteuning toegevoegd voor het instellen van een `HttpHeader` sleutel/waarde via `ServicePropertyChannel::HttpHeader` .
- **JavaScript:** er is ondersteuning toegevoegd voor de `ConversationTranscriber` API. Lees hier de [documentatie](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript). 
- **C++/C#**: Nieuwe `AudioDataStream FromWavFileInput` methode toegevoegd (om te lezen. [WAV-bestanden) hier (C++)](/cpp/cognitive-services/speech/audiodatastream) en [hier (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/C#/Java/Python/Objective-C/Swift:** Er is een methode toegevoegd om de synthese van `stopSpeakingAsync()` tekst naar spraak te stoppen. Lees hier de referentiedocumentatie [(C++),](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace)hier [(C#),](/dotnet/api/microsoft.cognitiveservices.speech)hier [(Java)](/java/api/com.microsoft.cognitiveservices.speech), [hier (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)en [hier (Objective-C/Swift).](/objectivec/cognitive-services/speech/)
- **C#, C++, Java:** Er is een functie toegevoegd aan de klasse die kan worden gebruikt voor het bewaken van verbindings- en `FromDialogServiceConnector()` `Connection` verbindingsgebeurtenissen voor `DialogServiceConnector` . Lees hier de [referentiedocumentatie (C#),](/dotnet/api/microsoft.cognitiveservices.speech.connection) [hier (C++)](/cpp/cognitive-services/speech/connection)en [hier (Java).](/java/api/com.microsoft.cognitiveservices.speech.connection)
- **C++/C#/Java/Python/Objective-C/Swift:** Er is ondersteuning toegevoegd voor Beoordeling van uitspraak, waarmee spraakuitspraken worden geëvalueerd en sprekers feedback geven over de nauwkeurigheid en vloeiendheid van gesproken audio. Lees de documentatie [hier.](how-to-pronunciation-assessment.md)

**Wijziging die grote veranderingen doorbreekt**
- **JavaScript:** PullAudioOutputStream.read() heeft een wijziging in het retourtype van een interne Promise in een native JavaScript Promise.

**Opgeloste fouten**
- **Alle:** er is een regressie van 1,13 opgelost waarbij `SetServiceProperty` waarden met bepaalde speciale tekens werden genegeerd.
- **C#**: Er zijn voorbeelden van Windows-consoles op Visual Studio 2019 niet gevonden om systeemeigen DLL's te vinden.
- **C#**: Er is een crash opgelost met geheugenbeheer als stream wordt gebruikt als `KeywordRecognizer` invoer.
- **ObjectiveC/Swift:** er is een crash opgelost met geheugenbeheer als stream wordt gebruikt als recognizer-invoer.
- **Windows:** probleem met co-bestaan opgelost met BT HFP/A2DP op UWP.
- **JavaScript:** de toewijzing van sessie-ID's is opgelost om logboekregistratie te verbeteren en te helpen bij interne foutopsporing/servicecorrelatie.
- **JavaScript:** er is een oplossing toegevoegd `DialogServiceConnector` voor het uitschakelen van `ListenOnce` aanroepen nadat de eerste aanroep is gedaan.
- **JavaScript:** er is een probleem opgelost waarbij resultaatuitvoer alleen maar 'eenvoudig' zou zijn.
- **JavaScript:** er is een probleem opgelost met continue herkenning in Safari in macOS.
- **JavaScript:** beperking van CPU-belasting voor scenario met hoge aanvraagdoorvoer.
- **JavaScript:** toegang tot details van het resultaat van registratie van spraakprofiel toestaan.
- **JavaScript:** Er is een oplossing toegevoegd voor continue herkenning in `IntentRecognizer` .
- **C++/C#/Java/Python/Swift/ObjectiveC:** Er is een onjuiste URL opgelost voor australiaeast en brazilsouth in `IntentRecognizer` .
- **C++/C#: toegevoegd** `VoiceProfileType` als argument bij het maken van een `VoiceProfile` object.
- **C++/C#/Java/Python/Swift/ObjectiveC:** Potentieel opgelost bij het lezen `SPX_INVALID_ARG` van een bepaalde `AudioDataStream` positie.
- **IOS:** Crash met spraakherkenning in Unity opgelost

**Voorbeelden**
- **ObjectiveC:** Hier is een voorbeeld toegevoegd voor [sleutelwoordherkenning.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples)
- **C#/JavaScript:** Quickstart toegevoegd voor gesprektranscriptie [hier (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) en [hier (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription).
- **C++/C#/Java/Python/Swift/ObjectiveC:** Er is hier een voorbeeld toegevoegd voor beoordeling van [uitspraak](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin:** quickstart bijgewerkt naar de meest recente Visual Studio [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin).

**Bekend probleem**
- DigiCert Global Root G2-certificaat wordt niet standaard ondersteund in HoloLens 2 en Android 4.4 (KitMap) en moet worden toegevoegd aan het systeem om de Speech SDK functioneel te maken. Het certificaat wordt in de nabije HoloLens 2 aan de besturingssysteemafbeeldingen toegevoegd. Android 4.4-klanten moeten het bijgewerkte certificaat toevoegen aan het systeem.

**CoVID-19 verkorte tests:** Omdat we de afgelopen weken op afstand hebben gewerkt, konden we niet zoveel handmatige verificatietests uitvoeren als normaal. We hebben geen wijzigingen aangebracht die volgens ons iets zouden kunnen hebben verbroken en onze geautomatiseerde tests zijn geslaagd. Laat het ons weten op GitHub in het onwaarschijnlijke geval dat we iets [hebben gemist.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Blijf in orde!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Speech CLI (ook wel bekend als SPX): release van 2020 tot oktober
SPX is de opdrachtregelinterface voor het gebruik van de Azure Speech-service zonder code te schrijven. Download hier de nieuwste [versie.](./spx-basics.md) <br>

**Nieuwe functies**
- `spx csr dataset upload --kind audio|language|acoustic` - gegevenssets maken op basis van lokale gegevens, niet alleen op basis van URL's.
- `spx csr evaluation create|status|list|update|delete` : vergelijk nieuwe modellen met basislijn-waarheid/andere modellen.
- `spx * list` – biedt ondersteuning voor niet-pagina's (vereist geen --top X --skip X).
- `spx * --http header A=B` – ondersteuning voor aangepaste headers (toegevoegd voor Office voor aangepaste verificatie). 
- `spx help` - verbeterde tekst en tekstkleur met code (blauw) in de back-tick.

## <a name="text-to-speech-2020-september-release"></a>Release van tekst-naar-spraak 2020-september

### <a name="new-features"></a>Nieuwe functies

* **Neurale TTS** 
    * **Uitgebreid ter ondersteuning van 18 nieuwe talen/talen.** Ze zijn Hongaars, Tsjechisch, Duits (Zwitserland), Duits (Zwitserland), Grieks, Engels (Ierland), Frans (Zwitserland), Hebreeuws, Hongaars, Hongaars, Grieks, Maleis, Roemeens, Slowaaks, Slowaaks, Slowaaks, Tamil, Telugu en Vietnamees. 
    * **Er zijn 14 nieuwe stemmen uitgebracht om de verscheidenheid in de bestaande talen te verrijken.** Zie [volledige taal en spraaklijst.](language-support.md#neural-voices)
    * **Nieuwe spreekstijlen voor `en-US` en `zh-CN` stemmen.** De nieuwe stem in het Engels (VS) biedt ondersteuning voor chatbot-, klantenservice- en assistentstijlen. Er zijn 10 nieuwe spreekstijlen beschikbaar met onze zh-CN-stem, Zalxiao. Daarnaast ondersteunt de neurale stem Van Xiao `StyleDegree` afstemming. Bekijk [hoe u de spreekstijlen gebruikt in SSML](speech-synthesis-markup.md#adjust-speaking-styles).

* **Containers: neurale TTS-container uitgebracht in openbare preview met 16 stemmen beschikbaar in 14 talen.** Meer informatie over [het implementeren van Spraakcontainers voor neurale TTS](speech-container-howto.md)  

Lees de [volledige aankondiging van de TTS-updates voor Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Release van tekst-naar-spraak 2020-augustus

### <a name="new-features"></a>Nieuwe functies

* **Neurale TTS: nieuwe spreekstijl voor `en-US` Aria-stem.** AriaNeural klinkt als een nieuwscaster bij het lezen van nieuws. De stijl 'newscast-formal' klinkt ernstiger, terwijl de stijl 'newscast-informeel' meer op de hoogte is. Bekijk [hoe u de spreekstijlen gebruikt in SSML](speech-synthesis-markup.md).

* **Custom Voice: er wordt een nieuwe functie uitgebracht om automatisch de kwaliteit van trainingsgegevens te controleren.** Wanneer u uw gegevens uploadt, onderzoekt het systeem verschillende aspecten van uw audio- en transcriptgegevens en worden problemen automatisch opgelost of gefilterd om de kwaliteit van het spraakmodel te verbeteren. Dit omvat het volume van uw audio, het niveau van ruis, de uitspraaknauwkeurigheid van spraak, de uitlijning van spraak met de genormaliseerde tekst, stilte in de audio, naast de audio- en scriptindeling. 

* **audio-inhoud maken: een set nieuwe functies voor krachtigere** stemafstemming en audiobeheermogelijkheden.

    * Uitspraak: de functie voor het afstemmen van de uitspraak is bijgewerkt naar de meest recente phoneme-set. U kunt het juiste phoneme-element kiezen uit de bibliotheek en de uitspraak verfijnen van de woorden die u hebt geselecteerd. 

    * Downloaden: De audiofunctie Downloaden/Exporteren is verbeterd ter ondersteuning van het genereren van audio per alinea. U kunt inhoud in hetzelfde bestand/dezelfde SSML bewerken terwijl u meerdere audio-uitvoer genereert. De bestandsstructuur van 'Downloaden' is ook verfijnd. U kunt nu eenvoudig alle audiobestanden in één map krijgen. 

    * Taakstatus: de ervaring voor het exporteren van meerdere bestanden is verbeterd. Wanneer u in het verleden meerdere bestanden exporteert en een van de bestanden is mislukt, mislukt de volledige taak. Maar nu worden alle andere bestanden met succes geëxporteerd. Het taakrapport is verrijkt met meer gedetailleerde en gestructureerde informatie. U kunt de logboeken nu controleren op alle mislukte bestanden en zinnen met het rapport. 

    * SSML-documentatie: gekoppeld aan een SSML-document om de regels te controleren voor het gebruik van alle afstemmingsfuncties.

* **De Spraaklijst-API wordt bijgewerkt met een gebruiksvriendelijke weergavenaam** en de spreekstijlen die worden ondersteund voor neurale stemmen.

### <a name="general-tts-voice-quality-improvements"></a>Algemene verbeteringen in de spraakkwaliteit van TTS

* Gereduceerd aantal uitspraak op woordniveau voor `ru-RU` (fouten gereduceerd met 56%) en `sv-SE` (fouten gereduceerd met 49%)

* Het lezen van polygodene woorden op `en-US` neurale stemmen is met 40% verbeterd. Voorbeelden van veelhoekige woorden zijn 'read', 'live', 'content', 'record', 'object', enzovoort. 

* De natuurlijkheid van de vraagtoon in is `fr-FR` verbeterd. MOS -winst (gemiddelde meningscore) : +0,28

* De vocoders zijn bijgewerkt voor de volgende stemmen, met betrouwbaarheidsverbeteringen en een versnelling van de algehele prestaties met 40%.

    | Landinstelling | Spraak |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Opgeloste fouten

* Er zijn een aantal fouten opgelost met het audio-inhoud maken hulpprogramma 
    * Probleem opgelost met automatisch vernieuwen. 
    * Problemen opgelost met spraakstijlen in zh-CN in de Azië - oost zuid-regio.
    * Er is een stabiliteitsprobleem opgelost, waaronder een exportfout met de tag 'break' en fouten in interpunctie.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nieuwe taal voor spraak-naar-tekst: release van 2020 tot augustus
Spraak-naar-tekst heeft 26 nieuwe landtalen uitgebracht in augustus: 2 Europese talen en , 5 Engelse landtalen en 19 Spaans land voor de meeste `cs-CZ` `hu-HU` Zuid-Amerikaanse landen. Hieronder vindt u een lijst met de nieuwe locales. Bekijk hier de volledige lijst [met talen.](./language-support.md)

| Landinstelling  | Taal                          |
|---------|-----------------------------------|
| `cs-CZ` | Tsjechisch (Tsjechische Republiek)            | 
| `en-HK` | Engels (Hongkong)               | 
| `en-IE` | Engels (Ierland)                 | 
| `en-PH` | Engels (Engelstalig)             | 
| `en-SG` | Engels (Singapore)               | 
| `en-ZA` | Engels (Zuid-Afrika)            | 
| `es-AR` | Spaans (Argentinië)               | 
| `es-BO` | Spaans (Spanje)                 | 
| `es-CL` | Spaans (Spanje)                   | 
| `es-CO` | Spaans (Columbia)                | 
| `es-CR` | Spaans (Hadoe)              | 
| `es-CU` | Spaans (Spanje)                    | 
| `es-DO` | Spaans (Republiek Korea)      | 
| `es-EC` | Spaans (Pter)                 | 
| `es-GT` | Spaans (Spanje)               | 
| `es-HN` | Spaans (Euro's)                | 
| `es-NI` | Spaans (Bezoeken)               | 
| `es-PA` | Spaans (Spanje)                  | 
| `es-PE` | Spaans (Cura)                    | 
| `es-PR` | Spaans (HadoE)             | 
| `es-PY` | Spaans (Spanje)                | 
| `es-SV` | Spaans (El Hadoe)             | 
| `es-US` | Spaans (VS)                     | 
| `es-UY` | Spaans (Vietname)                 | 
| `es-VE` | Spaans (Spanje)               | 
| `hu-HU` | Hongaars (Hongarije)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0: release van 2020 tot juli

> [!NOTE]
> De Speech SDK in Windows is afhankelijk van het gedeelde Microsoft Visual C++ Redistributable voor Visual Studio 2015, 2017 en 2019. Download en installeer deze [hier.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Nieuwe functies**
- **C#**: Ondersteuning toegevoegd voor asynchrone gesprektranscriptie. Zie de documentatie [hier.](./how-to-async-conversation-transcription.md)  
- **JavaScript:** er Speaker Recognition ondersteuning toegevoegd voor [browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) en [node.js.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition)
- **JavaScript:** er is ondersteuning toegevoegd voor automatische taaldetectie/taal-id. Zie de documentatie [hier.](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)
- **Objective-C:** Ondersteuning toegevoegd voor [gespreks-](./multi-device-conversation.md) en [gesprektranscriptie voor meerdere apparaten.](./conversation-transcription.md) 
- **Python:** er is gecomprimeerde audioondersteuning toegevoegd voor Python in Windows en Linux. Zie de documentatie [hier.](./how-to-use-codec-compressed-audio-input-streams.md) 

**Opgeloste fouten**
- **Alle**: Er is een probleem opgelost waardoor de KeywordRecognizer de streams na een herkenning niet verder kon verplaatsen.
- **Alle**: Er is een probleem opgelost waardoor de stroom die is verkregen van een KeywordRecognitionResult niet het trefwoord bevat.
- **Alle**: Er is een probleem opgelost dat sendMessageAsync het bericht niet echt via de kabel verzendt nadat de gebruikers erop hebben gewacht.
- **Alle**: Er is een crash in Speaker Recognition-API's opgelost wanneer gebruikers de methode VoiceProfileClient::SpeakerRecEnrollProfileAsync meerdere keren aanroepen en niet hebben gewacht tot de aanroepen zijn voltooien.
- **Alle:** logboekregistratie van bestanden inschakelen is opgelost in de klassen VoiceProfileClient en SpeakerRecognizer.
- **JavaScript:** er is een [probleem opgelost](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) met beperking wanneer de browser wordt geminimaliseerd.
- **JavaScript:** er is een [probleem opgelost](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) met een geheugenlek in stromen.
- **JavaScript:** caching toegevoegd voor OCSP-antwoorden van NodeJS.
- **Java:** Er is een probleem opgelost waardoor BigInteger-velden altijd 0 retourneren.
- **iOS:** er is een [probleem opgelost](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) met het publiceren van op speech-SDK gebaseerde apps in de iOS-App Store.

**Voorbeelden**
- **C++**: Hier is voorbeeldcode toegevoegd voor [Speaker Recognition.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)

**CoVID-19 verkorte tests:** Omdat we de afgelopen weken op afstand hebben gewerkt, konden we niet zoveel handmatige verificatietests uitvoeren als normaal. We hebben geen wijzigingen aangebracht die volgens ons alles zouden kunnen hebben verbroken en onze geautomatiseerde tests zijn geslaagd. Laat het ons weten op GitHub in het onwaarschijnlijke geval dat we iets [hebben gemist.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Blijf in orde!

## <a name="text-to-speech-2020-july-release"></a>Release van tekst-naar-spraak 2020-juli

### <a name="new-features"></a>Nieuwe functies

* **Neurale TTS, 15 nieuwe neurale stemmen:** de nieuwe stemmen die zijn toegevoegd aan de portfolio neurale TTS zijn Salma in het Arabisch `ar-EG` (India), Zaremah in het Arabisch `ar-SA` (Arabisch), Alba in Woord (Spanje), Hebtel in Deens (Brazilië), Neerja in het Engels `ca-ES` `da-DK` `es-IN` (India), Noora in `fi-FI` Fins (Fins) Hindi), Swara in `hi-IN` Hindi (India), Cotone in het Nederlands `nl-NL` (Nederland), Zofia in Pools (Rusland), Haia in het Portugees `pl-PL` `pt-PT` (Portugal), Dartone in Russisch `ru-RU` (Rusland), Hillevi in Zweeds `sv-SE` (Rusland), Moetara in `th-TH` Thai (Chinese), HiuGaai in `zh-HK` het Chinees (Cantonese, Traditioneel) en HchinoYu in `zh-TW` het Chinees (Chinese Mandarijn). Controleer alle [ondersteunde talen.](./language-support.md#neural-voices)  

* Custom Voice gestroomlijnde spraaktest met de **trainingsstroom** om de gebruikerservaring te vereenvoudigen: met de nieuwe testfunctie wordt elke stem automatisch getest met een vooraf gedefinieerde testset die is geoptimaliseerd voor elke taal, voor algemene scenario's en spraakassistentscenario's. Deze testsets worden zorgvuldig geselecteerd en getest om typische gebruiksgevallen en phonemes in de taal op te nemen. Bovendien kunnen gebruikers er nog steeds voor kiezen om hun eigen testscripts te uploaden bij het trainen van een model.

* **audio-inhoud maken: er wordt een set nieuwe functies uitgebracht om krachtigere mogelijkheden voor spraakafstemming en audiobeheer mogelijk te maken**

    * `Pitch`, en zijn verbeterd om afstemming te ondersteunen met een vooraf gedefinieerde waarde, zoals `rate` `volume` traag, gemiddeld en snel. Het is nu eenvoudig voor gebruikers om een 'constante' waarde te kiezen voor hun audiobewerking.

    ![Audio afstemmen](media/release-notes/audio-tuning.png)

    * Gebruikers kunnen nu de voor `Audio history` hun werkbestand controleren. Met deze functie kunnen gebruikers eenvoudig alle gegenereerde audio bijhouden die betrekking heeft op een werkbestand. Ze kunnen de geschiedenisversie controleren en de kwaliteit vergelijken terwijl ze tegelijkertijd afstemmen. 

    ![Audiogeschiedenis](media/release-notes/audio-history.png)

    * De `Clear` functie is nu flexibeler. Gebruikers kunnen een specifieke afstemmingsparameter verwijderen terwijl andere parameters beschikbaar blijven voor de geselecteerde inhoud.  

    * Er is een zelfstudievideo toegevoegd aan de [landingspagina](https://speech.microsoft.com/audiocontentcreation) om gebruikers snel aan de slag te helpen met TTS-stemafstemming en audiobeheer. 

### <a name="general-tts-voice-quality-improvements"></a>Algemene verbeteringen in de spraakkwaliteit van TTS

* Verbeterde TTS vocoder in voor een hogere betrouwbaarheid en lagere latentie.

    * Met de nieuwe vocoder is Er een nieuwe vocoder bijgewerkt waarmee +0,464 CMOS (Vergelijkende gemiddelde meningscore) een hogere spraakkwaliteit, 40% sneller in synthese en een afname van 30% op de latentie van de eerste `it-IT` byte is bereikt. 
    * Er is een update van Zalxiao in naar de nieuwe vocoder bijgewerkt met `zh-CN` +0148 CMOS-winst voor het algemene domein, +0,348 voor de newscast-stijl en +0,195 voor de stijl .0.195. 

* Bijgewerkte `de-DE` `ja-JP` modellen en spraakmodellen om de TTS-uitvoer natuurlijker te maken.
    
    * Katja in bijgewerkt met de nieuwste `de-DE` prosody-modelleringsmethode, de MOS-winst (Mean Opinion Score) is +0,13. 
    * Nanami in bijgewerkt met een nieuw `ja-JP` prosodymodel voor pitchaccenten, is de winst voor MOS (Mean Opinion Score) +0,19;  

* Verbeterde uitspraaknauwkeurigheid op woordniveau in vijf talen.

    | Taal | Vermindering van uitspraakfouten |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Opgeloste fouten

* Valuta lezen
    * Probleem opgelost met het lezen van valuta voor `es-ES` en `es-MX`
     
    | Taal | Invoer | Uitlezen na verbetering |
    |---|---|---|
    | `es-MX` | $ 1,58 | un cincuenta y ocho centavos |
    | `es-ES` | $ 1,58 | un dólar cincuenta y ocho centavos |

    * Ondersteuning voor negatieve valuta (zoals "-325 €" ) in de volgende gebieden: `en-US` , , , , , `en-GB` `fr-FR` `it-IT` `en-AU` . `en-CA`

* Verbeterde adreslees in `pt-PT` .
* Natasha ( ) en Libby ( ) uitspraak problemen opgelost `en-AU` op het woord `en-UK` "voor" en "vier".  
* Fouten in het hulpprogramma audio-inhoud maken opgelost
    * De extra en onverwachte onderbreking na de tweede alinea is opgelost.  
    * De functie 'Geen onderbreking' is toegevoegd na een regressie bug. 
    * Het probleem met het willekeurig vernieuwen van Speech Studio is opgelost.  

### <a name="samplessdk"></a>Voorbeelden/SDK

* JavaScript: lost het afspeelprobleem in Firefox en Safari op macOS en iOS op. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1: release van 2020 -juni
**Speech CLI (ook wel SPX genoemd)**
-   Help-zoekfuncties toegevoegd in de CLI:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Bijgewerkt om te werken met nieuw geïmplementeerde v3.0 Batch- en Custom Speech-API's:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nieuwe functies**
-   **C \# , C++**: Speaker Recognition Preview: deze functie maakt sprekeridentificatie (wie spreekt?) en sprekercontrole (is de spreker die ze beweren te zijn?) mogelijk. Begin met een [overzicht,](./speaker-recognition-overview.md)lees het artikel [Speaker Recognition basisprincipes](./get-started-speaker-recognition.md)of de [API-referentie docs](/rest/api/speakerrecognition/).

**Opgeloste fouten**
-   **C \# , C++**: Vaste microfoonopname werkte niet in 1.12 voor sprekerherkenning.
-   **JavaScript:** oplossingen voor tekst-naar-spraak in Firefox en Safari in macOS en iOS.
-   Oplossing voor schending van toegangsschending door Windows-toepassingsverdeler bij gesprektranscriptie bij gebruik van stream met acht kanalen.
-   Oplossing voor het vast komen te staan van toegangsschending door Windows-toepassingsverdeler bij het vertalen van gesprekken met meerdere apparaten.

**Voorbeelden**
-   **C#**: [Codevoorbeeld voor](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) sprekerherkenning.
-   **C++**: [Codevoorbeeld voor](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) sprekerherkenning.
-   **Java:** [codevoorbeeld voor](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) intentieherkenning in Android. 

**CoVID-19 verkorte tests:** Omdat we de afgelopen weken op afstand hebben gewerkt, konden we niet zoveel handmatige verificatietests uitvoeren als normaal. We hebben geen wijzigingen aangebracht die volgens ons iets zouden kunnen hebben verbroken en onze geautomatiseerde tests zijn geslaagd. Laat het ons weten op GitHub in het onwaarschijnlijke geval dat we iets [hebben gemist.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Blijf in orde!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0: release van 2020 mei
**Speech CLI (ook wel bekend als SPX)**
- **SPX** is een nieuw opdrachtregelprogramma waarmee u vanaf de opdrachtregel herkenning, synthese, vertaling, batchtranscriptie en aangepast spraakbeheer kunt uitvoeren. Gebruik deze om de Speech Service te testen of om een script te maken voor de Speech Service-taken die u moet uitvoeren. Download het hulpprogramma en lees hier de [documentatie.](./spx-overview.md)

**Nieuwe functies**
- **Go:** nieuwe go-taalondersteuning voor [spraakherkenning en](./get-started-speech-to-text.md?pivots=programming-language-go) [aangepaste spraakassistent](./quickstarts/voice-assistants.md?pivots=programming-language-go). Stel hier uw dev-omgeving [in.](./quickstarts/setup-platform.md?pivots=programming-language-go) Zie de sectie Voorbeelden hieronder voor voorbeeldcode. 
- **JavaScript:** Browserondersteuning toegevoegd voor Text-To-Speech. Zie de documentatie [hier.](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)
- **C++, C#, Java:** nieuw object en api's die worden ondersteund op iOS-platformen voor `KeywordRecognizer` Windows, Android, Linux &. Lees de documentatie [hier.](./custom-keyword-overview.md) Zie de sectie Voorbeelden hieronder voor voorbeeldcode. 
- **Java:** Gesprek met meerdere apparaten toegevoegd met vertaalondersteuning. Zie het referentiemateriaal [hier](/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Verbeteringen & optimalisaties**
- **JavaScript:** de geoptimaliseerde implementatie van de browsermicrofoon verbetert de nauwkeurigheid van spraakherkenning.
- **Java:** Herfactored bindingen met behulp van directe JNI-implementatie zonder S HEF. Deze wijziging vermindert de bindingsgrootte met 10x voor alle Java-pakketten die worden gebruikt voor Windows, Android, Linux en Mac en vereenigt de verdere ontwikkeling van de Speech SDK Java-implementatie.
- **Linux:** ondersteuningsdocumentatie [bijgewerkt](./speech-sdk.md?tabs=linux) met de meest recente specifieke opmerkingen voor RHEL 7.
- Verbeterde verbindingslogica om meerdere keren verbinding te maken wanneer er service- en netwerkfouten optreden.
- De pagina [portal.azure.com](https://portal.azure.com) Speech-quickstart bijgewerkt om ontwikkelaars te helpen de volgende stap in het Azure Speech-traject te zetten.

**Opgeloste fouten**
- **C#, Java:** Er is een probleem [opgelost](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) met het laden van SDK-bibliotheken in Linux ARM (zowel 32-bits als 64-bits).
- **C#**: Er is een expliciete verwijdering van native grepen voor TranslationRecognizer-, IntentRecognizer- en Connection-objecten opgelost.
- **C#**: Levensduurbeheer voor vaste audio-invoer voor ConversationTranscriber-object.
- Er is een probleem opgelost `IntentRecognizer` waarbij de reden van het resultaat niet juist was ingesteld bij het herkennen van intenties van eenvoudige zinnen.
- Er is een probleem opgelost `SpeechRecognitionEventArgs` waarbij resultaat offset niet juist is ingesteld.
- Er is een racevoorwaarde opgelost waarbij de SDK een netwerkbericht probeerde te verzenden voordat de websocket-verbinding werd geopend. Was reproduceerbaar voor tijdens `TranslationRecognizer` het toevoegen van deelnemers.
- Geheugenlekken in de engine voor sleutelwoord recognizer opgelost.

**Voorbeelden**
- **Go:** Er zijn quickstarts toegevoegd voor [spraakherkenning](./get-started-speech-to-text.md?pivots=programming-language-go) en [custom voice assistant.](./quickstarts/voice-assistants.md?pivots=programming-language-go) U vindt hier [voorbeeldcode.](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples) 
- **JavaScript:** er zijn quickstarts toegevoegd voor [Tekst-naar-spraak,](./get-started-text-to-speech.md?pivots=programming-language-javascript)Vertaling [en](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script) [Intentieherkenning.](./get-started-intent-recognition.md?pivots=programming-language-javascript)
- Voorbeelden van sleutelwoordherkenning [voor C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) en [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**CoVID-19 verkorte tests:** Omdat we de afgelopen weken op afstand hebben gewerkt, konden we niet zoveel handmatige verificatietests uitvoeren als normaal. We hebben geen wijzigingen aangebracht die volgens ons iets zouden kunnen hebben verbroken en onze geautomatiseerde tests zijn geslaagd. Laat het ons weten op [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)als we iets hebben gemist.<br>
Blijf in orde!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: release van 2020-maart
**Nieuwe functies**
- Linux: Ondersteuning toegevoegd voor Red Hat Enterprise Linux (RHEL)/CentOS 7 [](./how-to-configure-rhel-centos-7.md) x64 met instructies voor het configureren van het systeem voor speech-SDK.
- Linux: ondersteuning toegevoegd voor .NET Core C# op Linux ARM32 en ARM64. Meer informatie is [hier](./speech-sdk.md?tabs=linux) beschikbaar. 
- C#, C++: Toegevoegd in , een consistente id voor alle tussenliggende en uiteindelijke `UtteranceId` `ConversationTranscriptionResult` spraakherkenningsresultaat. Details voor [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Er is ondersteuning toegevoegd voor `Language ID` . Zie speech_sample.py in [de GitHub-opslagplaats](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: ondersteuning voor gecomprimeerde audio-invoerindeling toegevoegd op het Windows-platform voor alle win32-consoletoepassingen. Hier [kunt u meer informatie vinden.](./how-to-use-codec-compressed-audio-input-streams.md) 
- JavaScript: ondersteuning voor spraaksynthese (tekst-naar-spraak) in NodeJS. Klik [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) voor meer informatie. 
- JavaScript: voeg nieuwe API's toe om inspectie van alle verzonden en ontvangen berichten mogelijk te maken. Klik [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript) voor meer informatie. 
        
**Opgeloste fouten**
- C#, C++: Er is een probleem opgelost en `SendMessageAsync` verzendt nu een binair bericht als binair type. Details voor [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: Er is een probleem opgelost waarbij het gebruik van een gebeurtenis kan leiden tot `Connection MessageReceived` crash als wordt verwijderd `Recognizer` vóór `Connection` het object. Details voor [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: de audiobuffergrootte van de microfoon is afgenomen van 800 ms naar 100 ms om de latentie te verbeteren.
- Android: Er is een [probleem opgelost](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) met de x86 Android-emulator in Android Studio.
- JavaScript: ondersteuning toegevoegd voor regio's in China met de `fromSubscription` API. Details [hier.](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-) 
- JavaScript: voeg meer foutinformatie toe voor verbindingsfouten van NodeJS.
        
**Voorbeelden**
- Unity: Het openbare voorbeeld van intentieherkenning is opgelost, waarbij het importeren van LUIS JSON is mislukt. Details [hier.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: Voorbeeld toegevoegd voor `Language ID` . Details [hier.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Covid19 verkorte tests:** Omdat we de afgelopen weken op afstand hebben gewerkt, konden we niet zoveel handmatige tests voor apparaatverificatie uitvoeren als normaal. We kunnen bijvoorbeeld geen microfooninvoer en sprekeruitvoer testen in Linux, iOS en macOS. We hebben geen wijzigingen aangebracht die volgens ons alles zouden kunnen hebben verbroken op deze platformen, en onze geautomatiseerde tests zijn allemaal geslaagd. Laat het ons weten op GitHub in het onwaarschijnlijke geval dat we iets [hebben gemist.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br> Bedankt voor uw voortdurende ondersteuning. Zoals altijd kunt u vragen of feedback plaatsen op [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) of [Stack Overflow.](https://stackoverflow.microsoft.com/questions/tagged/731)<br>
Blijf in orde!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: release van 2020 tot februari

**Nieuwe functies**

 - Python-pakketten toegevoegd ter ondersteuning van de nieuwe versie 3.8 van Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64-ondersteuning (C++, C#, Java, Python).
   > [!NOTE] 
   > Klanten moeten OpenSSL configureren volgens [deze instructies.](./how-to-configure-openssl-linux.md)
 - Linux ARM32-ondersteuning voor Debian en Ubuntu.
 - DialogServiceConnector ondersteunt nu een optionele parameter 'bot-id' in BotFrameworkConfig. Met deze parameter kan het gebruik van meerdere Direct Line Speech bots met één Azure-spraakresource. Zonder de opgegeven parameter wordt de standaardbot gebruikt (zoals wordt bepaald door Direct Line Speech configuratiepagina van het kanaal).
 - DialogServiceConnector heeft nu de eigenschap SpeechActivityTemplate. De inhoud van deze JSON-tekenreeks wordt door Direct Line Speech gebruikt om vooraf een groot aantal ondersteunde velden in te vullen in alle activiteiten die een Direct Line Speech-bot bereiken, inclusief activiteiten die automatisch worden gegenereerd als reactie op gebeurtenissen zoals spraakherkenning.
 - TTS gebruikt nu een abonnementssleutel voor verificatie, waardoor de latentie van de eerste byte van het eerste syntheseresultaat na het maken van een autorisatie wordt verkleind.
 - Spraakherkenningsmodellen bijgewerkt voor 19 locales voor een gemiddelde foutfrequentievermindering van woorden met 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). De nieuwe modellen brengen aanzienlijke verbeteringen met zich mee in meerdere domeinen, waaronder Dicteren, Call-Center transcriptie- en video-indexeringsscenario's.

**Opgeloste fouten**

 - Er is een fout opgelost waarbij gesprekstranscriber niet op de juiste manier werd verwacht in JAVA-API's 
 - Android x86 emulator-oplossing voor Xamarin [GitHub-probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Ontbrekende toevoegen (Get| Instellen)Eigenschapsmethoden op AudioConfig
 - Er is een TTS-fout opgelost waarbij de audioDataStream niet kan worden gestopt wanneer de verbinding mislukt
 - Het gebruik van een eindpunt zonder regio zou leiden tot USP-fouten voor conversatievertalers
 - Het genereren van id's in Universele Windows-toepassingen maakt nu gebruik van een op de juiste wijze uniek GUID-algoritme; Voorheen werd de standaardwaarde per ongeluk een stubbed-implementatie die vaak voor een grote reeks interacties zorgt.
 
 **Voorbeelden**
 
 - Unity-voorbeeld voor het gebruik van speech-SDK [met Unity-microfoon en pushmodusstreaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Andere wijzigingen**

 - [OpenSSL-configuratiedocumentatie bijgewerkt voor Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: release van 2020-januari

**Nieuwe functies**

- Gesprek met meerdere apparaten: verbind meerdere apparaten met hetzelfde spraak- of tekstgebaseerd gesprek en vertaal optioneel berichten die ertussen worden verzonden. Meer informatie in [dit artikel](multi-device-conversation.md). 
- Ondersteuning voor sleutelwoordherkenning toegevoegd voor Android .aar-pakket en ondersteuning toegevoegd voor x86- en x64-varianten. 
- Objective-C: `SendMessage` en methoden toegevoegd aan `SetMessageProperty` `Connection` object. Zie de documentatie [hier.](/objectivec/cognitive-services/speech/spxconnection)
- TTS C++ api ondersteunt nu als tekstinvoer voor synthese, waardoor het niet meer nodig is om een wstring te converteren naar een tekenreeks voordat deze wordt door gegeven `std::wstring` aan de SDK. Bekijk hier [de details.](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: [Taal-id](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) [en brontaal-configuratie](./how-to-specify-source-language.md?pivots=programming-language-csharp) zijn nu beschikbaar.
- JavaScript: Er is een functie toegevoegd aan het object om aangepaste berichten van de Speech Service als `Connection` callback door te `receivedServiceMessage` geven.
- JavaScript: er is ondersteuning toegevoegd `FromHost API` voor om eenvoudig te gebruiken met on-prem-containers en onafhankelijke clouds. Zie de documentatie [hier.](speech-container-howto.md)
- JavaScript: We houden ons nu aan een bijdrage van `NODE_TLS_REJECT_UNAUTHORIZED` [orgads.](https://github.com/orgads) Bekijk hier [de details.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)

**Wijzigingen die fouten veroorzaken**

- `OpenSSL` is bijgewerkt naar versie 1.1.1b en is statisch gekoppeld aan de Speech SDK-kernbibliotheek voor Linux. Dit kan een onderbreking veroorzaken als uw Postvak IN `OpenSSL` niet is geïnstalleerd in de map in het `/usr/lib/ssl` systeem. Raadpleeg onze [documentatie onder](how-to-configure-openssl-linux.md) speech-SDK-documenten om het probleem op te lossen.
- We hebben het gegevenstype dat voor C# wordt geretourneerd, gewijzigd van in zodat er toegang mogelijk is tot wanneer `WordLevelTimingResult.Offset` `int` `long` `WordLevelTimingResults` spraakgegevens langer zijn dan 2 minuten.
- `PushAudioInputStream` en `PullAudioInputStream` verzenden nu wav-headergegevens naar de Speech-service op basis van , optioneel opgegeven toen `AudioStreamFormat` ze werden gemaakt. Klanten moeten nu de [ondersteunde audio-invoerindeling gebruiken.](how-to-use-audio-input-streams.md) Andere indelingen krijgen suboptimale herkenningsresultaten of kunnen andere problemen veroorzaken. 

**Opgeloste fouten**

- Zie de `OpenSSL` update onder Belangrijke wijzigingen hierboven. We hebben zowel een onregelmatige crash als een prestatieprobleem opgelost (vergrendelingsprobleem bij hoge belasting) in Linux en Java. 
- Java: Verbeteringen aangebracht in het sluiten van objecten in scenario's met hoge gelijktijdigheid.
- Ons NuGet-pakket is opnieuw gestructureerd. We hebben de drie kopieën van en onder lib-mappen verwijderd, waardoor het NuGet-pakket kleiner en sneller te downloaden was en we hebben headers toegevoegd die nodig zijn om een aantal `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` C++-apps te compileren.
- Quickstart-voorbeelden zijn [hier opgelost.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Deze zijn afgesloten zonder uitzondering 'microfoon niet gevonden' weer te geven in Linux, macOS, Windows.
- SDK-crash opgelost met resultaten voor lange spraakherkenning op bepaalde codepaden, [zoals dit voorbeeld.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Er is een SDK-implementatiefout in de Azure Web App-omgeving opgelost om dit [klantprobleem op te lossen.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)
- Er is een TTS-fout opgelost tijdens het gebruik van meerdere `<voice>` tags of tags om dit `<audio>` [klantprobleem op te lossen.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Er is een TTS 401-fout opgelost wanneer de SDK wordt hersteld van de tijdelijk vastgezet SDK.
- JavaScript: Er is een cirkelvormige import van audiogegevens opgelost dankzij een bijdrage [van euirim](https://github.com/euirim). 
- JavaScript: ondersteuning toegevoegd voor het instellen van service-eigenschappen, zoals is toegevoegd in 1.7.
- JavaScript: er is een probleem opgelost waarbij een verbindingsfout kan leiden tot doorlopende, mislukte websocket-pogingen om opnieuw verbinding te maken.

**Voorbeelden**

- Er is hier een voorbeeld van sleutelwoordherkenning voor Android [toegevoegd.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)
- Hier is een TTS-voorbeeld toegevoegd voor het [serverscenario.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)
- Quickstarts voor gesprekken met meerdere apparaten voor C# en C++ zijn [hier toegevoegd.](quickstarts/multi-device-conversation.md)

**Andere wijzigingen**

- Geoptimaliseerde SDK-kernbibliotheekgrootte op Android.
- SDK in 1.9.0 en hoger ondersteunt zowel als typen in het versieveld voor spraakhandtekeningen `int` `string` voor gesprekstranscriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: release van 2019-november

**Nieuwe functies**

- Er is `FromHost()` een API toegevoegd om het gebruik van on-prem-containers en onafhankelijke clouds te gemaken.
- Automatic Source Taaldetectie voor spraakherkenning (in Java en C++)
- Object `SourceLanguageConfig` toegevoegd voor spraakherkenning, gebruikt om verwachte brontalen op te geven (in Java en C++)
- Ondersteuning `KeywordRecognizer` toegevoegd voor Windows (UWP), Android en iOS via de NuGet- en Unity-pakketten
- Java-API voor externe gesprekken toegevoegd om gesprektranscriptie uit te voeren in asynchrone batches.

**Wijzigingen die fouten veroorzaken**

- Functies van gespreks transcriber verplaatst onder naamruimte `Microsoft.CognitiveServices.Speech.Transcription` .
- Delen van de gesprekstranscribermethoden worden verplaatst naar een nieuwe `Conversation` klasse.
- Ondersteuning voor 32-bits (ARMv7 en x86) iOS is gestopt

**Opgeloste fouten**

- Oplossing voor crash als local `KeywordRecognizer` wordt gebruikt zonder een geldige abonnementssleutel voor de Speech-service

**Voorbeelden**

- Xamarin-voorbeeld voor `KeywordRecognizer`
- Unity-voorbeeld voor `KeywordRecognizer`
- Voorbeelden van C++ en Java voor automatische Taaldetectie.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: release van 2019-september

**Nieuwe functies**

- Bèta-ondersteuning toegevoegd voor Xamarin op Universeel Windows-platform (UWP), Android en iOS
- iOS-ondersteuning toegevoegd voor Unity
- Er `Compressed` is invoerondersteuning toegevoegd voor A Kante, Muieten, FLAC op Android, iOS en Linux
- Toegevoegd `SendMessageAsync` in klasse voor het verzenden van een bericht naar de `Connection` service
- Toegevoegd `SetMessageProperty` aan klasse voor het instellen van de eigenschap van een `Connection` bericht
- TTS-bindingen toegevoegd voor Java (JRE en Android), Python, Swift en Objective-C
- Met TTS is afspeelondersteuning toegevoegd voor macOS, iOS en Android.
- Informatie over 'woordgrens' toegevoegd voor TTS.

**Opgeloste fouten**

- Il2CPP-buildprobleem opgelost in Unity 2019 voor Android
- Probleem opgelost met onjuiste headers in wav-bestandsinvoer die onjuist worden verwerkt
- Er is een probleem opgelost met UUID's die niet uniek zijn in sommige verbindingseigenschappen
- Er zijn enkele waarschuwingen over null-waarden in de Swift-bindingen opgelost (mogelijk zijn er kleine codewijzigingen vereist)
- Er is een fout opgelost waardoor websocket-verbindingen tijdens netwerkbelasting werden gesloten
- Er is een probleem opgelost in Android dat soms resulteert in dubbele indruk-ID's die worden gebruikt door `DialogServiceConnector`
- Verbeteringen in de stabiliteit van verbindingen tussen interacties met meerdere beurten en de rapportage van fouten (via `Canceled` gebeurtenissen) wanneer deze optreden met `DialogServiceConnector`
- `DialogServiceConnector` sessiestarts bieden nu op de juiste wijze gebeurtenissen, inclusief bij het aanroepen `ListenOnceAsync()` tijdens een actieve `StartKeywordRecognitionAsync()`
- Er is een crash verholpen die is gekoppeld `DialogServiceConnector` aan activiteiten die worden ontvangen

**Voorbeelden**

- Quickstart voor Xamarin
- CPP-snelstart bijgewerkt met Linux ARM64-informatie
- Unity-quickstart bijgewerkt met iOS-informatie

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: release van 2019 tot juni

**Voorbeelden**

- Quickstartvoorbeelden voor Text To Speech in UWP en Unity
- Quickstart-voorbeeld voor Swift in iOS
- Unity-voorbeelden voor spraak& Intentieherkenning en vertaling
- Quickstart-voorbeelden bijgewerkt voor `DialogServiceConnector`

**Verbeteringen/wijzigingen**

- Dialoogvensternaamruimte:
  - De naam van `SpeechBotConnector` is gewijzigd in `DialogServiceConnector`
  - De naam van `BotConfig` is gewijzigd in `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` opnieuw is gebruikt voor `DialogServiceConfig::FromBotSecret()`
  - Alle bestaande Direct Line Speech-clients worden na de naamsnoeming nog steeds ondersteund
- TTS REST-adapter bijwerken voor ondersteuning van proxy, permanente verbinding
- Foutbericht verbeteren wanneer een ongeldige regio wordt doorgegeven
- Swift/Objective-C:
  - Verbeterde foutrapportage: Methoden die tot een fout kunnen leiden, zijn nu aanwezig in twee versies: een die een object beschikbaar maakt voor foutafhandeling en een die een uitzondering `NSError` veroorzaakt. Eerstgenoemde wordt blootgesteld aan Swift. Deze wijziging vereist aanpassingen aan bestaande Swift-code.
  - Verbeterde gebeurtenisafhandeling

**Opgeloste fouten**

- Oplossing voor TTS: waar `SpeakTextAsync` in de toekomst wordt geretourneerd zonder te wachten tot de rendering van audio is voltooid
- Oplossing voor het inschakelen van volledige taalondersteuning voor tekenreeksen in C#
- Oplossing voor het probleem met de .NET Core-app voor het laden van een kernbibliotheek met net461-doel-framework in voorbeelden
- Oplossing voor incidentele problemen bij het implementeren van native bibliotheken in de uitvoermap in voorbeelden
- Oplossing voor betrouwbaar sluiten van websockocke
- Oplossing voor mogelijke crash tijdens het openen van een verbinding onder zware belasting in Linux
- Oplossing voor ontbrekende metagegevens in de frameworkbundel voor macOS
- Oplossing voor problemen met `pip install --user` in Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Dit is een release voor het oplossen van fouten en is alleen van invloed op de native/beheerde SDK. Dit heeft geen invloed op de JavaScript-versie van de SDK.

**Opgeloste fouten**

- Oplossing voor FromSubscription bij gebruik met gesprektranscriptie.
- Er is een fout opgelost in het herkennen van trefwoorden voor spraakassistenten.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: release van 2019-mei

**Nieuwe functies**

- Trefwoord spotting (KWS) is nu beschikbaar voor Windows en Linux. KWS-functionaliteit werkt mogelijk met elk type microfoon, officiële KWS-ondersteuning, maar is momenteel beperkt tot de microfoon matrices in de Azure Kinect DK hardware of de Speech Devices SDK.
- Functionaliteit voor woordgroepenhints is beschikbaar via de SDK. Klik [hier](./get-started-speech-to-text.md) voor meer informatie.
- Gesprektranscriptiefunctionaliteit is beschikbaar via de SDK. Kijk [hier.](./conversation-transcription.md)
- Voeg ondersteuning toe voor spraakassistenten met behulp van Direct Line Speech kanaal.

**Voorbeelden**

- Voorbeelden toegevoegd voor nieuwe functies of nieuwe services die worden ondersteund door de SDK.

**Verbeteringen/wijzigingen**

- Er zijn verschillende recognizer-eigenschappen toegevoegd om servicegedrag of serviceresultaten aan te passen (zoals het maskeren van grof taalgebruik en andere).
- U kunt de kiezer nu configureren via de standaardconfiguratie-eigenschappen, zelfs als u de recognizer hebt `FromEndpoint` gemaakt.
- Objective-C: `OutputFormat` de eigenschap is toegevoegd aan `SPXSpeechConfiguration` .
- De SDK ondersteunt nu Debian 9 als een Linux-distributie.

**Opgeloste fouten**

- Er is een probleem opgelost waarbij de sprekerresource te vroeg werd geïnstrueerd in tekst-naar-spraak.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Dit is een release voor het oplossen van fouten en is alleen van invloed op de native/beheerde SDK. Dit heeft geen invloed op de JavaScript-versie van de SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Dit is een alleen-JavaScript-release. Er zijn geen functies toegevoegd. De volgende oplossingen zijn aangebracht:

- Voorkomen dat webpack https-proxy-agent laadt.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: release van 2019-april

**Nieuwe functies**

- De SDK ondersteunt nu de text-to-speech-service als een bètaversie. Het wordt ondersteund in Windows en Linux Desktop vanuit C++ en C#. Raadpleeg het overzicht van [tekst-naar-spraak voor meer informatie.](text-to-speech.md#get-started)
- De SDK ondersteunt nu MP3- en Opus/OGG-audiobestanden als stroominvoerbestanden. Deze functie is alleen beschikbaar in Linux via C++ en C# en is momenteel beschikbaar als bètaversie (meer informatie [hier).](how-to-use-codec-compressed-audio-input-streams.md)
- De Speech-SDK voor Java, .NET Core, C++ en Objective-C heeft macOS-ondersteuning gekregen. De Objective-C-ondersteuning voor macOS is momenteel beschikbaar als bètaversie.
- iOS: De Speech SDK voor iOS (Objective-C) is nu ook gepubliceerd als een CocoaPod.
- JavaScript: ondersteuning voor een niet-standaardmicrofoon als invoerapparaat.
- JavaScript: proxyondersteuning voor Node.js.

**Voorbeelden**

- Voorbeelden voor het gebruik van de Speech-SDK met C++ en met Objective-C in macOS zijn toegevoegd.
- Voorbeelden die het gebruik van de text-to-speech-service demonstreren, zijn toegevoegd.

**Verbeteringen/wijzigingen**

- Python: Aanvullende eigenschappen van herkenningsresultaten worden nu beschikbaar gemaakt via de `properties` eigenschap .
- Voor aanvullende ondersteuning voor ontwikkeling en foutopsporing kunt u SDK-logboekregistratie en diagnostische gegevens omleiden naar een logboekbestand (hier vindt u meer [informatie).](how-to-use-logging.md)
- JavaScript: prestaties van audioverwerking verbeteren.

**Opgeloste fouten**

- Mac/iOS: Een fout die heeft geleid tot een lange wachttijd wanneer er geen verbinding met de Speech-service tot stand kon worden gebracht, is opgelost.
- Python: foutafhandeling voor argumenten in Python-callbacks verbeteren.
- JavaScript: Foutieve statusrapportage voor spraak beëindigd op RequestSession opgelost.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: 2019-februari vernieuwen

Dit is een release voor het oplossen van fouten en is alleen van invloed op de native/beheerde SDK. Dit heeft geen invloed op de JavaScript-versie van de SDK.

**Opgeloste fout**

- Er is een geheugenlek opgelost bij het gebruik van microfooninvoer. Stroom- of bestandsinvoer wordt niet beïnvloed.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: release van 2019-februari

**Nieuwe functies**

- De Speech SDK ondersteunt de selectie van de invoermicrofoon via de `AudioConfig` klasse . Hiermee kunt u audiogegevens streamen naar de Speech-service vanaf een niet-standaardmicrofoon. Zie de documentatie waarin de selectie van audio-invoerapparaat wordt [beschreven voor meer informatie.](how-to-select-audio-input-devices.md) Deze functie is nog niet beschikbaar via JavaScript.
- De Speech SDK ondersteunt nu Unity in een bètaversie. Geef feedback via de sectie probleem in de [GitHub-voorbeeldopslagplaats](https://aka.ms/csspeech/samples). Deze release ondersteunt Unity op Windows x86 en x64 (desktop- of Universeel Windows-platform-toepassingen) en Android (ARM32/64, x86). Meer informatie is beschikbaar in onze [Unity-quickstart.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)
- Het bestand `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (verzonden in eerdere versies) is niet meer nodig. De functionaliteit is nu geïntegreerd in de kern-SDK.

**Voorbeelden**

De volgende nieuwe inhoud is beschikbaar in onze [voorbeeldopslagplaats:](https://aka.ms/csspeech/samples)

- Aanvullende voorbeelden voor `AudioConfig.FromMicrophoneInput` .
- Aanvullende Python-voorbeelden voor intentieherkenning en -vertaling.
- Aanvullende voorbeelden voor het gebruik van `Connection` het -object in iOS.
- Aanvullende Java-voorbeelden voor vertaling met audio-uitvoer.
- Nieuw voorbeeld voor gebruik van de [batchtranscriptie REST API](batch-transcription.md).

**Verbeteringen/wijzigingen**

- Python
  - Verbeterde verificatie van parameters en foutberichten in `SpeechConfig` .
  - Voeg ondersteuning toe voor het `Connection` -object.
  - Ondersteuning voor 32-bits Python (x86) in Windows.
  - De Speech SDK voor Python is niet meer beschikbaar als bètaversie.
- iOS
  - De SDK is nu gebaseerd op iOS SDK versie 12.1.
  - De SDK ondersteunt nu iOS-versies 9.2 en hoger.
  - Verbeter de referentiedocumentatie en los verschillende eigenschapsnamen op.
- Javascript
  - Voeg ondersteuning toe voor het `Connection` -object.
  - Typedefinitiebestanden toevoegen voor gebundelde JavaScript
  - Initiële ondersteuning en implementatie voor woordgroepenhints.
  - Eigenschappenverzameling retourneren met service-JSON voor herkenning
- Windows-DLL's bevatten nu een versieresource.
- Als u een kiezer `FromEndpoint` maakt, kunt u parameters rechtstreeks aan de eindpunt-URL toevoegen. Met `FromEndpoint` kunt u de kiezer niet configureren via de standaardconfiguratie-eigenschappen.

**Opgeloste fouten**

- Lege proxy-gebruikersnaam en proxywachtwoord zijn niet correct verwerkt. Als u bij deze release de proxy-gebruikersnaam en het proxywachtwoord in een lege tekenreeks in stelt, worden deze niet verzonden wanneer u verbinding maakt met de proxy.
- SessionId's die door de SDK zijn gemaakt, waren niet altijd willekeurig voor sommige &nbsp; talen/omgevingen. Initialisatie van willekeurige generator toegevoegd om dit probleem op te lossen.
- Verwerking van autorisatie-token verbeteren. Als u een autorisatie-token wilt gebruiken, geeft u op in de `SpeechConfig` en laat u de abonnementssleutel leeg. Maak vervolgens de kiezer zoals u gewend bent.
- In sommige gevallen is `Connection` het object niet correct vrijgegeven. Dit probleem is opgelost.
- Het JavaScript-voorbeeld is opgelost om audio-uitvoer voor vertaalsynthese ook in Safari te ondersteunen.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Dit is een alleen-JavaScript-release. Er zijn geen functies toegevoegd. De volgende oplossingen zijn aangebracht:

- Einde van stream aan turn.end, niet aan speech.end.
- Er is een fout opgelost in de audiopomp die de volgende verzendplanning niet heeft gepland als de huidige verzend mislukt.
- Oplossing voor continue herkenning met een auth-token.
- Opgeloste fout voor verschillende kiezer/eindpunten.
- Verbeteringen in de documentatie.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: release van 2018-december

**Nieuwe functies**

- Python
  - De bètaversie van Python-ondersteuning (3.5 en hoger) is beschikbaar bij deze release. Zie hier voor meer informatie](quickstart-python.md).
- Javascript
  - De Speech-SDK voor JavaScript is open source. De broncode is beschikbaar op [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - We ondersteunen nu Node.js. Meer informatie vindt u [hier.](./get-started-speech-to-text.md)
  - De lengtebeperking voor audiosessies is verwijderd. Het opnieuw verbinden vindt automatisch plaats onder de omslag.
- `Connection` Object
  - Vanuit de `Recognizer` hebt u toegang tot een `Connection` -object. Met dit object kunt u de serviceverbinding expliciet initiëren en u abonneren om verbinding te maken en gebeurtenissen te verbreken.
    (Deze functie is nog niet beschikbaar in JavaScript en Python.)
- Ondersteuning voor Ubuntu 18.04.
- Android
  - ProGuard-ondersteuning ingeschakeld tijdens het genereren van de APK.

**Verbeteringen**

- Verbeteringen in het gebruik van interne threads, waardoor het aantal threads, vergrendelingen en mutexen wordt verkleind.
- Verbeterde foutrapportage / informatie. In verschillende gevallen zijn foutberichten niet helemaal naar buiten doorgegeven.
- Ontwikkelingsafhankelijkheden in JavaScript bijgewerkt voor het gebruik van bijgewerkte modules.

**Opgeloste fouten**

- Geheugenlekken opgelost omdat een type niet overeen komt in `RecognizeAsync` .
- In sommige gevallen werden uitzonderingen gelekt.
- Geheugenlekken in vertaalgebeurtenisargumenten oplossen.
- Er is een vergrendelingsprobleem opgelost bij het opnieuw verbinden in langlopende sessies.
- Er is een probleem opgelost dat kan leiden tot een ontbrekend eindresultaat voor mislukte vertalingen.
- C#: Als er geen bewerking in de hoofdthread werd verwacht, was het mogelijk dat de recognizer kon worden verwijderd voordat de `async` async-taak werd voltooid.
- Java: Er is een probleem opgelost dat leidde tot een crash van de Java-VM.
- Objective-C: Vaste enumtoewijzing; RecognizedIntent is geretourneerd in plaats van `RecognizingIntent` .
- JavaScript: stel de standaarduitvoerindeling in op 'eenvoudig' in `SpeechConfig` .
- JavaScript: inconsistentie tussen eigenschappen van het configuratieobject in JavaScript en andere talen verwijderen.

**Voorbeelden**

- Er zijn verschillende voorbeelden bijgewerkt en opgelost (bijvoorbeeld uitvoerstemmen voor vertaling, enzovoort).
- Er Node.js voorbeelden toegevoegd in de [voorbeeldopslagplaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nieuwe functies**

- Ondersteuning voor Android x86/x64.
- Proxyondersteuning: In het -object kunt u nu een functie aanroepen om de `SpeechConfig` proxygegevens (hostnaam, poort, gebruikersnaam en wachtwoord) in te stellen. Deze functie is nog niet beschikbaar op iOS.
- Verbeterde foutcode en -berichten. Als een herkenning een fout retourneert, is deze al ingesteld (in geannuleerde `Reason` gebeurtenis) of `CancellationDetails` (als herkenningsresultaat) op `Error` . De geannuleerde gebeurtenis bevat nu twee extra leden, `ErrorCode` en `ErrorDetails` . Als de server aanvullende foutgegevens heeft geretourneerd met de gerapporteerde fout, is deze nu beschikbaar in de nieuwe leden.

**Verbeteringen**

- Aanvullende verificatie toegevoegd in de recognizer-configuratie en extra foutbericht toegevoegd.
- Verbeterde verwerking van langdurige stilte in het midden van een audiobestand.
- NuGet-pakket: voor .NET Framework projecten voorkomt u dat er wordt gebouwd met AnyCPU-configuratie.

**Opgeloste fouten**

- Er zijn verschillende uitzonderingen opgelost die in recognizers zijn gevonden. Bovendien worden uitzonderingen af en geconverteerd naar `Canceled` een gebeurtenis.
- Herstel een geheugenlek in eigenschapsbeheer.
- Er is een fout opgelost waarbij een audio-invoerbestand de kiezer kon crashen.
- Er is een fout opgelost waarbij gebeurtenissen konden worden ontvangen na een sessie-stopgebeurtenis.
- Er zijn enkele racevoorwaarden in threading opgelost.
- Er is een iOS-compatibiliteitsprobleem opgelost dat kan leiden tot een crash.
- Stabiliteitsverbeteringen voor ondersteuning van Android-microfoons.
- Er is een fout opgelost waarbij een herkenning in JavaScript de herkenningstaal negeerde.
- Er is een fout opgelost waardoor het `EndpointId` instellen van (in sommige gevallen) niet mogelijk was in JavaScript.
- Parameter volgorde gewijzigd in AddIntent in JavaScript en ontbrekende `AddIntent` JavaScript-handtekening toegevoegd.

**Voorbeelden**

- C++ en C#-voorbeelden toegevoegd voor het gebruik van pull- en pushstreams in de [voorbeeldopslagplaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Verbeteringen in de betrouwbaarheid en oplossingen voor fouten:

- Mogelijke fatale fout opgelost vanwege racevoorwaarde bij het uitstellen van recognizer
- Mogelijke onveilige fout opgelost wanneer niet-beveiligde eigenschappen optreden.
- Aanvullende fout- en parametercontrole toegevoegd.
- Objective-C: Er is een mogelijke fatale fout opgelost die wordt veroorzaakt door het overschrijven van namen in NSString.
- Objective-C: Aangepaste zichtbaarheid van API
- JavaScript: Opgelost met betrekking tot gebeurtenissen en hun nettoladingen.
- Verbeteringen in de documentatie.

In onze [voorbeeldopslagplaats](https://aka.ms/csspeech/samples)is een nieuw voorbeeld voor JavaScript toegevoegd.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: release van 2018-september

**Nieuwe functies**

- Ondersteuning voor Objective-C op iOS. Bekijk onze [Objective-C-quickstart voor iOS.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone)
- Ondersteuning voor JavaScript in de browser. Bekijk onze [JavaScript-quickstart](./get-started-speech-to-text.md).

**Wijzigingen die fouten veroorzaken**

- Met deze release worden een aantal belangrijke wijzigingen geïntroduceerd.
  Controleer [deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: release van 2018 tot augustus

**Nieuwe functies**

- UWP-apps die zijn gebouwd met de Speech-SDK, kunnen nu slagen voor de Windows App Certification Kit (CERTIFICATION Kit).
  Bekijk de [SNELSTART VOOR UWP.](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp)
- Ondersteuning voor .NET Standard 2.0 op Linux (Ubuntu 16.04 x64).
- Experimenteel: ondersteuning voor Java 8 in Windows (64-bits) en Linux (Ubuntu 16.04 x64).
  Bekijk de [Java Runtime Environment quickstart](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre).

**Functionele wijziging**

- Meer informatie over foutdetails over verbindingsfouten beschikbaar maken.

**Wijzigingen die fouten veroorzaken**

- In Java (Android) is voor `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` de functie geen padparameter meer vereist. Het pad wordt nu automatisch gedetecteerd op alle ondersteunde platforms.
- De get-accessor van de eigenschap `EndpointUrl` in Java en C# is verwijderd.

**Opgeloste fouten**

- In Java is het resultaat van audiosynthese op de vertaal-recognizer nu geïmplementeerd.
- Er is een fout opgelost die inactieve threads en een groter aantal open en ongebruikte sockets kan veroorzaken.
- Er is een probleem opgelost waarbij een langdurige herkenning kan worden beëindigd tijdens de overdracht.
- Er is een racevoorwaarde opgelost bij het afsluiten van de kiezer.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: release van 2018 tot juli

**Nieuwe functies**

- Ondersteuning voor Android-platform (API 23: Android 6.0 Marshmallow of hoger). Bekijk de [Android-quickstart](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android).
- Ondersteuning voor .NET Standard 2.0 in Windows. Bekijk de [snelstart voor .NET Core.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)
- Experimenteel: ondersteuning voor UWP in Windows (versie 1709 of hoger).
  - Bekijk de [SNELSTART VOOR UWP.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)
  - Houd er rekening mee dat UWP-apps die zijn gebouwd met de Speech-SDK nog niet zijn geslaagd voor de Windows App Certification Kit (CERTIFICATION Kit).
- Ondersteuning voor langdurige herkenning met automatisch opnieuw verbinden.

**Functionele wijzigingen**

- `StartContinuousRecognitionAsync()` ondersteunt langdurige herkenning.
- Het herkenningsresultaat bevat meer velden. Ze worden verschoven van het audio-begin en de duur (beide in tikken) van de herkende tekst en aanvullende waarden die de herkenningsstatus vertegenwoordigen, `InitialSilenceTimeout` bijvoorbeeld en `InitialBabbleTimeout` .
- Ondersteuning voor AuthorizationToken voor het maken van factory-exemplaren.

**Wijzigingen die fouten veroorzaken**

- `NoMatch`Herkenningsgebeurtenissen: het gebeurtenistype is samengevoegd in de `Error` gebeurtenis.
- De naam speechOutputFormat in C# is gewijzigd in om `OutputFormat` op één lijn te blijven met C++.
- Het retourtype van sommige methoden van de `AudioInputStream` interface is enigszins gewijzigd:
  - In Java `read` retourneert de methode nu `long` in plaats van `int` .
  - In C# `Read` retourneert de methode nu `uint` in plaats van `int` .
  - In C++ retourneren `Read` de methoden en nu in plaats van `GetFormat` `size_t` `int` .
- C++: Exemplaren van audio-invoerstromen kunnen nu alleen worden doorgegeven als een `shared_ptr` .

**Opgeloste fouten**

- Onjuiste retourwaarden in het resultaat opgelost wanneer er `RecognizeAsync()` een times-out is.
- De afhankelijkheid van Media Foundation-bibliotheken in Windows is verwijderd. De SDK maakt nu gebruik van Core Audio-API's.
- Oplossing voor documentatie: Er is een pagina [met regio's](regions.md) toegevoegd om de ondersteunde regio's te beschrijven.

**Bekend probleem**

- De Speech SDK voor Android rapporteert geen resultaten voor spraaksynthese voor vertaling. Dit probleem wordt opgelost in de volgende release.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: release van 2018-juni

**Functionele wijzigingen**

- AudioInputStream

  Een kiezer kan nu een stroom als audiobron gebruiken. Zie de gerelateerde handleiding voor [meer informatie.](how-to-use-audio-input-streams.md)

- Gedetailleerde uitvoerindeling

  Wanneer u een `SpeechRecognizer` maakt, kunt u een `Detailed` aanvraag- of `Simple` uitvoerindeling aanvragen. De bevat een betrouwbaarheidsscore, herkende tekst, onbewerkte lexicale vorm, genormaliseerde vorm en genormaliseerde vorm `DetailedSpeechRecognitionResult` met gemaskeerd grof taalgebruik.

**Wijziging die grote veranderingen doorbreekt**

- Gewijzigd in `SpeechRecognitionResult.Text` van `SpeechRecognitionResult.RecognizedText` in C#.

**Opgeloste fouten**

- Er is een mogelijk callback-probleem opgelost in de USP-laag tijdens het afsluiten.
- Als een recognizer een audio-invoerbestand heeft verbruikt, was het langer dan nodig om de bestandsingang vast te houden.
- Er zijn verschillende impasses tussen de berichtpomp en de kiezer verwijderd.
- Brand een `NoMatch` resultaat wanneer er een time-out is voor het antwoord van de service.
- De Media Foundation-bibliotheken in Windows worden vertraagd geladen. Deze bibliotheek is alleen vereist voor microfooninvoer.
- De uploadsnelheid voor audiogegevens is beperkt tot ongeveer twee keer de oorspronkelijke audiosnelheid.
- In Windows hebben C# .NET-assemblies nu een sterke naam.
- Oplossing voor documentatie: `Region` is vereiste informatie voor het maken van een kiezer.

Er zijn meer voorbeelden toegevoegd die voortdurend worden bijgewerkt. Zie de [GitHub-opslagplaats speech-SDK-voorbeelden](https://aka.ms/csspeech/samples)voor de meest recente set voorbeelden.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: release van 2018-mei

Deze release is de eerste openbare preview-versie van de Cognitive Services Speech SDK.
