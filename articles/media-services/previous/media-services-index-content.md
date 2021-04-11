---
title: Media bestanden indexeren met Azure Media Indexer
description: Met Azure Media Indexer kunt u inhoud van uw media bestanden doorzoekbaar maken en een transcriptie van volledige tekst genereren voor ondertiteling en tref woorden. In dit onderwerp wordt beschreven hoe u Media Indexer gebruikt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b72cca319772fa52829c6b95237e15f083a2e7
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962659"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Media bestanden indexeren met Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Het wordt aanbevolen dat klanten migreren vanuit Indexeer functie v1 en Indexeer functie v2 naar het gebruik van de [Media Services v3 AudioAnalyzerPreset Basic-modus](../latest/analyze-video-audio-files-concept.md). De media processor van [Azure media indexer](media-services-index-content.md) media en [Azure media indexer 2 Preview](./legacy-components.md) worden buiten gebruik gesteld. Voor de pensioen datums raadpleegt u dit onderwerp over [oudere onderdelen](legacy-components.md) .

Met Azure Media Indexer kunt u inhoud van uw media bestanden doorzoekbaar maken en een transcriptie van volledige tekst genereren voor ondertiteling en tref woorden. U kunt één media bestand of meerdere media bestanden in een batch verwerken.  

Wanneer u inhoud indexeert, moet u ervoor zorgen dat u media bestanden gebruikt met duidelijke spraak (zonder achtergrond muziek, ruis, effecten of microfoon hiss). Enkele voor beelden van de juiste inhoud zijn: vastgelegde vergaderingen, colleges of presentaties. De volgende inhoud is mogelijk niet geschikt voor indexering: films, TV-Program ma's, alles met gemengde audio en geluids effecten, slecht vastgelegde inhoud met achtergrond geluid (hiss).

Een indexerings taak kan de volgende uitvoer genereren:

* Ondertitelings bestanden in de volgende indelingen: **TTML** en **WebVTT**.
  
    Ondertitelings bestanden bevatten een tag met de naam herkenning, waarmee een indexerings taak wordt gescoord op basis van de manier waarop de spraak in de bron video herkenbaar is.  U kunt de waarde van herkenning voor scherm uitvoer bestanden gebruiken voor bruikbaarheid. Een lage score zou een slechte indexerings resultaat hebben als gevolg van de audio kwaliteit.
* Trefwoord bestand (XML).

In dit artikel wordt beschreven hoe u Indexeer taken maakt voor **het indexeren van een Asset en het** **indexeren van meerdere bestanden**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Configuratie-en manifest bestanden gebruiken voor indexerings taken
U kunt meer informatie voor uw indexerings taken opgeven met behulp van een taak configuratie. U kunt bijvoorbeeld opgeven welke meta gegevens voor uw media bestand moeten worden gebruikt. Deze meta gegevens worden door de taal engine gebruikt om de bijbehorende woorden lijst uit te breiden, waardoor de nauw keurigheid van de spraak herkenning aanzienlijk wordt verbeterd.  U kunt ook de gewenste uitvoer bestanden opgeven.

U kunt ook meerdere media bestanden tegelijk verwerken door een manifest bestand te gebruiken.

Zie voor meer informatie [taak vooraf instellen voor Azure media indexer](./legacy-components.md).

## <a name="index-an-asset"></a>Een Asset indexeren
Met de volgende methode wordt een media bestand als een Asset geüpload en wordt een taak gemaakt om het activum te indexeren.

Als er geen configuratie bestand is opgegeven, wordt het Media bestand geïndexeerd met alle standaard instellingen.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Uitvoer bestanden
Standaard genereert een indexerings taak de volgende uitvoer bestanden. De bestanden worden opgeslagen in het eerste uitvoer activum.

Wanneer er meer dan één invoer Media bestand is, genereert de Indexeer functie een manifest bestand voor de taak uitvoer, met de naam JobResult.txt. Voor elk invoer Media bestand zijn de resulterende TTML-, WebVTT-en trefwoord bestanden opeenvolgend genummerd en benoemd met behulp van de alias.

