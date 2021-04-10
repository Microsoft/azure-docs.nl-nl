---
title: Use custom activities in an Azure Data Factory pipeline (Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn)
description: Meer informatie over het maken van aangepaste activiteiten en het gebruik ervan in een Azure Data Factory pijp lijn.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 3832175910f3a6d3e6a7de8da932b32436cc2452
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393017"
---
# <a name="use-custom-activities-in-an-azure-data-factory-version-1-pipeline"></a>Aangepaste activiteiten gebruiken in een Azure Data Factory versie 1-pijp lijn
> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-use-custom-activities.md)
> * [Versie 2 (huidige versie)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [aangepaste activiteiten in v2](../transform-data-using-dotnet-custom-activity.md).

Er zijn twee soorten activiteiten die u in een Azure Data Factory pijp lijn kunt gebruiken.

- [Gegevens verplaatsings activiteiten](data-factory-data-movement-activities.md) om gegevens te verplaatsen tussen het [ondersteunde bron-en Sink-gegevens archief](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md) voor het transformeren van gegevens met behulp van reken services zoals Azure HDInsight, Azure Batch en Azure machine learning Studio (klassiek).

Als u gegevens wilt verplaatsen naar/van een gegevens archief dat Data Factory niet wordt ondersteund, maakt u een **aangepaste activiteit** met uw eigen gegevens verplaatsings logica en gebruikt u de activiteit in een pijp lijn. Als u gegevens wilt transformeren of verwerken op een manier die niet wordt ondersteund door Data Factory, maakt u een aangepaste activiteit met uw eigen gegevens transformatie logica en gebruikt u de activiteit in een pijp lijn.

U kunt een aangepaste activiteit configureren om uit te voeren op een **Azure batch** pool met virtuele machines. Wanneer u Azure Batch gebruikt, kunt u alleen een bestaande Azure Batch groep gebruiken.

In de volgende walkthrough vindt u stapsgewijze instructies voor het maken van een aangepaste .NET-activiteit en het gebruik van de aangepaste activiteit in een pijp lijn. In dit overzicht wordt gebruikgemaakt van een **Azure batch** gekoppelde service.

> [!IMPORTANT]
> - Het is niet mogelijk om een Data Management Gateway te gebruiken van een aangepaste activiteit om toegang te krijgen tot on-premises gegevens bronnen. Momenteel ondersteunt [Data Management Gateway](data-factory-data-management-gateway.md) alleen de activiteit Kopieer activiteit en opgeslagen procedure in Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Walkthrough: een aangepaste activiteit maken
### <a name="prerequisites"></a>Vereisten
* Visual Studio 2012/2013/2015/2017
* Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Azure Batch vereisten
In het overzicht voert u uw aangepaste .NET-activiteiten uit met Azure Batch als reken resource. **Azure Batch** is een platformservice voor het efficiënt uitvoeren van grootschalige parallelle en HPC-toepassingen (High Performance Computing) in de cloud. Azure Batch plant computerintensieve werk om te worden uitgevoerd op een beheerde **verzameling virtuele machines**, en kan reken resources automatisch schalen om te voldoen aan de behoeften van uw taken. Zie [Azure batch-basis][batch-technical-overview] artikel voor een gedetailleerd overzicht van de Azure batch-service.

Voor de zelf studie maakt u een Azure Batch-account met een groep Vm's. Dit zijn de stappen:

1. Maak een **Azure batch-account** met behulp van de [Azure Portal](https://portal.azure.com). Zie [een artikel over een Azure batch-account maken en beheren][batch-create-account] voor instructies.
2. Noteer de naam van het Azure Batch account, de account sleutel, de URI en de naam van de groep. U hebt ze nodig om een Azure Batch gekoppelde service te maken.
    1. Op de start pagina van Azure Batch account ziet u een **URL** in de volgende indeling: `https://myaccount.westus.batch.azure.com` . In dit voor beeld is **MyAccount** de naam van het Azure batch-account. De URI die u in de definitie van de gekoppelde service gebruikt, is de URL zonder de naam van het account. Bijvoorbeeld: `https://<region>.batch.azure.com`.
    2. Klik in het linkermenu op **sleutels** en kopieer de **primaire toegangs sleutel**.
    3. Als u een bestaande pool wilt gebruiken, klikt u op **Pools** in het menu en noteert u de **id** van de groep. Als u geen bestaande groep hebt, gaat u naar de volgende stap.
2. Een **Azure batch groep** maken.

   1. Klik in het [Azure Portal](https://portal.azure.com)op **Bladeren** in het menu links en klik op **batch-accounts**.
   2. Selecteer uw Azure Batch-account om de Blade **batch-account** te openen.
   3. Klik op tegels voor **groepen** .
   4. Klik op de Blade **Pools** op de knop toevoegen op de werk balk om een groep toe te voegen.
      1. Voer een ID in voor de pool (groeps-ID). Noteer de **id van de groep**. u hebt deze nodig bij het maken van de Data Factory-oplossing.
      2. Geef **Windows Server 2012 R2** op voor de instelling van het besturings systeem.
      3. Selecteer een **prijs categorie voor het knoop punt**.
      4. Voer **2** in als waarde voor de **toegewezen doel** instelling.
      5. Voer **2** als waarde in voor de **maximum aantal taken per knoop punt** .
   5. Klik op **OK** om de groep te maken.
   6. Noteer de **id** van de pool.

### <a name="high-level-steps"></a>Stappen op hoog niveau
Dit zijn de twee stappen op hoog niveau die u als onderdeel van deze walkthrough uitvoert:

1. Maak een aangepaste activiteit die eenvoudige gegevens transformatie/verwerkings logica bevat.
2. Maak een Azure-data factory met een pijp lijn die gebruikmaakt van de aangepaste activiteit.

### <a name="create-a-custom-activity"></a>Een aangepaste activiteit maken
Als u een aangepaste .NET-activiteit wilt maken, maakt u een **.net-klassen bibliotheek** project met een klasse die de **IDotNetActivity** -interface implementeert. Deze interface heeft slechts één methode: [Execute](/dotnet/api/microsoft.azure.management.datafactories.runtime.idotnetactivity) en de bijbehorende hand tekening is:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

De methode heeft vier para meters:

- **linkedServices**. Deze eigenschap is een overzicht van de gegevens opslag gekoppelde services waarnaar wordt verwezen door de invoer-en uitvoer gegevens sets voor de activiteit.
- **gegevens sets**. Deze eigenschap is een overzicht van de invoer-en uitvoer gegevens sets voor de activiteit. U kunt deze para meter gebruiken om de locaties en schema's op te halen die worden gedefinieerd door de invoer-en uitvoer gegevens sets.
- **activiteit**. Deze eigenschap vertegenwoordigt de huidige activiteit. Het kan worden gebruikt om toegang te krijgen tot uitgebreide eigenschappen die zijn gekoppeld aan de aangepaste activiteit. Zie [uitgebreide eigenschappen van Access](#access-extended-properties) voor meer informatie.
- **logger**. Met dit object kunt u fout opsporingsgegevens schrijven die in het logboek van de gebruiker voor de pijp lijn vallen.

De-methode retourneert een woorden lijst die kan worden gebruikt om aangepaste activiteiten in de toekomst te koppelen. Deze functie is nog niet geïmplementeerd, dus retour neren een lege woorden lijst van de methode.

### <a name="procedure"></a>Procedure

1. Een **.net Class Library** -project maken.
   
    <ol type="a">
     <li>Start Visual Studio.</li>
     <li>Klik op <b>File</b>, houd de muisaanwijzer op <b>New</b> en klik op <b>Project</b>.</li>
     <li>Vouw <b>Templates</b> uit en selecteer <b>Visual C#</b> . In dit scenario gebruikt u C#, maar u kunt een .NET-taal gebruiken om de aangepaste activiteit te ontwikkelen.</li>
     <li>Selecteer <b>Class Library</b> in de lijst met project typen aan de rechter kant. Kies in Visual Studio <b>Class Library (.NET Framework)</b> </li>
     <li>Voer <b>MyDotNetActivity</b> in als <b>naam</b>.</li>
     <li>Selecteer <b>C:\ADFGetStarted</b> voor de <b>locatie</b>.</li>
     <li>Klik op <b>OK</b> om het project aan te maken.</li>
   </ol>

2. Klik op **Tools**, wijs **NuGet Package Manager** aan en klik op **Package Manager Console**.

3. Voer in de Package Manager-console de volgende opdracht uit om **micro soft. Azure. Management. DataFactories** te importeren.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```

4. Importeer het **Azure Storage** NuGet-pakket in het project.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory Service Launcher vereist de 4,3-versie van WindowsAzure. storage. Als u een verwijzing naar een latere versie van Azure Storage-assembly in uw aangepaste activiteiten project toevoegt, ziet u een fout melding wanneer de activiteit wordt uitgevoerd. Zie sectie [AppDomain-isolatie](#appdomain-isolation) om de fout op te lossen.
5. Voeg de volgende **using** -instructies toe aan het bron bestand in het project.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

6. Wijzig de naam van de naam **ruimte** in **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```

7. Wijzig de naam van de klasse in **MyDotNetActivity** en afleiden van de **IDotNetActivity** -interface, zoals wordt weer gegeven in het volgende code fragment:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```

8. Implementeer (toevoegen) de **Execute** -methode van de **IDotNetActivity** -interface aan de klasse **MyDotNetActivity** en kopieer de volgende voorbeeld code naar de-methode.

    In het volgende voor beeld wordt het aantal exemplaren van de zoek term geteld (' micro soft ') in elke blob die aan een gegevens segment is gekoppeld.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);

            // Calculate method returns the number of occurrences of
            // the search term ("Microsoft") in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```

9. Voeg de volgende helper-methoden toe:

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>

    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    De methode GetFolderPath retourneert het pad naar de map waarnaar de DataSet verwijst en de methode GetFileName retourneert de naam van het BLOB/bestand waarnaar de gegevensset verwijst. Als folderPath is gedefinieerd met behulp van variabelen zoals {Year}, {month}, {Day} enzovoort, retourneert de methode de teken reeks alsof deze is vervangen door runtime-waarden. Zie de sectie [uitgebreide eigenschappen van Access](#access-extended-properties) voor meer informatie over het openen van slice start, SliceEnd, etc.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    De methode Calculate berekent het aantal exemplaren van het sleutel woord micro soft in de invoer bestanden (blobs in de map). De zoek term (' micro soft ') is vastgelegd in de code.

10. Het project compileren. Klik op **Build** in het menu en klik op **Build Solution**.

    > [!IMPORTANT]
    > Stel 4.5.2-versie van .NET Framework in als het doel raamwerk voor uw project: Klik met de rechter muisknop op het project en klik op **Eigenschappen** om het doel raamwerk in te stellen. Data Factory biedt geen ondersteuning voor aangepaste activiteiten die zijn gecompileerd op .NET Framework-versies later dan 4.5.2.

11. Start **Windows Verkenner** en navigeer naar de map **bin\debug** of **bin\release** , afhankelijk van het type Build.

12. Maak een zip-bestand **MyDotNetActivity.zip** dat alle binaire bestanden in de \<project folder\> map \bin\Debug bevat. Neem het bestand **MyDotNetActivity. pdb** op zodat u aanvullende details krijgt, zoals regel nummer in de bron code die het probleem heeft veroorzaakt als er een fout is opgetreden.

    > [!IMPORTANT]
    > Alle bestanden in het zip-bestand voor de aangepaste activiteit moeten zich op het **hoogste niveau** bevinden, zonder submappen.

    ![Binaire uitvoer bestanden](./media/data-factory-use-custom-activities/Binaries.png)

13. Maak een BLOB-container met de naam **customactivitycontainer** als deze nog niet bestaat.

14. Upload MyDotNetActivity.zip als een BLOB naar de customactivitycontainer in een **Algemeen** Azure Blob-opslag (geen warme/cool Blob Storage) die wordt verwezen door AzureStorageLinkedService.

> [!IMPORTANT]
> Als u dit .NET-activiteiten project toevoegt aan een oplossing in Visual Studio met een Data Factory project en een verwijzing naar een .NET-activiteits project toevoegt vanuit het Data Factory-toepassings project, hoeft u de laatste twee stappen voor het hand matig maken van het zip-bestand niet uit te voeren en het te uploaden naar de Azure Blob-opslag voor algemeen gebruik. Wanneer u Data Factory entiteiten publiceert met Visual Studio, worden deze stappen automatisch uitgevoerd door het publicatie proces. Zie [Data Factory project in Visual Studio](#data-factory-project-in-visual-studio) voor meer informatie.

## <a name="create-a-pipeline-with-custom-activity"></a>Een pijp lijn met aangepaste activiteit maken

U hebt een aangepaste activiteit gemaakt en het zip-bestand met binaire bestanden geüpload naar een BLOB-container in een Azure Storage account voor **algemeen gebruik** . In deze sectie maakt u een Azure-data factory met een pijp lijn die gebruikmaakt van de aangepaste activiteit.

De invoer gegevensset voor de aangepaste activiteit vertegenwoordigt blobs (bestanden) in de map customactivityinput van de container adftutorial in de Blob-opslag. De uitvoer gegevensset voor de activiteit vertegenwoordigt uitvoer-blobs in de map customactivityoutput van de adftutorial-container in de Blob-opslag.

Maak **file.txt** -bestand met de volgende inhoud en upload het naar de map **customactivityinput** van de container **adftutorial** . Maak de adftutorial-container als deze nog niet bestaat.

```
test custom activity Microsoft test custom activity Microsoft
```

De map invoer komt overeen met een segment in Azure Data Factory, zelfs als de map twee of meer bestanden bevat. Wanneer elk segment door de pijp lijn wordt verwerkt, doorloopt de aangepaste activiteit alle blobs in de map invoer voor dat segment.

U ziet één uitvoer bestand met in de map adftutorial\customactivityoutput met een of meer regels (hetzelfde als het aantal blobs in de map invoer):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Hier volgen de stappen die u in deze sectie uitvoert:

1. Maak een **Data Factory**.
2. Maak **gekoppelde services** voor de Azure batch pool van vm's waarop de aangepaste activiteit wordt uitgevoerd en de Azure Storage die de invoer/uitvoer-blobs bevatten.
3. Maak invoer-en uitvoer **gegevens sets** die de invoer en uitvoer van de aangepaste activiteit vertegenwoordigen.
4. Maak een **pijp lijn** die gebruikmaakt van de aangepaste activiteit.

> [!NOTE]
> Maak het **file.txt** en upload het naar een BLOB-container als u dit nog niet hebt gedaan. Zie de instructies in de voor gaande sectie.

### <a name="step-1-create-the-data-factory"></a>Stap 1: de data factory maken
1. Nadat u zich hebt aangemeld bij de Azure Portal, voert u de volgende stappen uit:
   1. Klik op **een resource maken** in het menu links.
   2. Klik op **gegevens en analyses** in de **nieuwe** Blade.
   3. Klik op de blade **Gegevensanalyse** op **Gegevensfactory**.

      ![Menu Nieuw Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Voer op de Blade **nieuw Data Factory** **CustomActivityFactory** in als naam. De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als het volgende fout bericht wordt weer gegeven: **naam van Data Factory "CustomActivityFactory" is niet beschikbaar**, wijzigt u de naam van de Data Factory (bijvoorbeeld **yournameCustomActivityFactory**) en probeert u het opnieuw.

    ![Nieuwe Azure Data Factory Blade](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klik op **naam van resource groep** en selecteer een bestaande resource groep of maak een resource groep.
4. Controleer of u het juiste **abonnement** gebruikt en de **regio** waar u de Data Factory wilt maken.
5. Klik op de blade **Nieuwe gegevensfactory** op **Maken**.
6. U ziet dat de data factory wordt gemaakt in het **dash board** van de Azure Portal.
7. Nadat de data factory is gemaakt, ziet u de Blade Data Factory, waarin de inhoud van de data factory wordt weer gegeven.

    ![Blade Gegevensfactory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Stap 2: gekoppelde services maken
Met gekoppelde services worden gegevensarchieven of compute-services gekoppeld aan een Azure Data Factory. In deze stap koppelt u uw Azure Storage-account en Azure Batch account aan uw data factory.

#### <a name="create-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken
1. Klik op de tegel **ontwerpen en implementeren** op de Blade **Data Factory** voor **CustomActivityFactory**. U ziet de Data Factory Editor.
2. Klik op **Nieuw gegevens archief** op de opdracht balk en kies **Azure Storage**. U ziet het JSON-script voor het maken van een gekoppelde Azure Storage-service in de editor.

    ![Nieuwe gegevens opslag-Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Vervang door `<accountname>` de naam van uw Azure Storage-account en `<accountkey>` door de toegangs sleutel van het Azure-opslag account. Zie [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md) voor informatie over het ophalen van uw toegangssleutel voor opslag.

    ![Azure Storage bevonden service](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Klik op de opdrachtbalk op **Implementeren** om de gekoppelde service te implementeren.

#### <a name="create-azure-batch-linked-service"></a>Azure Batch gekoppelde service maken
1. Klik in de Data Factory editor op **... Meer** op de opdracht balk, klikt u op **nieuwe Compute** en selecteert u **Azure batch** in het menu.

    ![Nieuwe Compute-Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Breng de volgende wijzigingen aan in het JSON-script:

   1. Geef Azure Batch account naam op voor de eigenschap **AccountName** . De **URL** van de **Blade Azure batch account** heeft de volgende indeling: `http://accountname.region.batch.azure.com` . Voor de eigenschap **batchUri** in de JSON moet u verwijderen `accountname.` uit de URL en de `accountname` voor de `accountName` JSON-eigenschap gebruiken.
   2. Geef de sleutel van het Azure Batch-account op voor de eigenschap **accessKey** .
   3. Geef de naam op van de groep die u hebt gemaakt als onderdeel van vereisten voor de eigenschap **pool** naam. U kunt ook de ID van de pool opgeven in plaats van de naam van de pool.
   4. Geef Azure Batch URI op voor de eigenschap **batchUri** . Bijvoorbeeld: `https://westus.batch.azure.com`.
   5. Geef de **AzureStorageLinkedService** op voor de eigenschap **linkedServiceName** .

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       Voor de eigenschap **pool** naam kunt u ook de id van de pool opgeven in plaats van de naam van de groep.

### <a name="step-3-create-datasets"></a>Stap 3: gegevens sets maken
In deze stap maakt u gegevens sets om invoer-en uitvoer gegevens weer te geven.

#### <a name="create-input-dataset"></a>Invoergegevensset maken
1. Klik in de **Editor** voor de Data Factory op **... Meer** op de opdracht balk klikt u op **nieuwe gegevensset** en selecteert u vervolgens **Azure Blob-opslag** in de vervolg keuzelijst.
2. Vervang de JSON in het rechterdeel venster met het volgende JSON-fragment:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```

   U maakt later in dit overzicht een pijp lijn met de begin tijd: 2016-11-16T00:00:00Z en eind tijd: 2016-11-16T05:00:00Z. Het is gepland om gegevens elk uur te produceren, dus er zijn vijf invoer/uitvoer segmenten (tussen **00**: 00:00-> **05**: 00:00).

   De **frequentie** en het **interval** voor de invoer gegevensset worden ingesteld op **uur** en **1**, wat betekent dat het invoer segment per uur beschikbaar is. In dit voor beeld is het hetzelfde bestand (file.txt) in de intputfolder.

   Hier volgen de begin tijden van elk segment, dat wordt vertegenwoordigd door de systeem variabele slice start in het bovenstaande JSON-fragment.
3. Klik op **implementeren** op de werk balk om de **input dataset** te maken en te implementeren. Controleer of u **TABEL IS GEMAAKT** ziet op de titelbalk van de editor.

#### <a name="create-an-output-dataset"></a>Een uitvoergegevensset maken
1. Klik in de **Data Factory editor** op **... Meer** op de opdracht balk, klikt u op **nieuwe gegevensset** en selecteert u **Azure Blob-opslag**.
2. Vervang het JSON-script in het rechterdeel venster met het volgende JSON-script:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     De uitvoer locatie is **adftutorial/customactivityoutput/** en de naam van het uitvoer bestand is yyyy-MM-dd-HH.txt waarbij JJJJ-MM-DD-UU het jaar, de maand, de datum en het uur van het gegenereerde segment is. Zie [referentie voor ontwikkel aars][adf-developer-reference] voor meer informatie.

    Er wordt een uitvoer-blob/-bestand gegenereerd voor elk invoer segment. Hier ziet u hoe een uitvoer bestand een naam krijgt voor elk segment. Alle uitvoer bestanden worden gegenereerd in één Uitvoermap: **adftutorial\customactivityoutput**.

   | Gereedschap | Begintijd | Uitvoerbestand |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Houd er rekening mee dat alle bestanden in een invoer map deel uitmaken van een segment met de hierboven vermelde begin tijden. Wanneer dit segment wordt verwerkt, wordt door elk bestand door de aangepaste activiteit gescand en wordt er een regel in het uitvoer bestand gemaakt met het aantal exemplaren van de zoek term (' micro soft '). Als er drie bestanden in de map voor invoer zijn, zijn er drie regels in het uitvoer bestand voor elk elk uur segment: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt, enzovoort.
3. Klik op **implementeren** op de opdracht balk om de **output dataset** te implementeren.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Een pijp lijn maken en uitvoeren die gebruikmaakt van de aangepaste activiteit
1. Klik in de Data Factory editor op **... Meer** en selecteer **nieuwe pijp lijn** op de opdracht balk.
2. Vervang de JSON in het rechterdeel venster met het volgende JSON-script:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Houd rekening met de volgende punten:

   * **Gelijktijdigheid** wordt ingesteld op **2** zodat twee segmenten parallel worden verwerkt door 2 virtuele machines in de groep Azure batch.
   * De sectie activities bevat één activiteit en is van het type: **DotNetActivity**.
   * **Assemblyname** is ingesteld op de naam van de DLL: **MyDotnetActivity.dll**.
   * **Entry Point** wordt ingesteld op **MyDotNetActivityNS. MyDotNetActivity**.
   * **PackageLinkedService** wordt ingesteld op **AzureStorageLinkedService** die verwijst naar de Blob-opslag met het zip-bestand van de aangepaste activiteit. Als u verschillende Azure Storage accounts gebruikt voor invoer-en uitvoer bestanden en het zip-bestand van de aangepaste activiteit, maakt u een andere Azure Storage gekoppelde service. In dit artikel wordt ervan uitgegaan dat u hetzelfde Azure Storage-account gebruikt.
   * **PackageFile** is ingesteld op **customactivitycontainer/MyDotNetActivity.zip**. Deze heeft de volgende indeling: containerforthezip/nameofthezip.zip.
   * De aangepaste activiteit krijgt **input dataset** als invoer en **output dataset** als uitvoer.
   * De eigenschap linkedServiceName van de aangepaste activiteit verwijst naar de **AzureBatchLinkedService**, die aangeeft Azure Data Factory dat de aangepaste activiteit moet worden uitgevoerd op Azure batch vm's.
   * de eigenschap **isPaused** is standaard ingesteld op **Onwaar** . De pijp lijn wordt direct in dit voor beeld uitgevoerd, omdat de segmenten in het verleden beginnen. U kunt deze eigenschap instellen op True om de pijp lijn te onderbreken en weer in te stellen op false om opnieuw op te starten.
   * De **begin** -en **eind** tijd zijn **vijf** uur gescheiden en segmenten worden elk uur geproduceerd, waardoor er vijf segmenten worden geproduceerd door de pijp lijn.
3. Klik op **implementeren** op de opdracht balk om de pijp lijn te implementeren.

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken
1. Klik op de Blade Data Factory in de Azure Portal op **diagram**.

    ![Tegel Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Klik nu in de diagram weergave op de output DataSet.

    ![Diagramweergave](./media/data-factory-use-custom-activities/diagram.png)
3. U ziet dat de vijf uitvoer segmenten de status gereed hebben. Als ze zich niet in de status gereed bevinden, zijn ze nog niet gemaakt.

   ![Uitvoer segmenten](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Controleer of de uitvoer bestanden worden gegenereerd in de Blob-opslag in de **adftutorial** -container.

   ![uitvoer van aangepaste activiteit][image-data-factory-output-from-custom-activity]
5. Als u het uitvoer bestand opent, ziet u de uitvoer vergelijkbaar met de volgende uitvoer:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Gebruik de [Azure Portal][azure-preview-portal] -of Azure PowerShell-cmdlets om uw Data Factory, pijp lijnen en gegevens sets te bewaken. U kunt berichten van de **ActivityLogger** weer geven in de code voor de aangepaste activiteit in de logboeken (met name User-0. log) die u kunt downloaden via de portal of met behulp van cmdlets.

   ![Logboeken downloaden vanuit een aangepaste activiteit][image-data-factory-download-logs-from-custom-activity]

Zie [pijp lijnen bewaken en beheren](data-factory-monitor-manage-pipelines.md) voor gedetailleerde stappen voor het bewaken van gegevens sets en pijp lijnen.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory project in Visual Studio
U kunt Data Factory entiteiten maken en publiceren met behulp van Visual Studio in plaats van Azure Portal. Zie [uw eerste pijp lijn bouwen met Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) en [gegevens kopiëren van Azure Blob naar Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) -artikelen voor gedetailleerde informatie over het maken en publiceren van Data Factory entiteiten met behulp van Visual Studio.

Voer de volgende aanvullende stappen uit als u Data Factory project maakt in Visual Studio:

1. Voeg het Data Factory project toe aan de Visual Studio-oplossing die het aangepaste activiteiten project bevat.
2. Voeg een verwijzing naar het .NET-activiteiten project toe vanuit het Data Factory-project. Klik met de rechter muisknop op Data Factory project, wijs **toevoegen** aan en klik vervolgens op **verwijzing**.
3. Selecteer in het dialoog venster **verwijzing toevoegen** het project **MyDotNetActivity** en klik op **OK**.
4. Bouw en publiceer de oplossing.

    > [!IMPORTANT]
    > Wanneer u Data Factory entiteiten publiceert, wordt automatisch een zip-bestand voor u gemaakt en geüpload naar de BLOB-container: customactivitycontainer. Als de BLOB-container niet bestaat, wordt deze ook automatisch gemaakt.

## <a name="data-factory-and-batch-integration"></a>Integratie van Data Factory en batch
De Data Factory-service maakt een taak in Azure Batch met de naam: **ADF-poolnaam: Job-xxx**. Klik op **taken** in het menu links.

![Azure Data Factory-batch taken](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Er wordt een taak gemaakt voor elke activiteit uitvoering van een segment. Als er vijf segmenten gereed zijn om te worden verwerkt, worden er vijf taken in deze taak gemaakt. Als er meerdere reken knooppunten in de batch-pool zijn, kunnen twee of meer segmenten parallel worden uitgevoerd. Als het maximum aantal taken per Compute-knoop punt is ingesteld op > 1, kunt u ook meer dan één segment op dezelfde Compute uitvoeren.

![Azure Data Factory-batch taak taken](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

In het volgende diagram ziet u de relatie tussen Azure Data Factory-en batch taken.

![Data Factory & batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Fouten bij probleemoplossing
Het oplossen van problemen bestaat uit een aantal basis technieken:

1. Als u de volgende fout ziet, kunt u een hot/cool Blob-opslag gebruiken in plaats van een algemene Azure Blob-opslag te gebruiken. Upload het zip-bestand naar een **Azure Storage-account voor algemeen gebruik**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Als u de volgende fout ziet, controleert u of de naam van de klasse in het CS-bestand overeenkomt met de naam die u hebt opgegeven voor de eigenschap **Entry Point** in de JSON van de pijp lijn. In het scenario is de naam van de klasse: MyDotNetActivity en het ingangs punt in de JSON: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Als de namen overeenkomen, controleert u of alle binaire bestanden zich in de **hoofdmap** van het zip-bestand bevinden. Dat wil zeggen: wanneer u het zip-bestand opent, ziet u alle bestanden in de hoofdmap, niet in submappen.
3. Als het invoer segment niet is ingesteld op **gereed**, controleert u of de indeling van de invoer mappen juist is en **file.txt** in de invoer mappen bestaat.
3. Gebruik in de methode **Execute** van uw aangepaste activiteit het object **IActivityLogger** om informatie te registreren die u helpt bij het oplossen van problemen. De geregistreerde berichten worden weer gegeven in de logboek bestanden van de gebruiker (een of meer bestanden met de naam: User-0. log, User-1. log, User-2. log, enzovoort).

   Klik op de Blade **output dataset** op het segment om de Blade **gegevens segment** voor dat segment weer te geven. U ziet de uitvoering van de **activiteit** voor dat segment. U ziet dat er één activiteit wordt uitgevoerd voor het segment. Als u in de opdracht balk op uitvoeren klikt, kunt u een andere uitvoering van de activiteit voor hetzelfde segment starten.

   Wanneer u op de uitvoering van de activiteit klikt, ziet u de Blade **Details uitvoering van activiteit** met een lijst met logboek bestanden. U ziet berichten die zijn geregistreerd in het bestand user_0. log. Wanneer er een fout optreedt, ziet u dat er drie activiteiten worden uitgevoerd omdat het aantal nieuwe pogingen is ingesteld op 3 in de JSON van de pijp lijn/activiteit. Wanneer u op de uitvoering van de activiteit klikt, ziet u de logboek bestanden die u kunt controleren om de fout op te lossen.

   Klik in de lijst met logboek bestanden op de **User-0. log**. In het rechterdeel venster vindt u de resultaten van het gebruik van de methode **IActivityLogger. write** . Als u niet alle berichten ziet, controleert u of er meer logboek bestanden met de naam: user_1. log, user_2. log, enzovoort. Anders is het mogelijk dat de code na het laatste geregistreerde bericht is mislukt.

   Controleer bovendien **System-0. log** op systeem fout berichten en uitzonde ringen.
4. Neem het **PDB** -bestand op in het zip-bestand, zodat de fout Details informatie, zoals **aanroep stack** , bevatten wanneer er een fout optreedt.
5. Alle bestanden in het zip-bestand voor de aangepaste activiteit moeten zich op het **hoogste niveau** bevinden, zonder submappen.
6. Zorg ervoor dat **de assemblyname** (MyDotNetActivity.dll), het **ingangs punt**(MyDotNetActivityNS. MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) en **packageLinkedService** (moet verwijzen naar de Azure Blob-opslag voor **algemeen gebruik** die het zip-bestand bevat) is ingesteld op de juiste waarden.
7. Als u een fout hebt hersteld en het segment opnieuw wilt verwerken, klikt u met de rechtermuisknop op het segment op de blade **OutputDataset** en klikt u op **Uitvoeren**.
8. Als u de volgende fout ziet, gebruikt u de Azure Storage pakket versie > 4.3.0. Data Factory Service Launcher vereist de 4,3-versie van WindowsAzure. storage. Zie de sectie [AppDomain-isolatie](#appdomain-isolation) voor een tijdelijke oplossing als u de nieuwere versie van Azure Storage-assembly moet gebruiken.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Als u de 4.3.0-versie van Azure Storage pakket kunt gebruiken, verwijdert u de bestaande verwijzing naar Azure Storage pakket versie > 4.3.0. Voer vervolgens de volgende opdracht uit vanuit de NuGet Package Manager-console.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Maak het project. Verwijder de Azure. Storage-assembly van versie > 4.3.0 uit de map bin\Debug. Maak een zip-bestand met binaire bestanden en het PDB-bestand. Vervang het oude zip-bestand door deze in de BLOB-container (customactivitycontainer). Voer de segmenten die zijn mislukt opnieuw uit (Klik met de rechter muisknop op segment en klik op uitvoeren).
8. De aangepaste activiteit maakt geen gebruik van het **app.config** bestand uit uw pakket. Als uw code verbindings reeksen uit het configuratie bestand leest, werkt deze daarom niet tijdens runtime. De best practice wanneer u Azure Batch gebruikt om geheimen te bewaren in een **Azure**-sleutel kluis, een service-principal op basis van certificaten te gebruiken om de sleutel **kluis** te beveiligen en het certificaat te distribueren naar Azure batch groep. De aangepaste .NET-activiteit heeft dan in runtime toegang tot geheimen in de KeyVault. Deze oplossing is een algemene oplossing en kan worden geschaald naar elk type geheim, niet alleen connection string.

   Er is een eenvoudiger tijdelijke oplossing (maar geen best practice): u kunt een **gekoppelde Azure SQL-service** maken met Connection String-instellingen, een gegevensset maken die gebruikmaakt van de gekoppelde service en de gegevensset als een dummy-invoer gegevensset koppelen aan de aangepaste .net-activiteit. U kunt vervolgens de connection string van de gekoppelde service openen in de code van de aangepaste activiteit.

## <a name="update-custom-activity"></a>Aangepaste activiteit bijwerken
Als u de code voor de aangepaste activiteit bijwerkt, bouwt u deze op en uploadt u het zip-bestand dat nieuwe binaire bestanden bevat naar de Blob-opslag.

## <a name="appdomain-isolation"></a>AppDomain-isolatie
Zie [Cross AppDomain voor beeld](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) waarin wordt uitgelegd hoe u een aangepaste activiteit maakt die niet is beperkt tot assembly versies die worden gebruikt door de Data Factory Launcher (bijvoorbeeld: WindowsAzure. Storage v 4.3.0, Newtonsoft.Jsop v 6.0. x enzovoort).

## <a name="access-extended-properties"></a>Uitgebreide eigenschappen van Access
U kunt uitgebreide eigenschappen declareren in de JSON van de activiteit, zoals wordt weer gegeven in het volgende voor beeld:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

In het voor beeld zijn er twee uitgebreide eigenschappen: **slice start** en **DataFactoryName**. De waarde voor slice start is gebaseerd op de systeem variabele slice start. Zie [systeem variabelen](data-factory-functions-variables.md) voor een lijst met ondersteunde systeem variabelen. De waarde voor DataFactoryName wordt vastgelegd in CustomActivityFactory.

Gebruik de volgende code om toegang te krijgen tot deze uitgebreide eigenschappen in de methode **Execute** :

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Automatisch schalen van Azure Batch
U kunt ook een Azure Batch groep maken met de functie voor **automatisch schalen** . U kunt bijvoorbeeld een Azure batch-pool maken met 0 toegewezen Vm's en een formule voor automatisch schalen op basis van het aantal in behandeling zijnde taken.

Met de voorbeeld formule wordt het volgende gedrag gerealiseerd: wanneer de pool voor het eerst wordt gemaakt, begint deze met 1 VM. $PendingTasks metriek definieert het aantal taken in de status actief + actief (in wachtrij).  Met de formule vindt u het gemiddelde aantal taken in de afgelopen 180 seconden dat in behandeling is en stelt u TargetDedicated dienovereenkomstig in. Zo zorgt u ervoor dat TargetDedicated nooit meer dan 25 Vm's verloopt. Als er nieuwe taken worden ingediend, neemt de groep automatisch toe en als de taken zijn voltooid, worden virtuele machines één voor één vrijgegeven en wordt de virtuele machine met automatisch schalen kleiner. startingNumberOfVMs en maxNumberofVMs kunnen worden aangepast aan uw behoeften.

Formule voor automatisch schalen:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zie [reken knooppunten automatisch schalen in een Azure batch groep](../../batch/batch-automatic-scaling.md) voor meer informatie.

Als de groep de standaard [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)gebruikt, kan de batch-service 15-30 minuten in beslag nemen voordat de aangepaste activiteit wordt uitgevoerd.  Als de groep een andere autoScaleEvaluationInterval gebruikt, kan de batch-service autoScaleEvaluationInterval + 10 minuten duren.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Een aangepaste activiteit maken met behulp van .NET SDK
In de procedure in dit artikel maakt u een data factory met een pijp lijn die gebruikmaakt van de aangepaste activiteit met behulp van de Azure Portal. In de volgende code ziet u hoe u de data factory maakt met behulp van .NET SDK. U vindt meer informatie over het gebruik van SDK om via programma code pijp lijnen te maken in het artikel [een pijp lijn maken met een Kopieer activiteit met behulp van .net API](data-factory-copy-activity-tutorial-using-dotnet-api.md) .

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Fouten opsporen in aangepaste activiteit in Visual Studio
Het voor beeld van de [Azure Data Factory-lokale omgeving](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) op github bevat een hulp programma waarmee u fouten in aangepaste .net-activiteiten in Visual Studio kunt opsporen.

## <a name="sample-custom-activities-on-github"></a>Voor beelden van aangepaste activiteiten op GitHub
| Voorbeeld | Wat aangepaste activiteit doet |
| --- | --- |
| [Download programma voor http-gegevens](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Hiermee worden gegevens van een HTTP-eind punt gedownload naar Azure Blob Storage met behulp van aangepaste C#-activiteit in Data Factory. |
| [Voor beeld van Twitter Sentimentanalyse](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Hiermee wordt een Azure Machine Learning Studio (klassiek)-model aangeroepen en sentiment analyse, Score ring, voor spelling, enzovoort. |
| [R-script uitvoeren](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Hiermee wordt het R-script aangeroepen door RScript.exe uit te voeren op uw HDInsight-cluster waarop al R is geïnstalleerd. |
| [.NET-activiteit over meerdere forests](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Maakt gebruik van verschillende assembly versies van die worden gebruikt door de Data Factory Launcher |
| [Een model opnieuw verwerken in Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Verwerkt een model opnieuw in Azure Analysis Services. |

[batch-net-library]: ../../batch/quick-run-dotnet.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]:../../azure-sql/database/sql-database-paas-overview.md
[batch-get-started]: ../../batch/quick-run-dotnet.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: /previous-versions/azure/mt125880(v=azure.100)
[new-azure-batch-pool]: /previous-versions/azure/mt125936(v=azure.100)
[azure-batch-blog]: /archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png