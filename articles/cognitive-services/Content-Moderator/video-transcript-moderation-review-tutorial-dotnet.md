---
title: "Zelfstudie: Video's en transcripties beoordelen in .NET - Content Moderator"
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie leert u hoe u een complete oplossing voor het beoordelen van video's en transcripties bouwt die resulteert in een beoordeling die deels automatisch tot stand komt en deels wordt uitgevoerd door menselijke beoordelaars.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: ad689c746a0f4d7232e7f61982fb8c4f735cbe34
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95737799"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Zelfstudie: Video's en transcripties beoordelen

In deze zelfstudie leert u hoe u een volledige oplossing voor het beoordelen van video's en transcripties ontwikkelt die resulteert in een beoordeling die deels automatisch tot stand komt en deels wordt uitgevoerd door menselijke beoordelaars.

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> - De invoervideo('s) comprimeren voor een snellere verwerking
> - De video beoordelen om opnamen en frames met inzichten te verkrijgen
> - Miniaturen (afbeeldingen) maken aan de hand van de tijdstempels
> - Tijdstempels en miniaturen verzenden voor het maken van videobeoordelingen
> - Videospraak converteren naar tekst (transcriptie) met de Media Indexer-API
> - De transcriptie beoordelen met de service voor tekstbeoordeling
> - De beoordeelde transcriptie toevoegen aan het video-overzicht

## <a name="prerequisites"></a>Vereisten

- Registreer u voor de website [Content Moderator-beoordelingsprogramma](https://contentmoderator.cognitive.microsoft.com/) en maak aangepaste tags voor de functies die u wilt identificeren. Zie [Tags gebruiken](./review-tool-user-guide/configure.md#tags) als u hulp nodig hebt met deze stap.

    ![Schermopname van Aangepaste tags voor beoordelen van video's](images/video-tutorial-custom-tags.png)
- Om de voorbeeldtoepassing uit te voeren, hebt u een Azure-account, een Azure Media Services-resource, een Azure Content Moderator-resource en Azure Active Directory-referenties nodig. Voor instructies over het verkrijgen van deze resources raadpleegt u de handleiding [API voor videobeoordeling](video-moderation-api.md).
- Download de [consoletoepassing Videobeoordeling](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) in GitHub.

## <a name="enter-credentials"></a>Referenties opgeven

Bewerk het bestand `App.config` en voeg de naam van de Active Directory-tenant, de service-eindpunten en de abonnementssleutels toe aangegeven door `#####`. U hebt de volgende informatie nodig:

|Sleutel|Beschrijving|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Eindpunt voor de API van Azure Media Services (AMS)|
|`ClientSecret`|Abonnementssleutel voor Azure Media Services|
|`ClientId`|Client-id voor Azure Media Services|
|`AzureAdTenantName`|Naam van Active Directory-tenant voor uw organisatie|
|`ContentModeratorReviewApiSubscriptionKey`|Abonnementssleutel voor de beoordelings-API van Content Moderator|
|`ContentModeratorApiEndpoint`|Eindpunt voor de Content Moderator-API|
|`ContentModeratorTeamId`|Team-id voor Content Moderator|

## <a name="examine-the-main-code"></a>De hoofdcode controleren

De klasse **Programma** in _Program.cs_ is het belangrijkste invoerpunt voor de toepassing voor videobeoordeling.

### <a name="methods-of-program-class"></a>Methoden van de klasse Program

|Methode|Beschrijving|
|-|-|
|`Main`|Parseert de opdrachtregel, verzamelt invoer van de gebruiker en start de verwerking.|
|`ProcessVideo`|Comprimeert, uploadt, beoordeelt en maakt videobeoordelingen.|
|`CreateVideoStreamingRequest`|Maakt een stream voor het uploaden van een video|
|`GetUserInputs`|Verzamelt invoer van de gebruiker; wordt gebruikt als er geen opdrachtregelopties aanwezig zijn.|
|`Initialize`|Initialiseert objecten die nodig zijn voor het beoordelingsproces.|

### <a name="the-main-method"></a>De Main-methode

De uitvoering begint bij `Main()`, dus is dit het punt vanaf waar het belangrijk is om het beoordelingsproces van video's te begrijpen.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` verwerkt de volgende opdrachtregelargumenten:

- Het pad naar een map met MPEG-4-videobestanden die moeten worden aangeboden voor beoordeling. Alle `*.mp4`-bestanden in deze map en submappen worden aangeboden voor beoordeling.
- Optioneel een Booleaanse vlag (true/false) die aangeeft of er al dan niet transcripties moeten worden gegenereerd om ook de audio te beoordelen.

Als er geen opdrachtregelargumenten zijn opgegeven, wordt `GetUserInputs()` aangeroepen met `Main()`. Deze methode vraagt de gebruiker om het pad naar één videobestand in te voeren en om aan te geven of er een transcriptie moet worden gegenereerd.

> [!NOTE]
> De consoletoepassing maakt gebruik van de [API van Azure Media Indexer](../../media-services/previous/legacy-components.md) om transcripties te genereren van de audiotrack van de geüploade video. De resultaten worden aangeboden in de indeling WebVTT. Zie de Engelstalige site [Web Video Text Tracks Format (WebVTT)](https://developer.mozilla.org/docs/Web/API/WebVTT_API) voor meer informatie over deze indeling.

### <a name="initialize-and-processvideo-methods"></a>De methoden Initialize en ProcessVideo

Ongeacht of de opties voor het programma afkomstig zijn van de opdrachtregel of uit interactieve invoer van de gebruiker, roept `Main()` vervolgens `Initialize()` aan om de volgende instanties te maken:

|Klasse|Beschrijving|
|-|-|
|`AMSComponent`|Comprimeert videobestanden voordat deze worden verzonden voor beoordeling.|
|`AMSconfigurations`|Interface naar de configuratiegegevens van de toepassing, gevonden in `App.config`.|
|`VideoModerator`| Uploaden, coderen, versleutelen en beoordelen met behulp van de AMS-SDK|
|`VideoReviewApi`|Videobeoordelingen beheren in de Content Moderator-service|

Deze klassen (afgezien van `AMSConfigurations`, die voor zichzelf spreekt) worden verderop in deze zelfstudie afzonderlijk behandeld.

Ten slotte worden de videobestanden één voor één verwerkt door voor elk bestand `ProcessVideo()` aan te roepen.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

De methode `ProcessVideo()` is redelijk eenvoudig. De methode voert de volgende bewerkingen in de onderstaande volgorde uit:

- De video comprimeren
- De video uploaden naar een asset van Azure Media Services
- Een AMS-taak maken voor het beoordelen van de video
- Een videobeoordeling maken in Content Moderator

In de volgende gedeelten worden enkele van de afzonderlijke processen die worden aangeroepen door `ProcessVideo()` in meer detail besproken. 

## <a name="compress-the-video"></a>De video comprimeren

Teneinde het netwerkverkeer zo veel mogelijk te beperken, converteert de toepassing videobestanden naar de indeling H.264 (MPEG-4 AVC) en worden de bestanden vervolgens geschaald naar een maximale breedte van 640 pixels. De codec H.264 wordt aanbevolen vanwege de hoge efficiëntie (compressie). De compressie wordt gedaan met behulp van het gratis opdrachtregelprogramma `ffmpeg`, dat u kunt vinden in de map `Lib` van Visual Studio. De invoerbestanden kunnen elke indeling hebben die wordt ondersteund door `ffmpeg`, dus ook de meest gebruikte indeling en codecs voor videobestanden.

> [!NOTE]
> Wanneer u het programma start met opdrachtregelopties, geeft u een map op die de videobestanden bevat die moeten worden verzonden voor beoordeling. Alle bestanden in deze map met de extensie `.mp4` worden verwerkt. Als u andere bestanden met andere extensies wilt verwerken, moet u de methode `Main()` in _Program.cs_ bijwerken om de gewenste extensies toe te voegen.

De code voor het comprimeren van een videobestand is de klasse `AmsComponent` in `AMSComponent.cs`. De methode die verantwoordelijk is voor deze functionaliteit is `CompressVideo()`, die hier wordt weergegeven.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

De code voert de volgende stappen uit:

- Controleert of de configuratie in `App.config` alle benodigde gegevens bevat
- Controleert of het binaire bestand `ffmpeg` aanwezig is
- Stelt de naam van het uitvoerbestand samen door `_c.mp4` toe te voegen aan de basisnaam van het bestand (zoals `Example.mp4` -> `Example_c.mp4`)
- Stelt een opdrachtregeltekenreeks samen om de conversie uit te voeren
- Start een proces `ffmpeg` vanaf de opdrachtregel
- Wacht tot de video is verwerkt

> [!NOTE]
> Als u weet dat uw video's al zijn gecomprimeerd met H.264 en de juiste afmetingen hebben, kunt u `CompressVideo()` herschrijven om de compressiestap over te slaan.

De methode retourneert de bestandsnaam van het gecomprimeerde uitvoerbestand.

## <a name="upload-and-moderate-the-video"></a>De video uploaden en beoordelen

De video moet worden opgeslagen in Azure Media Services voordat deze kan worden verwerkt door de Content Moderation-service. De klasse **Programma** in _Program.cs_ heeft een korte methode `CreateVideoStreamingRequest()` die een object retourneert dat de streaming-aanvraag voorstelt voor het uploaden van de video.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

Het resulterende object `UploadVideoStreamRequest` is gedefinieerd in `UploadVideoStreamRequest.cs` (en het bovenliggende object, `UploadVideoRequest`, in `UploadVideoRequest.cs`). Deze klassen worden hier niet weergegeven. Ze zijn kort en zijn alleen bedoeld voor het opslaan van de gecomprimeerde videogegevens en de bijbehorende metagegevens. Een andere klasse voor alleen gegevens, `UploadAssetResult` (`UploadAssetResult.cs`), wordt gebruikt voor het opslaan van de resultaten van het uploadproces. Nu hebben we genoeg informatie om deze regels in `ProcessVideo()` te begrijpen:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Deze regels voeren de volgende taken uit:

- Een `UploadVideoStreamRequest` maken om de gecomprimeerde video te uploaden
- De vlag `GenerateVTT` van de aanvraag instellen als de gebruiker een transcriptie heeft aangevraagd
- `CreateAzureMediaServicesJobToModerateVideo()` aanroepen om de upload uit te voeren en het resultaat te ontvangen

## <a name="examine-video-moderation-code"></a>De videobeoordelingscode controleren

De methode `CreateAzureMediaServicesJobToModerateVideo()` bevindt zich in `VideoModerator.cs`, de klasse met het overgrote deel van de code die interactie heeft met Azure Media Services. De broncode van de methode wordt weergegeven in het volgende fragment.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Met deze code worden de volgende taken uitgevoerd:

- Een AMS-taak maken voor de bewerkingen die moeten worden uitgevoerd
- Taken toevoegen voor het coderen van het videobestand, het beoordelen van het bestand en het genereren van een transcriptie
- De taak verzenden, het bestand uploaden en de verwerking starten
- De resultaten van de beoordeling, de transcriptie (indien aangevraagd) en andere informatie ophalen

## <a name="sample-video-moderation-output"></a>Voorbeeld van videobeoordelingsuitvoer

Het resultaat van de videobeoordelingstaak (zie de [snelstartgids over videobeoordeling](video-moderation-api.md)) is een JSON-gegevensstructuur met de resultaten van de beoordeling. Deze resultaten omvatten een overzicht van de fragmenten (opnamen) binnen de video, elk met gebeurtenissen (clips) met belangrijke frames die zijn gemarkeerd voor beoordeling. Elk sleutelframe krijgt een score die aangeeft hoe groot de kans is dat het frame inhoud voor volwassenen of ongepaste inhoud bevat. Hieronder ziet u een voorbeeld van een JSON-antwoord:

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Er wordt ook een transcriptie van de audio van de video gemaakt wanneer de vlag `GenerateVTT` is ingesteld.

> [!NOTE]
> De consoletoepassing maakt gebruik van de [API van Azure Media Indexer](../../media-services/previous/legacy-components.md) om transcripties te genereren van de audiotrack van de geüploade video. De resultaten worden aangeboden in de indeling WebVTT. Zie de Engelstalige site [Web Video Text Tracks Format (WebVTT)](https://developer.mozilla.org/docs/Web/API/WebVTT_API) voor meer informatie over deze indeling.

## <a name="create-a-human-review"></a>Een menselijke beoordeling maken

Het beoordelingsproces retourneert een lijst met sleutelframes uit de video, samen met een transcriptie van de audiotracks. De volgende stap is het maken van een beoordeling in Content Moderator voor menselijke beoordelaars. Als we teruggaan naar de methode `ProcessVideo()` in _Program.cs_, ziet u de aanroep van de methode `CreateVideoReviewInContentModerator()`. Deze methode maakt deel uit van de klasse `videoReviewApi`, in `VideoReviewAPI.cs`, en wordt hier weergegeven.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` roept verschillende andere methoden aan om de volgende taken uit te voeren:

> [!NOTE]
> De consoletoepassing maakt gebruik van de bibliotheek [FFmpeg](https://ffmpeg.org/) voor het genereren van miniaturen. Deze miniaturen (afbeeldingen) komen overeen met de tijdstempels van frames in de uitvoer voor videobeoordeling.

|Taak|Methoden|File|
|-|-|-|
|De sleutelframes uit de video extraheren en er miniatuurafbeeldingen van maken|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|De transcriptie, indien beschikbaar, scannen op tekst voor volwassenen of ongepaste tekst|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Een aanvraag voor videobeoordeling voorbereiden en verzenden voor menselijke controle|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

In het volgende scherm ziet u de resultaten van de vorige stappen.

![Standaardweergave voor videobeoordeling](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>De transcriptie verwerken

De code in deze zelfstudie is tot nu toe gericht op de visuele inhoud. Beoordeling van gesproken inhoud is een afzonderlijk en optioneel proces dat, zoals gezegd, gebruikmaakt van een transcriptie die wordt gegenereerd van de audio. Het is nu tijd om te kijken hoe transcripties worden gemaakt en gebruikt in het beoordelingsproces. Het genereren van transcripties wordt uitgevoerd door de [Azure Media Indexer](../../media-services/previous/media-services-index-content.md)-service.

De toepassing voert de volgende taken uit:

|Taak|Methoden|File|
|-|-|-|
|Vaststellen of er transcripties moeten worden gegenereerd|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Zo ja, een transcriptietaak verzenden als onderdeel van de beoordeling|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Een lokale kopie van de transcriptie opvragen|`GenerateTranscript()`|`VideoModerator.cs`|
|Frames van de video markeren die ongepaste audio bevatten|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|De resultaten toevoegen aan de beoordeling|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Configuratie van de taak

We beginnen gewoon meteen met het verzenden van de transcriptietaak. `CreateAzureMediaServicesJobToModerateVideo()` (al eerder beschreven) roept `ConfigureTranscriptTask()` aan.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

De configuratie voor de transcriptietaak wordt gelezen uit het bestand `MediaIndexerConfig.json` in de map `Lib` van de oplossing. Er worden AMS-assets gemaakt voor het configuratiebestand en voor de uitvoer van het transcriptieproces. Wanneer de AMS-taak wordt uitgevoerd, maakt deze taak een transcriptie van de audiotrack van het videobestand.

> [!NOTE]
> De voorbeeldtoepassing herkent alleen spraak in het Amerikaans-Engels.

### <a name="transcript-generation"></a>Transcriptie genereren

De transcriptie wordt gepubliceerd als een AMS-asset. Om de transcriptie te scannen op aanstootgevende inhoud, downloadt de toepassing de asset uit Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` roept `GenerateTranscript()` aan, zoals hier wordt weergegeven, om het bestand op te halen.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Nadat enkele AMS-instellingen zijn geconfigureerd, wordt het bestand gedownload door het aanroepen van `DownloadAssetToLocal()`, een algemene functie voor het kopiëren van AMS-assets naar een lokaal bestand.

## <a name="moderate-the-transcript"></a>De transcriptie beoordelen

Met de transcriptie bij de hand kan deze worden gescand en gebruikt in de beoordeling. Het maken van de beoordeling is de taak van `CreateVideoReviewInContentModerator()`, die `GenerateTextScreenProfanity()` aanroept om het werk te doen. Deze methode roept op zijn beurt `TextScreen()` aan, die de meeste functionaliteit bevat.

`TextScreen()` voert de volgende taken uit:

- De transcriptie parseren op tijdstempels en ondertitels
- Elke ondertitel verzenden voor beoordeling
- Frames markeren die mogelijk aanstootgevende tekst bevatten

Laten we deze taken eens afzonderlijk bekijken:

### <a name="initialize-the-code"></a>Code initialiseren

Eerst worden alle variabelen en verzamelingen geïnitialiseerd.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Transcriptie parseren op ondertiteling

Vervolgens wordt de transcriptie met VTT-indeling geparseerd op ondertiteling en tijdstempels. Het beoordelingsprogramma geeft deze ondertitels weer op het tabblad Transcript van het scherm voor videobeoordeling. De tijdstempels worden gebruikt om de ondertitels te synchroniseren met de bijbehorende videoframes.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Ondertitels beoordelen met de service voor tekstbeoordeling

Vervolgens scannen we de geparseerde ondertitels met de tekst=API van Content Moderator.

> [!NOTE]
> Er geldt een limiet voor het aantal aanvragen per seconde (RPS) voor de sleutel van de Content Moderator-service. Als u de limiet overschrijdt, veroorzaakt dit een uitzondering met foutcode 429 in de SDK.
>
> Een sleutel voor de gratis laag heeft een limiet van één RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Uitsplitsing van tekstbeoordeling

`TextScreen()` is een omvangrijke methode, dus is het beter om die op te splitsen.

1. De methode leest eerst het transcriptiebestand regel voor regel. Lege regels en regels met een `NOTE` worden genegeerd en krijgen geen betrouwbaarheidsscore. De tijdstempels en tekstitems worden geëxtraheerd uit de *cues* in het bestand. Een cue vertegenwoordigt tekst uit de audiotrack en bevat begin- en eindtijden. Een cue begint met de tijdlijnregel met de tekenreeks `-->`. en wordt gevolgd door een of meer regels tekst.

1. Instanties van `CaptionScreentextResult` (gedefinieerd in `TranscriptProfanity.cs`) worden gebruikt voor het opslaan van de gegevens die uit elke cue worden geparseerd.  Wanneer er een nieuw tijdstempel wordt gedetecteerd, of een maximale tekstlengte van 1024 tekens wordt bereikt, wordt er een nieuw `CaptionScreentextResult` toegevoegd aan de `csrList`. 

1. De methode verzendt vervolgens elke cue naar de API voor tekstbeoordeling. De methode roept vervolgens zowel `ContentModeratorClient.TextModeration.DetectLanguageAsync()` als `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` aan, die beide zijn gedefinieerd in de assembly `Microsoft.Azure.CognitiveServices.ContentModerator`. Om te voorkomen dat er een frequentielimiet wordt afgedwongen, wordt de methode tussen het verzenden van de cues steeds één seconde onderbroken.

1. Na ontvangst van de resultaten van de service voor tekstbeoordeling, analyseert de methode de resultaten om te controleren of deze voldoen aan de drempelwaarden voor vertrouwen. Deze waarden worden vastgelegd in `App.config` als `OffensiveTextThreshold`, `RacyTextThreshold` en `AdultTextThreshold`. Ten slotte worden de aanstootgevende termen zelf ook opgeslagen. Alle frames binnen het tijdsbereik van de cue worden gemarkeerd als frames die aanstootgevende tekst, ongepaste tekst en/of tekst voor volwassenen bevatten.

1. `TextScreen()` retourneert een instantie van `TranscriptScreenTextResult` met het resultaat van de tekstbeoordeling van de video als geheel. Dit object bevat vlaggen en scores voor de verschillende typen aanstootgevende inhoud, samen met een lijst met alle ongewenste termen. De aanroepende functie, `CreateVideoReviewInContentModerator()`, roept `UploadScreenTextResult()` aan om deze informatie te koppelen aan de beoordeling, om deze beschikbaar te maken voor menselijke beoordeling.

In het volgende scherm ziet u het resultaat van de transcriptie die is gegenereerd en de stappen voor beoordeling.

![Weergave van transcriptie van videobeoordeling](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Programma-uitvoer

De volgende uitvoer op de opdrachtregel van het programma toont de verschillende taken terwijl die worden voltooid. Het resultaat van de beoordeling (in JSON-indeling) en de transcriptie van de tekst zijn bovendien beschikbaar in dezelfde map als de oorspronkelijke videobestanden.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u een toepassing in die video-inhoud beoordeelt, &mdash;waaronder transcriptie-inhoud&mdash;, en beoordelingen maakt in het beoordelingsprogramma. In de volgende zelfstudie wordt meer aandacht besteed aan videobeoordeling.

> [!div class="nextstepaction"]
> [Beheer van video](./video-moderation-human-review.md)