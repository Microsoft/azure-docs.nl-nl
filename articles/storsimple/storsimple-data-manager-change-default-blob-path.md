---
title: Het BLOB-pad wijzigen van de standaard instelling
description: Meer informatie over het instellen van een Azure-functie voor het wijzigen van de naam van een standaard pad naar een BLOB en het verplaatsen van de blobs naar een andere locatie.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011435"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Het standaardpad van een blob wijzigen

Wanneer de StorSimple Data Manager-service de gegevens transformeert, plaatst deze standaard de getransformeerde blobs in een opslag container zoals opgegeven tijdens het maken van de doel opslagplaats. Wanneer de blobs op deze locatie arriveren, kunt u deze blobs naar een andere locatie verplaatsen. In dit artikel wordt beschreven hoe u een Azure-functie instelt voor het wijzigen van de naam van een standaard pad naar een BLOB en de blobs dus naar een andere locatie kunt verplaatsen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u een correct geconfigureerde taak definitie hebt in uw StorSimple Data Manager-service.

## <a name="create-an-azure-function"></a>Een Azure-functie maken

Voer de volgende stappen uit om een Azure-functie te maken:

1. Ga naar de [Azure Portal](https://portal.azure.com/).

2. Klik op **+ Een resource maken**. Typ **functie-app** in het **zoekvak** en druk op **Enter**. Selecteer en klik op **functie-app** in de lijst met weer gegeven apps.

    ![Typ ' functie-app ' in het zoekvak](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klik op **Create**.

    ![De knop ' maken ' van het functie-app venster](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Voer de volgende stappen uit op de Blade **functie-app** configuratie:

    1. Geef een unieke **app-naam** op.
    2. Selecteer het **abonnement** in de vervolg keuzelijst. Dit abonnement moet gelijk zijn aan de versie die is gekoppeld aan uw StorSimple Data Manager service.
    3. Selecteer **nieuwe** resource groep maken.
    4. Selecteer **verbruiks abonnement** voor de vervolg keuzelijst **hosting plan** .
    5. Geef een locatie op waar de functie wordt uitgevoerd. U wilt dezelfde regio plaatsen waar de StorSimple Data Manager-service en het opslag account die aan de taak definitie zijn gekoppeld, zich bevinden.
    6. Selecteer een bestaand opslagaccount of maak een nieuw opslagaccount. Een opslag account wordt intern gebruikt voor de functie.

        ![Nieuwe functie-app configuratie gegevens invoeren](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klik op **Create**. De functie-app is gemaakt.
     
        ![functie-app gemaakt](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selecteer **functies** en klik op **+ nieuwe functie**.

    ![Klik op + nieuwe functie](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Selecteer **C#** voor de taal. Selecteer in de matrix met sjabloon tegels **C#** in de tegel **Queue trigger-csharp** .

7. In de **wachtrij trigger**:

    1. Voer een **naam** in voor de functie.
    2. In het vak **wachtrij naam** typt u de naam van de taak voor gegevens transformatie.
    3. Klik onder **verbinding met opslag account** op **Nieuw**. Selecteer in de lijst met opslag accounts het account dat is gekoppeld aan de taak definitie. Noteer de naam van de verbinding (gemarkeerd). De naam is later vereist in de functie Azure.

        ![Een nieuwe C#-functie maken](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klik op **Create**. De **functie** wordt gemaakt.

     
10. Voer in het functie venster het bestand _. CSX_ uit.

    ![Een nieuwe C#-functie 2 maken](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Voer de volgende stappen uit.

    1. Plak de volgende code:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Vervang **STORAGE_CONNECTIONNAME** op regel 11 door de verbinding met uw opslag account (zie stap 7C).

        ![Opslag verbindings naam kopiëren](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Sla** de functie op.

        ![Functie opslaan](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Als u de functie wilt volt ooien, voegt u nog een bestand toe door de volgende stappen uit te voeren:

    1. Klik op **bestanden weer geven**.

       ![De koppeling bestanden weer geven](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klik op **+ Toevoegen**.
        
        ![Nieuwe functie toevoegen bestand toevoegen](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Typ **project.jsop** en druk op **Enter**. Plak de volgende code in de **project.jsin** het bestand:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Klik op **Opslaan**.

        ![Nieuwe functie project JSON](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

U hebt een Azure-functie gemaakt. Deze functie wordt geactiveerd wanneer een nieuwe BLOB wordt gegenereerd door de taak voor gegevens transformatie.

## <a name="next-steps"></a>Volgende stappen

[StorSimple Data Manager-gebruikers interface gebruiken om uw gegevens te transformeren](storsimple-data-manager-ui.md)
