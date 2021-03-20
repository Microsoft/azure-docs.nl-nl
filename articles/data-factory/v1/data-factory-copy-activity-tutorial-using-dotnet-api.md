---
title: 'Zelfstudie: een pijplijn maken met de kopieeractiviteit in .NET API '
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn met Copy Activity. Hiervoor gebruikt u .NET API.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8c2baf4da4e7893b650e1f885fb66be835c4b736
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377071"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Zelfstudie: een pijplijn maken met de kopieeractiviteit in .NET API
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md). 

In dit artikel leert u hoe u [.NET API](https://portal.azure.com) kunt gebruiken om een data factory te maken met een pijplijn waarmee gegevens worden gekopieerd van een Azure blobopslag naar Azure SQL Database. Als u niet bekend bent met Azure Data Factory, lees dan het artikel [Inleiding tot Azure Data Factory](data-factory-introduction.md) voordat u deze zelfstudie volgt.   

In deze zelfstudie maakt u een pijplijn met één activiteit erin: kopieeractiviteit. De kopieeractiviteit in Data Factory kopieert gegevens uit een ondersteund gegevensarchief naar een ondersteund sinkgegevensarchief. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten.

Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Meerdere activiteiten in een pijplijn](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie. 

> [!NOTE] 
> Zie [Naslaginformatie over de .NET API voor Data Factory](/dotnet/api/overview/azure/data-factory) voor volledige documentatie over .NET API voor Data Factory.
> 
> In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Meer informatie over het transformeren van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: een pijplijn bouwen om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Neem [Overzicht van de zelfstudie en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) door voor een overzicht van de zelfstudie en voer de **vereiste** stappen uit.
* Visual Studio 2012 of 2013 of 2015
* Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/)
* Azure PowerShell. Volg de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps) om Azure PowerShell te installeren op uw computer. Azure PowerShell wordt gebruikt om een Azure Active Directory-toepassing te maken.

### <a name="create-an-application-in-azure-active-directory"></a>Een toepassing maken in Azure Active Directory
Maak een Azure Active Directory-toepassing, maak een service-principal voor de toepassing en wijs deze toe aan de rol **Inzender Data Factory**.

1. Start **PowerShell**.
2. Voer de volgende opdracht uit en geef de gebruikersnaam en het wachtwoord op waarmee u zich aanmeldt bij Azure Portal.

    ```powershell
    Connect-AzAccount
    ```
3. Voer de volgende opdracht uit om alle abonnementen voor dit account weer te geven.

    ```powershell
    Get-AzSubscription
    ```
4. Voer de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken. Vervang **&lt;NameOfAzureSubscription**&gt; door de naam van uw Azure-abonnement.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Noteer de **SubscriptionId** en de **TenantId** uit de uitvoer van deze opdracht.

5. Maak een Azure-resourcegroep met de naam **ADFTutorialResourceGroup** door de volgende opdracht uit te voeren in PowerShell.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Als de resourcegroep al bestaat, geeft u aan of u deze wilt bijwerken (Y) of ongewijzigd wilt laten (N).

    Als u een andere resourcegroep gebruikt, moet u voor deze zelfstudie de naam van uw resourcegroep gebruiken in plaats van ADFTutorialResourceGroup.
6. Maak een Azure Active Directory-toepassing.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Als u de volgende fout ziet, geeft u een andere URL op en voert u de opdracht opnieuw uit.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. Maak de AD-service-principal.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Voeg de service-principal toe aan de rol **Inzender Data Factory**.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Haal de toepassings-id op.

    ```powershell
    $azureAdApplication
    ```
    Noteer de toepassings-id (applicationID in de uitvoer).

U moet na deze stappen beschikken over de volgende vier waarden:

* Tenant-id
* Abonnements-id
* Toepassings-id
* Wachtwoord (opgegeven in de eerste opdracht)

## <a name="walkthrough"></a>Walkthrough
1. Maak met behulp van Visual Studio 2012/2013/2015 een C# .NET-consoletoepassing.
   1. Open **Visual Studio** 2012/2013/2015.
   2. Klik op **File**, houd de muisaanwijzer op **New** en klik op **Project**.
   3. Vouw **Templates** uit en selecteer **Visual C#** . Tijdens deze walkthrough gebruikt u C#, maar u kunt een willekeurige .NET-taal gebruiken.
   4. Selecteer **Console Application** uit de lijst met projecttypen aan de rechterkant.
   5. Voer **DataFactoryAPITestApp** in als de naam.
   6. Selecteer **C:\ADFGetStarted** als de locatie.
   7. Klik op **OK** om het project aan te maken.
