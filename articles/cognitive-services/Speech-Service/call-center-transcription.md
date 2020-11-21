---
title: Call Center transcriptie-Speech Service
titleSuffix: Azure Cognitive Services
description: Een veelvoorkomend scenario voor spraak naar tekst is het transcriberen van grote hoeveel heden telefoon gegevens die afkomstig zijn van verschillende systemen, zoals Interactive Voice Response (IVR). Met behulp van de speech-service en het Unified speech-model kan een bedrijf transcripties van hoge kwaliteit krijgen met Audio Capture-systemen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 19d4cc388494e149b7f258a8e9f154041a3dd070
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021963"
---
# <a name="speech-service-for-telephony-data"></a>Spraak service voor telefoon gegevens

Telefoon gegevens die via vaste tekst, mobiele telefoons en radio's worden gegenereerd, zijn doorgaans lage kwaliteit en narrowband in het bereik van 8 KHz, waardoor er uitdagingen ontstaan bij het converteren van spraak-naar-teken. De nieuwste spraakherkennings modellen van de speech-service Excel bij het transcriberen van deze telefoon gegevens, zelfs in gevallen waarin de gegevens moeilijk te begrijpen zijn. Deze modellen zijn getraind met grote hoeveel heden telefoon gegevens en hebben de beste nauw keurigheid van de markt herkenning, zelfs in ruis omgevingen.

Een veelvoorkomend scenario voor spraak naar tekst is het transcriberen van grote hoeveel heden telefoon gegevens die afkomstig kunnen zijn van verschillende systemen, zoals Interactive Voice Response (IVR). De audio die deze systemen bieden, kunnen stereo of mono zijn, en RAW met weinig-naar-geen post verwerking op het signaal. Met behulp van de speech-service en het Unified speech-model kan een bedrijf transcripties van hoge kwaliteit krijgen, ongeacht welke systemen worden gebruikt voor het vastleggen van audio.

Telefoon gegevens kunnen worden gebruikt om de behoeften van uw klanten beter te begrijpen, nieuwe marketing kansen te identificeren of de prestaties van call center-agents te evalueren. Nadat de gegevens zijn getranscribeerd, kan een bedrijf de uitvoer gebruiken voor doel einden, zoals verbeterde telemetrie, het identificeren van belang rijke zinnen of het analyseren van de sentiment van de klant.

De technologieën die op deze pagina worden beschreven, worden door micro soft intern voor verschillende services voor service voor het verwerken van aanroepen, zowel in realtime als in batch modus.

Laten we een aantal van de technologie en verwante functies van de aanbieding voor spraak Services eens bekijken.

> [!IMPORTANT]
> Het uniforme model van de speech-service is getraind met diverse gegevens en biedt een oplossing met één model voor een aantal scenario's van dicteren tot analyse van telefonie.

## <a name="azure-technology-for-call-centers"></a>Azure-technologie voor call centers

Buiten het functionele aspect van de functies van de spraak service, hun primaire doel, wanneer dit wordt toegepast op het Call Center – is de klant ervaring te verbeteren. Er zijn drie duidelijke domeinen in dit opzicht:

- Post-Call-analyses, die in feite batch verwerking van aanroep opnames hebben na de aanroep.
- Real-time analyse, waarmee het audio signaal wordt verwerkt om verschillende inzichten te extra heren wanneer de aanroep plaatsvindt (waarbij sentiment een prominente use-case is).
- Spraak assistenten (bots), de dialoog tussen de klant en de bot in een poging om het probleem van de klant op te lossen zonder deel te nemen aan de agent of de toepassing van de AI-protocollen (kunst matige intelligentie) om de agent te helpen.

