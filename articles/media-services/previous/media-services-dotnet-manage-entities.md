---
title: Activa en gerelateerde entiteiten beheren met Media Services .NET SDK
description: Meer informatie over het beheren van assets en gerelateerde entiteiten met de Media Services SDK voor .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: d90e48d15e20b21f4c6032547517b3190cb46085
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695608"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Activa en gerelateerde entiteiten beheren met Media Services .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In dit onderwerp wordt beschreven hoe u Azure Media Services entiteiten beheert met .NET.

Vanaf 1 april 2017 wordt elke taakrecord in uw account die ouder is dan 90 dagen, automatisch verwijderd, samen met de bijbehorende onderliggende taakrecords, zelfs als het totale aantal records lager is dan het maximale quotum. Bijvoorbeeld, op 1 april 2017, wordt een taak record in uw account die ouder is dan 31 december 2016 automatisch verwijderd. Als u de taak/taak gegevens wilt archiveren, kunt u de code gebruiken die in dit onderwerp wordt beschreven.

## <a name="prerequisites"></a>Vereisten

Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 

## <a name="get-an-asset-reference"></a>Een Asset-verwijzing ophalen
Een frequente taak is het ophalen van een verwijzing naar een bestaande asset in Media Services. In het volgende code voorbeeld ziet u hoe u een Asset-verwijzing kunt ophalen uit de verzameling assets op het server context object, op basis van een activa-id. In het volgende code voorbeeld wordt een LINQ-query gebruikt voor het ophalen van een verwijzing naar een bestaand IAsset-object.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Alle assets weer geven
Naarmate het aantal activa dat u in de opslag ruimte bevindt groeit, is het handig om uw assets weer te geven. In het volgende code voorbeeld ziet u hoe u de verzameling assets doorloopt op het object Server context. Met elk activum schrijft het code voorbeeld ook een aantal eigenschaps waarden naar de-console. Elk activum kan bijvoorbeeld veel media bestanden bevatten. Het code voorbeeld schrijft alle bestanden die zijn gekoppeld aan elke Asset.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Een taak verwijzing ophalen

Wanneer u met verwerkings taken in Media Services code werkt, moet u vaak een verwijzing naar een bestaande taak op basis van een id ophalen. In het volgende code voorbeeld ziet u hoe u een verwijzing naar een IJob-object uit de verzameling Jobs kunt ophalen.

Mogelijk moet u een taak verwijzing ophalen bij het starten van een langlopende coderings taak en moet u de taak status van een thread controleren. In dergelijke gevallen moet u, wanneer de methode wordt geretourneerd van een thread, een vernieuwde verwijzing naar een taak ophalen.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Taken en assets weer geven
Een belang rijke gerelateerde taak is het vermelden van assets met hun bijbehorende taak in Media Services. In het volgende code voorbeeld ziet u hoe u elk IJob-object kunt weer geven en vervolgens voor elke taak de eigenschappen van de taak, alle gerelateerde taken, alle invoer assets en alle uitvoer activa. De code in dit voor beeld kan handig zijn voor talloze andere taken. Als u bijvoorbeeld de uitvoer assets wilt weer geven van een of meer coderings taken die u eerder hebt uitgevoerd, laat deze code zien hoe u de uitvoer assets kunt openen. Wanneer u een verwijzing naar een uitvoer activum hebt, kunt u de inhoud vervolgens naar andere gebruikers of toepassingen leveren door deze te downloaden of Url's op te geven. 

Zie [activa leveren met de Media Services SDK voor .net](media-services-deliver-streaming-content.md)voor meer informatie over opties voor het leveren van activa.

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Alle beleids regels voor toegang weer geven
In Media Services kunt u een toegangs beleid definiëren voor een Asset of de bijbehorende bestanden. Een toegangs beleid definieert de machtigingen voor een bestand of een asset (wat voor type toegang en de duur). In uw Media Services code definieert u doorgaans een toegangs beleid door een IAccessPolicy-object te maken en het vervolgens te koppelen aan een bestaande Asset. Vervolgens maakt u een ILocator-object waarmee u directe toegang tot assets kunt bieden in Media Services. Het Visual Studio-project dat wordt meegeleverd met deze documentatie reeks bevat verschillende code voorbeelden die laten zien hoe u toegangs beleid en Locators kunt maken en toewijzen aan assets.