2. Klik op **Tools**, wijs **NuGet Package Manager** aan en klik op **Package Manager Console**.
3. Voer de volgende stappen uit in de **Package Manager Console**:
   1. Voer de volgende opdracht uit om het Data Factory-pakket te installeren: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Voer de volgende opdracht uit om het Azure Active Directory-pakket te installeren (u gebruikt de Active Directory API in de code): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Voeg de volgende sectie **appSetttings** toe aan het bestand **App.config**. Deze instellingen worden gebruikt door de Help-methode: **GetAuthorizationHeader**.

    Vervang de waarden voor **&lt;Application ID&gt;** , **&lt;Password&gt;** , **&lt;Subscription ID&gt;** en **&lt;Tenant ID&gt;** door uw eigen waarden.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Voeg de volgende **using**-instructies toe aan het bronbestand (Program.cs) in het project.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. Voeg de volgende code toe aan de methode **Main** om een instantie van de klasse **DataPipelineManagementClient** te maken. U gebruikt dit object om een gegevensfactory, een gekoppelde service, gegevenssets voor invoer en uitvoer, en een pijplijn te maken. U gebruikt dit object ook om segmenten van een gegevensset te bewaken tijdens runtime.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Vervang de waarde van **resourceGroupName** door de naam van uw Azure-resourcegroep.
   >
   > Werk de naam van de data factory (dataFactoryName) zodanig bij dat deze uniek is. De naam van de gegevensfactory moet wereldwijd uniek zijn. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

7. Voeg de volgende code die een **gegevensfactory** maakt toe aan de methode **Main**.

    ```csharp
    // create a data factory
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
    ```

    Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens van een bron- naar een doelgegevensopslagplaats te kopiëren en een HDInsight Hive-activiteit om een Hive-script uit te voeren voor het transformeren van invoergegevens naar productuitvoergegevens. U begint in deze stap met het maken van de gegevensfactory.
