---
title: Grootschalige gegevens sets verwerken met behulp van Data Factory en batch
description: Hierin wordt beschreven hoe u enorme hoeveel heden gegevens in een Azure Data Factory pijp lijn verwerkt met behulp van de parallelle verwerkings capaciteit van Azure Batch.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 2568fc2ea133a0cdd49e0a2577ce1e580056a74c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783129"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Grootschalige gegevens sets verwerken met behulp van Data Factory en batch
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory, die algemeen beschikbaar is. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [aangepaste activiteiten in Data Factory](../transform-data-using-dotnet-custom-activity.md).

In dit artikel wordt een architectuur beschreven van een voor beeld van een oplossing waarmee grootschalige gegevens sets op een automatische en geplande manier worden verplaatst en verwerkt. Het bevat ook een end-to-end-procedure voor het implementeren van de oplossing met behulp van Data Factory en Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dit artikel is langer dan een typisch artikel omdat het een overzicht bevat van een volledige voorbeeld oplossing. Als u geen ervaring hebt met batch en Data Factory, kunt u meer informatie vinden over deze services en hoe ze samen werken. Als u iets weet over de services en een oplossing ontwerpt/opzet, kunt u zich concentreren op de sectie architectuur van het artikel. Als u een prototype of een oplossing ontwikkelt, wilt u mogelijk de stapsgewijze instructies in het overzicht proberen. We nodigen uw opmerkingen uit over deze inhoud en hoe u deze gebruikt.

Laten we eerst eens kijken hoe Data Factory en batch Services u kunnen helpen grote gegevens sets in de cloud te verwerken.     


## <a name="why-azure-batch"></a>Waarom Azure Batch?
 U kunt batch gebruiken om op efficiënte wijze grootschalige parallelle en HPC-toepassingen (High Performance Computing) in de Cloud uit te voeren. Het is een platform service die reken intensief werk plant om te worden uitgevoerd op een beheerde verzameling virtuele machines (Vm's). Hiermee kunnen reken resources automatisch worden geschaald om te voldoen aan de behoeften van uw taken.

Met de Batch-service definieert u Azure-rekenresources om uw toepassingen parallel en op de juiste schaal uit te voeren. U kunt taken uitvoeren op aanvraag of gepland. U hoeft niet hand matig een HPC-cluster, afzonderlijke Vm's, virtuele netwerken of een complexe taak en een infra structuur voor taak planning te maken, configureren en beheren.

 Als u niet bekend bent met batch, kunt u de volgende artikelen gebruiken om inzicht te krijgen in de architectuur/implementatie van de oplossing die in dit artikel wordt beschreven:   

* [Basis principes van batch](../../azure-sql/database/sql-database-paas-overview.md)
* [Overzicht van de functies van Batch](../../batch/batch-service-workflow-features.md)

Zie ook [de batch-documentatie](../../batch/index.yml)voor meer informatie over batch.

## <a name="why-azure-data-factory"></a>Waarom Azure Data Factory?
Een Data Factory is een cloudgebaseerde gegevensintegratieservice waarmee de verplaatsing en transformatie van gegevens wordt beheerd en geautomatiseerd. U kunt Data Factory gebruiken om beheerde gegevens pijplijnen te maken waarmee gegevens worden verplaatst van on-premises en gegevens opslag in de Cloud naar een gecentraliseerd gegevens archief. Een voor beeld is Azure Blob-opslag. U kunt Data Factory gebruiken om gegevens te verwerken/transformeren met behulp van services zoals Azure HDInsight en Azure Machine Learning. U kunt ook gegevens pijplijnen zodanig plannen dat deze op een geplande manier worden uitgevoerd (bijvoorbeeld elk uur, dagelijks en wekelijks). U kunt de pijp lijnen in één oogopslag bewaken en beheren om problemen te identificeren en actie te ondernemen.

  Als u niet bekend bent met Data Factory, kunt u de volgende artikelen gebruiken om inzicht te krijgen in de architectuur/implementatie van de oplossing die in dit artikel wordt beschreven:  

* [Inleiding tot Data Factory](data-factory-introduction.md)
* [Uw eerste gegevens pijplijn bouwen](data-factory-build-your-first-pipeline.md)   

Zie ook [de Data Factory documentatie](/rest/api/datafactory/v1/data-factory-data-factory)voor meer informatie over Data Factory.

## <a name="data-factory-and-batch-together"></a>Data Factory en batch samen
Data Factory bevat ingebouwde activiteiten. De Kopieer activiteit wordt bijvoorbeeld gebruikt voor het kopiëren/verplaatsen van gegevens uit een brongegevens archief naar een doel gegevens archief. De Hive-activiteit wordt gebruikt voor het verwerken van gegevens met behulp van Hadoop-clusters (HDInsight) in Azure. Zie [activiteiten voor gegevens transformatie](data-factory-data-transformation-activities.md)voor een lijst met ondersteunde transformatie activiteiten.

U kunt ook aangepaste .NET-activiteiten maken om gegevens met uw eigen logica te verplaatsen of te verwerken. U kunt deze activiteiten uitvoeren op een HDInsight-cluster of op een batch-pool van Vm's. Wanneer u batch gebruikt, kunt u de groep zodanig configureren dat deze automatisch wordt geschaald (Vm's toevoegen of verwijderen op basis van de werk belasting) op basis van een door u verstrekte formule.     

## <a name="architecture-of-a-sample-solution"></a>Architectuur van een voorbeeld oplossing
  De architectuur die in dit artikel wordt beschreven, is voor een eenvoudige oplossing. Het is ook relevant voor complexe scenario's, zoals risico modellen door financiële services, verwerking van afbeeldingen en rendering en genomic-analyses.

