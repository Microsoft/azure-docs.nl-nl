---
title: .NET-SDK gebruiken voor Microsoft Azure StorSimple Data Manager taken
description: Meer informatie over hoe u de .NET SDK binnen de StorSimple Data Manager-service kunt gebruiken om StorSimple-apparaatgegevens te transformeren.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b18627d2806662d6d966af95d51873d5623b2393
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015533"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>De .NET SDK gebruiken om de gegevenstransformatie te initiëren

## <a name="overview"></a>Overzicht

In dit artikel wordt uitgelegd hoe u de functie voor gegevens transformatie binnen de StorSimple Data Manager-service kunt gebruiken om StorSimple te transformeren. De getransformeerde gegevens worden vervolgens door andere Azure-Services in de Cloud gebruikt.

U kunt een gegevens transformatie taak op twee manieren starten:

- De .NET SDK gebruiken
- Azure Automation runbook gebruiken
 
  In dit artikel vindt u informatie over het maken van een voor beeld van een .NET-console toepassing om een gegevens transformatie taak te initiëren en deze vervolgens te volgen om deze te volt ooien. Als u meer wilt weten over het initiëren van gegevens transformatie via Automation, gaat u naar [Azure Automation Runbook gebruiken om taken voor gegevens transformatie te activeren](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat:
*   Een computer met:

    - Visual Studio 2012, 2013, 2015 of 2017.

    - Azure Power shell. [Down load Azure Power shell](/powershell/azure/).
*   Een correct geconfigureerde taak definitie in StorSimple Data Manager binnen een resource groep.
*   Alle vereiste dll-bestanden. Down load deze DLL-bestanden vanuit de [github-opslag plaats](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) script uit de GitHub-opslag plaats.

## <a name="step-by-step-procedure"></a>Stapsgewijze procedure

Voer de volgende stappen uit om .NET te gebruiken om een gegevens transformatie taak te starten.

1. Voer de volgende stappen uit om de configuratie parameters op te halen:
    1. Down load de `Get-ConfigurationParams.ps1` van het github-opslagplaats script op `C:\DataTransformation` locatie.
    1. Voer het `Get-ConfigurationParams.ps1` script uit vanuit de GitHub-opslag plaats. Typ de volgende opdracht:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        U kunt alle waarden voor de ActiveDirectoryKey en AppName door geven.

2. Met dit script worden de volgende waarden uitgevoerd:
    * Client-id
    * Tenant-id
    * Active Directory sleutel (hetzelfde als het hierboven opgegeven)
    * Abonnements-id

        ![Script uitvoer voor configuratie parameters](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Maak met behulp van Visual Studio 2012, 2013 of 2015 een C# .NET-console toepassing.

    1. Start **Visual Studio 2012/2013/2015**.
    1. Selecteer **Bestand > Nieuw > Project**.

        ![Een project maken 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Selecteer **geïnstalleerde > sjablonen > Visual C# >-console toepassing**.
    3. Voer **DataTransformationApp** in als **naam**.
    4. Selecteer **C:\DataTransformation** voor de **locatie**.
    6. Klik op **OK** om het project aan te maken.

        ![Een project maken 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Voeg nu alle dll's die aanwezig zijn in de [map dll's](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) toe als **verwijzingen** in het project dat u hebt gemaakt. Voer de volgende handelingen uit om de dll-bestanden toe te voegen:

   1. Ga in Visual Studio naar **> Solution Explorer weer geven**.
   2. Klik op de pijl links van het app-project voor gegevens transformatie. Klik op **verwijzingen** en klik vervolgens met de rechter muisknop om **referentie toe te voegen**.
    
       ![Dll 1 toevoegen](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Blader naar de locatie van de map pakketten, selecteer alle dll's en klik op **toevoegen**. Klik vervolgens op **OK**.

       ![Dll 2 toevoegen](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Voeg de volgende **using**-instructies toe aan het bronbestand (Program.cs) in het project.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. De volgende code initialiseert het taak exemplaar van de gegevens transformatie. Voeg dit toe in de **methode Main**. Vervang de waarden van configuratie parameters zoals eerder is verkregen. Sluit de waarden van de **naam van de resource groep** en de **ResourceName**. De **ResourceGroupName** is gekoppeld aan de StorSimple Data Manager waarop de taak definitie is geconfigureerd. De **ResourceName** is de naam van uw StorSimple Data Manager service.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Geef de para meters op waarmee de taak definitie moet worden uitgevoerd

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    OF

    Als u de para meters voor de taak definitie tijdens runtime wilt wijzigen, voegt u de volgende code toe:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Voeg na de initialisatie de volgende code toe om een gegevens transformatie taak te activeren voor de taak definitie. Sluit de juiste **taak definitie naam** in.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Als de code is geplakt, bouwt u de oplossing. Hier volgt een scherm opname van het code fragment voor het initialiseren van het taak exemplaar van de gegevens transformatie.

   ![Code fragment voor het initialiseren van de taak voor gegevens transformatie](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Met deze taak worden de gegevens getransformeerd die overeenkomen met de hoofdmap en bestands filters binnen het StorSimple-volume en worden deze in de opgegeven container/bestands share geplaatst. Wanneer een bestand wordt getransformeerd, wordt een bericht toegevoegd aan een opslag wachtrij (in hetzelfde opslag account als de container/bestands share) met dezelfde naam als de taak definitie. Dit bericht kan worden gebruikt als een trigger voor het initiëren van verdere verwerking van het bestand.

10. Zodra de taak is geactiveerd, kunt u de volgende code gebruiken om de taak voor voltooiing bij te houden. Het is niet verplicht om deze code toe te voegen voor de uitvoering van de taak.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Hier volgt een scherm opname van het hele code voorbeeld dat wordt gebruikt om de taak te activeren met behulp van .NET.

    ![Volledig code fragment voor het activeren van een .NET-taak](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Volgende stappen

[Gebruik StorSimple Data Manager gebruikers interface om uw gegevens te transformeren](storsimple-data-manager-ui.md).