In het volgende code voorbeeld ziet u hoe alle beleids regels voor toegang op de server worden weer gegeven en ziet u het type machtigingen dat aan elk beleid is gekoppeld. Een andere handige manier om toegangs beleid weer te geven is een lijst van alle ILocator-objecten op de server, en vervolgens kunt u voor elke Locator het bijbehorende toegangs beleid vermelden met behulp van de eigenschap AccessPolicy.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Toegangs beleid beperken 

>[!NOTE]
> Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). 

U kunt bijvoorbeeld een algemene set beleids regels maken met de volgende code die slechts één keer in uw toepassing zou worden uitgevoerd. U kunt Id's vastleggen in een logboek bestand voor later gebruik:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Daarna kunt u de bestaande Id's in uw code als volgt gebruiken:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Alle Locators weer geven
Een Locator is een URL die een direct pad biedt voor toegang tot een Asset, samen met machtigingen voor de Asset, zoals gedefinieerd door het bijbehorende toegangs beleid van de Locator. Elk activum kan een verzameling ILocator-objecten bevatten die zijn gekoppeld aan de eigenschap Locators. De server context heeft ook een verzameling Locators die alle Locators bevat.

Het volgende code voorbeeld geeft een lijst van alle Locators op de server. Voor elke Locator wordt de id voor het gerelateerde activum en het bijbehorende toegangs beleid weer gegeven. Ook worden het type machtigingen, de verval datum en het volledige pad naar de Asset weer gegeven.

Houd er rekening mee dat een pad naar een locatie naar een Asset alleen een basis-URL naar de Asset is. Als u een direct pad wilt maken naar afzonderlijke bestanden waarnaar een gebruiker of toepassing kan bladeren, moet uw code het specifieke bestandspad toevoegen aan het pad van de Locator. Zie het onderwerp [assets leveren met de Media Services SDK voor .net](media-services-deliver-streaming-content.md)voor meer informatie over hoe u dit doet.

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Inventariseren via grote verzamelingen entiteiten
Bij het uitvoeren van een query op entiteiten is er een limiet van 1000 entiteiten die tegelijk worden geretourneerd, omdat open bare REST v2 de query resultaten beperkt tot 1000 resultaten. U moet Skip en Take gebruiken bij het inventariseren van grote verzamelingen entiteiten. 

Met de volgende functie worden alle taken in het gegeven Media Services-account door lopen. Media Services retourneert 1000-taken in de verzameling taken. De functie maakt gebruik van overs Laan en nemen om ervoor te zorgen dat alle taken worden geïnventariseerd (als u meer dan 1000 taken in uw account hebt).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Een Asset verwijderen
In het volgende voor beeld wordt een Asset verwijderd.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Een taak verwijderen
Als u een taak wilt verwijderen, moet u de status van de taak controleren zoals aangegeven in de eigenschap State. Taken die zijn voltooid of geannuleerd, kunnen worden verwijderd, terwijl taken in bepaalde andere statussen, zoals in de wachtrij geplaatst, gepland of verwerkt, eerst moeten worden geannuleerd en vervolgens kunnen worden verwijderd.

In het volgende code voorbeeld ziet u een methode voor het verwijderen van een taak door de status van de taak te controleren en vervolgens te verwijderen wanneer de toestand is voltooid of geannuleerd. Deze code is afhankelijk van de vorige sectie in dit onderwerp voor het ophalen van een verwijzing naar een taak: een taak verwijzing ophalen.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Een toegangs beleid verwijderen
In het volgende code voorbeeld ziet u hoe u een verwijzing naar een toegangs beleid op basis van een beleids-id krijgt en vervolgens het beleid verwijdert.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