| Bestandsnaam | Description |
| --- | --- |
| **InputFileName. ttml**<br/>**InputFileName. VTT** |Ondertitelings bestanden (CC) in TTML en WebVTT-indelingen.<br/><br/>Ze kunnen worden gebruikt om audio-en video bestanden toegankelijk te maken voor mensen met een gehoor handicap.<br/><br/>Ondertitelings bestanden bevatten een tag met de naam <b>herkenning</b> waarmee een indexerings taak wordt gescoord op basis van de manier waarop de spraak in de bron video herkenbaar is.  U kunt de waarde van <b>herkenning</b> voor scherm uitvoer bestanden gebruiken voor bruikbaarheid. Een lage score zou een slechte indexerings resultaat hebben als gevolg van de audio kwaliteit. |
| **InputFileName.kw.xml<br/> InputFileName.info** |Trefwoord-en info bestanden. <br/><br/>Trefwoord bestand is een XML-bestand dat tref woorden bevat die zijn geëxtraheerd uit de spraak inhoud, met frequentie en verschuivings gegevens. <br/><br/>Info bestand is een bestand met onbewerkte tekst dat gedetailleerde informatie bevat over elke herkende term. De eerste regel is speciaal en bevat de herken bare Score. Elke volgende regel is een door tabs gescheiden lijst met de volgende gegevens: begin tijd, eind tijd, woord/zin, betrouw baarheid. De tijden worden in seconden gegeven en het vertrouwen wordt gegeven als een getal van 0-1. <br/><br/>Voorbeeld regel: "1,20 1,45 Word 0,67" <br/><br/>Deze bestanden kunnen worden gebruikt voor een aantal doel einden, zoals, voor het uitvoeren van een spraak analyse of voor het weer geven van zoek machines zoals Bing, Google of micro soft share point, om de media bestanden meer detecteerbaar te maken of zelfs te gebruiken voor het leveren van meer relevante advertenties. |
| **JobResult.txt** |Uitvoer manifest, alleen aanwezig bij het indexeren van meerdere bestanden, met de volgende gegevens:<br/><br/><table border="1"><tr><th>Invoer</th><th>Alias</th><th>MediaLength</th><th>Fout</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Als niet alle invoer media bestanden zijn geïndexeerd, mislukt de indexerings taak met fout code 4000. Zie [fout codes](#error_codes)voor meer informatie.

## <a name="index-multiple-files"></a>Meerdere bestanden indexeren
Met de volgende methode uploadt u meerdere media bestanden als een Asset en maakt u een taak voor het indexeren van alle bestanden in een batch.

Een manifest bestand met de extensie '. lst ' wordt gemaakt en geüpload naar de Asset. Het manifest bestand bevat de lijst met alle asset-bestanden. Zie voor meer informatie [taak vooraf instellen voor Azure media indexer](./legacy-components.md).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Gedeeltelijk geslaagde taak
Als niet alle invoer media bestanden zijn geïndexeerd, mislukt de indexerings taak met fout code 4000. Zie [fout codes](#error_codes)voor meer informatie.

Dezelfde uitvoer (als geslaagde taken) worden gegenereerd. U kunt naar het uitvoer manifest bestand zoeken om te ontdekken welke invoer bestanden zijn mislukt, volgens de fout kolom waarden. Voor invoer bestanden die zijn mislukt, worden de resulterende TTML-, WebVTT-en trefwoord bestanden niet gegenereerd.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a> Taak voorinstelling voor Azure Media Indexer
De verwerking van Azure Media Indexer kan worden aangepast door naast de taak een optionele vooraf ingestelde taak te geven.  Hieronder wordt de indeling van deze XML-configuratie beschreven.

| Name | Vereist | Description |
| --- | --- | --- |
| **ingevoerd** |onjuist |Activa bestand (en) die u wilt indexeren.</p><p>Azure Media Indexer ondersteunt de volgende indelingen voor media bestanden: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>U kunt de bestands namen opgeven in het kenmerk **name** of **List** van het **input** -element (zoals hieronder weer gegeven). Als u niet opgeeft welk item bestand moet worden geïndexeerd, wordt het primaire bestand gekozen. Als er geen primair activa bestand is ingesteld, wordt het eerste bestand in de invoer Asset geïndexeerd.</p><p>Ga als volgt te werk om de naam van het activa bestand expliciet op te geven:<br/>`<input name="TestFile.wmv">`<br/><br/>U kunt ook meerdere Asset-bestanden tegelijk indexeren (Maxi maal 10 bestanden). Om dit te doen:<br/><br/><ol class="ordered"><li><p>Maak een tekst bestand (manifest bestand) en geef het de extensie. lst. </p></li><li><p>Voeg een lijst met alle bestands namen van assets in uw invoer Asset toe aan dit manifest bestand. </p></li><li><p>Voeg het manifest bestand toe (upload) naar de Asset.  </p></li><li><p>Geef de naam op van het manifest bestand in het lijst kenmerk van de invoer.<br/>`<input list="input.lst">`</li></ol><br/><br/>Opmerking: als u meer dan 10 bestanden aan het manifest bestand toevoegt, mislukt de indexerings taak met de fout code 2006. |
| **metagegevensarchiefmethode** |onjuist |Meta gegevens voor het opgegeven item bestand (en) die worden gebruikt voor het aanpassen van de woorden lijst.  Het is handig om de Indexeer functie voor te bereiden om niet-standaard woordenlijst woorden, zoals de juiste naam woorden, te herkennen.<br/>`<metadata key="..." value="..."/>` <br/><br/>U kunt **waarden** voor vooraf gedefinieerde **sleutels** opgeven. Momenteel worden de volgende sleutels ondersteund:<br/><br/>"title" en "Description": wordt gebruikt voor het aanpassen van de terminologie van het taal model voor uw taak en het verbeteren van de nauw keurigheid van spraak herkenning.  De waarden Seed internet zoekt naar context afhankelijke relevante tekst documenten, waarbij de inhoud wordt gebruikt om de interne woorden lijst voor de duur van uw indexerings taak te verg Roten.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **functies** <br/><br/> Toegevoegd in versie 1,2. Op dit moment is de enige ondersteunde functie spraak herkenning (ASR). |onjuist |De functie voor spraak herkenning heeft de volgende instellingen sleutels:<table><tr><th><p>Sleutel</p></th>        <th><p>Beschrijving</p></th><th><p>Voorbeeldwaarde</p></th></tr><tr><td><p>Taal</p></td><td><p>De natuurlijke taal die moet worden herkend in het multimedia bestand.</p></td><td><p>Engels, Spaans</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>een door punt komma's gescheiden lijst met de gewenste indelingen van de uitvoer bijschriften (indien van toepassing)</p></td><td><p>ttml; webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Echte Terecht</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Een Booleaanse vlag die aangeeft of een XML-bestand met een tref woord is vereist.</p></td><td><p>Echte Terecht. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Een Booleaanse vlag die aangeeft of volledige bijschriften moeten worden geforceerd (ongeacht het betrouwbaarheids niveau).  </p><p>De standaard waarde is False, in het geval dat woorden en zinsdelen met een kleiner dan 50% betrouw niveau worden wegge laten uit de laatste bijschrift uitvoer en worden vervangen door beletsel tekens ("...").  De weglatings tekens zijn handig voor het controleren van bijschriften en controles.</p></td><td><p>Echte Terecht. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Fout codes
In het geval van een fout moet Azure Media Indexer een van de volgende fout codes rapporteren:

| Code | Name | Mogelijke redenen |
| --- | --- | --- |
| 2000 |Ongeldige configuratie |Ongeldige configuratie |
| 2001 |Ongeldige invoer assets |Ontbrekende invoer assets of lege activa. |
| 2002 |Ongeldig manifest |Het manifest is leeg of het manifest bevat ongeldige items. |
| 2003 |Kan het Media bestand niet downloaden |De URL in het manifest bestand is ongeldig. |
| 2004 |Niet-ondersteund protocol |Het Protocol van de media-URL wordt niet ondersteund. |
| 2005 |Niet-ondersteund bestandstype |Invoer media bestands type wordt niet ondersteund. |
| 2006 |Te veel invoer bestanden |Er zijn meer dan 10 bestanden in het invoer manifest. |
| 3000 |Kan Media bestand niet decoderen |Niet-ondersteunde media-codec <br/>of<br/> Beschadigd media bestand <br/>of<br/> Geen audio stroom in invoer media. |
| 4000 |Batch indexeren is gedeeltelijk voltooid |Een aantal invoer media bestanden kan niet worden geïndexeerd. Zie <a href="#output_files">uitvoer bestanden</a>voor meer informatie. |
| other |Interne fouten |Neem contact op met het ondersteunings team. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Ondersteunde talen
Op dit moment worden de Engelse en Spaanse talen ondersteund.  

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](./legacy-components.md)

[Media bestanden indexeren met Azure Media Indexer 2 Preview](./legacy-components.md)