Het diagram illustreert hoe Data Factory gegevens verplaatsing en-verwerking organiseert. Ook wordt aangegeven hoe de gegevens op een parallelle manier door batch worden verwerkt. Down load het diagram en druk het af voor eenvoudige Naslag informatie (11 x 17 inch of a3). Voor toegang tot het diagram, zodat u het kunt afdrukken, raadpleegt u [HPC en gegevens indeling met behulp van batch en Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagram voor grootschalige gegevens verwerking](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

De volgende lijst bevat de basis stappen van het proces. De oplossing bevat code en uitleg om de end-to-end-oplossing te bouwen.

* **Een batch configureren met een pool van reken knooppunten (Vm's).** U kunt het aantal knoop punten en de grootte van elk knoop punt opgeven.

* **Maak een Data Factory-exemplaar** dat is geconfigureerd met entiteiten die Blob Storage vertegenwoordigen, de batch compute-service, invoer/uitvoer gegevens en een werk stroom/pijp lijn met activiteiten waarmee gegevens worden verplaatst en getransformeerd.

* **Maak een aangepaste .NET-activiteit in de Data Factory-pijp lijn.** De activiteit is uw gebruikers code die wordt uitgevoerd op de batch-pool.

* **Sla grote hoeveel heden invoer gegevens op als blobs in Azure Storage.** Gegevens worden onderverdeeld in logische segmenten (meestal op tijd).

* **Data Factory kopieert gegevens die parallel worden verwerkt** naar de secundaire locatie.

* **Data Factory voert de aangepaste activiteit uit met behulp van de pool toegewezen door batch.** Data Factory kunt activiteiten gelijktijdig uitvoeren. Elke activiteit verwerkt een gegevens segment. De resultaten worden opgeslagen in de opslag.

* **Data Factory de uiteindelijke resultaten naar een derde locatie verplaatst,** hetzij voor distributie via een app of voor verdere verwerking door andere hulpprogram ma's.

## <a name="implementation-of-the-sample-solution"></a>Implementatie van de voorbeeld oplossing
De voorbeeld oplossing is opzettelijk eenvoudig. Het is ontworpen om u te laten zien hoe u Data Factory en batch samen kunt gebruiken om gegevens sets te verwerken. De oplossing telt het aantal exemplaren van de zoek term ' micro soft ' in invoer bestanden die zijn ingedeeld in een tijd reeks. Vervolgens wordt het aantal uitgevoerd naar uitvoer bestanden.

**Tijd:** Als u bekend bent met de basis principes van Azure, Data Factory en batch en u de volgende vereisten hebt voltooid, duurt het één tot twee uur om deze oplossing te volt ooien.

### <a name="prerequisites"></a>Vereisten
#### <a name="azure-subscription"></a>Azure-abonnement
Als u geen Azure-abonnement hebt, kunt u snel een gratis proef account maken. Zie [gratis proef versie](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie.

#### <a name="azure-storage-account"></a>Azure Storage-account
In deze zelf studie gebruikt u een opslag account om de gegevens op te slaan. Zie [een opslag account maken](../../storage/common/storage-account-create.md)als u geen opslag account hebt. De voorbeeld oplossing maakt gebruik van Blob Storage.

#### <a name="azure-batch-account"></a>Azure Batch-account
Maak een batch-account met behulp van de [Azure Portal](https://portal.azure.com/). Zie [een batch-account maken en beheren](../../batch/batch-account-create-portal.md)voor meer informatie. Noteer de batch-account naam en de account sleutel. U kunt ook de cmdlet [New-AzBatchAccount](/powershell/module/az.batch/new-azbatchaccount) gebruiken om een batch-account te maken. Zie [aan de slag met batch-Power shell-cmdlets](../../batch/batch-powershell-cmdlets-get-started.md)voor instructies over het gebruik van deze cmdlet.

De voorbeeld oplossing maakt gebruik van batch (indirect via een data factory pijp lijn) om gegevens parallel te verwerken op een pool van reken knooppunten (een beheerde verzameling virtuele machines).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch pool van virtuele machines
Maak een batch-pool met ten minste twee reken knooppunten.

1. Selecteer in het [Azure Portal](https://portal.azure.com) **Bladeren** in het menu links en selecteer batch- **accounts**.

1. Selecteer uw batch-account om de Blade **batch-account** te openen.

1. Selecteer de tegel **groepen** .

1. Selecteer op de Blade **Pools** de knop **toevoegen** op de werk balk om een groep toe te voegen.

   a. Voer een ID in voor de pool (**groeps-id**). Noteer de ID van de groep. U hebt deze nodig wanneer u de data factory-oplossing maakt.

   b. Geef **Windows Server 2012 R2** op voor de instelling van het **besturings systeem** .

   c. Selecteer een **prijs categorie voor het knoop punt**.

   d. Voer **2** in als de waarde voor de **toegewezen doel** instelling.

   e. Voer **2** in als de waarde voor de instelling **Max. aantal taken per knoop punt** .

   f. Selecteer **OK** om de groep te maken.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
U gebruikt [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) of [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (van ClumsyLeaf-software) om de gegevens in uw opslag projecten te controleren en te wijzigen. U kunt ook de gegevens in de logboeken van uw in de Cloud gehoste toepassingen controleren en wijzigen.

1. Maak een container met de naam **mycontainer** met persoonlijke toegang (geen anonieme toegang).

1. Als u CloudXplorer gebruikt, maakt u mappen en submappen met de volgende structuur:

   ![Structuur van mappen en submappen](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` en `outputfolder` zijn mappen op het hoogste niveau in `mycontainer` . De `inputfolder` map bevat submappen met een datum/tijd stempel (jjjj-mm-dd-UU).

   Als u Storage Explorer gebruikt, uploadt u in de volgende stap bestanden met de volgende namen: `inputfolder/2015-11-16-00/file.txt` , `inputfolder/2015-11-16-01/file.txt` , enzovoort. Met deze stap worden de mappen automatisch gemaakt.

1. Maak een tekst bestand **file.txt** op uw computer met inhoud met het sleutel woord **micro soft**. Een voor beeld is ' aangepaste activiteit testen die micro soft heeft gepaard micro soft '.

1. Upload het bestand naar de volgende invoer mappen in Blob Storage:

   ![Invoer mappen](./media/data-factory-data-processing-using-batch/image4.png)

   Als u Storage Explorer gebruikt, uploadt u het **file.txt** bestand naar **mycontainer**. Selecteer **kopiëren** op de werk balk om een kopie van de BLOB te maken. Wijzig in het dialoog venster **BLOB kopiëren** de naam van de **doel-BLOB** in `inputfolder/2015-11-16-00/file.txt` . Herhaal deze stap om te maken `inputfolder/2015-11-16-01/file.txt` , `inputfolder/2015-11-16-02/file.txt` ,,, enzovoort `inputfolder/2015-11-16-03/file.txt` `inputfolder/2015-11-16-04/file.txt` . Met deze actie worden automatisch de mappen gemaakt.

1. Maak een andere container met de naam `customactivitycontainer` . Upload het zip-bestand van de aangepaste activiteit naar deze container.

#### <a name="visual-studio"></a>Visual Studio
Installeer Visual Studio 2012 of hoger om de aangepaste batch-activiteit te maken die in de data factory oplossing moet worden gebruikt.

### <a name="high-level-steps-to-create-the-solution"></a>Stappen op hoog niveau voor het maken van de oplossing
1. Maak een aangepaste activiteit die de logica voor gegevens verwerking bevat.

1. Maak een data factory die gebruikmaakt van de aangepaste activiteit.

### <a name="create-the-custom-activity"></a>De aangepaste activiteit maken
De data factory aangepaste activiteit is het hart van deze voorbeeld oplossing. De voorbeeld oplossing maakt gebruik van batch om de aangepaste activiteit uit te voeren. Zie [aangepaste activiteiten gebruiken in een Data Factory-pijp lijn](data-factory-use-custom-activities.md)voor meer informatie over het ontwikkelen van aangepaste activiteiten en het gebruik ervan in Data Factory pijp lijnen.

Als u een aangepaste .NET-activiteit wilt maken die u in een data factory pijp lijn kunt gebruiken, maakt u een .NET-klassen bibliotheek project met een klasse die de IDotNetActivity-interface implementeert. Deze interface heeft slechts één methode: Execute. Dit is de hand tekening van de methode:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

De-methode heeft enkele belang rijke onderdelen die u moet begrijpen:

* De methode heeft vier para meters:

  * **linkedServices**. Deze para meter is een overzicht van gekoppelde services die invoer/uitvoer-gegevens bronnen (bijvoorbeeld Blob Storage) koppelen aan de data factory. In dit voor beeld is er slechts één gekoppelde service van het type Azure Storage gebruikt voor invoer en uitvoer.
  * **gegevens sets**. Deze para meter is een overzicht van gegevens sets. U kunt deze para meter gebruiken om de locaties en schema's op te halen die worden gedefinieerd door de invoer-en uitvoer gegevens sets.
  * **activiteit**. Deze para meter vertegenwoordigt de huidige Compute-entiteit. In dit geval is het een batch-service.
  * **logger**. U kunt de logboek registratie gebruiken om fout opsporingsgegevens te schrijven die het Opper vlak ' gebruiker ' voor de pijp lijn.
* De-methode retourneert een woorden lijst die kan worden gebruikt om aangepaste activiteiten in de toekomst te koppelen. Deze functie is nog niet geïmplementeerd, dus retour neren alleen een lege woorden lijst van de methode.

#### <a name="procedure-create-the-custom-activity"></a>Procedure: de aangepaste activiteit maken
1. Maak een .NET Class Library-project in Visual Studio.

   a. Start Visual Studio 2012/2013/2015.

   b. Selecteer **Bestand** > **Nieuw** > **Project**.

   c. Vouw **sjablonen** uit en selecteer **Visual C \#**. In dit scenario gebruikt u C \# , maar u kunt elke .net-taal gebruiken om de aangepaste activiteit te ontwikkelen.

   d. Selecteer **Class Library** in de lijst met project typen aan de rechter kant.

   e. Voer **MyDotNetActivity** in als **naam**.

   f. Selecteer **C: \\ ADF** voor de **locatie**. Maak de map **ADF** als deze nog niet bestaat.

   g. Selecteer **OK** om het project te maken.

1. Selecteer **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager-console**.

1. Voer in de Package Manager-console de volgende opdracht uit om micro soft. Azure. Management. DataFactories te importeren:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importeer het **Azure Storage** NuGet-pakket in het project. U hebt dit pakket nodig omdat u de Blob Storage-API in dit voor beeld gebruikt:

    ```powershell
    Install-Package Az.Storage
    ```
1. Voeg de volgende instructies toe aan het bron bestand in het project:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Wijzig de naam van de naam ruimte in **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Wijzig de naam van de klasse in **MyDotNetActivity** en afleiden van de **IDotNetActivity** -interface, zoals wordt weer gegeven:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementeer (toevoegen) de **Execute** -methode van de **IDotNetActivity** -interface aan de klasse **MyDotNetActivity** . Kopieer de volgende voorbeeld code naar de-methode. Zie de sectie [methode uitvoeren](#execute-method) voor een uitleg van de logica die wordt gebruikt in deze methode.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
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

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Voeg de volgende Help-methoden toe aan de klasse. Deze methoden worden aangeroepen door de **Execute** -methode. Met de methode **Calculate** wordt de code voor het door lopen van elke BLOB geïsoleerd.

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

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

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

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    De methode GetFolderPath retourneert het pad naar de map waarnaar de DataSet verwijst en de methode GetFileName retourneert de naam van het BLOB/bestand waarnaar de gegevensset verwijst.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    De methode Calculate berekent het aantal exemplaren van het sleutel woord ' micro soft ' in de invoer bestanden (blobs in de map). De zoek term micro soft is vastgelegd in de code.

1. Het project compileren. Selecteer **Build** in het menu en selecteer vervolgens **Build Solution**.

1. Start Windows Verkenner en ga naar de map **bin \\ debug** of **bin \\ release** . De keuze van de map is afhankelijk van het type Build.

1. Maak een zip-bestand **MyDotNetActivity.zip** dat alle binaire bestanden bevat in de map **\\ bin \\ debug** . Mogelijk wilt u de MyDotNetActivity toevoegen. **PDB** -bestand, zodat u aanvullende informatie krijgt, zoals het regel nummer in de bron code die het probleem heeft veroorzaakt als er een fout optreedt.

   ![De lijst met bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Upload **MyDotNetActivity.zip** als een BLOB naar de BLOB-container `customactivitycontainer` in de Blob-opslag die door de StorageLinkedService gekoppelde service in ADFTutorialDataFactory wordt gebruikt. Maak de BLOB-container `customactivitycontainer` als deze nog niet bestaat.

#### <a name="execute-method"></a>Methode uitvoeren
In deze sectie vindt u meer informatie over de code in de methode Execute.

1. De leden voor het herhalen van de invoer verzameling vindt u in de naam ruimte [micro soft. WindowsAzure. storage. blob](/java/api/com.microsoft.azure.storage.blob) . Als u de BLOB-verzameling wilt door lopen, moet u de **BlobContinuationToken** -klasse gebruiken. In essentie moet u een do-while-lus met het token gebruiken als het mechanisme voor het afsluiten van de lus. Zie [Blob Storage van .net gebruiken](../../storage/blobs/storage-quickstart-blobs-dotnet.md)voor meer informatie. Hier ziet u een eenvoudige lus:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   Zie de documentatie voor de methode [ListBlobsSegmented](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) voor meer informatie.

1. De code voor het werken met de set blobs is logisch binnen de lus-while. In de **Execute** -methode geeft de lus-while de lijst met blobs door aan een methode met de naam **Calculate**. De methode retourneert een teken reeks variabele met de naam **output** die het resultaat is van het door lopen van alle blobs in het segment.

   Hiermee wordt het aantal exemplaren geretourneerd van de zoek term ' micro soft ' in de blob die is door gegeven aan de methode **Calculate** .

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Nadat de methode **Calculate** is voltooid, moet deze worden geschreven naar een nieuwe blob. Voor elke set verwerkte blobs kan een nieuwe BLOB worden geschreven met de resultaten. Als u naar een nieuwe BLOB wilt schrijven, moet u eerst de uitvoer gegevensset vinden.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. De code roept ook de Help-methode **GetFolderPath** aan om het mappad (de naam van de opslag container) op te halen.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   De methode GetFolderPath cast het object DataSet naar een AzureBlobDataSet, dat een eigenschap heeft met de naam FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. De code roept de **GetFileName** -methode op om de bestands naam (BLOB name) op te halen. De code is vergelijkbaar met de vorige code die is gebruikt om het mappad op te halen.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. De naam van het bestand wordt geschreven door een URI-object te maken. De URI-constructor gebruikt de eigenschap **BlobEndpoint** om de container naam te retour neren. Het pad en de bestands naam van de map worden toegevoegd om de URI van de uitvoer-BLOB te maken.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Nadat de naam van het bestand is geschreven, kunt u de uitvoer teken reeks van de methode **Calculate** naar een nieuwe BLOB schrijven:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>De data factory maken
In de sectie [aangepaste activiteit maken](#create-the-custom-activity) hebt u een aangepaste activiteit gemaakt en het zip-bestand met binaire bestanden en het PDB-bestand geüpload naar een BLOB-container. In deze sectie maakt u een data factory met een pijp lijn die gebruikmaakt van de aangepaste activiteit.

De invoer gegevensset voor de aangepaste activiteit vertegenwoordigt de blobs (bestanden) in de invoercel ( `mycontainer\\inputfolder` ) in Blob Storage. De uitvoer gegevensset voor de activiteit vertegenwoordigt de uitvoer-blobs in de uitvoermap ( `mycontainer\\outputfolder` ) in Blob Storage.

Een of meer bestanden neerzetten in de invoer mappen:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

U kunt bijvoorbeeld één bestand (file.txt) met de volgende inhoud in elk van de mappen verwijderen:

```
test custom activity Microsoft test custom activity Microsoft
```

Elke invoercel komt overeen met een segment in het data factory, zelfs als de map twee of meer bestanden bevat. Wanneer elk segment door de pijp lijn wordt verwerkt, doorloopt de aangepaste activiteit alle blobs in de map invoer voor dat segment.

U ziet vijf uitvoer bestanden met dezelfde inhoud. Het uitvoer bestand van verwerking van het bestand in de map 2015-11-16-00 heeft bijvoorbeeld de volgende inhoud:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Als u meerdere bestanden (file.txt, file2.txt, file3.txt) met dezelfde inhoud in de map invoer neerzet, ziet u de volgende inhoud in het uitvoer bestand. Elke map (2015-11-16-00, enz.) komt overeen met een segment in dit voor beeld, ook al heeft de map meerdere invoer bestanden.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Het uitvoer bestand heeft nu drie regels, één voor elk invoer bestand (BLOB) in de map die is gekoppeld aan het segment (2015-11-16-00).

Er wordt een taak gemaakt voor elke uitvoering van de activiteit. In dit voor beeld is er slechts één activiteit in de pijp lijn. Wanneer een segment door de pijp lijn wordt verwerkt, wordt de aangepaste activiteit uitgevoerd op batch om het segment te verwerken. Omdat er vijf segmenten zijn (elk segment kan meerdere blobs of een bestand bevatten), worden er vijf taken in batch gemaakt. Wanneer een taak wordt uitgevoerd op batch, is dit de aangepaste activiteit die wordt uitgevoerd.

In de volgende walkthrough vindt u meer informatie.

#### <a name="step-1-create-the-data-factory"></a>Stap 1: de data factory maken
1. Nadat u zich hebt aangemeld bij de [Azure Portal](https://portal.azure.com/), voert u de volgende stappen uit:

   a. Selecteer **Nieuw** in het menu links.

   b. Selecteer **gegevens en analyses** op de **nieuwe** Blade.

   c. Selecteer **Data Factory** op de Blade **gegevens analyse** .

1. Voer op de Blade **nieuw Data Factory** **CustomActivityFactory** in als naam. De naam van de data factory moet een Globally Unique Identifier zijn. Als het fout bericht ' Data Factory-naam CustomActivityFactory is niet beschikbaar ' wordt weer gegeven, wijzigt u de naam van de data factory. Gebruik bijvoorbeeld yournameCustomActivityFactory en maak de data factory opnieuw.

1. Selecteer de naam van de **resource groep** en selecteer een bestaande resource groep of maak een resource groep.

1. Controleer of het abonnement en de regio waar u de data factory wilt maken juist zijn.

1. Selecteer **maken** op de Blade **Nieuw Data Factory** .

1. De data factory wordt gemaakt in het dash board van de portal.

1. Nadat de data factory is gemaakt, ziet u de pagina **Data Factory** , waarin de inhoud van de Data Factory wordt weer gegeven.

   ![Pagina Data Factory](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Stap 2: gekoppelde services maken
Met gekoppelde services worden gegevens archieven of COMPUTE-Services gekoppeld aan een data factory. In deze stap koppelt u uw opslag account en batch-account aan uw data factory.

#### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken
1. Selecteer de tegel **ontwerpen en implementeren** op de Blade **Data Factory** voor **CustomActivityFactory**. De Data Factory editor wordt weer gegeven.

1. Selecteer **Nieuw gegevens archief** op de opdracht balk en kies **Azure Storage.** Het JSON-script dat u gebruikt voor het maken van een gekoppelde opslag service in de editor wordt weer gegeven.

   ![Nieuwe gegevens opslag](./media/data-factory-data-processing-using-batch/image7.png)

1. Vervang de **accountnaam** door de naam van uw opslagaccount. Vervang de **accountsleutel** met de toegangssleutel van uw opslagaccount. Zie [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md) voor informatie over het ophalen van uw toegangssleutel voor opslag.

1. Selecteer in de opdrachtbalk **Implementeren** om de gekoppelde service te implementeren.

   ![Implementeren](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Een Azure Batch gekoppelde service maken
In deze stap maakt u een gekoppelde service voor uw batch-account dat wordt gebruikt voor het uitvoeren van de data factory aangepaste activiteit.

1. Selecteer **nieuwe Compute** op de opdracht balk en kies **Azure batch.** Het JSON-script dat u gebruikt voor het maken van een gekoppelde batch-service in de editor wordt weer gegeven.

1. In het JSON-script:

   a. Vervang de **account naam** door de naam van uw batch-account.

   b. Vervang de **toegangs sleutel** door de toegangs sleutel van het batch-account.

   c. Voer de ID van de pool in voor de eigenschap **pool** naam. Voor deze eigenschap kunt u de naam van de groep of de groeps-ID opgeven.

   d. Voer de batch-URI in voor de JSON-eigenschap **batchUri** .

      > [!IMPORTANT]
      > De URL van de Blade **batch-account** heeft de volgende indeling: `<accountname>.<region>.batch.azure.com` . Voor de `batchUri` eigenschap in het JSON-script moet u verwijderen `<accountname>.` uit de URL. Een voorbeeld is `"batchUri": "https://eastus.batch.azure.com"`.

      ![Blade batch-account](./media/data-factory-data-processing-using-batch/image9.png)

      Voor de eigenschap **pool** naam kunt u ook de id van de pool opgeven in plaats van de naam van de groep.

      > [!NOTE]
      > De Data Factory-service biedt geen ondersteuning voor een on-demand optie voor batch zoals voor HDInsight. U kunt alleen uw eigen batch-pool gebruiken in een data factory.
      >
      >

   e. Geef **StorageLinkedService** op voor de eigenschap **linkedServiceName** . U hebt deze gekoppelde service gemaakt in de vorige stap. Deze opslag wordt gebruikt als tijdelijke ruimte voor bestanden en Logboeken.

1. Selecteer in de opdrachtbalk **Implementeren** om de gekoppelde service te implementeren.

#### <a name="step-3-create-datasets"></a>Stap 3: gegevens sets maken
In deze stap maakt u gegevens sets om invoer-en uitvoer gegevens weer te geven.

#### <a name="create-the-input-dataset"></a>De invoergegevensset maken
1. Selecteer in de Data Factory editor de knop **nieuwe gegevensset** op de werk balk. Selecteer **Azure Blob-opslag** in de vervolg keuzelijst.

1. Vervang het JSON-script in het rechterdeel venster met het volgende JSON-fragment:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    U maakt later in dit overzicht een pijp lijn met de begin tijd 2015-11-16T00:00:00Z en de eind tijd 2015-11-16T05:00:00Z. Het is gepland om gegevens per uur te produceren, dus er zijn vijf invoer/uitvoer segmenten (tussen **00**: 00:00- \> **05**: 00:00).

    De **frequentie** en het **interval** voor de invoer gegevensset zijn ingesteld op **uur** en **1**, wat betekent dat het invoer segment per uur beschikbaar is.

    De begin tijd voor elk segment wordt weer gegeven door de systeem variabele **slice start** in het vorige JSON-code fragment. Dit zijn de begin tijden voor elk segment.

    | **Gereedschap** | **Begin tijd**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T **00**: 00:00 |
    | 2         | 2015-11-16T **01**: 00:00 |
    | 3         | 2015-11-16T **02**: 00:00 |
    | 4         | 2015-11-16T **03**: 00:00 |
    | 5         | 2015-11-16T **04**: 00:00 |

    De **FolderPath** wordt berekend met behulp van het jaar, de maand, de dag en het uur gedeelte van de begin tijd van het segment (**slice start**). Hier ziet u hoe een uitvoermap wordt toegewezen aan een segment.

    | **Gereedschap** | **Begin tijd**          | **Invoer map**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16-**04** |

1. Selecteer **implementeren** op de werk balk om de tabel **input dataset** te maken en te implementeren.

#### <a name="create-the-output-dataset"></a>De uitvoergegevensset maken
In deze stap maakt u een andere gegevensset van het type AzureBlob om de uitvoer gegevens weer te geven.

1. Selecteer in de Data Factory editor de knop **nieuwe gegevensset** op de werk balk. Selecteer **Azure Blob-opslag** in de vervolg keuzelijst.

1. Vervang het JSON-script in het rechterdeel venster met het volgende JSON-fragment:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Er wordt een uitvoer-blob/-bestand gegenereerd voor elk invoer segment. Hier ziet u hoe een uitvoer bestand een naam krijgt voor elk segment. Alle uitvoer bestanden worden gegenereerd in één uitvoermap, `mycontainer\\outputfolder` .

    | **Gereedschap** | **Begin tijd**          | **Uitvoerbestand**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T **00**: 00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T **01**: 00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T **02**: 00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T **03**: 00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T **04**: 00:00 | 2015-11-16-**04.txt** |

    Houd er rekening mee dat alle bestanden in een map voor invoer (bijvoorbeeld 2015-11-16-00) deel uitmaken van een segment met de begin tijd 2015-11-16-00. Wanneer dit segment wordt verwerkt, scant de aangepaste activiteit elk bestand en produceert een regel in het uitvoer bestand met het aantal exemplaren van de zoek term micro soft. Als er drie bestanden in de map 2015-11-16-00 staan, zijn er drie regels in het uitvoer bestand 2015-11-16-00.txt.

1. Selecteer **implementeren** op de werk balk om de **output dataset** te maken en te implementeren.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Stap 4: de pijp lijn maken en uitvoeren met een aangepaste activiteit
In deze stap maakt u een pijp lijn met één activiteit, de aangepaste activiteit die u eerder hebt gemaakt.

> [!IMPORTANT]
> Als u **file.txt** niet hebt geüpload naar invoer mappen in de BLOB-container, moet u dit doen voordat u de pijp lijn maakt. De eigenschap **isPaused** is ingesteld op ONWAAR in de JSON van de pijp lijn, zodat de pijp lijn onmiddellijk wordt uitgevoerd, omdat de **begin** datum in het verleden ligt.
>
>

1. Selecteer in de Data Factory editor **nieuwe pijp lijn** op de opdracht balk. Als u de opdracht niet ziet, selecteert u het weglatings teken om het weer te geven.

1. Vervang het JSON-script in het rechterdeel venster met het volgende JSON-fragment:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Houd rekening met de volgende punten:

   * Er bevindt zich slechts één activiteit in de pijp lijn en het is van het type **DotNetActivity**.
   * **Assemblyname** is ingesteld op de naam van de dll **MyDotNetActivity.dll**.
   * **Entry Point** wordt ingesteld op **MyDotNetActivityNS. MyDotNetActivity**. Dat is in wezen \<namespace\> .\<classname\> in uw code.
   * **PackageLinkedService** is ingesteld op **StorageLinkedService**, die verwijst naar de Blob-opslag met het zip-bestand van de aangepaste activiteit. Als u verschillende opslag accounts gebruikt voor invoer-en uitvoer bestanden en het zip-bestand van de aangepaste activiteit, moet u een andere opslag-gekoppelde service maken. In dit artikel wordt ervan uitgegaan dat u hetzelfde opslag account gebruikt.
   * **PackageFile** is ingesteld op **customactivitycontainer/MyDotNetActivity.zip**. Het is in de indeling \<containerforthezip\> / \<nameofthezip.zip\> .
   * De aangepaste activiteit krijgt **input dataset** als invoer en **output dataset** als uitvoer.
   * De eigenschap **linkedServiceName** van de aangepaste activiteit verwijst naar **AzureBatchLinkedService**, wat aangeeft Data Factory dat de aangepaste activiteit moet worden uitgevoerd op batch.
   * De **gelijktijdigheids** instelling is belang rijk. Als u de standaard waarde (1) gebruikt, zelfs als u twee of meer reken knooppunten in de batch-pool hebt, worden de segmenten na elkaar verwerkt. Daarom neemt u geen gebruik van de parallelle verwerkings mogelijkheid van batch. Als u **gelijktijdigheid** instelt op een hogere waarde, zegt 2, betekent dit dat twee segmenten (overeenkomt met twee taken in batch) tegelijk kunnen worden verwerkt. In dit geval worden zowel de virtuele machines in de batch-pool gebruikt. Stel de gelijktijdigheids eigenschap op de juiste wijze in.
   * Er wordt standaard slechts één taak (segment) uitgevoerd op een virtuele machine. **Maximum aantal taken per VM** is standaard ingesteld op 1 voor een batch-pool. Als onderdeel van de vereisten hebt u een pool gemaakt waarvoor deze eigenschap is ingesteld op 2. Daarom kunnen twee data factory segmenten tegelijk worden uitgevoerd op een virtuele machine.
     - De eigenschap **isPaused** is standaard ingesteld op ONWAAR. De pijp lijn wordt direct in dit voor beeld uitgevoerd, omdat de segmenten in het verleden beginnen. U kunt deze eigenschap instellen op **True** om de pijp lijn te onderbreken en weer in te stellen op **False** om opnieuw op te starten.
     -   De **begin** -en **eind** tijd zijn vijf uur van elkaar. Segmenten worden elk uur geproduceerd, dus vijf segmenten worden geproduceerd door de pijp lijn.

1. Selecteer in de opdrachtbalk **Implementeren** om de pijplijn te implementeren.

#### <a name="step-5-test-the-pipeline"></a>Stap 5: de pijp lijn testen
In deze stap test u de pijp lijn door bestanden in de invoer mappen neer te zetten. Begin met het testen van de pijp lijn met één bestand voor elke invoercel.

1. Selecteer **diagram** op de Blade **gegevensfactory** in de Azure Portal.

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. Dubbel klik in de **diagram** weergave op de gegevensset **input dataset**.

   ![Input DataSet](./media/data-factory-data-processing-using-batch/image11.png)

1. De Blade **input dataset** wordt weer gegeven met alle vijf segmenten die klaar zijn. Let op de **begin tijd** van het segment en **eind tijd** van het segment voor elk segment.

   ![Begin-en eind tijd van invoer segment](./media/data-factory-data-processing-using-batch/image12.png)

1. Selecteer in de **diagram** weergave **output dataset**.

1. De vijf uitvoer segmenten worden weer gegeven met de status **gereed** als ze zijn geproduceerd.

   ![Begin-en eind tijd van uitvoer segment](./media/data-factory-data-processing-using-batch/image13.png)

1. Gebruik de portal om de taken weer te geven die zijn gekoppeld aan de segmenten en te zien op welke VM elk segment is uitgevoerd. Zie de sectie [Data Factory en batch-integratie](#data-factory-and-batch-integration) voor meer informatie.

1. De uitvoer bestanden worden weer gegeven onder `mycontainer` in `outputfolder` uw Blob-opslag.

   ![Uitvoer bestanden in opslag](./media/data-factory-data-processing-using-batch/image15.png)

   Vijf uitvoer bestanden worden weer gegeven, één voor elk invoer segment. Elk van de uitvoer bestanden heeft inhoud die vergelijkbaar is met de volgende uitvoer:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   In het volgende diagram ziet u hoe de data factory segmenten worden toegewezen aan taken in batch. In dit voor beeld heeft een segment slechts één run.

   ![Diagram van segment toewijzing](./media/data-factory-data-processing-using-batch/image16.png)

1. Probeer nu met meerdere bestanden in een map. Maak de bestanden **file2.txt**, **file3.txt**, **file4.txt** en **file5.txt** met dezelfde inhoud als in file.txt in de map **2015-11-06-01**.

1. Verwijder in de map uitvoermap het uitvoer bestand **2015-11-16-01.txt**.

1. Klik op de Blade **output dataset** met de rechter muisknop op het segment met de **begin tijd** van het segment ingesteld op **11/16/2015 01:00:00 uur**. Selecteer **uitvoeren** om het segment opnieuw op te starten of opnieuw te verwerken. Het segment heeft nu vijf bestanden in plaats van één bestand.

    ![Uitvoeren](./media/data-factory-data-processing-using-batch/image17.png)

1. Nadat het segment is uitgevoerd en de status **gereed** is, controleert u de inhoud van het uitvoer bestand voor dit segment (**2015-11-16-01.txt**). Het uitvoer bestand wordt weer gegeven onder `mycontainer` in `outputfolder` uw Blob-opslag. Er moet een regel voor elk bestand van het segment zijn.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Als u het uitvoer bestand niet hebt verwijderd 2015-11-16-01.txt voordat u met vijf invoer bestanden hebt geprobeerd, ziet u een regel uit de vorige segment uitvoering en vijf regels van de huidige segment uitvoering. Standaard wordt de inhoud toegevoegd aan het uitvoer bestand als dit al bestaat.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integratie van Data Factory en batch
De Data Factory-service maakt een taak in batch met de naam `adf-poolname:job-xxx` .

![Batchtaken](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Er wordt een taak in de taak gemaakt voor elke activiteit uitvoering van een segment. Als 10 segmenten klaar zijn om te worden verwerkt, worden er 10 taken in de taak gemaakt. U kunt meer dan één segment parallel uitvoeren als u meerdere reken knooppunten in de groep hebt. Als het maximum aantal taken per Compute-knoop punt is ingesteld op meer dan één, kan er meer dan één segment op dezelfde Compute worden uitgevoerd.

In dit voor beeld zijn er vijf segmenten, waardoor er vijf taken in batch zijn. Wanneer de **gelijktijdigheid** is ingesteld op **5** in de JSON van de pijp lijn in de Data Factory en het **maximum aantal taken per VM** ingesteld op **2** in de batch-pool met **2** vm's, worden de taken snel uitgevoerd. (Controleer de begin-en eind tijden voor taken.)

Gebruik de portal om de batch-taak en de bijbehorende taken weer te geven die zijn gekoppeld aan de segmenten en te zien op welke VM elk segment is uitgevoerd.

![Batch-taak taken](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Fouten opsporen in de pijplijn
Fout opsporing bestaat uit een aantal basis technieken.

1. Als het invoer segment niet is ingesteld op **gereed**, controleert u of de indeling van de invoer mappen juist is en of file.txt in de invoer mappen bestaat.

   ![Mapstructuur van invoer](./media/data-factory-data-processing-using-batch/image3.png)

1. Gebruik in de methode **Execute** van uw aangepaste activiteit het object **IActivityLogger** om informatie te registreren die u helpt bij het oplossen van problemen. De logboek berichten worden weer gegeven in het \_ bestand gebruiker 0. log.

   Selecteer het segment op de Blade **output dataset** om de Blade **gegevens segment** voor dat segment weer te geven. Onder **uitvoeringen van activiteit** ziet u de uitvoering van één activiteit voor het segment. Als u **uitvoeren** in de opdracht balk selecteert, kunt u een andere uitvoering van de activiteit voor hetzelfde segment starten.

   Wanneer u de uitvoering van de activiteit selecteert, ziet u de Blade **Details uitvoering van activiteit** met een lijst met logboek bestanden. U ziet berichten die zijn geregistreerd in het \_ bestand gebruiker 0. log. Wanneer er een fout optreedt, ziet u dat er drie activiteiten worden uitgevoerd omdat het aantal nieuwe pogingen is ingesteld op 3 in de JSON van de pijp lijn/activiteit. Wanneer u de uitvoering van de activiteit selecteert, ziet u de logboek bestanden die u kunt controleren om de fout op te lossen.

   ![Blades voor output dataset en gegevens segmenten](./media/data-factory-data-processing-using-batch/image18.png)

   Selecteer in de lijst met logboek bestanden **User-0. log**. In het rechterdeel venster worden de resultaten van het gebruik van de methode **IActivityLogger. write** weer gegeven.

   ![Blade Details uitvoering van activiteit](./media/data-factory-data-processing-using-batch/image19.png)

   Controleer System-0. log op systeem fout berichten en uitzonde ringen.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Neem het **PDB** -bestand op in het zip-bestand, zodat de fout Details informatie, zoals aanroep stack, bevatten wanneer er een fout optreedt.

1. Alle bestanden in het zip-bestand voor de aangepaste activiteit moeten zich op het hoogste niveau zonder submappen bevinden.

   ![Lijst met aangepaste post bestanden](./media/data-factory-data-processing-using-batch/image20.png)

1. Zorg ervoor dat **assemblyname** (MyDotNetActivity.dll), **Entry Point** (MyDotNetActivityNS. MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) en **packageLinkedService** (moet verwijzen naar de Blob-opslag die het zip-bestand bevat) zijn ingesteld op de juiste waarden.

1. Als u een fout hebt opgelost en het segment opnieuw wilt verwerken, klikt u met de rechter muisknop op het segment op de Blade **output dataset** en selecteert u **uitvoeren**.

   ![De optie Blade uitvoeren output DataSet](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Er bevindt zich in de Blob-opslag een container met de naam `adfjobs` . Deze container wordt niet automatisch verwijderd, maar u kunt deze veilig verwijderen nadat u klaar bent met het testen van de oplossing. Op dezelfde manier maakt de data factory oplossing een batch-taak met de naam `adf-\<pool ID/name\>:job-0000000001` . U kunt deze taak verwijderen nadat u de oplossing hebt getest.
   >
   >
1. De aangepaste activiteit maakt geen gebruik van het **app.config** -bestand uit uw pakket. Als uw code verbindings reeksen uit het configuratie bestand leest, werkt deze daarom niet tijdens runtime. De best practice wanneer u batch gebruikt, is het opslaan van geheimen in Azure Key Vault. Gebruik vervolgens een service-principal op basis van een certificaat om de sleutel kluis te beveiligen en het certificaat naar de batch-pool te distribueren. De aangepaste .NET-activiteit kan tijdens runtime toegang hebben tot geheimen van de sleutel kluis. Deze algemene oplossing kan worden geschaald naar elk type geheim, niet alleen op een connection string.

    Er is een eenvoudige tijdelijke oplossing, maar dit is geen best practice. U kunt een SQL Database gekoppelde service maken met connection string-instellingen. Vervolgens kunt u een gegevensset maken die gebruikmaakt van de gekoppelde service en de gegevensset als een dummy invoer gegevensset koppelen aan de aangepaste .NET-activiteit. U kunt vervolgens de connection string van de gekoppelde service openen in de code van de aangepaste activiteit. Het werkt prima tijdens runtime.  

#### <a name="extend-the-sample"></a>Het voor beeld uitbreiden
U kunt dit voor beeld uitbreiden voor meer informatie over Data Factory-en batch-functies. Als u bijvoorbeeld segmenten in een ander tijds bereik wilt verwerken, voert u de volgende stappen uit:

1. Voeg de volgende submappen toe in `inputfolder` : 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 en 2015-11-16-09. Plaats invoer bestanden in deze mappen. Wijzig de eind tijd voor de pijp lijn van `2015-11-16T05:00:00Z` in `2015-11-16T10:00:00Z` . Dubbel klik in de **diagram** weergave op **input dataset** en bevestig dat de invoer segmenten gereed zijn. Dubbel klik op **output dataset** om de status van de uitvoer segmenten te bekijken. Als ze de status **gereed** hebben, controleert u de uitvoermap voor de uitvoer bestanden.

1. Verg root of verklein de **gelijktijdigheids** instelling om te begrijpen hoe dit van invloed is op de prestaties van uw oplossing, met name de verwerking die op batch wordt uitgevoerd. Zie "stap 4: de pijp lijn maken en uitvoeren met een aangepaste activiteit" voor meer informatie over de **gelijktijdigheids** instelling.

1. Maak een pool met een hoger/lager **maximum aantal taken per VM**. Als u de nieuwe groep wilt gebruiken die u hebt gemaakt, werkt u de gekoppelde batch-service bij in de data factory-oplossing. Zie "stap 4: de pijp lijn maken en uitvoeren met een aangepaste activiteit" voor meer informatie over de **maximum taken per VM** -instelling.

1. Maak een batch-pool met de functie voor **automatisch schalen** . Het automatisch schalen van reken knooppunten in een batch-pool is de dynamische aanpassing van het verwerkings vermogen dat door uw toepassing wordt gebruikt.

    Met de voorbeeld formule wordt het volgende gedrag gerealiseerd. Wanneer de pool voor het eerst wordt gemaakt, begint deze met één virtuele machine. Met de $PendingTasks meet waarde wordt het aantal taken in de status van actief en actief (in wachtrij) gedefinieerd. Met de formule vindt u het gemiddelde aantal taken in de afgelopen 180 seconden dat in behandeling is en stelt u TargetDedicated dienovereenkomstig in. Zo zorgt u ervoor dat TargetDedicated nooit meer dan 25 Vm's verloopt. Wanneer er nieuwe taken worden verzonden, wordt de groep automatisch verg root. Zodra de taken zijn voltooid, worden virtuele machines één voor één vrijgegeven en worden de Vm's automatisch geschaald. U kunt startingNumberOfVMs en maxNumberofVMs aan uw behoeften aanpassen.

    Formule voor automatisch schalen:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Zie [reken knooppunten automatisch schalen in een batch-pool](../../batch/batch-automatic-scaling.md)voor meer informatie.

   Als de groep de standaard [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)gebruikt, kan de batch-service 15 tot 30 minuten duren voordat de virtuele machine wordt voor bereid voordat de aangepaste activiteit wordt uitgevoerd. Als de groep een andere autoScaleEvaluationInterval gebruikt, kan de batch-service autoScaleEvaluationInterval plus 10 minuten duren.

1. In de voorbeeld oplossing roept de **Execute** -methode de methode **Calculate** aan waarmee een invoer gegevens segment wordt verwerkt om een segment met uitvoer gegevens te produceren. U kunt uw eigen methode schrijven om invoer gegevens te verwerken en de aanroep methode **berekenen** in de methode **Execute** te vervangen door een aanroep van uw methode.

### <a name="next-steps-consume-the-data"></a>Volgende stappen: de gegevens worden gebruikt
Nadat u gegevens hebt verwerkt, kunt u deze gebruiken met online hulpprogram ma's zoals Power BI. Hier vindt u koppelingen naar informatie over Power BI en hoe u deze kunt gebruiken in Azure:

* [Een gegevensset in Power BI verkennen](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Aan de slag met Power BI Desktop](/power-bi/fundamentals/desktop-getting-started)
* [Gegevens vernieuwen in Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure en Power BI: basis overzicht](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referenties
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Inleiding tot de Data Factory-Service](data-factory-introduction.md)
  * [Aan de slag met Data Factory](data-factory-build-your-first-pipeline.md)
  * [Aangepaste activiteiten gebruiken in een Data Factory pijp lijn](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Basis principes van batch](../../azure-sql/database/sql-database-paas-overview.md)
  * [Overzicht van batch-functies](../../batch/batch-service-workflow-features.md))
  * [Een batch-account maken en beheren in de Azure Portal](../../batch/batch-account-create-portal.md)
  * [Aan de slag met de batch-client bibliotheek voor .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: /archive/blogs/windowshpc/azure-batch-explorer-sample-walkthrough