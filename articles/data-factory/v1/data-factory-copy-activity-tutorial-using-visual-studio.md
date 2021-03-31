---
title: 'Zelfstudie: Een pijplijn maken met de kopieeractiviteit in Visual Studio '
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn met een kopieeractiviteit. Hiervoor gebruikt u Visual Studio.
author: linda33wj
ms.service: data-factory
ms.custom: vs-azure
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f6ff31c3c0d37078f8dfbd934593adb4b6a6ef4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377105"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Zelfstudie: Een pijplijn maken met de kopieeractiviteit in Visual Studio
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md). 

In dit artikel leert u hoe u Microsoft Visual Studio kunt gebruiken om een data factory te maken met een pijplijn waarmee gegevens worden gekopieerd van een Azure blobopslag naar Azure SQL Database. Als u niet bekend bent met Azure Data Factory, lees dan het artikel [Inleiding tot Azure Data Factory](data-factory-introduction.md) voordat u deze zelfstudie volgt.   

In deze zelfstudie maakt u een pijplijn met één activiteit erin: kopieeractiviteit. De kopieeractiviteit in Data Factory kopieert gegevens uit een ondersteund gegevensarchief naar een ondersteund sinkgegevensarchief. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten.

Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Meerdere activiteiten in een pijplijn](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie.

> [!NOTE] 
> In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Meer informatie over het transformeren van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: een pijplijn bouwen om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Lees het artikel [Overzicht van de zelfstudie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) en voer de **vereiste** stappen uit.       
2. Als u Data Factory-exemplaren wilt maken, moet u lid zijn van de rol [Inzender Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) op abonnements-/resourcegroepsniveau.
3. De volgende zaken moeten op uw computer zijn geïnstalleerd: 
   * Visual Studio 2013 of Visual Studio 2015
   * Download de Azure SDK voor Visual Studio 2013 of Visual Studio 2015. Ga naar de [Azure-downloadpagina](https://azure.microsoft.com/downloads/) en klik in het gedeelte **.NET** op **VS 2013** of **VS 2015**.
   * Download de nieuwste Azure Data Factory-invoegtoepassing voor Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) of [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). U kunt de invoegtoepassing ook bijwerken door de volgende stappen uit te voeren: Klik in het menu op **Extra** -> **Extensies en updates** -> **Online** -> **Visual Studio-galerie** -> **Microsoft Azure Data Factory-hulpprogramma's voor Visual Studio** -> **Bijwerken**.

## <a name="steps"></a>Stappen

Hier volgen de stappen die u uitvoert als onderdeel van deze zelfstudie:

1. **Gekoppelde services** maken in de gegevensfactory. In deze stap maakt u twee gekoppelde services van de typen: Azure Storage en Azure SQL Database. 

    De AzureStorageLinkedService koppelt uw Azure-opslagaccount aan de gegevensfactory. U hebt een container gemaakt en gegevens naar dit opslagaccount geüpload als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

    Met AzureSqlLinkedService wordt Azure SQL Database aan de data factory gekoppeld. De gegevens die worden gekopieerd uit de blobopslag worden opgeslagen in deze database. Als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hebt u een SQL-tabel in deze database gemaakt.     

2. Maak **invoer- en uitvoergegevenssets** in de gegevensfactory.  

    De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. En de blobgegevensset voor invoer geeft de container en de map met de invoergegevens op.  

    Op dezelfde manier geeft de gekoppelde Azure SQL Database-service de verbindingsreeks op die de Data Factory-service in runtime gebruikt om verbinding te maken met Azure SQL Database. En de uitvoergegevensset van de SQL-tabel geeft de tabel in de database op waarnaar de gegevens uit de blobopslag worden gekopieerd.

3. Maak een **pijplijn** in de gegevensfactory. In deze stap maakt u een pijplijn met een kopieeractiviteit.   

    Met de kopieeractiviteit worden gegevens uit een blob in de Azure-blobopslag naar een tabel in de Azure SQL Database gekopieerd. U kunt een kopieeractiviteit gebruiken in een pijplijn om gegevens uit ondersteunde bronnen te kopiëren naar een ondersteunde bestemming. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met ondersteunde gegevensarchieven.

4. Maak een Azure-**gegevensfactory** tijdens het implementeren van de Data Factory-entiteiten (gekoppelde services, gegevenssets/tabellen en pijplijnen). 

## <a name="create-visual-studio-project"></a>Een Visual Studio-project maken

1. Open **Visual Studio 2015**. Klik op **File**, houd de muisaanwijzer op **New** en klik op **Project**. Het dialoogvenster **New Project** wordt weergegeven.  

2. Selecteer in het dialoogvenster **New Project** de sjabloon **DataFactory** en klik op **Empty Data Factory Project**.  

    ![Het dialoogvenster New Project](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Geef de naam van het project, de locatie van de oplossing en de naam van de oplossing op en klik vervolgens op **OK**.

    ![Solution Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Gekoppelde services maken

U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze zelfstudie gebruikt u niet een willekeurige compute-service, zoals Azure HDInsight of Azure Data Lake Analytics. U gebruikt twee gegevensarchieven van het type Azure Storage (bron) en Azure SQL Database (doel). 

Daarvoor maakt u twee gekoppelde services van de typen: AzureStorage en AzureSqlDatabase.  

De gekoppelde Azure Storage-service koppelt uw Azure-opslagaccount aan de gegevensfactory. Dit opslagaccount is het account waarin u een container hebt gemaakt en gegevens hebt geüpload als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Met de gekoppelde Azure SQL-service wordt Azure SQL Database aan de data factory gekoppeld. De gegevens die worden gekopieerd uit de blobopslag worden opgeslagen in deze database. Als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hebt u de emp-tabel in deze database gemaakt.

Met gekoppelde services worden gegevensarchieven of compute-services gekoppeld aan een Azure Data Factory. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor alle bronnen en sinks die worden ondersteund door de kopieerbewerking. Zie [Gekoppelde services berekenen](data-factory-compute-linked-services.md) voor de lijst met compute-services die worden ondersteund door Data Factory. In deze zelfstudie gebruikt u geen compute-service. 

### <a name="create-the-azure-storage-linked-service"></a>De gekoppelde Azure Storage-service maken

1. Klik in **Solution Explorer** met de rechtermuisknop op **Linked Services**. Houd de muisaanwijzer op **Add** en klik op **New Item**.      

2. Selecteer in het dialoogvenster **Add New Item** de optie **Azure Storage Linked Service** in de lijst en klik op **Add**. 

    ![Nieuwe gekoppelde service](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)

3. Vervang `<accountname>` en `<accountkey>`* door de naam van uw Azure-opslagaccount en de bijbehorende sleutel. 

    ![Een gekoppelde Azure Storage-service](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Sla het bestand **AzureStorageLinkedService1.json** op.

    Zie het artikel [Azure Blob Storage-connector](data-factory-azure-blob-connector.md#linked-service-properties) voor meer informatie over de JSON-eigenschappen in de definitie van de gekoppelde service.

### <a name="create-the-azure-sql-linked-service"></a>De gekoppelde Azure SQL-service maken

1. Klik met de rechtermuisknop opnieuw op het knooppunt **Linked Services** in **Solution Explorer**. Houd de muisaanwijzer op **Add** en klik op **New Item**. 
2. Selecteer deze keer **Azure SQL Linked Service** en klik op **Add**. 
3. In het bestand **AzureSqlLinkedService1.json** vervangt u `<servername>`, `<databasename>`, `<username@servername>` en `<password>` door de namen van uw server, database en gebruikersaccount en voert u uw wachtwoord in.    
4. Sla het bestand **AzureSqlLinkedService1.json** op. 

    Zie [Azure SQL Database-connector](data-factory-azure-sql-connector.md#linked-service-properties) voor meer informatie over deze JSON-eigenschappen.

## <a name="create-datasets"></a>Gegevenssets maken

In de vorige stap hebt u gekoppelde services gemaakt om uw Azure-opslagaccount en Azure SQL Database aan de data factory te koppelen. In deze stap definieert u twee gegevenssets, InputDataset en OutputDataset genaamd, die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven waarnaar wordt verwezen door respectievelijk de AzureStorageLinkedService1 en de AzureSqlLinkedService1.

De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. En de blobgegevensset voor invoer (InputDataset) geeft de container en de map met de invoergegevens op.  

Op dezelfde manier geeft de gekoppelde Azure SQL Database-service de verbindingsreeks op die de Data Factory-service in runtime gebruikt om verbinding te maken met Azure SQL Database. En de uitvoergegevensset van de SQL-tabel (OututDataset) geeft de tabel in de database op waarnaar de gegevens uit de blobopslag worden gekopieerd. 

### <a name="create-input-dataset"></a>Invoergegevensset maken

In deze stap maakt u een gegevensset met de naam InputDataset die verwijst naar een blobbestand (emp.txt) in de hoofdmap van een blobcontainer (adftutorial) in Azure Storage. Deze container wordt vertegenwoordigd door de gekoppelde AzureStorageLinkedService1-service. Als u geen waarde voor de fileName hebt opgeven (of hebt overgeslagen), worden gegevens uit alle blobs in de invoermap naar het doel gekopieerd. In deze zelfstudie geeft u een waarde op voor de fileName. 

Hier kunt u de term 'tabellen' gebruiken in plaats van 'gegevenssets'. Een tabel is een rechthoekige gegevensset en is het enige type gegevensset dat nu wordt ondersteund. 

1. Klik in **Solution Explorer** met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
2. In het dialoogvenster **Add New Item** selecteert u **Azure Blob** en klikt u op **Add**.   
3. Vervang de JSON-tekst door de volgende tekst en sla het bestand **AzureBlobLocation1.json** op. 

   ```json   
   {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
   }
   ``` 
    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

    | Eigenschap | Beschrijving |
    |:--- |:--- |
    | type | De eigenschap type wordt ingesteld op **AzureBlob**, omdat de gegevens zich in een Azure-blobopslag bevinden. |
    | linkedServiceName | Verwijst naar de **AzureStorageLinkedService** die u eerder hebt gemaakt. |
    | folderPath | Geeft de blob **container** op, plus de **map** die de invoerblobs bevat. In deze zelfstudie is adftutorial de blobcontainer en is folder de hoofdmap. | 
    | fileName | Deze eigenschap is optioneel. Als u deze eigenschap niet opgeeft, worden alle bestanden uit folderPath gekozen. In deze zelfstudie wordt **emp.txt** opgegeven voor de fileName. Alleen dit bestand wordt dus opgehaald voor verwerking. |
    | format -> type |Het invoerbestand is in de tekstindeling. Daarom gebruiken we **TextFormat**. |
    | columnDelimiter | De kolommen in het invoerbestand worden gescheiden met een **komma (`,`)** . |
    | frequency/interval | Als frequency wordt ingesteld op **Uur** en het interval wordt ingesteld op **1**, betekent dit dat de invoersegmenten één keer per **uur** beschikbaar worden gemaakt. Met andere woorden, de Data Factory-service zoekt elk uur naar invoergegevens in de hoofdmap van de opgegeven blobcontainer (**adftutorial**). Er wordt gezocht naar gegevens binnen de begin- en eindtijd van de pijplijn, niet voor of na deze tijden.  |
    | external | Deze eigenschap wordt ingesteld op **true** als de gegevens niet worden gegenereerd door deze pijplijn. De invoergegevens in deze zelfstudie bevinden zich in het bestand emp.txt, dat niet wordt gegenereerd door deze pijplijn. Daarom stellen we deze eigenschap in op true. |

    Zie het [artikel over Azure Blob-connectoren](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie over deze JSON-eigenschappen.   

### <a name="create-output-dataset"></a>Uitvoergegevensset maken

In deze stap maakt u een uitvoergegevensset met de naam **OutputDataset**. Deze gegevensset wijst naar een SQL-tabel in Azure SQL Database die wordt vertegenwoordigd door **AzureSqlLinkedService1**. 

1. Klik in **Solution Explorer** opnieuw met de rechtermuisknop op **Tables**. Houd de muisaanwijzer op **Add** en klik op **New Item**.
2. In het dialoogvenster **Add New Item** selecteert u **Azure SQL** en klikt u op **Add**. 
3. Vervang de JSON-tekst door de volgende JSON en sla het bestand **AzureSqlTableLocation1.json** op.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
        }
    }
    ```

    De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

    | Eigenschap | Beschrijving |
    |:--- |:--- |
    | type | De eigenschap type wordt ingesteld op **AzureSqlTable** omdat gegevens naar een tabel in Azure SQL Database worden gekopieerd. |
    | linkedServiceName | Deze eigenschap verwijst naar de **AzureSqlLinkedService** die u eerder hebt gemaakt. |
    | tableName | Geeft de **tabel** aan waarnaar de gegevens worden gekopieerd. | 
    | frequency/interval | De frequentie is ingesteld op **Hour** en het interval is **1**, wat betekent dat de uitvoersegmenten worden geproduceerd **per uur** tussen de begin- en eindtijd van de pijplijn, niet voor of na deze tijden.  |

    De tabel emp in de database bevat drie kolommen: **ID**, **FirstName** en **LastName**. ID is een identiteitskolom, zodat u alleen **FirstName** en **LastName** hoeft op te geven.

    Zie het [artikel over Azure SQL-connectoren](data-factory-azure-sql-connector.md#dataset-properties) voor meer informatie over deze JSON-eigenschappen.

## <a name="create-pipeline"></a>Pijplijn maken

In deze stap maakt u een pijplijn met een **kopieeractiviteit** die gebruikmaakt van **InputDataset** als invoer en **OutputDataset** als uitvoer.

Momenteel is de uitvoergegevensset dat wat de planning aanstuurt. In deze zelfstudie is de uitvoergegevensset geconfigureerd voor het produceren van een segment eenmaal per uur. De pijplijn heeft een begintijd en eindtijd die één dag uit elkaar liggen, ofwel 24 uur. Daarom worden 24 segmenten van de uitvoergegevensset door de pijplijn geproduceerd. 

1. Klik in **Solution Explorer** met de rechtermuisknop op **Pipelines**. Houd de muisaanwijzer op **Add** en klik op **New Item**.  
2. Selecteer **Copy Data Pipeline** in het dialoogvenster **Add New Item** en klik op **Add**. 
3. Vervang de JSON door de volgende JSON en sla het bestand **CopyActivity1.json** op.

   ```json
  {
   "name": "ADFTutorialPipeline",
   "properties": {
     "description": "Copy data from a blob to Azure SQL table",
     "activities": [
       {
         "name": "CopyFromBlobToSQL",
         "type": "Copy",
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
         "typeProperties": {
           "source": {
             "type": "BlobSource"
           },
           "sink": {
             "type": "SqlSink",
             "writeBatchSize": 10000,
             "writeBatchTimeout": "60:00:00"
           }
         },
         "Policy": {
           "concurrency": 1,
           "executionPriorityOrder": "NewestFirst",
           "style": "StartOfInterval",
           "retry": 0,
           "timeout": "01:00:00"
         }
       }
     ],
     "start": "2017-05-11T00:00:00Z",
     "end": "2017-05-12T00:00:00Z",
     "isPaused": false
   }
  }
    ```   
   - In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **Copy**. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten. In Data Factory-oplossingen kunt u ook [activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) gebruiken.
   - De invoer voor de activiteit is ingesteld op **InputDataset** en de uitvoer voor de activiteit is ingesteld op **OutputDataset**. 
   - In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een volledige lijst van gegevensarchieven die worden ondersteund door kopieeractiviteiten als bronnen en sinks. Klik op de koppeling in de tabel voor informatie over het gebruik van een specifiek ondersteund gegevensarchief als een bron/sink.  
     
     Vervang de waarde van de eigenschap **start** door de huidige dag en de waarde **end** door de volgende dag. U hoeft alleen de datum in te vullen en kunt de tijd overslaan. Dit wordt dan bijvoorbeeld 2016-02-03, wat gelijk staat aan 2016-02-03T00:00:00Z
     
     Zowel de begin- als einddatum en -tijd moeten de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601) hebben. Bijvoorbeeld: 2016-10-14T16:32:41Z. De **eindtijd** is optioneel, maar we gebruiken hem in deze zelfstudie. 
     
     Als u geen waarde opgeeft voor de eigenschap **end**, wordt automatisch **start + 48 uur** gebruikt. Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**.
     
     In het voorgaande voorbeeld zijn er 24 gegevenssegmenten omdat er elk uur één gegevenssegment wordt gemaakt.

     Zie het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor beschrijvingen van JSON-eigenschappen in de definitie van een pijplijn. Zie [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voor beschrijvingen van JSON-eigenschappen in de definitie van een kopieeractiviteit. Zie het [artikel over Azure Blob-connectoren](data-factory-azure-blob-connector.md) voor beschrijvingen van JSON-eigenschappen die worden ondersteund door BlobSource. Zie het [artikel over Azure SQL Database-connectoren](data-factory-azure-sql-connector.md) voor beschrijvingen van JSON-eigenschappen die worden ondersteund door SqlSink.

## <a name="publishdeploy-data-factory-entities"></a>Data Factory-entiteiten publiceren/implementeren
In deze stap publiceert u Data Factory-entiteiten (gekoppelde services, gegevenssets en pijplijn) die u eerder hebt gemaakt. U specificeert ook de naam van de nieuwe gegevensfactory die moet worden gemaakt voor deze entiteiten.  

1. Klik met de rechtermuisknop op het project in Solution Explorer. Klik vervolgens op **Publish**. 
2. Als u het dialoogvenster **Sign in to your Microsoft account** ziet, voert u uw referenties in voor het account met het Azure-abonnement en klikt u op **Sign in**.
3. Het volgende dialoogvenster wordt weergegeven:
   
   ![Het dialoogvenster Publish](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Op de pagina Configure data factory voert u de volgende stappen uit: 
   
   1. Selecteer **Create New Data Factory**.
   2. Voer **VSTutorialFactory** in als **naam**.  
      
      > [!IMPORTANT]
      > De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u tijdens het publiceren een foutmelding ontvangt over de naam van de gegevensfactory, wijzigt u de naam ervan (naar bijvoorbeeld uwnaamVSTutorialFactory) en publiceert u opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.        
      > 
      > 
   3. Selecteer uw Azure-abonnement voor het veld **Abonnement**.
      
      > [!IMPORTANT]
      > Als u geen abonnement niet ziet, controleert u of u bent aangemeld met een account dat een beheerder of co-beheerder is van het abonnement.  
      > 
      > 
   4. Selecteer de **resourcegroep** voor de gegevensfactory die u wilt maken. 
   5. Selecteer de **regio** voor de gegevensfactory. Alleen regio's die worden ondersteund door de Data Factory-service worden weergegeven in de vervolgkeuzelijst.
   6. Klik op **Next** om over te schakelen naar de pagina **Publish Items**.
      
       ![Pagina Data Factory configureren](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Op de pagina **Publish Items** controleert u of alle Data Factory-entiteiten zijn geselecteerd en klikt u op **Next** om over te schakelen naar de pagina **Summary**.
   
   ![Pagina Items publiceren](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Controleer de samenvatting en klik op **Next** om te beginnen met het implementatieproces en om de **implementatiestatus** te bekijken.
   
   ![Pagina Samenvatting publiceren](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Op de pagina **Deployment Status** ziet u de status van het implementatieproces. Klik op Finish wanneer de implementatie is uitgevoerd.
 
   ![Pagina Deployment Status](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Houd rekening met de volgende punten: 

* Als de volgende foutmelding wordt weergegeven: 'Dit abonnement is niet geregistreerd voor gebruik van de naamruimte Microsoft.DataFactory', voert u een van de volgende stappen uit en probeert u opnieuw te publiceren: 
  
  * Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-provider te registreren. 

  ```powershell    
  Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
  U kunt de volgende opdracht uitvoeren om te bevestigen dat de Data Factory-provider is geregistreerd. 
    
  ```powershell
  Get-AzResourceProvider
    ```
  * Meld u bij de [Azure Portal](https://portal.azure.com) aan met behulp van het Azure-abonnement en navigeer naar een Data Factory-blade of maak een gegevensfactory in de Azure Portal. Door deze actie wordt de provider automatisch voor u geregistreerd.
* De naam van de gegevensfactory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.

> [!IMPORTANT]
> Als u Data Factory-exemplaren wilt maken, moet u beheerder/co-beheerder van het Azure-abonnement zijn

## <a name="monitor-pipeline"></a>De pijplijn bewaken
Navigeer naar de startpagina van uw gegevensfactory:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **Meer services** in het linkermenu en op **Gegevensfactory's**.

  ![Door gegevensfactory’s bladeren](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Begin de naam van uw gegevensfactory te typen.

  ![Naam van gegevensfactory](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Klik op uw gegevensfactory in de lijst met resultaten om de startpagina van uw gegevensfactory te bekijken.

  ![Startpagina van de gegevensfactory](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Volg de instructies in [Gegevenssets en pijplijn bewaken](data-factory-monitor-manage-pipelines.md) voor het bewaken van de pijplijn en gegevenssets die u tijdens deze zelfstudie hebt gemaakt. Visual Studio biedt momenteel geen ondersteuning voor het bewaken van Data Factory-pijplijnen. 

## <a name="summary"></a>Samenvatting
In deze zelfstudie hebt u een data factory in Azure gemaakt om gegevens te kopiëren van een Azure-blob naar Azure SQL Database. U hebt Visual Studio gebruikt om de gegevensfactory, gekoppelde services, gegevenssets en pijplijn te maken. In deze zelfstudie hebt u de volgende hoofdstappen uitgevoerd:  

1. U hebt een Azure-**gegevensfactory** gemaakt.
2. U hebt **gekoppelde services** gemaakt:
   1. Een gekoppelde **Azure Storage**-service om uw Azure-opslagaccount te koppelen dat invoergegevens bevat.     
   2. Een gekoppelde **Azure SQL**-service om uw database te koppelen die uitvoergegevens bevat. 
3. U hebt **gegevenssets** gemaakt waarin de invoer- en uitvoergegevens van pijplijnen worden beschreven.
4. U hebt een **pijplijn** gemaakt met een **kopieeractiviteit** met **BlobSource** als bron en **SqlSink** als sink. 

Als u wilt weten hoe u een HDInsight Hive-activiteit gebruikt om gegevens te transformeren met een Azure HDInsight-cluster, raadpleegt u [Zelfstudie: Uw eerste pijplijn maken om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md).

U kunt twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md) voor gedetailleerde informatie. 

## <a name="view-all-data-factories-in-server-explorer"></a>Alle gegevensfactory’s weergeven in Server Explorer
In deze sectie wordt beschreven hoe u Server Explorer in Visual Studio gebruikt voor het weergeven van alle gegevensfactory’s in uw Azure-abonnement en het maken van een Visual Studio-project op basis van een bestaande gegevensfactory. 

1. Klik in het menu van **Visual Studio** op **View** en vervolgens op **Server Explorer**.
2. Vouw in het Server Explorer-venster **Azure** en **Data Factory** uit. Wanneer u **Sign in to Visual Studio** ziet, voert u het **account** in dat aan uw Azure-abonnement is gekoppeld, en klikt u op **Continue**. Voer het **wachtwoord** in en klik op **Sign in**. Visual Studio haalt informatie op uit alle Azure Data Factory’s in uw abonnement. U ziet de status van deze bewerking in het venster **Data Factory Task List**.

    ![Server Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Een Visual Studio-project maken voor een bestaande gegevensfactory

- Klik met de rechtermuisknop op een gegevensfactory in Server Explorer en selecteer **Export Data Factory to New Project** om een Visual Studio-project te maken op basis van een bestaande gegevensfactory.

  ![Een gegevensfactory exporteren naar een VS-project](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Data Factory-hulpprogramma's voor Visual Studio bijwerken
Voer de volgende stappen uit om Azure Data Factory-hulpprogramma's voor Visual Studio bij te werken:

1. Klik in het menu op **Extra** en selecteer **Extensies en updates**. 
2. Selecteer **Updates** in het linkerdeelvenster en selecteer vervolgens **Visual Studio-galerie**.
3. Selecteer **Azure Data Factory-hulpprogramma's voor Visual Studio** en klik op **Bijwerken**. Als u deze vermelding niet ziet, beschikt u al over de nieuwste versie van de hulpprogramma's. 

## <a name="use-configuration-files"></a>Configuratiebestanden gebruiken
U kunt in Visual Studio configuratiebestanden gebruiken om de eigenschappen voor gekoppelde services/tabellen/pijplijnen anders te configureren voor elke omgeving.

Overweeg de volgende JSON-definitie te gebruiken voor een gekoppelde Azure Storage-service. Geef **connectionString** op met verschillende waarden voor accountname en accountkey op basis van de omgeving (ontwikkeling/tests/productie) waarin u Data Factory-entiteiten implementeert. U kunt dit gedrag bewerkstelligen door een afzonderlijk configuratiebestand te gebruiken voor elke omgeving.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Een configuratiebestand toevoegen
Voeg een configuratiebestand voor elke omgeving toe door de volgende stappen uit te voeren:   

1. Klik met de rechtermuisknop op het Data Factory-project in uw Visual Studio-oplossing, houd de muisaanwijzer op **Add** en klik op **New item**.
2. Selecteer in de lijst met geïnstalleerde sjablonen aan de linkerkant de optie **Config**, selecteer **Configuration File**, voer een **naam** in voor het configuratiebestand en klik op **Add**.

    ![Een configuratiebestand toevoegen](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Voeg configuratieparameters en de bijbehorende waarden toe in de volgende indeling:

  ```json
  {
      "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
      "AzureStorageLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
          }
      ],
      "AzureSqlLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
          }
      ]
  }
    ```

    In dit voorbeeld configureert u de eigenschap connectionString van een gekoppelde Azure Storage-service en een gekoppelde Azure SQL-service. De syntaxis voor het opgeven van de naam is [JsonPath](https://goessner.net/articles/JsonPath/).   

    Als JSON een eigenschap heeft met een matrix van waarden zoals in de volgende code wordt weergegeven:  

  ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Eigenschappen configureren zoals wordt weergegeven in het volgende configuratiebestand (gebruik op nul gebaseerde indexering):

  ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Eigenschapnamen met spaties
Als de naam van een eigenschap spaties bevat, gebruikt u vierkante haken, zoals in het volgende voorbeeld wordt weergegeven (databaseservernaam):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Een oplossing implementeren met behulp van een configuratie
Wanneer u Azure Data Factory-entiteiten publiceert in de VS, kunt u opgeven welke configuratie u voor die publicatiebewerking wilt gebruiken.

Entiteiten publiceren in een Azure Data Factory-project via een configuratiebestand:   

1. Klik met de rechtermuisknop op het Data Factory-project en klik op **Publish** om het dialoogvenster **Publish Items** weer te geven.
2. Selecteer een bestaande gegevensfactory of geef op de pagina **Configure data factory** waarden op voor het maken van een nieuwe gegevensfactory. Klik vervolgens op **Next**.   
3. Op de pagina **Publish Items**: u ziet een vervolgkeuzelijst met beschikbare configuraties voor het veld **Select Deployment Config**.

    ![Een configuratiebestand selecteren](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Selecteer het **configuratiebestand** dat u wilt gebruiken en klik op **Next**.
5. Controleer of u de naam van het JSON-bestand ziet op de pagina **Summary** en klik op **Next**.
6. Klik op **Finish** nadat de implementatiebewerking is voltooid.

Tijdens de implementatie worden de waarden van het configuratiebestand gebruikt voor de eigenschappen in de JSON-bestanden voor Data Factory-entiteiten voordat de entiteiten worden geïmplementeerd in de Azure Data Factory-service.   

## <a name="use-azure-key-vault"></a>Azure Key Vault gebruiken
Het wordt niet aangeraden en het is vaak in strijd met het beveiligingsbeleid om gevoelige gegevens, zoals verbindingsreeksen, op te slaan in de codeopslagplaats. Zie het voorbeeld [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) op GitHub voor meer informatie over de opslag van vertrouwelijke gegevens in Azure Key Vault en het gebruik daarvan tijdens de publicatie van Data Factory-entiteiten. Met de extensie Secure Publish voor Visual Studio kunnen de geheimen worden opgeslagen in Key Vault en worden alleen verwijzingen naar deze geheimen opgegeven in de gekoppelde services/implementatieconfiguraties. Deze verwijzingen worden opgelost wanneer u Data Factory-entiteiten publiceert naar Azure. Deze bestanden kunnen vervolgens worden doorgevoerd naar een bronopslagplaats zonder dat er geheimen worden weergegeven.


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u voor een kopieerbewerking Azure-blobopslag gebruikt als brongegevensarchief en Azure SQL Database als doelgegevensarchief. De volgende tabel bevat een lijst met gegevensarchieven die worden ondersteund als bron en doel voor de kopieeractiviteit: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Klik op de koppeling voor de gegevensopslag in de tabel voor meer informatie over het kopiëren van gegevens naar/uit een gegevensarchief.