8. Voeg de volgende code die een **gekoppelde Azure Storage-service** maakt toe aan de methode **Main**.

   > [!IMPORTANT]
   > Vervang **storageaccountname** en **accountkey** door de naam en sleutel van uw Azure Storage-account.

    ```csharp
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
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

    U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze zelfstudie gebruikt u niet een willekeurige compute-service, zoals Azure HDInsight of Azure Data Lake Analytics. U gebruikt twee gegevensarchieven van het type Azure Storage (bron) en Azure SQL Database (doel). 

    Daarom maakt u twee gekoppelde services met de naam AzureStorageLinkedService en AzureSqlLinkedService van de typen AzureStorage en AzureSqlDatabase.  

    De AzureStorageLinkedService koppelt uw Azure-opslagaccount aan de gegevensfactory. Dit opslagaccount is het account waarin u een container hebt gemaakt en gegevens hebt geüpload als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
9. Voeg de volgende code die een **gekoppelde Azure SLQ-service** maakt toe aan de methode **Main**.

   > [!IMPORTANT]
   > Vervang **servername**, **databasename**, **username** en **password** door de namen van uw server, database, gebruiker en wachtwoord.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    Met AzureSqlLinkedService wordt Azure SQL Database aan de data factory gekoppeld. De gegevens die worden gekopieerd uit de blobopslag worden opgeslagen in deze database. Als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hebt u de emp-tabel in deze database gemaakt.
10. Voeg de volgende code die **gegevenssets voor invoer en uitvoer** maakt toe aan de methode **Main**.

    ```csharp
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
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```
    
    In de vorige stap hebt u gekoppelde services gemaakt om uw Azure-opslagaccount en Azure SQL Database aan de data factory te koppelen. In deze stap definieert u twee gegevenssets, InputDataset en OutputDataset genaamd, die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven waarnaar wordt verwezen door respectievelijk de AzureStorageLinkedService en de AzureSqlLinkedService.

    De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. En de blobgegevensset voor invoer (InputDataset) geeft de container en de map met de invoergegevens op.  

    Op dezelfde manier geeft de gekoppelde Azure SQL Database-service de verbindingsreeks op die de Data Factory-service in runtime gebruikt om verbinding te maken met uw database. En de uitvoergegevensset van de SQL-tabel (OututDataset) geeft de tabel in de database op waarnaar de gegevens uit de blobopslag worden gekopieerd.

    In deze stap maakt u een gegevensset met de naam InputDataset die verwijst naar een blobbestand (emp.txt) in de hoofdmap van een blobcontainer (adftutorial) in Azure Storage. Deze container wordt vertegenwoordigd door de gekoppelde AzureStorageLinkedService-service. Als u geen waarde voor de fileName hebt opgeven (of hebt overgeslagen), worden gegevens uit alle blobs in de invoermap naar het doel gekopieerd. In deze zelfstudie geeft u een waarde op voor de fileName.    

    In deze stap maakt u een uitvoergegevensset met de naam **OutputDataset**. Deze gegevensset wijst naar een SQL-tabel in de database die wordt vertegenwoordigd door **AzureSqlLinkedService**.
11. Voeg de volgende code die **een pijplijn maakt en activeert** toe aan de methode **Main**. In deze stap maakt u een pijplijn met een **kopieeractiviteit** die gebruikmaakt van **InputDataset** als invoer en **OutputDataset** als uitvoer.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
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
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

    Houd rekening met de volgende punten:
   
    - In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **Copy**. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten. In Data Factory-oplossingen kunt u ook [activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) gebruiken.
    - De invoer voor de activiteit is ingesteld op **InputDataset** en de uitvoer voor de activiteit is ingesteld op **OutputDataset**. 
    - In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een volledige lijst van gegevensarchieven die worden ondersteund door kopieeractiviteiten als bronnen en sinks. Klik op de koppeling in de tabel voor informatie over het gebruik van een specifiek ondersteund gegevensarchief als een bron/sink.  
   
    Momenteel is de uitvoergegevensset dat wat de planning aanstuurt. In deze zelfstudie is de uitvoergegevensset geconfigureerd voor het produceren van een segment eenmaal per uur. De pijplijn heeft een begintijd en eindtijd die één dag uit elkaar liggen, ofwel 24 uur. Daarom worden 24 segmenten van de uitvoergegevensset door de pijplijn geproduceerd.
12. Voeg de volgende code toe aan de methode **Main** om de status van een gegevenssegment van de uitvoergegevensset te achterhalen. In dit voorbeeld wordt alleen een segment verwacht.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Voeg de volgende code toe om details van de uitvoering van een gegevenssegment naar de methode **Main** te achterhalen.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. Voeg de volgende Help-methode toe die door de methode **Main** wordt gebruikt voor de klasse **Program**.

    > [!NOTE] 
    > Wanneer u de volgende code kopieert en plakt, zorg er dan voor dat de gekopieerde code zich op hetzelfde niveau bevindt als de Main-methode.

    ```csharp
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
    ```

15. Vouw in Solution Explorer het project (DataFactoryAPITestApp) uit, klik met de rechtermuisknop op **References** en klik vervolgens op **Add Reference**. Schakel het selectievakje voor **System.Configuration**-assembly in. Klik vervolgens op **OK**.
16. Bouw de consoletoepassing. Klik op **Build** in het menu en klik op **Build Solution**.
17. Controleer of er ten minste één bestand in de **adftutorial**-container in uw Azure Blob Storage staat. Als dit niet het geval is, maakt u in Kladblok het **Emp.txt**-bestand met de volgende inhoud en uploadt u het bestand naar de adftutorial-container.

    ```
    John, Doe
    Jane, Doe
    ```
18. Voer het voorbeeld uit door op **Debug** -> **Start Debugging** te klikken in het menu. Als u **Getting run details of a data slice** ziet, wacht u een paar minuten en drukt u op **ENTER**.
19. Gebruik Azure Portal om te controleren of de gegevensfactory **APITutorialFactory** wordt gemaakt met de volgende artefacten:
    * Gekoppelde service: **LinkedService_AzureStorage**
    * Gegevensset: **InputDataset** en **OutputDataset**.
    * Pijplijn: **PipelineBlobSample**
20. Controleer of de twee werknemersrecords zijn gemaakt in de tabel **emp** in de opgegeven database.

## <a name="next-steps"></a>Volgende stappen
Zie [Naslaginformatie over de .NET API voor Data Factory](/dotnet/api/overview/azure/data-factory) voor volledige documentatie over .NET API voor Data Factory.

In deze zelfstudie hebt u voor een kopieerbewerking Azure-blobopslag gebruikt als brongegevensarchief en Azure SQL Database als doelgegevensarchief. De volgende tabel bevat een lijst met gegevensarchieven die worden ondersteund als bron en doel voor de kopieeractiviteit: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Klik op de koppeling voor de gegevensopslag in de tabel voor meer informatie over het kopiëren van gegevens naar/uit een gegevensarchief.