Een typisch architectuur diagram van de implementatie van een batch-scenario wordt weer gegeven in de afbeelding onder ![ Call Center transcriptie-architectuur](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Technologie onderdelen voor spraak analyse

Of het domein na het aanroepen of realtime is, Azure biedt een reeks rijpe en opkomende technologieën om de gebruikers ervaring te verbeteren.

### <a name="speech-to-text-stt"></a>Spraak naar tekst (STT)

[Spraak naar tekst](speech-to-text.md) is de meest gewenste-na-functie in een aanroep Center-oplossing. Omdat veel van de downstream Analytics-processen zijn gebaseerd op getranscribeerde tekst, is de fout verhouding van Word (_wer_) van het hoogste belang. Een van de belangrijkste uitdagingen in Call Center transcriptie is het geluid dat zich voordoet in het Call Center (bijvoorbeeld andere agents die op de achtergrond praten), de vele taal instellingen en-dialecten, evenals de lage kwaliteit van het daad werkelijke telefoon signaal. WER is zeer gecorreleerd met de manier waarop de akoestische en taal modellen zijn getraind voor een bepaalde land instelling, waardoor de mogelijkheid om het model aan uw land instelling aan te passen, belang rijk is. Onze meest recente gecombineerde versie 4. x modellen zijn de oplossing voor zowel transcriptie nauwkeurigheid als latentie. Getraind met tien duizenden uren aan akoestische gegevens en miljarden aan lexicale informatie, zijn uniforme modellen de meest nauw keurige modellen in de markt om Call Center-gegevens te transcriberen.

### <a name="sentiment"></a>Sentiment

Gauging of de klant een goede ervaring heeft met een van de belangrijkste aspecten van spraak analyse, wanneer deze wordt toegepast op de oproep centrum ruimte. Onze [batch transcriptie-API](batch-transcription.md) biedt sentiment-analyse per utterance. U kunt de set waarden die zijn verkregen als onderdeel van een transcript van een oproep samen voegen om de sentiment van de oproep voor zowel uw agents als de klant te bepalen.

### <a name="silence-non-talk"></a>Stilte (niet-pratend)

Het is niet ongebruikelijk dat er 35 procent van een ondersteunings oproep wordt gebeld om niet-pratende tijd te bellen. Enkele scenario's waarvoor niet-pratend gebeurt: agenten die de eerdere Case geschiedenis opzoeken met een klant, agents die hen in staat stellen om toegang te krijgen tot het bureau blad van de klant en functies uit te voeren, klanten die wachten op een overdracht, enzovoort. Het is zeer belang rijk om te meten wanneer stilte plaatsvindt in een aanroep, omdat er sprake is van een belang rijk klant sensitivities dat wordt uitgevoerd door deze scenario's en waar deze zich voordoen in de aanroep.

### <a name="translation"></a>Omzetting

Sommige bedrijven experimenteren met het bieden van vertaalde transcripten van ondersteunings gesprekken van vreemde talen zodat bezorg managers de wereld wijde ervaring van hun klanten kunnen begrijpen. Onze [Vertaal](./speech-translation.md) mogelijkheden zijn niet meer zo. We kunnen audio-naar-audio-of audio-naar-tekst omzetten voor een groot aantal land instellingen.

### <a name="text-to-speech"></a>Text to Speech

[Tekst-naar-spraak](text-to-speech.md) is een ander belang rijk gebied bij het implementeren van bots dat communiceert met de klanten. De typische locatie is dat de klant spreekt, hun stem wordt getranscribeerd naar tekst, de tekst wordt geanalyseerd voor intenties, een antwoord wordt op basis van de herkende intentie en vervolgens een activum wordt geoppereerd aan de klant of een gewerkte spraak respons wordt gegenereerd. Natuurlijk is dit allemaal snel te doen, waardoor lage latentie een belang rijk onderdeel is van het succes van deze systemen.

Onze end-to-end-latentie is aanzienlijk laag voor de verschillende technologieën, zoals [spraak naar tekst](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [bot Framework](https://dev.botframework.com/), [tekst naar spraak](text-to-speech.md).

Onze nieuwe stemmen worden ook niet onderscheiden van mensen stemmen. U kunt onze stemmen gebruiken om uw bot unieke persoonlijkheid te geven.

### <a name="search"></a>Zoeken

Een andere nietmachine van analyses is het identificeren van interacties waar een bepaalde gebeurtenis of ervaring heeft plaatsgevonden. Dit gebeurt meestal met een van de twee benaderingen; een ad-hoc-zoek opdracht waarbij de gebruiker simpelweg een woord groep en het systeem reageert, of een meer gestructureerde query waarbij een analist een set logische instructies kan maken die een scenario in een aanroep identificeert en vervolgens elke aanroep kan worden geïndexeerd op basis van die set query's. Een goed Zoek voorbeeld is de alomtegenwoordige-nalevings verklaring "deze oproep moet worden geregistreerd voor kwaliteits doeleinden... ". Veel bedrijven willen er zeker van zijn dat hun agents deze disclaimer aan klanten leveren voordat de aanroep daad werkelijk wordt geregistreerd. De meeste analyse systemen hebben de mogelijkheid om de gedragingen te trenden die worden gevonden door query-en zoek algoritmen, en deze rapportage van trends is uiteindelijk een van de belangrijkste functies van een Analytics-systeem. Met de functie [cognitieve Services](https://azure.microsoft.com/services/cognitive-services/directory/search/) kunt u uw end-to-end-oplossing aanzienlijk verbeteren met indexerings-en zoek mogelijkheden.

### <a name="key-phrase-extraction"></a>Sleuteltermextractie

Dit gebied is een van de meest uitdagende analyse toepassingen en één die voor deel uitmaakt van de toepassing van AI en machine learning. Het primaire scenario in dit geval is om klant intentie af te leiden. Waarom wordt de klant gebeld? Wat is het probleem van de klant? Waarom heeft de klant een negatieve ervaring opgedaan? Onze [tekst analyse service](https://azure.microsoft.com/services/cognitive-services/text-analytics/) voorziet in een set analyse van het vak voor het snel upgraden van uw end-to-end oplossing voor het uitpakken van die belang rijke tref woorden of zinsdelen.

Laten we nu eens kijken naar de batch verwerking en de real-time pijp lijnen voor spraak herkenning in iets meer details.

## <a name="batch-transcription-of-call-center-data"></a>Batch-transcriptie van Call Center-gegevens

Voor het transcriberen van bulk audio hebben we de [batch transcriptie-API](batch-transcription.md)ontwikkeld. De batch transcriptie-API is ontwikkeld om grote hoeveel heden audio gegevens asynchroon te transcriberen. Met betrekking tot het transcriberen van Call Center-gegevens is onze oplossing gebaseerd op deze pijlers:

- **Nauw keurigheid** : met geïntegreerde modellen van de vierde generatie bieden we een ongekende transcriptie kwaliteit.
- **Latentie** : we begrijpen dat bij het uitvoeren van bulk transcripties de transcripties snel nodig zijn. De transcriptie-taken die via de [batch transcriptie-API](batch-transcription.md) worden geïnitieerd, worden onmiddellijk in de wachtrij geplaatst en zodra de taak is gestart, wordt deze sneller uitgevoerd dan in realtime-transcriptie.
- **Beveiliging** : we begrijpen dat aanroepen mogelijk gevoelige gegevens bevatten. Houd er zeker van dat de beveiliging een van onze hoogste prioriteiten is. Onze service heeft ISO, SOC, HIPAA, PCI-certificeringen verkregen.

Met Call Centers worden dagelijks grote hoeveel heden audio gegevens gegenereerd. Als uw bedrijf telefonische gegevens opslaat op een centrale locatie, zoals Azure Storage, kunt u de [batch transcriptie API](batch-transcription.md) gebruiken voor het asynchroon aanvragen en ontvangen van transcripties.

Een typische oplossing maakt gebruik van de volgende services:

- De speech-service wordt gebruikt om spraak-naar-tekst te transcriberen. Voor de speech-service is een Standard-abonnement (S0) vereist om de batch transcriptie-API te gebruiken. Gratis abonnementen (F0) werken niet.
- [Azure Storage](https://azure.microsoft.com/services/storage/) wordt gebruikt voor het opslaan van telefoon gegevens en de transcripten die worden geretourneerd door de batch transcriptie API. Voor dit opslag account moeten meldingen worden gebruikt, specifiek voor wanneer nieuwe bestanden worden toegevoegd. Deze meldingen worden gebruikt om het transcriptie-proces te activeren.
- [Azure functions](../../azure-functions/index.yml) wordt gebruikt om de SAS-URI (Shared Access signatures) voor elke record te maken en de HTTP POST-aanvraag voor het starten van een transcriptie te activeren. Daarnaast wordt Azure Functions gebruikt voor het maken van aanvragen voor het ophalen en verwijderen van transcripties met behulp van de batch transcriptie-API.

Intern gebruiken we de bovenstaande technologieën om micro soft-klant gesprekken in batch-modus te ondersteunen.
:::image type="content" source="media/scenarios/call-center-batch-pipeline.png" alt-text="Technologieën die worden gebruikt ter ondersteuning van micro soft-klant gesprekken in batch-modus.":::

## <a name="real-time-transcription-for-call-center-data"></a>Real-time transcriptie voor Call Center-gegevens

Sommige bedrijven zijn verplicht om gesp rekken in realtime te transcriberen. Realtime-transcriptie kunnen worden gebruikt om belang rijke woorden en triggers te identificeren voor inhoud en resources die relevant zijn voor de conversatie, voor het bewaken van sentiment, het verbeteren van de toegankelijkheid of het bieden van vertalingen voor klanten en agents die geen systeem eigen sprekers zijn.

Voor scenario's waarvoor realtime-transcriptie zijn vereist, kunt u het beste de [Speech SDK](speech-sdk.md)gebruiken. Op dit moment is spraak-naar-tekst beschikbaar in [meer dan 20 talen](language-support.md)en is de SDK beschikbaar in C++, C#, Java, Python, Node.js, objectief-C en Java script. Voor beelden zijn beschikbaar in elke taal op [github](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Zie [release opmerkingen](releasenotes.md)voor het laatste nieuws en de meest recente updates.

Intern gebruiken we de bovenstaande technologieën om in realtime micro soft-klant gesprekken te analyseren wanneer deze zich voordoen, zoals in het volgende diagram wordt geïllustreerd.

![Batch-architectuur](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Een woord op IVRs

De speech-service kan eenvoudig worden geïntegreerd in een oplossing met behulp van de [spraak-SDK](speech-sdk.md) of de [rest API](./overview.md#reference-docs). Call Center transcriptie kan echter extra technologieën vereisen. Normaal gesp roken is er een verbinding tussen een IVR-systeem en Azure vereist. Hoewel we deze onderdelen niet aanbieden, is hier een beschrijving van wat een verbinding met een IVR tot stand brengt.

Verschillende IVR-of Telephony-service producten (zoals Genesys of AudioCodes) bieden integratie mogelijkheden die kunnen worden gebruikt voor het inschakelen van inkomende en uitgaande audio doorvoer door middel van een Azure-service. In principe kan een aangepaste Azure-service een specifieke interface bieden voor het definiëren van telefoon gesprek sessies (zoals het aanroepen van de start of het aanroepen van een WebSocket) en een API voor het ontvangen van inkomende streams die wordt gebruikt met de spraak service. Uitgaande reacties, zoals conversatie transcriptie of verbindingen met het bot-Framework, kunnen worden gesynthesizerd met de tekst-naar-spraak-service van micro soft en worden teruggestuurd naar de IVR voor afspelen.

Een ander scenario is directe integratie met behulp van SIP (Session Initiation Protocol). Een Azure-service maakt verbinding met een SIP-server, waardoor een inkomende stroom en een uitgaande stroom worden opgehaald, die wordt gebruikt voor de fase van spraak naar tekst en tekst naar spraak. Als u verbinding wilt maken met een SIP-server, zijn er commerciële software, zoals Ozeki SDK, of [teams die de API aanroepen en vergaderingen](/graph/api/resources/communications-api-overview) (momenteel in bèta) hebben, die zijn ontworpen ter ondersteuning van dit type scenario voor audio-aanroepen.

## <a name="customize-existing-experiences"></a>Bestaande ervaringen aanpassen

 De speech-service werkt goed met ingebouwde modellen. Het is echter mogelijk dat u de ervaring voor uw product of omgeving verder wilt aanpassen en afstemmen. Aanpassingsopties variëren van het afstemmen van akoestische modellen tot unieke spraakstijlen voor uw merk. Nadat u een aangepast model hebt gemaakt, kunt u het gebruiken met een van de functies van de speech-service in realtime of in batch modus.

| Speech Service | Modelleren | Description |
| -------------- | ----- | ----------- |
| Spraak naar tekst | [Akoestisch model](./how-to-custom-speech-train-model.md) | Maak een aangepast akoestische model voor toepassingen, hulpprogram ma's of apparaten die in bepaalde omgevingen worden gebruikt, zoals in een auto of op een fabriek, elk met specifieke registratie voorwaarden. Voor beelden zijn onder andere geaccentde spraak, specifieke achtergrond geluiden of het gebruik van een specifieke microfoon voor de opname. |
|                | [Taalmodel](./how-to-custom-speech-train-model.md) | Maak een aangepast taal model om transcriptie te verbeteren van de branchespecifieke woorden lijst en grammatica, zoals medische terminologie of het jargon. |
|                | [Uitspraakmodel](./how-to-custom-speech-train-model.md) | Met een aangepast uitspraak model kunt u het fonetische formulier definiëren en weer geven voor een woord of term. Het is handig voor het afhandelen van aangepaste voor waarden, zoals product namen of acroniemen. Alles wat u nodig hebt om aan de slag te gaan is een uitspraak bestand dat een eenvoudig `.txt` bestand is. |
| Tekst naar spraak | [Spraakstijl](./how-to-custom-voice-create-voice.md) | Met aangepaste spraak lettertypen kunt u een herken bare, een-op-een-spraak voor uw merk maken. Er is slechts een kleine hoeveelheid gegevens nodig om aan de slag te gaan. Hoe meer gegevens u verstrekt, des te meer natuurlijke en Human-like uw gesp roken letter type klinkt. |

## <a name="sample-code"></a>Voorbeeldcode

De voorbeeld code is beschikbaar op GitHub voor elk van de functies van de spraak service. Deze voorbeelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen. Gebruik de volgende koppelingen om SDK- en REST-voorbeelden te bekijken:

- [Voor beelden van spraak-naar-tekst-en spraak omzetting (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voorbeelden van batchtranscriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Voorbeelden van tekst-naar-spraak (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Naslagdocumentatie

- [Speech-SDK](./speech-sdk.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Spraak-naar-tekst](rest-speech-to-text.md)
- [REST API: Tekst-naar-spraak](rest-text-to-speech.md)
- [REST API: Batchtranscriptie en aanpassing](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verkrijg gratis een spraakserviceabonnementssleutel](overview.md#try-the-speech-service-for-free)