---
title: Release opmerkingen-spraak service
titleSuffix: Azure Cognitive Services
description: Een actief logboek van de functie versies, verbeteringen, fout oplossingen en bekende problemen met betrekking tot de spraak service.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 6b03458ce5ea4286e59de8d0e4b35b860088ca91
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500764"
---
# <a name="speech-service-release-notes"></a>Release opmerkingen bij de spraak service

## <a name="speech-sdk-1150-2021-january-release"></a>Speech SDK 1.15.0:2021-januari release

**Opmerking**: de spraak-SDK in Windows is afhankelijk van de gedeelde micro soft Visual C++ Redistributable voor visual studio 2015, 2017 en 2019. Down load deze [hier](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Overzicht van hooglichten**
- Kleiner geheugen en schijf ruimte waardoor de SDK efficiënter wordt.
- Er zijn hoogwaardige uitvoer indelingen beschikbaar voor de aangepaste preview-versie van Neural Voice private.
- De intentie herkenning kan nu retour neren meer dan het hoogste doel, waardoor u de mogelijkheid hebt om een afzonderlijke beoordeling te maken over de intentie van uw klant.
- Uw Voice-assistent of bot is nu gemakkelijker te installeren en u kunt deze niet meteen meer laten Luis teren en meer controle uitoefenen over hoe deze reageert op fouten.
- Verbeterd bij de prestaties van het apparaat door compressie optioneel te maken.
- Gebruik de Speech SDK op Windows ARM/ARM64.
- Verbeterde fout opsporing op laag niveau.
- De functie voor het beoordelen van uitspraak is nu meer beschikbaar.
- Verschillende oplossingen voor problemen die u, onze gewaardeerde klanten, hebben gevlagd op GitHub! Dank u! Blijf de feedback ontvangen.

**Rijke**
- De Speech SDK is nu efficiënter en lichter. We hebben een poging tot het uitvoeren van meerdere releases gestart om het geheugen gebruik en de schijf footprint van de Speech SDK te verminderen. Als eerste stap hebben we aanzienlijke vermindering van de bestands grootte in gedeelde bibliotheken op de meeste platforms gemaakt. Vergeleken met de 1,14-release:
  - 64-bits UWP-compatibele Windows-bibliotheken zijn 30% kleiner.
  - 32-bits Windows-bibliotheken zien nog geen verbeteringen voor de grootte.
  - Linux-bibliotheken zijn 20-25% kleiner.
  - Android-bibliotheken zijn 3-5% kleiner.

**Nieuwe functies**
- **Alle**: nieuwe 48KHz-uitvoer indelingen die beschikbaar zijn voor de persoonlijke preview van aangepaste Neural-stem via de TTS Speech Synthesis API: Audio48Khz192KBitRateMonoMp3, audio-48KHz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48KHz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, RAW-48KHz-16-mono-PCM, Riff48Khz16BitMonoPcm, RIFF-48KHz-16-mono-PCM.
- **Alle**: aangepaste spraak is ook eenvoudiger te gebruiken. Er is ondersteuning toegevoegd voor het instellen van aangepaste spraak via `EndpointId` ([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [Java script](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [objectief-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). Voordat deze wijziging werd doorgebracht, moeten aangepaste Voice-gebruikers de eind punt-URL instellen via de- `FromEndpoint` methode. Klanten kunnen de methode nu gebruiken `FromSubscription` zoals bij open bare stemmen en vervolgens de implementatie-id opgeven door in te stellen `EndpointId` . Dit vereenvoudigt het instellen van aangepaste stemmen. 
- **C++/c #/Java/Objective-C/python**: haalt meer op dan het hoogste doel van `IntentRecognizer` . Het ondersteunt nu het configureren van het JSON-resultaat met alleen de intenties en niet alleen de bovenste Score intentie via de `LanguageUnderstandingModel FromEndpoint` methode met behulp van de `verbose=true` URI-para meter. Hiermee wordt [github-probleem #880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880). Zie de bijgewerkte documentatie [hier](./quickstarts/intent-recognition.md#add-a-languageunderstandingmodel-and-intents).
- **C++/c #/Java**: u kunt het Luis teren van uw Voice Assistant of bot voor komen. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) beschikt nu over een `StopListeningAsync()` methode om te worden meegeleverd `ListenOnceAsync()` . Hiermee wordt de audio-opname onmiddellijk gestopt en wordt er gewacht op een resultaat, waardoor het ideaal is voor gebruik met de knop nu stoppen.
- **C++/c #/Java/JavaScript**: Zorg ervoor dat uw Voice Assistant of bot beter reageert op onderliggende systeem fouten. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java script](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) heeft nu een nieuwe `TurnStatusReceived` gebeurtenis-handler. Deze optionele gebeurtenissen komen overeen met elke [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext?view=botbuilder-dotnet-stable) oplossing op de bot en rapporten genereren fouten wanneer ze plaatsvinden, bijvoorbeeld als gevolg van een onverwerkte uitzonde ring, time-out of netwerk verwijdering tussen directe lijn spraak en de bot. `TurnStatusReceived` maakt het gemakkelijker om te reageren op fout voorwaarden. Als een bot bijvoorbeeld te lang duurt voor een back-end-database query (bijvoorbeeld het opzoeken van een product), kan `TurnStatusReceived` de client vragen om te worden gevraagd met ' Sorry ', is dat niet helemaal, kan het zijn dat u het opnieuw probeert ' of iets dergelijks.
- **C++/c #**: gebruik de Speech SDK op meer platforms. Het [Speech SDK nuget-pakket](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) biedt nu ondersteuning voor Windows arm/ARM64 Desktop native binaire bestanden (UWP werd al ondersteund) om de spraak-SDK handiger te maken voor meer computer typen.
- **Java**: [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) heeft nu een `setSpeechActivityTemplate()` methode die per ongeluk is uitgesloten van de taal eerder. Dit komt overeen met het instellen `Conversation_Speech_Activity_Template` van de eigenschap en om te vragen om alle toekomstige bot-Framework activiteiten die afkomstig zijn van de direct line Speech-Service, waarbij de gegeven inhoud wordt samengevoegd in de JSON-nettoladingen.
- **Java**: verbeterde fout opsporing op laag niveau. De [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) klasse heeft nu een `MessageReceived` gebeurtenis, vergelijkbaar met andere programmeer talen (C++, C#). Deze gebeurtenis biedt toegang op laag niveau voor inkomende gegevens van de service en kan nuttig zijn voor diagnoses en fout opsporing.
- **Java script**: eenvoudigere installatie voor spraak assistenten en botsingen [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) , die nu `fromHost()` en `fromEndpoint()` Factory-methoden hebben waarmee het gebruik van aangepaste service locaties en hand matig ingestelde eigenschappen wordt vereenvoudigd. We hebben ook een standaard optionele specificatie van `botId` voor het gebruik van een niet-standaard bot over de configuratie fabrieken.
- **Java script**: verbeterde prestaties van het apparaat via toegevoegde string Control-eigenschap voor WebSocket-compressie. Vanwege de prestaties zijn de WebSocket-compressie standaard uitgeschakeld. Dit kan opnieuw worden ingeschakeld voor scenario's met lage band breedte. Meer informatie [vindt u hier](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid). Hiermee wordt [github-probleem #242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242).
- **Java script**: er is ondersteuning toegevoegd voor de beoordeling van de uitspraak om de spraak uitspraak te kunnen evalueren. Bekijk [hier](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)de Snelstartgids.

**Opgeloste fouten**
- **Alle** (behalve java script): er is een regressie in versie 1,14 opgelost, waardoor er te veel geheugen is toegewezen door de herkenner.
- **C++**: er is een probleem opgetreden bij het opschonen van een garbagecollection met `DialogServiceConnector` , [github issue #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C#**: er is een probleem opgelost waarbij de thread wordt afgesloten, waardoor objecten worden geblokkeerd voor een seconde wanneer deze worden verwijderd.
- **C++/c #/Java**: er is een uitzonde ring opgetreden bij het voor komen van een toepassing voor het instellen van een spraak autorisatie token of activiteiten sjabloon meer dan één keer op een `DialogServiceConnector` .
- **C++/c #/Java**: er is een crash van een herkenner opgelost vanwege een race voorwaarde in Teardown.
- **Java script**: [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) voldoet niet aan de optionele `botId` para meter die is opgegeven in `BotFrameworkConfig` fabrieken. Hierdoor is het nood zakelijk om de `botId` query teken reeks parameter hand matig in te stellen voor gebruik van een niet-standaard bot. De fout is gecorrigeerd en `botId` waarden die aan `BotFrameworkConfig` de fabrieken worden gegeven, worden gehonoreerd en gebruikt, inclusief de nieuwe `fromHost()` en `fromEndpoint()` toevoegingen. Dit geldt ook voor de `applicationId` para meter voor `CustomCommandsConfig` .
- **Java script**: [probleem met github opgelost #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), zodat herkenner object opnieuw kan worden gebruikt.
- **Java script**: een probleem opgelost waarbij de SKD `speech.config` meerdere keren is verzonden in één TTS-sessie, waarbij de band breedte wordt verspild.
- **Java script**: vereenvoudigde fout afhandeling op microfoon autorisatie, waardoor een duidelijker bericht wordt weer gegeven wanneer de gebruiker geen microfoon invoer in hun browser heeft toegestaan.
- **Java script**: [probleem met vaste github #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) waarbij type fouten in `ConversationTranslator` en `ConversationTranscriber` een compilatie fout veroorzaakt voor type script-gebruikers.
- **Doel-C**: er is een probleem opgelost waarbij de gstreamer-build is mislukt voor Ios op Xcode 11,4, waarbij [GitHub een probleem #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python**: vast [GitHub-probleem #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870), het verwijderen van ' DeprecationWarning: de module IMP is afgeschaft ten gunste van importlib '.

**Voorbeelden**
- [Van het bestands voorbeeld voor de Java script-browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) gebruikt nu bestanden voor spraak herkenning. Hiermee wordt [github-probleem #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884).

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Speech CLI (ook wel SPX genoemd): 2021-januari release

**Nieuwe functies**
- Speech CLI is nu beschikbaar als een [NuGet-pakket](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) en kan worden geïnstalleerd via .net cli als .net-algemeen hulp programma dat u kunt aanroepen vanuit de shell/opdracht regel.
- De [Custom speech DevOps-sjabloon opslag plaats](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) is bijgewerkt voor het gebruik van spraak-CLI voor de Custom speech-werk stromen.

**COVID-19-** afkortings test: als de voortdurende Pandemic voor het uitvoeren van de werkzaamheden van onze technici nog steeds nodig is, zijn de hand matige verificatie scripts van vóór Pandemic aanzienlijk lager. We testen op minder apparaten met minder configuraties en de kans op specifieke problemen met de omgeving kan worden verg root. Er wordt nog steeds grondig gecontroleerd met een grote set automatisering. In het onwaarschijnlijke geval dat we iets hebben gemist, laat het ons weten op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Blijf op de hoogte.

## <a name="text-to-speech-2020-december-release"></a>Tekst-naar-spraak 2020-december release

**Nieuwe Neural stemmen in GA en preview**

51 nieuwe stemmen uitgebracht voor een totaal van 129 Neural stemmen tussen 54 talen/land instellingen:

- **46 nieuwe stemmen in ga land instellingen**: Shakir in het `ar-EG` Arabisch (Egypte), Hamed in het `ar-SA` Arabisch (Saoedi-Arabië), Borislav in `bg-BG` Bulgaars (Bulgarije), Joana in `ca-ES` Catalaans (Spanje), Antonin in `cs-CZ` Tsjechië, Jeppe in het `da-DK` Deens (Denemarken), Jonas in het `de-AT` Duits (Oosten rijk), Jan in het `de-CH` Duits (Zwitser land), Nestoras in het `el-GR` Grieks (Grieken land), Liam in het `en-CA` Engels (Canada), Connor in het `en-IE` Engels (Ierland), Madhur in `en-IN` Hindi (India), Mohan in Telugu (India), Prabhat in het Engels (India), `en-IN` `en-IN` Valluvar in `en-IN` Tamil (India), Enric in `es-ES` Catalaans (Spanje), Kert in `et-EE` Estlands (Estland), Harri in `fi-FI` Fins (Finland), Selma in `fi-FI` Fins (Finland), Fabrice in `fr-CH` Frans (Zwitser land), Colm in `ga-IE` Ierland (Ierland), AVRI in het `he-IL` Hebreeuws (Israël), Srecko in `hr-HR` Kroatisch (Kroatië), Tamas in `hu-HU` Hong aars (Hongarije), Gadis in `id-ID` Indonesisch (Indonesië), Leonas in `lt-LT` Litouws (Litouwen), Nils in `lv-LV` Lets (Letland), Osman in `ms-MY` Maleis (Maleisië), Joseph in `mt-MT` Maltees (Malta) , Finn in `nb-NO` Noors, Bokmål (Noor wegen), Pernille in `nb-NO` Noors, Bokmål (Noor wegen), Fenna in het `nl-NL` Nederlands (Nederland), Maarten `nl-NL` Nederlands (Nederland), Agnieszka in `pl-PL` Pools (Polen), Marek in `pl-PL` Pools (Polen), Duarte in het Portugees (Brazilië), Raquel in het `pt-BR` Portugees ( `pt-PT` Potugal), Emil in `ro-RO` Roemeens (Roemenië), Dmitry in `ru-RU` Russisch (Rusland) Svetlana in `ru-RU` Russisch (Rusland), Lukas in `sk-SK` Slowaaks (Slowakije), rok in `sl-SI` Sloveens (Slovenië), Mattias in `sv-SE` Zweeds (Zweden), Sofie in `sv-SE` Zweeds (Zweden), niwat in het `th-TH` Thais (Thai land), Ahmet in `tr-TR` Turks (Turkije), NamMinh in `vi-VN` Vietnamees (Vietnam), HsiaoChen in `zh-TW` Taiwan Mandarijn (Taiwan), YunJhe in `zh-TW` Taiwan Mandarijn (Taiwan), HiuMaan in het `zh-HK` Chinees Kantonees (Hongkong), WanLung in het `zh-HK` Chinees Kantonees (Hong Kong).

- **5 nieuwe stemmen in Preview-land instellingen**: Kert in `et-EE` Estlands (Estland), Colm in Ierland `ga-IE` (Ierland), Nils in `lv-LV` Lets (Letland), Leonas in `lt-LT` Litouws (Litouwen), Joseph in `mt-MT` Maltees (Malta).

In deze release ondersteunen we nu een totaal van 129 Neural stemmen op 54 talen/land instellingen. Daarnaast zijn meer dan 70 standaard stemmen beschikbaar in 49 talen/land instellingen. Ga naar [taal ondersteuning](language-support.md#text-to-speech) voor de volledige lijst.

**Updates voor het maken van audio-inhoud**
- Verbeterde gebruikers interface voor spraak selectie met spraak categorieën en gedetailleerde gesp roken beschrijvingen. 
- Intonation tuning is ingeschakeld voor alle Neural stemmen in verschillende talen.
- De localizaiton van de gebruikers interface is geautomatiseerd op basis van de taal van de browser.
- Ingeschakelde `StyleDegree` besturings elementen voor alle `zh-CN` Neural stemmen.
Ga naar het [hulp programma](https://speech.microsoft.com/audiocontentcreation) voor het maken van een audio-inhoud om de nieuwe functies te bekijken. 

**Updates voor zh-CN-stemmen**
- Alle `zh-CN` Neural stemmen zijn bijgewerkt ter ondersteuning van Engels spreken.
- Alle `zh-CN` Neural stemmen in te scha kelen ter ondersteuning van de intonation-aanpassing. SSML of het hulp programma voor het maken van audio-inhoud kan worden gebruikt om aan te passen voor de beste intonation.
- Alle `zh-CN` Neural stemmen met meerdere stijlen bijgewerkt om `StyleDegree` het besturings element te ondersteunen. Emotion-intensiteit (zacht of sterk) is aanpasbaar.
- Bijgewerkt `zh-CN-YunyeNeural` om meerdere stijlen te ondersteunen die verschillende emoties kunnen uitvoeren.

## <a name="text-to-speech-2020-november-release"></a>Tekst-naar-spraak 2020-november release

**Nieuwe land instellingen en stemmen in Preview**
- Er zijn **vijf nieuwe stemmen en talen** geïntroduceerd in de Neural TTS-Port Folio. Dit zijn: respijt in Maltees (Malta), één in Litouws (Litouwen), Anu in Estlands (Estland), Orla in het Ierse (Ierland) en Everita in Lets (Letland).
- **Vijf nieuwe `zh-CN` stemmen met meerdere stijlen en rollen ondersteunen**: Xiaohan, Xiaomo, Xiaorui, xiaoxuan en Yunxi.

> Deze stemmen zijn beschikbaar in de open bare preview in drie Azure-regio's: Oost-, SouthEastAsia-en Europa West.

**Neural TTS-container GA**
- Met Neural TTS-container kunnen ontwikkel aars spraak synthese uitvoeren met de meest natuurlijke digitale stemmen in hun eigen omgeving voor specifieke vereisten op het gebied van beveiliging en gegevens beheer. Lees [hoe u spraak containers installeert](speech-container-howto.md). 

**Nieuwe functies**
- **Aangepaste stem**: enabed gebruikers een spraak model van de ene regio naar de andere kopiëren; ondersteunde eind punten worden geblokkeerd en worden hervat. Ga hier naar de [Portal](https://speech.microsoft.com/customvoice) .
- Ondersteuning voor [SSML stilte-tag](speech-synthesis-markup.md#add-silence) . 
- Algemene verbeteringen in de TTS-stem kwaliteit: verbeterde nauw keurigheid van de uitspraak op woord niveau in nb-NO. Gereduceerde fout van 53%.

> Meer informatie vindt u in [deze technische blog](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604).

## <a name="text-to-speech-2020-october-release"></a>Tekst-naar-spraak 2020-oktober release

**Nieuwe functies**
- Wilma ondersteunt een nieuwe `newscast` stijl. Zie [de gesp roken stijlen gebruiken in SSML](speech-synthesis-markup.md#adjust-speaking-styles).
- **Neural stemmen met een upgrade van HiFiNet vocoder, met een grotere geluids kwaliteit en een snellere synthese snelheid**. Deze voor delen van klanten waarvan het scenario afhankelijk is van Hi-Fi-audio of lange interacties, zoals het nahanden van Video's, audio boeken of online onderwijs materiaal. [Lees meer over het verhaal en luister naar de spraak voorbeelden in onze technische community-blog](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Aangepaste spraak](https://speech.microsoft.com/customvoice)  &  [Audio Content Creation Studio](https://speech.microsoft.com/audiocontentcreation) gelokaliseerd in 17 landen**. Gebruikers kunnen de gebruikers interface eenvoudig overschakelen naar een lokale taal voor een gebruiks vriendelijker ervaring.   
- **Audio-inhoud maken**: het besturings element stijl graden is toegevoegd voor XiaoxiaoNeural; Verfijnen de aangepaste break-functie om incrementele onderbrekingen van 50ms op te nemen. 

**Algemene verbeteringen voor spraak kwaliteit van TTS**
- Verbeterde nauw keurigheid van de uitspraak op woord niveau in `pl-PL` (reductie van fout frequentie: 51%) en `fi-FI` (reductie van fout frequentie: 58%)
- Verbeterd `ja-JP` lezen van één woord voor het woordenlijst scenario. De uitspraak fout met 80% is verminderd.
- `zh-CN-XiaoxiaoNeural`: Verbeterde sentiment/CustomerService/Newscast/cheerful/boos de kwaliteit van de stem.
- `zh-CN`: Verbeterde Erhua-uitspraak en lichte tint en prosody, waardoor de intelligibility aanzienlijk beter wordt. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0:2020-oktober release

**Opmerking**: de spraak-SDK in Windows is afhankelijk van de gedeelde micro soft Visual C++ Redistributable voor visual studio 2015, 2017 en 2019. Down load deze [hier](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nieuwe functies**
- **Linux**: er is ondersteuning toegevoegd voor Debian 10 en Ubuntu 20,04 LTS.
- **Python/objectief-C**: er is ondersteuning toegevoegd voor de `KeywordRecognizer` API. [Hier](./custom-keyword-basics.md)wordt de documentatie weer gegeven.
- **C++/Java/C #**: er is ondersteuning toegevoegd om een `HttpHeader` sleutel/waarde in te stellen via `ServicePropertyChannel::HttpHeader` .
- **Java script**: er is ondersteuning toegevoegd voor de `ConversationTranscriber` API. Lees [hier](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript)de documentatie. 
- **C++/c #**: nieuwe `AudioDataStream FromWavFileInput` methode toegevoegd (om te lezen. WAV-bestanden) [hier (C++)](/cpp/cognitive-services/speech/audiodatastream) en [hier (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/c #/Java/python/Objective-C/Swift**: er is een `stopSpeakingAsync()` methode toegevoegd om de tekst-naar-spraak-synthese te stoppen. Lees de referentie documentatie [hier (C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), [hier (C#](/dotnet/api/microsoft.cognitiveservices.speech)), hier ( [Java)](/java/api/com.microsoft.cognitiveservices.speech), [hier (python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech), en [hier (objectief-C/Swift)](/objectivec/cognitive-services/speech/).
- **C#, C++, Java**: een `FromDialogServiceConnector()` functie toegevoegd aan de `Connection` klasse die kan worden gebruikt voor het bewaken van verbindings-en verbreken van gebeurtenissen voor `DialogServiceConnector` . Lees de referentie documentatie [hier (C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [hier (C++)](/cpp/cognitive-services/speech/connection), en [hier (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection).
- **C++/c #/Java/python/Objective-C/Swift**: er is ondersteuning toegevoegd voor de beoordeling van de uitspraak, waarmee de uitspraak van een stem wordt geëvalueerd en feedback wordt gegeven over de nauw keurigheid en fluency van gesp roken audio. Lees de documentatie [hier](how-to-pronunciation-assessment.md).

**Laatste wijziging**
- **Java script**: PullAudioOutputStream. Read () heeft een wijziging van het retour type van een interne belofte naar een systeem eigen Java script-belofte.

**Opgeloste fouten**
- **Alle**: vaste 1,13-regressie in `SetServiceProperty` waar waarden met bepaalde speciale tekens worden genegeerd.
- **C#**: voor beelden van vaste Windows-consoles in Visual Studio 2019 kan geen systeem eigen dll's vinden.
- **C#**: vaste crash met geheugen beheer als de stroom wordt gebruikt als `KeywordRecognizer` invoer.
- **ObjectiveC/Swift**: Fixed crash met geheugen beheer als de stroom wordt gebruikt als de invoer van de herkenner.
- **Windows**: vast probleem met de samen werking met BT HFP/A2DP op UWP.
- **Java script**: een vaste toewijzing van sessie-id's voor het verbeteren van logboek registratie en ondersteuning bij interne fout opsporing/service-correlaties.
- **Java script**: toegevoegde correctie `DialogServiceConnector` voor `ListenOnce` het uitschakelen van aanroepen nadat de eerste aanroep is uitgevoerd.
- **Java script**: er is een probleem opgelost waarbij de uitvoer van resultaten alleen ooit ' eenvoudig ' zou zijn.
- **Java script**: probleem met vaste doorlopende herkenning in Safari op macOS.
- **Java script**: CPU-belasting beperken voor een scenario met hoge aanvraag doorvoer.
- **Java script**: Hiermee staat u toegang toe tot details van het registratie resultaat van het spraak profiel.
- **Java script**: toegevoegde correctie voor continue herkenning in `IntentRecognizer` .
- **C++/c #/Java/python/Swift/ObjectiveC**: er is een onjuiste URL voor australiaeast en brazilsouth in vastgelegd `IntentRecognizer` .
- **C++/c #**: toegevoegd `VoiceProfileType` als een-argument bij het maken van een `VoiceProfile` object.
- **C++/c #/Java/python/Swift/ObjectiveC**: er is een probleem opgelost `SPX_INVALID_ARG` bij het lezen `AudioDataStream` van een bepaalde positie.
- **IOS**: vast vastlopen met spraak herkenning op eenheid

**Voorbeelden**
- **ObjectiveC**: voor beeld voor trefwoord herkenning is [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples)toegevoegd.
- **C#/JavaScript**: Quick Start [(hier)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) en [hier (Java script)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription)toevoegen.
- **C++/c #/Java/python/Swift/ObjectiveC**: er is een voor beeld toegevoegd voor de [uitspraak beoordeling](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin**: Quick Start [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin)de meest recente Visual Studio-sjabloon bijgewerkt.

**Bekend probleem**
- DigiCert Global root G2-certificaat wordt niet standaard ondersteund in HoloLens 2 en Android 4,4 (KitKat) en moet aan het systeem worden toegevoegd om de spraak-SDK functioneel te maken. Het certificaat wordt in de nabije toekomst toegevoegd aan de installatie kopieën van het besturings systeem van HoloLens 2. Android 4,4-klanten moeten het bijgewerkte certificaat toevoegen aan het systeem.

**COVID-19-verkorte tests:** Omdat we in de afgelopen paar weken op afstand werken, kunnen we zoveel hand matige verificatie tests uitvoeren als we normaal gesp roken doen. We hebben geen wijzigingen aangebracht die denken dat we niets hebben gepaard en onze geautomatiseerde tests zijn allemaal geslaagd. In het onwaarschijnlijke geval dat we iets hebben gemist, laat het ons weten op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Blijf op de hoogte.

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Speech CLI (ook wel SPX genoemd): 2020-oktober release
SPX is de opdracht regel interface voor het gebruik van de service Azure speech zonder code te schrijven. Down load [hier](./spx-basics.md)de nieuwste versie. <br>

**Nieuwe functies**
- `spx csr dataset upload --kind audio|language|acoustic` -gegevens sets maken op basis van lokale gegevens, niet alleen vanuit Url's.
- `spx csr evaluation create|status|list|update|delete` -nieuwe modellen vergelijken met waarheid van de basis lijn/andere modellen.
- `spx * list` : biedt ondersteuning voor niet-wissel bare ervaring (vereist niet--top X--Skip X).
- `spx * --http header A=B` -ondersteunt aangepaste headers (toegevoegd voor Office voor aangepaste verificatie). 
- `spx help` : verbeterde tekst en tekst kleur code (blauw).

## <a name="text-to-speech-2020-september-release"></a>Tekst-naar-spraak 2020-release van september

### <a name="new-features"></a>Nieuwe functies

* **Neural TTS** 
    * **Uitgebreid ter ondersteuning van 18 nieuwe talen/land instellingen.** Dit zijn Bulgaars, Tsjechisch, Duits (Oosten rijk), Duits (Zwitser land), Grieks, Engels (Ierland), Frans (Zwitser land), Hebreeuws, Kroatisch, Hong aars, Indonesisch, Maleis, Roemeens, Slowaaks, Sloveens, Tamil, Telugu en Vietnamees. 
    * **14 nieuwe stemmen uitgebracht om het RAS in de bestaande talen te verrijken.** Zie [volledige taal en spraak lijst](language-support.md#neural-voices).
    * **Nieuwe gesp roken stijlen voor `en-US` en `zh-CN` stemmen.** Wilma, de nieuwe Voice in het Engels (Verenigde Staten), ondersteunt chatbot, Customer service en assistent-stijlen. Er zijn 10 nieuwe spraak stijlen beschikbaar met onze zh-CN Voice, XiaoXiao. Daarnaast ondersteunt de XiaoXiao Neural-stem het `StyleDegree` afstemmen. Zie [de gesp roken stijlen gebruiken in SSML](speech-synthesis-markup.md#adjust-speaking-styles).

* **Containers: Neural TTS-container uitgebracht in open bare Preview met 16 stemmen beschikbaar in 14 talen.** Meer informatie over [het implementeren van spraak containers voor NEURAL TTS](speech-container-howto.md)  

Lees de [volledige aankondiging van de TTS-updates voor Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Tekst-naar-spraak 2020-augustus-release

### <a name="new-features"></a>Nieuwe functies

* **NEURAL TTS: nieuwe spreek stijl voor `en-US` Aria-stem**. AriaNeural kan klinken als een nieuws-Caster bij het lezen van nieuws. De stijl ' Newscast-formeel ' klinkt meer ernstig, terwijl de stijl ' Newscast-inform ' meer besoepelder en informeel is. Zie [de gesp roken stijlen gebruiken in SSML](speech-synthesis-markup.md).

* **Aangepaste spraak: er wordt een nieuwe functie uitgebracht om de kwaliteit van de trainings gegevens automatisch te controleren**. Wanneer u uw gegevens uploadt, onderzoekt het systeem diverse aspecten van uw audio-en transcript gegevens, en worden automatisch problemen opgelost of gefilterd om de kwaliteit van het spraak model te verbeteren. Dit heeft betrekking op het volume van uw audio, het geluids niveau, de nauw keurigheid van de uitspraak van spraak, de uitlijning van spraak met de genormaliseerde tekst, stilte in de audio, naast de audio-en script indeling. 

* **Audio-inhoud maken: een aantal nieuwe functies om krachtigere mogelijkheden voor spraak afstemming en audio beheer mogelijk te** maken.

    * Uitspraak: de functie voor het afstemmen van de uitspraak wordt bijgewerkt naar de meest recente foneem-set. U kunt het juiste foneem-element kiezen uit de bibliotheek en de uitspraak verfijnen van de woorden die u hebt geselecteerd. 

    * Downloaden: de audio-functie voor downloaden/exporteren is verbeterd ter ondersteuning van het genereren van audio per alinea. U kunt inhoud in dezelfde bestands-SSML bewerken tijdens het genereren van meerdere audio-uitvoer. De bestands structuur van ' downloaden ' is ook verfijnd. U kunt nu eenvoudig alle audio bestanden in één map ophalen. 

    * Taak status: de ervaring voor het exporteren van meerdere bestanden is verbeterd. Wanneer u meerdere bestanden in het verleden exporteert en een van de bestanden is mislukt, mislukt de volledige taak. Maar nu worden alle andere bestanden geëxporteerd. Het taak rapport is verrijkt met gedetailleerde en gestructureerde informatie. U kunt de logboeken voor alle mislukte bestanden en zinnen nu met het rapport controleren. 

    * SSML-documentatie: gekoppeld aan SSML-document om u te helpen bij het controleren van de regels voor het gebruik van alle afstemmings functies.

* **De Voice List-API is bijgewerkt met een gebruiks vriendelijke weergave naam en de spraak stijlen die voor Neural stemmen worden ondersteund**.

### <a name="general-tts-voice-quality-improvements"></a>Algemene verbeteringen voor spraak kwaliteit van TTS

* Er is een minder uitspraak van woord niveau% voor `ru-RU` (fouten verminderd door 56%) en `sv-SE` (fouten verminderd door 49%)

* Verbeterd polyphony-woord voor het lezen van `en-US` Neural stemmen met 40%. Voor beelden van polyphony-woorden zijn ' read ', ' live ', ' content ', ' record ', ' object ', enzovoort. 

* De natuurlijkeheid van de vraag Toon is verbeterd in `fr-FR` . MOS (gemiddelde opinie Score): + 0,28

* De vocoders is bijgewerkt voor de volgende stemmen, met betrouw baarheid en een totale prestatie snelheid van 40%.

    | Landinstelling | Spraak |
    |---|---|    
    | `en-GB` | Quote |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Dick |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Opgeloste fouten

* Een aantal bugs opgelost met het hulp programma voor het maken van de audio-inhoud 
    * Probleem opgelost met automatisch vernieuwen. 
    * Opgeloste problemen met spraak stijlen in zh-CN in de regio Zuid-Azië-oost.
    * Probleem met vaste stabiliteit, met inbegrip van een export fout met het label ' breuk ' en fouten in Lees tekens.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nieuwe land instellingen voor spraak naar tekst: 2020-augustus release
Spraak-naar-tekst vrijgegeven 26 nieuwe land instellingen in augustus: 2 Europese talen `cs-CZ` en `hu-HU` , 5 Engelse land instellingen en 19 Spaanse land instellingen die betrekking hebben op de meeste Zuid-Amerikaanse landen. Hieronder ziet u een lijst met de nieuwe land instellingen. Zie de [volledige lijst met](./language-support.md)talen.

| Landinstelling  | Taal                          |
|---------|-----------------------------------|
| `cs-CZ` | Tsjechisch (Tsjechische Republiek)            | 
| `en-HK` | Engels (Hongkong)               | 
| `en-IE` | Engels (Ierland)                 | 
| `en-PH` | Engels (Filipijnen)             | 
| `en-SG` | Engels (Singapore)               | 
| `en-ZA` | Engels (Zuid-Afrika)            | 
| `es-AR` | Spaans (Argentinië)               | 
| `es-BO` | Spaans (Bolivia)                 | 
| `es-CL` | Spaans (Chili)                   | 
| `es-CO` | Spaans (Colombia)                | 
| `es-CR` | Spaans (Costa Rica)              | 
| `es-CU` | Spaans (Cuba)                    | 
| `es-DO` | Spaans (Dominicaanse Republiek)      | 
| `es-EC` | Spaans (Ecuador)                 | 
| `es-GT` | Spaans (Guatemala)               | 
| `es-HN` | Spaans (Honduras)                | 
| `es-NI` | Spaans (Nicaragua)               | 
| `es-PA` | Spaans (Panama)                  | 
| `es-PE` | Spaans (Peru)                    | 
| `es-PR` | Spaans (Puerto Rico)             | 
| `es-PY` | Spaans (Paraguay)                | 
| `es-SV` | Spaans (El Salvador)             | 
| `es-US` | Spaans (Verenigde Staten)                     | 
| `es-UY` | Spaans (Uruguay)                 | 
| `es-VE` | Spaans (Venezuela)               | 
| `hu-HU` | Hongaars (Hongarije)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0:2020-juli release

**Opmerking**: de spraak-SDK in Windows is afhankelijk van de gedeelde micro soft Visual C++ Redistributable voor visual studio 2015, 2017 en 2019. Down load en installeer deze [hier](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nieuwe functies**
- **C#**: er is ondersteuning toegevoegd voor asynchrone conversatie-transcriptie. Raadpleeg [hier](./how-to-async-conversation-transcription.md)de documentatie.  
- **Java script**: er is speaker Recognition ondersteuning toegevoegd voor zowel [browser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) als [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **Java script**: ondersteuning toegevoegd voor automatische taal detectie/taal-id. Raadpleeg [hier](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)de documentatie.
- **Doel-C**: er is ondersteuning toegevoegd voor conversaties en [conversaties](./conversation-transcription.md)voor [meerdere apparaten](./multi-device-conversation.md) transcriptie. 
- **Python**: er is ondersteuning toegevoegd voor gecomprimeerde audio voor python in Windows en Linux. Raadpleeg [hier](./how-to-use-codec-compressed-audio-input-streams.md)de documentatie. 

**Opgeloste fouten**
- **Alle**: er is een probleem opgelost waardoor de KeywordRecognizer de streams na een herkenning niet meer heeft door gestuurd.
- **Alle**: er is een probleem opgelost waardoor de stroom die is verkregen van een KeywordRecognitionResult niet het sleutel woord bevat.
- **Alle**: er is een probleem opgelost dat de SendMessageAsync het bericht niet echt via de kabel verzendt nadat de gebruikers hun wacht tijd voor het gesprek hebben opgedaan.
- **Alle**: er is een crash opgelopen in speaker Recognition-api's wanneer gebruikers VoiceProfileClient:: SpeakerRecEnrollProfileAsync-methode meerdere keren aanroepen en niet hebben gewacht totdat de aanroepen zijn voltooid.
- **Alle**: vaste logboek registratie van bestanden inschakelen in VoiceProfileClient-en SpeakerRecognizer-klassen.
- **Java script**: er is een [probleem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) opgelost met de beperking wanneer de browser is geminimaliseerd.
- **Java script**: er is een [probleem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) opgelost met een geheugenlek op streams.
- **Java script**: cache toegevoegd voor OCSP-antwoorden van NodeJS.
- **Java**: er is een probleem opgelost dat ertoe leidt dat BigInteger-velden altijd 0 retour neren.
- **IOS**: er is een [probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) opgelost met het publiceren van spraak-SDK-apps in de IOS App Store.

**Voorbeelden**
- **C++**: [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)kunt u voorbeeld code voor Speaker Recognition toevoegen.

**COVID-19-verkorte tests:** Omdat we in de afgelopen paar weken op afstand werken, kunnen we zoveel hand matige verificatie tests uitvoeren als we normaal gesp roken doen. We hebben geen wijzigingen aangebracht die denken dat we niets hebben gepaard en onze geautomatiseerde tests zijn allemaal geslaagd. In het onwaarschijnlijke geval dat we iets hebben gemist, laat het ons weten op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Blijf op de hoogte.

## <a name="text-to-speech-2020-july-release"></a>Tekst-naar-spraak 2020-juli release

### <a name="new-features"></a>Nieuwe functies

* **NEURAL TTS, 15 nieuwe Neural stemmen**: de nieuwe stemmen die zijn toegevoegd aan de Neural TTS-Port Folio zijn Salma in het `ar-EG` Arabisch (Egypte), Zariyah in `ar-SA` Arabisch (Saoedi-Arabië), Alba in `ca-ES` Catalaans (Spanje), Christel in het `da-DK` Deens (Denemarken), Neerja in het `es-IN` Engels (India), Noora in het `fi-FI` Fins (Finland), Swara in `hi-IN` Hindi (India), Colette in het `nl-NL` Nederlands (Nederland), Zofia in `pl-PL` Pools (Polen), Fernanda in het `pt-PT` Portugees (Portugal), Dariya in `ru-RU` Russisch (Rusland), Hillevi in `sv-SE` Zweeds (Zweden), Achara in `th-TH` Thai (Thai land), HiuGaai in `zh-HK` Chinees (Kantonees, traditioneel) en HsiaoYu in het `zh-TW` Chinees (Taiwan Mandarijn). Alle [ondersteunde talen](./language-support.md#neural-voices)controleren.  

* **Aangepaste spraak, gestroomlijnde stem tests met de trainings stroom om de gebruikers ervaring te vereenvoudigen**: met de nieuwe test functie wordt elke stem automatisch getest met een vooraf gedefinieerde testset die is geoptimaliseerd voor elke taal om algemene en handelsassistent scenario's te kunnen behandelen. Deze test sets zijn zorgvuldig geselecteerd en getest om typische use cases en fonemen in de taal te bevatten. Daarnaast kunnen gebruikers nog steeds hun eigen test scripts uploaden bij het trainen van een model.

* **Audio-inhoud maken: er wordt een aantal nieuwe functies uitgebracht om krachtigere mogelijkheden voor spraak afstemming en audio beheer mogelijk te maken**

    * `Pitch`, `rate` en `volume` zijn verbeterd om het afstemmen met een vooraf gedefinieerde waarde, zoals langzaam, gemiddeld en snel, te ondersteunen. Het is nu eenvoudig voor gebruikers om een constante waarde te kiezen voor hun audio bewerking.

    ![Audio afstemmen](media/release-notes/audio-tuning.png)

    * Gebruikers kunnen nu het `Audio history` voor hun werk bestand bekijken. Met deze functie kunnen gebruikers eenvoudig alle gegenereerde audio met betrekking tot een werk bestand bijhouden. Ze kunnen de geschiedenis versie controleren en de kwaliteit vergelijken tijdens het afstemmen. 

    ![Audio geschiedenis](media/release-notes/audio-history.png)

    * De `Clear` functie is nu flexibeler. Gebruikers kunnen een specifieke afstemmings parameter wissen en andere beschik bare para meters voor de geselecteerde inhoud behouden.  

    * Er is een zelfstudie video toegevoegd op de [landings pagina](https://speech.microsoft.com/audiocontentcreation) waarmee gebruikers snel aan de slag kunnen met TTS Voice Tuning en Audio Management. 

### <a name="general-tts-voice-quality-improvements"></a>Algemene verbeteringen voor spraak kwaliteit van TTS

* Verbeterde TTS-vocoder in voor betere beeld kwaliteit en een lagere latentie.

    * De versie van Elsa `it-IT` is bijgewerkt naar een nieuwe Vocoder die is bereikt + 0,464 CMOS (vergelijkend gemiddelde advies score) in de geluids kwaliteit, 40% sneller in synthese en 30% voor de eerste byte latentie. 
    * Xiaoxiao bijgewerkt `zh-CN` naar de nieuwe vocoder met + 0148 CMOS-toename voor het algemene domein, + 0,348 voor de newscast-stijl en + 0,195 voor de Lyrical-stijl. 

* Bijgewerkte `de-DE` en `ja-JP` spraak modellen om de TTS-uitvoer natuurlijk te maken.
    
    * Katja is bijgewerkt `de-DE` met de meest recente prosody-model methode, de Mos (gemiddelde opinie Score) is + 0,13. 
    * Nanami is bijgewerkt in `ja-JP` met een nieuw prosody-model voor de Mos (de gemiddelde opinie Score) is + 0,19;  

* Verbeterde nauw keurigheid van de uitspraak op woord niveau in vijf talen.

    | Taal | Fout reductie van uitspraak |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | Nr |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Opgeloste fouten

* Valuta lezen
    * Het probleem opgelost met valuta-lezen voor `es-ES` en `es-MX`
     
    | Taal | Invoer | Na verbetering aflezing |
    |---|---|---|
    | `es-MX` | $1,58 | oncincuentae y Ocho centavos |
    | `es-ES` | $1,58 | dólar cincuenta y Ocho centavos |

    * Ondersteuning voor negatieve valuta (zoals "-€325") in de volgende land instellingen: `en-US` ,, `en-GB` `fr-FR` , `it-IT` ,, `en-AU` `en-CA` .

* Verbeterd lezen van het adres in `pt-PT` .
* Opgeloste problemen met Natasha ( `en-AU` ) en Libby ( `en-UK` ) in het woord "voor" en "vier".  
* Probleem opgelost met hulp programma voor het maken van een audio-inhoud
    * De extra en onverwachte onderbreking nadat de tweede alinea is opgelost.  
    * De functie ' geen onderbreken ' is weer toegevoegd vanuit een regressie fout. 
    * Het probleem met wille keurig vernieuwen van speech Studio is opgelost.  

### <a name="samplessdk"></a>Voor beelden/SDK

* Java script: Hiermee worden problemen met afspelen in Firefox en Safari in macOS en iOS opgelost. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1:2020-juni release
**Speech CLI (ook wel SPX genoemd)**
-   Toegevoegde in-CLI Help zoek functies:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Bijgewerkt om te werken met pas geïmplementeerde v 3.0-batch-en Custom Speech-Api's:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nieuwe functies**
-   **C \# , C++**: Speaker Recognition Preview: met deze functie kunt u de identificatie van de spreker (die spreekt?) en de verificatie van de spreker (de spreker die hij/zij beweert te zijn). Begin met een [overzicht](./speaker-recognition-overview.md), lees het [artikel](./get-started-speaker-recognition.md)over de speaker Recognition-basis of de [API-documentatie](/rest/api/speakerrecognition/).

**Opgeloste fouten**
-   **C \# , C++**: de opname van een vaste microfoon werkt niet in 1,12 in de luidspreker herkenning.
-   **Java script**: oplossingen voor tekst naar spraak in Firefox en Safari in MacOS en IOS.
-   Correctie voor de toegangs schending van Windows Application Verifier bij gesprek transcriptie bij gebruik van acht kanalen stroom.
-   Oplossing voor een probleem met de toegangs fout voor Windows Application Verifier tijdens het omzetten van conversaties met meerdere apparaten.

**Voorbeelden**
-   **C#**: [code voorbeeld](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) voor luidspreker herkenning.
-   **C++**: [code voorbeeld](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) voor luidspreker herkenning.
-   **Java**: [code voorbeeld](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) voor intentie herkenning op Android. 

**COVID-19-verkorte tests:** Omdat we in de afgelopen paar weken op afstand werken, kunnen we zoveel hand matige verificatie tests uitvoeren als we normaal gesp roken doen. We hebben geen wijzigingen aangebracht die denken dat we niets hebben gepaard en onze geautomatiseerde tests zijn allemaal geslaagd. In het onwaarschijnlijke geval dat we iets hebben gemist, laat het ons weten op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Blijf op de hoogte.


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0:2020-mei release
**Speech CLI (ook bekend als SPX)**
- **SPX** is een nieuw opdracht regel programma waarmee u herkenning, synthese, vertaling, batch transcriptie en aangepaste spraak beheer kunt uitvoeren vanaf de opdracht regel. Gebruik deze functie om de speech-service te testen of om de spraak service taken te script die u moet uitvoeren. Down load het hulp programma en lees de documentatie [hier](./spx-overview.md).

**Nieuwe functies**
- **Go**: nieuwe go-taal ondersteuning voor [spraak herkenning](./get-started-speech-to-text.md?pivots=programming-language-go) en [aangepaste spraak assistenten](./quickstarts/voice-assistants.md?pivots=programming-language-go). Stel [hier](./quickstarts/setup-platform.md?pivots=programming-language-go)uw ontwikkel omgeving in. Zie de sectie voor beelden hieronder voor voorbeeld code. 
- **Java script**: browser ondersteuning toegevoegd voor tekst naar spraak. Raadpleeg [hier](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)de documentatie.
- **C++, C#, Java**: nieuwe `KeywordRecognizer` objecten en api's die worden ondersteund op Windows-, Android-, Linux-& IOS-platforms. Lees de documentatie [hier](./custom-keyword-overview.md). Zie de sectie voor beelden hieronder voor voorbeeld code. 
- **Java**: een conversatie met meerdere apparaten met ondersteuning voor vertalingen is toegevoegd. Zie het referentie document [hier](/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Verbeteringen & optimalisaties**
- **Java script**: geoptimaliseerde browser microfoon implementatie verbeteren nauw keurigheid van spraak herkenning.
- **Java**: herstructured bindingen met behulp van directe jni-implementatie zonder swig. Deze wijziging vermindert de grootte van de bindingen voor alle Java-pakketten die worden gebruikt voor Windows, Android, Linux en Mac en vereenvoudigt de ontwikkeling van de Speech SDK Java-implementatie.
- **Linux**: bijgewerkte ondersteunings [documentatie](./speech-sdk.md?tabs=linux) met de meest recente RHEL 7-opmerkingen.
- Verbeterde verbindings logica om meerdere keren te proberen verbinding te maken wanneer er service-en netwerk fouten optreden.
- De pagina [Portal.Azure.com](https://portal.azure.com) speech Quick start is bijgewerkt, zodat ontwikkel aars de volgende stap in de Azure-spraak traject kunnen volgen.

**Opgeloste fouten**
- **C#, Java**: er is een [probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) opgelost met het laden van SDK-bibliotheken op Linux arm (zowel 32-bits als 64-bits).
- **C#**: verholpen expliciete verwijdering van systeem eigen ingangen voor TranslationRecognizer-, IntentRecognizer-en Connection-objecten.
- **C#**: vast beheer van de levens duur van audio-invoer voor het object ConversationTranscriber.
- Er is een probleem opgelost waarbij de `IntentRecognizer` reden van het resultaat niet juist is ingesteld bij het herkennen van intenties uit eenvoudige frasen.
- Er is een probleem opgelost waarbij de `SpeechRecognitionEventArgs` resultaat verschuiving niet juist is ingesteld.
- Er is een race voorwaarde opgelost waarbij SDK probeerde een netwerk bericht te verzenden voordat de WebSocket-verbinding wordt geopend. Is reproduceerbaar voor `TranslationRecognizer` het toevoegen van deel nemers.
- Opgeloste geheugen lekken in de engine voor trefwoord herkenning.

**Voorbeelden**
- **Go: Quick starts** toegevoegd voor [spraak herkenning](./get-started-speech-to-text.md?pivots=programming-language-go) en [aangepaste spraak assistenten](./quickstarts/voice-assistants.md?pivots=programming-language-go). Zoek [hier](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)de voorbeeld code. 
- **Java script**: Quick starts toegevoegd voor [tekst naar spraak](./get-started-text-to-speech.md?pivots=programming-language-javascript), [vertaling](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)en [Intentieherkenning](./quickstarts/intent-recognition.md?pivots=programming-language-javascript).
- Voor beelden van herkenning van tref woorden voor [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) en [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**COVID-19-verkorte tests:** Omdat we in de afgelopen paar weken op afstand werken, kunnen we zoveel hand matige verificatie tests uitvoeren als we normaal gesp roken doen. We hebben geen wijzigingen aangebracht die denken dat we niets hebben gepaard en onze geautomatiseerde tests zijn allemaal geslaagd. Als we iets hebben gemist, laat het ons dan weten op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Blijf op de hoogte.

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0:2020-maart release
**Nieuwe functies**
- Linux: er is ondersteuning toegevoegd voor Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 met [instructies](./how-to-configure-rhel-centos-7.md) voor het configureren van het systeem voor spraak-SDK.
- Linux: er is ondersteuning toegevoegd voor .NET core C# op Linux ARM32 en ARM64. Meer informatie is [hier](./speech-sdk.md?tabs=linux) beschikbaar. 
- C#, C++: toegevoegd `UtteranceId` aan `ConversationTranscriptionResult` , een consistente id voor alle tussenliggende en laatste spraak herkennings resultaten. Details voor [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: er is ondersteuning toegevoegd voor `Language ID` . Zie speech_sample. py in [github opslag plaats](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: er is ondersteuning toegevoegd voor gecomprimeerde audio-invoer indeling op het Windows-platform voor alle Win32-console toepassingen. [Hier vindt](./how-to-use-codec-compressed-audio-input-streams.md)u meer informatie. 
- Java script: ondersteuning voor spraak synthese (tekst-naar-spraak) in NodeJS. Klik [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) voor meer informatie. 
- Java script: Voeg nieuwe API'S toe om inspectie van alle berichten over verzenden en ontvangen in te scha kelen. Klik [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript) voor meer informatie. 
        
**Opgeloste fouten**
- C#, C++: er is een probleem opgelost waardoor `SendMessageAsync` nu een binair bericht wordt verzonden als binair type. Details voor [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: er is een probleem opgelost waarbij het gebruik van een `Connection MessageReceived` gebeurtenis vastloopt als dat `Recognizer` voor object is verwijderd `Connection` . Details voor [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: de grootte van de audio buffer van de microfoon is afgenomen van 800ms naar 100 MS om de latentie te verbeteren.
- Android: er is een [probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) opgelost met de x86 Android-emulator in Android Studio.
- Java script: ondersteuning toegevoegd voor regio's in China met de `fromSubscription` API. [Hier vindt](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-)u meer informatie. 
- Java script: meer fout informatie toevoegen voor verbindings fouten van NodeJS.
        
**Voorbeelden**
- Eenheid: het open bare voor beeld van de intentie herkenning is vast, waarbij LUIS JSON-import mislukt. [Hier vindt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)u meer informatie.
- Python: voor beeld is toegevoegd voor `Language ID` . [Hier vindt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)u meer informatie.
    
**Covid19 verkorte tests:** Omdat we in de afgelopen paar weken op afstand aan de slag kunnen, is het niet zo veel hand matig om de verificatie van het apparaat te testen. Het is bijvoorbeeld niet gelukt microfoon invoer en luidspreker uitvoer te testen op Linux, iOS en macOS. We hebben geen wijzigingen aangebracht die denken dat ze op deze platformen kunnen zijn gebroken en onze geautomatiseerde tests zijn allemaal geslaagd. In het onwaarschijnlijke geval dat we iets hebben gemist, laat het ons weten op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Hartelijk dank voor uw voortdurende ondersteuning. U kunt altijd vragen of feedback plaatsen op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) of [stack overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Blijf op de hoogte.

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0:2020-februari release

**Nieuwe functies**

 - Python-pakketten zijn toegevoegd ter ondersteuning van de nieuwe 3,8-versie van python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64-ondersteuning (C++, C#, Java, python).
   > [!NOTE] 
   > Klanten moeten OpenSSL configureren volgens [deze instructies](./how-to-configure-openssl-linux.md).
 - Linux ARM32-ondersteuning voor Debian en Ubuntu.
 - DialogServiceConnector ondersteunt nu een optionele para meter ' bot ID ' op BotFrameworkConfig. Met deze para meter kunt u meerdere directe-regel spraak botsen met één Azure-spraak bron. Als de para meter niet is opgegeven, wordt de standaard-bot gebruikt (zoals wordt bepaald door de configuratie pagina voor direct-spraak kanaal).
 - DialogServiceConnector heeft nu een SpeechActivityTemplate-eigenschap. De inhoud van deze JSON-teken reeks wordt gebruikt door directe regel spraak om vooraf een breed scala aan ondersteunde velden te vullen in alle activiteiten die een bot met directe lijnen bereiken, inclusief activiteiten die automatisch zijn gegenereerd als reactie op gebeurtenissen als spraak herkenning.
 - TTS maakt nu gebruik van abonnements sleutel voor verificatie en vermindert de eerste byte latentie van het eerste synthese resultaat nadat een synthesizer is gemaakt.
 - Bijgewerkte modellen voor spraak herkenning voor 19 land instellingen voor een gemiddelde reductie van een woord fout van 18,6% (es-ES, es-MX, FR-CA, fr-FR, it-IT, ja-JP, ko-KR, punt-k, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, CA-ES, zh-TW, th-TH, pt-PT, tr-TR). De nieuwe modellen bieden aanzienlijke verbeteringen in meerdere domeinen, inclusief dicteren, Call-Center transcriptie-en video indexerings scenario's.

**Opgeloste fouten**

 - Er is een probleem opgelost waarbij conversatie transcriber niet correct heeft gewacht in JAVA-Api's 
 - Android x86-emulator reparatie voor Xamarin [github-probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Ontbrekende toevoegen (ophalen | Set) eigenschaps methoden naar AudioConfig
 - Een TTS-fout oplossen waarbij de audioDataStream niet kan worden gestopt wanneer de verbinding is mislukt
 - Het gebruik van een eind punt zonder regio zou USP storingen voor conversatie-vertaler veroorzaken
 - Voor het genereren van een ID in universele Windows-toepassingen wordt nu een adequaat uniek GUID-algoritme gebruikt. het was voorheen en onbedoeld standaard ingesteld op een stubbed-implementatie die vaak conflicten veroorzaakt in grote sets met interacties.
 
 **Voorbeelden**
 
 - Unit-voor beeld voor het gebruik van Speech SDK met [Unity-microfoon en push-modus streaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Andere wijzigingen**

 - [Documentatie voor OpenSSL-configuratie is bijgewerkt voor Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020-januari release

**Nieuwe functies**

- Conversatie met meerdere apparaten: u kunt meerdere apparaten verbinden met dezelfde spraak of op tekst gebaseerd gesprek en optioneel berichten vertalen die ertussen worden verzonden. Meer informatie vindt u in [dit artikel](multi-device-conversation.md). 
- Ondersteuning voor trefwoord herkenning is toegevoegd voor Android. aar-pakket en er is ondersteuning toegevoegd voor x86-en x64-versies. 
- Doel-C: `SendMessage` en `SetMessageProperty` methoden die zijn toegevoegd aan het `Connection` object. Raadpleeg [hier](/objectivec/cognitive-services/speech/spxconnection)de documentatie.
- TTS C++ API ondersteunt nu `std::wstring` de tekst invoer van de synthese, waarbij een wstring moet worden geconverteerd naar een teken reeks voordat deze wordt door gegeven aan de SDK. Bekijk [hier](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)meer informatie. 
- C#: [taal-id](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) en [bron taal configuratie](./how-to-specify-source-language.md?pivots=programming-language-csharp) zijn nu beschikbaar.
- Java script: een functie toegevoegd aan `Connection` object voor het door geven van aangepaste berichten van de spraak service als call back `receivedServiceMessage` .
- Java script: er is ondersteuning toegevoegd voor `FromHost API` om gebruik te vereenvoudigen met on-premises containers en soevereine Clouds. Raadpleeg [hier](speech-container-howto.md)de documentatie.
- Java script: we gaan nu `NODE_TLS_REJECT_UNAUTHORIZED` door met een bijdrage van [orgads](https://github.com/orgads). Bekijk [hier](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)meer informatie.

**Wijzigingen die fouten veroorzaken**

- `OpenSSL` is bijgewerkt naar versie 1.1.1 b en is statisch gekoppeld aan de Speech SDK core-bibliotheek voor Linux. Dit kan een storing veroorzaken als uw postvak in `OpenSSL` niet is geïnstalleerd in de `/usr/lib/ssl` map in het systeem. Raadpleeg [onze documentatie](how-to-configure-openssl-linux.md) onder documenten voor spraak-SDK om het probleem te omzeilen.
- Het geretourneerde gegevens type voor C# is gewijzigd in om `WordLevelTimingResult.Offset` `int` `long` toegang te krijgen tot `WordLevelTimingResults` wanneer spraak gegevens langer dan twee minuten zijn.
- `PushAudioInputStream` en `PullAudioInputStream` verzenden nu WAV-header gegevens naar de spraak service op basis van `AudioStreamFormat` , eventueel opgegeven wanneer ze zijn gemaakt. Klanten moeten nu de [ondersteunde audio-invoer indeling](how-to-use-audio-input-streams.md)gebruiken. Andere indelingen krijgen de meest optimale herkennings resultaten of kunnen andere problemen veroorzaken. 

**Opgeloste fouten**

- Zie de `OpenSSL` Update onder belang rijke wijzigingen hierboven. We hebben een crash en een prestatie probleem opgelost (conflicten met hoge belasting vergren delen) in Linux en Java. 
- Java: verbeteringen aangebracht in het sluiten van objecten in gelijktijdige scenario's.
- Het NuGet-pakket is herstructureeerd. We hebben de drie kopieën van `Microsoft.CognitiveServices.Speech.core.dll` en `Microsoft.CognitiveServices.Speech.extension.kws.dll` onder lib-mappen verwijderd, waardoor het NuGet-pakket kleiner en sneller kan worden gedownload en er zijn headers toegevoegd die nodig zijn om bepaalde C++ native apps te compileren.
- [Hier vindt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)u voor beelden van vaste Quick Start. Deze zijn afgesloten zonder weer gave van de uitzonde ring ' microfoon niet gevonden ' op Linux, macOS, Windows.
- Vaste SDK-crash met lange spraak herkennings resultaten voor bepaalde code paden zoals [dit voor beeld](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Er is een vaste SDK-implementatie fout opgetreden in de Azure web app-omgeving om [deze klant probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)op te lossen
- Een TTS-fout opgelost tijdens het gebruik van meerdere `<voice>` Tags of `<audio>` Tags om [Dit probleem van de klant](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)op te lossen. 
- Er is een fout met TTS 401 opgelost tijdens het herstellen van de SDK vanuit Suspended.
- Java script: een circulaire invoer van audio gegevens is opgelost dankzij een bijdrage van [euirim](https://github.com/euirim). 
- Java script: ondersteuning toegevoegd voor het instellen van service-eigenschappen, zoals toegevoegd in 1,7.
- Java script: er is een probleem opgelost waarbij een verbindings fout kan leiden tot doorlopende, niet-geslaagde WebSocket-pogingen om verbinding te maken.

**Voorbeelden**

- Voor beeld van trefwoord herkenning voor [Android is](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)toegevoegd.
- Er is [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)een voor beeld van TTS toegevoegd voor het Server scenario.
- De Quick starts voor multi-device-conversaties zijn toegevoegd voor C# [en C++.](quickstarts/multi-device-conversation.md)

**Andere wijzigingen**

- Geoptimaliseerde grootte van de SDK-kern bibliotheek op Android.
- De SDK in 1.9.0 en daarna ondersteunt zowel `int` als- `string` type in het veld met de spraak handtekening versie voor de conversatie-transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019-november release

**Nieuwe functies**

- Er `FromHost()` is een API toegevoegd om gebruik te vereenvoudigen met on-premises containers en soevereine Clouds.
- Automatische bron Taaldetectie toegevoegd voor spraak herkenning (in Java en C++)
- Toegevoegd `SourceLanguageConfig` object voor spraak herkenning, gebruikt om de verwachte bron talen op te geven (in Java en C++)
- `KeywordRecognizer`Er is ondersteuning toegevoegd voor Windows (UWP), Android en IOS via de NuGet-en Unity-pakketten
- De Java-API voor externe conversaties is toegevoegd aan de conversatie transcriptie in asynchrone batches.

**Wijzigingen die fouten veroorzaken**

- De functie voor het verzetten van conversaties in gesprek onder naam ruimte is verplaatst `Microsoft.CognitiveServices.Speech.Transcription` .
- Delen van de audio transcriber-methoden worden verplaatst naar een nieuwe `Conversation` klasse.
- Ondersteuning voor verloren voor 32-bits (ARMv7 en x86) iOS

**Opgeloste fouten**

- Herstel voor crash als lokaal `KeywordRecognizer` zonder geldige abonnements sleutel van een spraak service wordt gebruikt

**Voorbeelden**

- Xamarin-voor beeld voor `KeywordRecognizer`
- Unit-voor beeld voor `KeywordRecognizer`
- Voor beelden van C++ en Java voor automatische bron Taaldetectie.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019-release van september

**Nieuwe functies**

- Bèta-ondersteuning toegevoegd voor Xamarin op Universeel Windows-platform (UWP), Android en iOS
- Ondersteuning voor iOS toegevoegd voor Unity
- `Compressed`Ondersteuning voor invoer toegevoegd voor ALaw, mulaw, FLAC op Android, Ios en Linux
- Toegevoegd `SendMessageAsync` in `Connection` klasse voor het verzenden van een bericht naar de service
- Toegevoegd `SetMessageProperty` in `Connection` de klasse voor het instellen van de eigenschap van een bericht
- Aan TTS toegevoegde bindingen voor Java (JRE en Android), Python, SWIFT en objectief-C
- TTS heeft ondersteuning voor afspelen toegevoegd voor macOS, iOS en Android.
- Informatie over woord grens toegevoegd voor TTS.

**Opgeloste fouten**

- Probleem met vast IL2CPP-Build op unit 2019 voor Android
- Probleem opgelost met onjuiste headers in WAV-bestand invoer wordt onjuist verwerkt
- Er is een probleem opgelost met UUIDs die niet uniek zijn in sommige verbindings eigenschappen
- Enkele waarschuwingen over de specificaties van nulwaarden in de SWIFT-bindingen zijn opgelost (hiervoor zijn mogelijk kleine code wijzigingen vereist)
- Er is een fout opgelost waardoor WebSocket-verbindingen zonder problemen kunnen worden gesloten onder netwerk belasting
- Er is een probleem opgelost in Android dat soms resulteert in dubbele indruk-Id's die worden gebruikt door `DialogServiceConnector`
- Verbeteringen in de stabiliteit van verbindingen tussen multi-turn-interacties en de rapportage van fouten (via `Canceled` gebeurtenissen) wanneer deze optreden in `DialogServiceConnector`
- `DialogServiceConnector` sessie start nu op juiste wijze gebeurtenissen, zoals wanneer wordt aangeroepen `ListenOnceAsync()` tijdens een actieve `StartKeywordRecognitionAsync()`
- Een crash gericht op het `DialogServiceConnector` Ontvangen van activiteiten

**Voorbeelden**

- Quick start voor Xamarin
- Update CPP Snelstartgids met Linux ARM64 Information
- Quick start voor Unity update met iOS-gegevens

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019-juni release

**Voorbeelden**

- Quick start-voor beelden voor tekst-naar-spraak op UWP en eenheid
- Voor beeld van Quick start voor Swift op iOS
- Unit-voor beelden voor spraak & Intentieherkenning en omzetting
- Bijgewerkte Quick start-voor beelden voor `DialogServiceConnector`

**Verbeteringen/wijzigingen**

- Naam ruimte dialoog venster:
  - De naam van `SpeechBotConnector` is gewijzigd in `DialogServiceConnector`
  - De naam van `BotConfig` is gewijzigd in `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` is opnieuw toegewezen aan `DialogServiceConfig::FromBotSecret()`
  - Alle bestaande clients met directe spraak blijven worden ondersteund na de naamswijziging
- TTS REST-Adapter bijwerken ter ondersteuning van proxy, permanente verbinding
- Fout bericht verbeteren wanneer een ongeldige regio is door gegeven
- SWIFT/objectief-C:
  - Verbeterde fout rapportage: methoden die kunnen resulteren in een fout, zijn nu beschikbaar in twee versies: een die een `NSError` object beschrijft voor fout afhandeling en één waarmee een uitzonde ring wordt gegenereerd. De voormalige worden weer gegeven aan SWIFT. Deze wijziging vereist aanpassingen in bestaande SWIFT-code.
  - Verbeterde verwerking van gebeurtenissen

**Opgeloste fouten**

- Fix voor TTS: waar wordt de `SpeakTextAsync` toekomst teruggestuurd zonder te wachten totdat de rendering van audio is voltooid
- Correctie voor het samen stellen van teken reeksen in C# om ondersteuning voor volledige taal in te scha kelen
- Oplossing voor een probleem met de .NET core-app voor het laden van de kern bibliotheek met net461 Target Framework in samples
- Problemen oplossen om systeem eigen bibliotheken in voor beelden te implementeren in de uitvoermap
- Oplossing voor het sluiten van de WebSocket is betrouwbaar
- Oplossing voor mogelijke crash tijdens het openen van een verbinding onder zware belasting voor Linux
- Oplossing voor ontbrekende meta gegevens in de Framework bundel voor macOS
- Oplossen voor problemen met `pip install --user` in Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

**Opgeloste fouten**

- Herstel FromSubscription wanneer het wordt gebruikt met de conversatie-transcriptie.
- Los de fout op in trefwoord herkennen voor spraak assistenten.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019-mei release

**Nieuwe functies**

- Trefwoord herkennen (KWS) is nu beschikbaar voor Windows en Linux. KWS-functionaliteit kan worden gebruikt met elk type microfoon, maar de officiële KWS-ondersteuning is momenteel beperkt tot de microfoon matrices die zijn gevonden in de Azure Kinect DK-hardware of de speech-apparaten SDK.
- De functionaliteit van de woordgroepen Hint is beschikbaar via de SDK. Klik [hier](./get-started-speech-to-text.md) voor meer informatie.
- De functionaliteit van de conversatie transcriptie is beschikbaar via de SDK. [Hier](./conversation-transcription.md)weer geven.
- Voeg ondersteuning toe voor spraak assistenten met behulp van het directe lijn spraak kanaal.

**Voorbeelden**

- Er zijn voor beelden toegevoegd voor nieuwe functies of nieuwe services die door de SDK worden ondersteund.

**Verbeteringen/wijzigingen**

- Er zijn verschillende kenmerken van de Recognizer toegevoegd om het service gedrag of de service resultaten aan te passen (zoals het maskeren van scheld woorden en andere).
- U kunt de herkenner nu configureren via de standaard configuratie-eigenschappen, zelfs als u de herkenner hebt gemaakt `FromEndpoint` .
- Eigenschap doel-C: `OutputFormat` is toegevoegd aan `SPXSpeechConfiguration` .
- De SDK ondersteunt nu Debian 9 als een Linux-distributie.

**Opgeloste fouten**

- Er is een probleem opgelost waarbij de resource van de spreker te vroeg is afgezet in tekst-naar-spraak.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Dit is een alleen-Java script-versie. Er zijn geen functies toegevoegd. De volgende oplossingen zijn uitgevoerd:

- Voor komen dat het webpakket de HTTPS-proxy-agent laadt.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019-april release

**Nieuwe functies**

- De SDK ondersteunt nu de tekst-naar-spraak-service als een bèta versie. Het wordt ondersteund op het bureau blad van Windows en Linux vanuit C++ en C#. Controleer het [tekst-naar-spraak-overzicht](text-to-speech.md#get-started)voor meer informatie.
- De SDK ondersteunt nu MP3-en opus/OGG-audio bestanden als invoer bestanden voor streams. Deze functie is alleen beschikbaar op Linux vanuit C++ en C# en is momenteel in bèta ( [meer informatie hierover](how-to-use-codec-compressed-audio-input-streams.md)).
- De Speech-SDK voor Java, .NET core, C++ en objectief-C hebben de macOS-ondersteuning verkregen. De doel-C-ondersteuning voor macOS bevindt zich momenteel in een bèta versie.
- iOS: de Speech SDK voor iOS (objectief-C) wordt nu ook gepubliceerd als een CocoaPod.
- Java script: ondersteuning voor niet-standaard microfoon als invoer apparaat.
- Java script: proxy-ondersteuning voor Node.js.

**Voorbeelden**

- Voor beelden voor het gebruik van de Speech SDK met C++ en met de doel-C op macOS zijn toegevoegd.
- Er zijn voor beelden toegevoegd waarmee het gebruik van de tekst-naar-spraak-service wordt gedemonstreerd.

**Verbeteringen/wijzigingen**

- Python: aanvullende eigenschappen van herkennings resultaten worden nu weer gegeven via de `properties` eigenschap.
- Voor aanvullende ontwikkel-en probleemoplossings ondersteuning kunt u informatie over de SDK-logboek registratie en diagnostische gegevens omleiden naar een logboek bestand (meer informatie [hierover).](how-to-use-logging.md)
- Java script: Verbeter de prestaties van de audio verwerking.

**Opgeloste fouten**

- Mac/iOS: een fout die heeft geleid tot een lange wacht tijd wanneer een verbinding met de spraak service niet tot stand kan worden gebracht, is opgelost.
- Python: de fout afhandeling voor argumenten in python-retour aanroepen wordt verbeterd.
- Java script: onjuiste status rapportage voor spraak gestopt op RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019-februari vernieuwen

Dit is een release van de oplossing voor fouten en alleen van invloed op de systeem eigen/beheerde SDK. Dit heeft geen invloed op de Java script-versie van de SDK.

**Bug oplossen**

- Er is een geheugenlek opgelost bij het gebruik van een microfoon invoer. Streamen of bestands invoer worden niet beïnvloed.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019-februari release

**Nieuwe functies**

- De Speech SDK ondersteunt het selecteren van de invoer microfoon via de- `AudioConfig` klasse. Zo kunt u audio gegevens streamen naar de spraak service vanuit een niet-standaard microfoon. Zie de documentatie over het [selecteren van audio-invoer apparaten](how-to-select-audio-input-devices.md)voor meer informatie. Deze functie is nog niet beschikbaar via Java script.
- De Speech SDK ondersteunt nu eenheid in een bèta versie. Feedback geven via de sectie probleem in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples). Deze versie ondersteunt eenheid op Windows x86-en x64-computers (desktop-of Universeel Windows-platform-toepassingen) en Android (ARM32/64, x86). Meer informatie is beschikbaar in onze [Quick](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)start van onze unit.
- Het bestand `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (geleverd bij eerdere versies) is niet meer nodig. De functionaliteit is nu geïntegreerd in de core-SDK.

**Voorbeelden**

De volgende nieuwe inhoud is beschikbaar in onze [voorbeeld opslagplaats](https://aka.ms/csspeech/samples):

- Aanvullende voor beelden voor `AudioConfig.FromMicrophoneInput` .
- Aanvullende python-voor beelden voor intentie herkenning en-omzetting.
- Aanvullende voor beelden voor het gebruik van het `Connection` object in Ios.
- Aanvullende Java-voor beelden voor vertaling met audio-uitvoer.
- Nieuw voor beeld voor het gebruik van de [batch-transcriptie rest API](batch-transcription.md).

**Verbeteringen/wijzigingen**

- Python
  - Verbeterde verificatie van de para meter en fout berichten in `SpeechConfig` .
  - Voeg ondersteuning toe voor het `Connection` object.
  - Ondersteuning voor 32-bits python (x86) in Windows.
  - De spraak-SDK voor python valt buiten de bèta versie.
- iOS
  - De SDK is nu gebouwd op basis van de iOS SDK-versie 12,1.
  - De SDK ondersteunt nu iOS-versies 9,2 en hoger.
  - Verbeter de referentie documentatie en los diverse eigenschapnamen op.
- Javascript
  - Voeg ondersteuning toe voor het `Connection` object.
  - Type definitie bestanden voor gebundelde java script toevoegen
  - Eerste ondersteuning en implementatie voor woordgroepen hints.
  - Verzameling eigenschappen retour neren met Service-JSON voor herkenning
- Windows-Dll's bevatten nu een versie bron.
- Als u een herkenner maakt, `FromEndpoint` kunt u de para meters rechtstreeks aan de eind punt-URL toevoegen. `FromEndpoint`U kunt de herkenner niet configureren via de standaard configuratie-eigenschappen.

**Opgeloste fouten**

- De lege proxy-gebruikers naam en het proxy wachtwoord zijn niet goed afgehandeld. Als u met deze versie proxy gebruikersnaam en proxy wachtwoord instelt op een lege teken reeks, worden ze niet verzonden wanneer er verbinding wordt gemaakt met de proxy.
- SessionId die door de SDK is gemaakt, is niet altijd echt wille keurig voor sommige talen &nbsp; /omgevingen. De initialisatie van de wille keurige Generator is toegevoegd om dit probleem op te lossen.
- De verwerking van het autorisatie token verbeteren. Als u een autorisatie token wilt gebruiken, geeft u in de `SpeechConfig` en laat u de sleutel van het abonnement leeg. Maak vervolgens zoals gebruikelijk de herkenner.
- In sommige gevallen is het `Connection` object niet correct vrijgegeven. Dit probleem is opgelost.
- Het Java script-voor beeld is opgelost voor de ondersteuning van audio-uitvoer voor vertaal synthese ook in Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Dit is een alleen-Java script-versie. Er zijn geen functies toegevoegd. De volgende oplossingen zijn uitgevoerd:

- Fire end van de stroom op turn. End, niet op Speech. End.
- Los de fout op in een audio pomp die de volgende verzen ding niet heeft gepland als de huidige verzen ding is mislukt.
- Herstel doorlopende herkenning met verificatie token.
- Fout oplossing voor andere Recognizer/eind punten.
- Verbeterde documentatie.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018-december release

**Nieuwe functies**

- Python
  - De bèta versie van python-ondersteuning (3,5 en hoger) is beschikbaar in deze release. Zie hier] (Quick Start-python.md) voor meer informatie.
- Javascript
  - De Speech SDK voor Java script is open source. De bron code is beschikbaar op [github](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - We bieden nu ondersteuning voor Node.js. u kunt [hier](./get-started-speech-to-text.md)meer informatie vinden.
  - De lengte beperking voor audio sessies is verwijderd. de verbinding wordt automatisch hersteld onder de dekking.
- `Connection` object
  - Vanuit de `Recognizer` kunt u toegang krijgen tot een `Connection` object. Met dit object kunt u de service verbinding expliciet initiëren en zich abonneren op verbinding maken en verbreken van gebeurtenissen.
    (Deze functie is nog niet beschikbaar vanuit Java script en python.)
- Ondersteuning voor Ubuntu 18,04.
- Android
  - Er is ondersteuning geboden voor proguard tijdens het genereren van APK.

**Rijke**

- Verbeteringen in het gebruik van de interne thread, waardoor het aantal threads, vergren delingen en mutexen wordt verkleind.
- Verbeterde fout rapportage/-informatie. In verschillende gevallen zijn fout berichten niet helemaal door gegeven.
- Bijgewerkte ontwikkelings afhankelijkheden in Java script voor het gebruik van up-to-date-modules.

**Opgeloste fouten**

- Er zijn problemen met het geheugen vastgesteld vanwege een niet-overeenkomend type in `RecognizeAsync` .
- In sommige gevallen werden uitzonde ringen gelekt.
- Oplossen van geheugen lekkage in Vertaal gebeurtenis argumenten.
- Er is een vergrendelings probleem opgelost bij het opnieuw verbinden van langlopende sessies.
- Er is een probleem opgelost dat kan leiden tot een ontbrekend eind resultaat voor mislukte vertalingen.
- C#: als een `async` bewerking niet is ingecheckt in de hoofd thread, zou de herkenner kunnen worden verwijderd voordat de asynchrone taak werd voltooid.
- Java: er is een probleem opgelost dat resulteert in het vastlopen van de Java-VM.
- Doel-C: vaste Enum-toewijzing; RecognizedIntent is geretourneerd in plaats van `RecognizingIntent` .
- Java script: Stel de standaard uitvoer indeling in op ' Simple ' in `SpeechConfig` .
- Java script: inconsistentie verwijderen tussen eigenschappen van het configuratie object in Java script en andere talen.

**Voorbeelden**

- Er zijn verschillende voor beelden bijgewerkt en opgelost (bijvoorbeeld uitvoer stemmen voor vertaling, enz.).
- Er zijn Node.js-voor beelden toegevoegd aan de voor [beeld-opslag plaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nieuwe functies**

- Ondersteuning voor Android x86/x64.
- Proxy ondersteuning: in het `SpeechConfig` object kunt u nu een functie aanroepen om de proxy gegevens in te stellen (hostnaam, poort, gebruikers naam en wacht woord). Deze functie is nog niet beschikbaar op iOS.
- De fout code en berichten zijn verbeterd. Als een herkenning een fout retourneert, is deze al ingesteld `Reason` (in geannuleerde gebeurtenis) of `CancellationDetails` (bij herkennings resultaat) tot `Error` . De geannuleerde gebeurtenis bevat nu twee extra leden `ErrorCode` en `ErrorDetails` . Als de server aanvullende fout informatie heeft geretourneerd met de gerapporteerde fout, is deze nu beschikbaar in de nieuwe leden.

**Rijke**

- Extra verificatie toegevoegd in de configuratie van de herkenner en er is extra fout bericht toegevoegd.
- Verbeterde verwerking van langdurige stilte tijd in het midden van een audio bestand.
- NuGet-pakket: voor .NET Framework projecten kan het niet worden gebouwd met AnyCPU-configuratie.

**Opgeloste fouten**

- Er zijn verschillende uitzonde ringen gevonden in recognizers. Daarnaast worden uitzonde ringen gedetecteerd en omgezet in een `Canceled` gebeurtenis.
- Los een geheugenlek op in eigenschaps beheer.
- Er is een fout opgelost waarbij een audio-invoer bestand de herkenner kan vastlopen.
- Er is een fout opgelost waarbij gebeurtenissen kunnen worden ontvangen na een sessie-stop gebeurtenis.
- Er zijn enkele race voorwaarden in threading opgelost.
- Er is een probleem opgelost met een compatibiliteit met iOS dat kan leiden tot crashes.
- Stabiliteits verbeteringen voor Android-microfoon ondersteuning.
- Er is een fout opgelost waarbij een herkenner in Java script de herkennings taal negeert.
- Er is een fout opgelost waardoor het instellen van `EndpointId` (in sommige gevallen) in Java script niet kan worden ingesteld.
- De parameter volgorde is gewijzigd in AddIntent in Java script en de ontbrekende `AddIntent` Java script-hand tekening is toegevoegd.

**Voorbeelden**

- Er zijn voor beelden van C++ en C# toegevoegd voor het gebruik van pull-en push streams in de voor [beeld-opslag plaats](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Verbeteringen van de betrouw baarheid en oplossingen:

- Vast mogelijke fatale fout vanwege een conflict situatie in de herkenner
- Vaste mogelijke fatale fout bij het opheffen van eigenschappen.
- Er zijn aanvullende fout-en parameter controles toegevoegd.
- Doel-C: er is een vast mogelijke fatale fout opgetreden die is veroorzaakt door de naam die overschrijft in NSString.
- Doel-C: aangepaste zicht baarheid van de API
- Java script: opgelost met betrekking tot gebeurtenissen en hun nettoladingen.
- Verbeterde documentatie.

In onze [voorbeeld opslagplaats](https://aka.ms/csspeech/samples)is een nieuw voor beeld voor Java script toegevoegd.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018-september release

**Nieuwe functies**

- Ondersteuning voor objectief-C op iOS. Bekijk onze [doel stelling-C Quick start voor IOS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone).
- Ondersteuning voor Java script in browser. Bekijk onze [Snelstartgids voor Java script](./get-started-speech-to-text.md).

**Wijzigingen die fouten veroorzaken**

- In deze versie wordt een aantal belang rijke wijzigingen geïntroduceerd.
  Raadpleeg [Deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018-augustus release

**Nieuwe functies**

- UWP-apps die zijn gebouwd met de Speech SDK, kunnen nu de Windows app Certification Kit (WACK) door geven.
  Bekijk de Quick Start van [UWP](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp).
- Ondersteuning voor .NET Standard 2,0 op Linux (Ubuntu 16,04 x64).
- Experimentele: ondersteuning voor Java 8 op Windows (64-bits) en Linux (Ubuntu 16,04 x64).
  Bekijk de [Java Runtime Environment Snelstartgids](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre).

**Functionele wijziging**

- Meer informatie over fout details beschikbaar maken voor verbindings fouten.

**Wijzigingen die fouten veroorzaken**

- Op Java (Android) heeft de `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` functie geen para meter Path meer nodig. Nu wordt het pad automatisch gedetecteerd op alle ondersteunde platforms.
- De Get-accessor van de eigenschap `EndpointUrl` in Java en C# is verwijderd.

**Opgeloste fouten**

- In Java wordt het resultaat van de audio synthese op de vertalings herkenning nu geïmplementeerd.
- Er is een fout opgelost die kan leiden tot inactieve threads en een groter aantal open en ongebruikte sockets.
- Er is een probleem opgelost waarbij een langlopende herkenning in het midden van de verzen ding kan worden beëindigd.
- Er is een conflict voorwaarde opgelost in de herkenner is afgesloten.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018-juli release

**Nieuwe functies**

- Ondersteuning voor Android-platform (API 23: Android 6,0 Marshmallow of hoger). Bekijk de [Android-Quick](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)start.
- Ondersteuning voor .NET Standard 2,0 in Windows. Bekijk de [Snelstartgids voor .net core](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experiment: ondersteuning voor UWP in Windows (versie 1709 of hoger).
  - Bekijk de Quick Start van [UWP](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp).
  - Opmerking: UWP-apps die zijn gebouwd met de Speech SDK, geven nog geen gebruik van de Windows app Certification Kit (WACK).
- Ondersteuning voor langlopende herkenning met automatisch opnieuw verbinden.

**Functionele wijzigingen**

- `StartContinuousRecognitionAsync()` ondersteunt langlopende herkenning.
- Het herkennings resultaat bevat meer velden. Ze worden gecompenseerd vanaf het begin en de duur van de audio (zowel in Ticks) van de herkende tekst als aanvullende waarden die de herkennings status vertegenwoordigen, bijvoorbeeld `InitialSilenceTimeout` en `InitialBabbleTimeout` .
- AuthorizationToken ondersteunen voor het maken van fabrieks instanties.

**Wijzigingen die fouten veroorzaken**

- Herkennings gebeurtenissen: `NoMatch` gebeurtenis type is samengevoegd in de `Error` gebeurtenis.
- De naam van SpeechOutputFormat in C# is gewijzigd in `OutputFormat` om te blijven uitgelijnd met C++.
- Het retour type van sommige methoden van de `AudioInputStream` interface is enigszins gewijzigd:
  - In Java wordt de `read` methode nu geretourneerd in `long` plaats van `int` .
  - In C# wordt de `Read` methode nu geretourneerd in `uint` plaats van `int` .
  - In C++ `Read` retour neren de and- `GetFormat` methoden nu `size_t` in plaats van `int` .
- C++: exemplaren van audio-invoer stromen kunnen nu alleen worden door gegeven als een `shared_ptr` .

**Opgeloste fouten**

- Onjuist geretourneerde retour waarden in het resultaat wanneer er een `RecognizeAsync()` time-out optreedt.
- De afhankelijkheid van Media Foundation-bibliotheken in Windows is verwijderd. De SDK gebruikt nu kern audio-Api's.
- Documentatie oplossing: er is een pagina met [regio's](regions.md) toegevoegd om de ondersteunde regio's te beschrijven.

**Bekend probleem**

- De Speech-SDK voor Android rapporteert geen resultaten voor spraak synthese voor vertaling. Dit probleem wordt opgelost in de volgende release.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018-juni release

**Functionele wijzigingen**

- AudioInputStream

  Een herkenner kan nu een stream als de audio bron gebruiken. Zie de verwante [hand leiding](how-to-use-audio-input-streams.md)voor meer informatie.

- Gedetailleerde uitvoer indeling

  Wanneer u een maakt `SpeechRecognizer` , kunt u een aanvraag- `Detailed` of `Simple` uitvoer indeling. De `DetailedSpeechRecognitionResult` bevat een betrouwbaarheids Score, herkende tekst, ruwe lexicale vorm, genormaliseerde vorm en genormaliseerde vorm met gemaskerde Grove woorden.

**Laatste wijziging**

- Gewijzigd in `SpeechRecognitionResult.Text` van `SpeechRecognitionResult.RecognizedText` in C#.

**Opgeloste fouten**

- Er is een mogelijk probleem met een retour aanroep in de USP-laag opgelost tijdens het afsluiten.
- Als een herkenner een audio-invoer bestand heeft gebruikt, houdt het de bestands ingang langer dan nodig aan.
- Er zijn verschillende deadlocks tussen de bericht pomp en de herkenner verwijderd.
- Als er een time-out optreedt voor `NoMatch` het antwoord van de service, treedt er een resultaat op.
- De Media Foundation-bibliotheken in Windows zijn vertraging geladen. Deze bibliotheek is alleen vereist voor invoer van de microfoon.
- De upload snelheid voor audio gegevens is beperkt tot ongeveer twee keer de oorspronkelijke audio snelheid.
- In Windows hebben C# .NET-assembly's nu een sterke naam.
- Documentatie oplossing: `Region` is vereist informatie voor het maken van een herkenner.

Er zijn meer voor beelden toegevoegd en worden voortdurend bijgewerkt. Zie voor de meest recente set voor beelden de [github-opslag plaats Speech SDK samples](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018-mei release

Deze versie is de eerste open bare preview-versie van de Cognitive Services Speech SDK.