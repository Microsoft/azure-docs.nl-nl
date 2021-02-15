---
title: 'Zelfstudie: Een pijplijn maken om gegevens te verplaatsen met Azure PowerShell '
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn met een kopieeractiviteit. Hiervoor gebruikt u Azure PowerShell.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 54c296ed8013b9962de9487cfec3e2568c03e738
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377037"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Zelfstudie: Een Data Factory-pijplijn maken die gegevens verplaatst met Azure PowerShell
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-powershell.md). 

In dit artikel leert u hoe u PowerShell kunt gebruiken om een gegevensfactory te maken met een pijplijn waarmee gegevens worden gekopieerd van een Azure blobopslag naar Azure SQL Database. Als u niet bekend bent met Azure Data Factory, lees dan het artikel [Inleiding tot Azure Data Factory](data-factory-introduction.md) voordat u deze zelfstudie volgt.   

In deze zelfstudie maakt u een pijplijn met één activiteit erin: kopieeractiviteit. De kopieeractiviteit in Data Factory kopieert gegevens uit een ondersteund gegevensarchief naar een ondersteund sinkgegevensarchief. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten.

Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Meerdere activiteiten in een pijplijn](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie.

> [!NOTE]
> Dit artikel omvat niet alle Data Factory-cmdlets. Zie [Data Factory Cmdlet Reference](/powershell/module/az.datafactory) (Naslaginformatie voor Data Factory-cmdlets) voor uitgebreide documentatie over deze cmdlets.
> 
> In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Meer informatie over het transformeren van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: een pijplijn bouwen om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- U dient eerst te voldoen aan de vereisten in het artikel [Vereisten voor de zelfstudie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Installeer **Azure PowerShell**. Volg de instructies in [How to install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren).

## <a name="steps"></a>Stappen
Hier volgen de stappen die u uitvoert als onderdeel van deze zelfstudie:

1. Een Azure-**gegevensfactory** maken. In deze stap maakt u een gegevensfactory met de naam ADFTutorialDataFactoryPSH. 
1. **Gekoppelde services** maken in de gegevensfactory. In deze stap maakt u twee gekoppelde services van de typen: Azure Storage en Azure SQL Database. 

    De AzureStorageLinkedService koppelt uw Azure-opslagaccount aan de gegevensfactory. U hebt een container gemaakt en gegevens naar dit opslagaccount geüpload als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

    Met AzureSqlLinkedService wordt Azure SQL Database aan de data factory gekoppeld. De gegevens die worden gekopieerd uit de blobopslag worden opgeslagen in deze database. Als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hebt u een SQL-tabel in deze database gemaakt.   
1. Maak **invoer- en uitvoergegevenssets** in de gegevensfactory.  

    De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. En de blobgegevensset voor invoer geeft de container en de map met de invoergegevens op.  

    Op dezelfde manier geeft de gekoppelde Azure SQL Database-service de verbindingsreeks op die de Data Factory-service in runtime gebruikt om verbinding te maken met uw database. En de uitvoergegevensset van de SQL-tabel geeft de tabel in de database op waarnaar de gegevens uit de blobopslag worden gekopieerd.
1. Maak een **pijplijn** in de gegevensfactory. In deze stap maakt u een pijplijn met een kopieeractiviteit.   

    Met de kopieeractiviteit worden gegevens uit een blob in de Azure-blobopslag naar een tabel in de Azure SQL Database gekopieerd. U kunt een kopieeractiviteit gebruiken in een pijplijn om gegevens uit ondersteunde bronnen te kopiëren naar een ondersteunde bestemming. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met ondersteunde gegevensarchieven. 
1. Bewaak de pijplijn. In deze stap **bewaakt** u segmenten van de invoer- en uitvoergegevenssets met behulp van PowerShell.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
> [!IMPORTANT]
> Voldoe aan de [vereisten voor de zelfstudie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) als u dat nog niet hebt gedaan.   

Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens van een bron- naar een doelgegevensopslagplaats te kopiëren en een HDInsight Hive-activiteit om een Hive-script uit te voeren voor het transformeren van invoergegevens naar productuitvoergegevens. U begint in deze stap met het maken van de gegevensfactory.

1. Start **PowerShell**. Houd Azure PowerShell open tot het einde van deze zelfstudie. Als u het programma sluit en opnieuw opent, moet u de opdrachten opnieuw uitvoeren.

    Voer de volgende opdracht uit en geef de gebruikersnaam en het wachtwoord op waarmee u zich aanmeldt bij Azure Portal:

    ```powershell
    Connect-AzAccount
    ```   

    Voer de volgende opdracht uit om alle abonnementen voor dit account weer te geven:

    ```powershell
    Get-AzSubscription
    ```

    Voer de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken. Vervang **&lt;NameOfAzureSubscription**&gt; door de naam van uw Azure-abonnement:

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. Maak een Azure-resourcegroep met de naam **ADFTutorialResourceGroup** door de volgende opdracht uit te voeren:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Voor sommige van de stappen in deze zelfstudie wordt ervan uitgegaan dat u de resourcegroep met de naam **ADFTutorialResourceGroup** gebruikt. Als u een andere resourcegroep gebruikt, moet u voor deze zelfstudie die groep gebruiken in plaats van ADFTutorialResourceGroup.
1. Voer de cmdlet **New-AzDataFactory** uit om een data factory met de naam **ADFTutorialDataFactoryPSH** te maken:  

    ```powershell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    Deze naam is mogelijk al in gebruik. Zorg daarom dat de naam van de gegevensfactory uniek is door een voor- of achtervoegsel toe te voegen (bijvoorbeeld: ADFTutorialDataFactoryPSH05152017) en voer de opdracht opnieuw uit.  

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u het volgende foutbericht krijgt, moet u de naam (bijvoorbeeld uwnaamADFTutorialDataFactoryPSH) wijzigen. Gebruik deze naam in plaats van ADFTutorialFactoryPSH tijdens het uitvoeren van de stappen in de zelfstudie. Raadpleeg [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory - Naamgevingsregels) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

    ```
    Data factory name "ADFTutorialDataFactoryPSH" is not available
    ```
* Als u Data Factory-exemplaren wilt maken, moet u bijdrager of beheerder zijn van het Azure-abonnement.
* De naam van de data factory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.
* De volgende fout kan optreden: '**Dit abonnement is niet geregistreerd voor gebruik van de naamruimte Microsoft.DataFactory.** ' Voer een van de volgende handelingen uit en probeer opnieuw te publiceren:

  * Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-provider te registreren:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Voer de volgende opdracht uit om te bevestigen dat de Data Factory-provider is geregistreerd:

    ```powershell
    Get-AzResourceProvider
    ```
  * Meld u met het Azure-abonnement aan bij [Azure Portal](https://portal.azure.com). Ga naar een Data Factory-blade of maak een data factory in Azure Portal. Door deze actie wordt de provider automatisch voor u geregistreerd.

## <a name="create-linked-services"></a>Gekoppelde services maken
U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze zelfstudie gebruikt u niet een willekeurige compute-service, zoals Azure HDInsight of Azure Data Lake Analytics. U gebruikt twee gegevensarchieven van het type Azure Storage (bron) en Azure SQL Database (doel). 

Daarom maakt u twee gekoppelde services met de naam AzureStorageLinkedService en AzureSqlLinkedService van de typen AzureStorage en AzureSqlDatabase.  

De AzureStorageLinkedService koppelt uw Azure-opslagaccount aan de gegevensfactory. Dit opslagaccount is het account waarin u een container hebt gemaakt en gegevens hebt geüpload als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Met AzureSqlLinkedService wordt Azure SQL Database aan de data factory gekoppeld. De gegevens die worden gekopieerd uit de blobopslag worden opgeslagen in deze database. Als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hebt u de emp-tabel in deze database gemaakt. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Een gekoppelde service maken voor een Azure-opslagaccount
In deze stap koppelt u uw Azure Storage-account aan uw gegevensfactory.

1. Maak een JSON-bestand met de naam **AzureStorageLinkedService.json** in de map **C:\ADFGetStartedPSH** met de volgende inhoud. (Maak de map ADFGetStartedPSH als deze nog niet bestaat.)

    > [!IMPORTANT]
    > Vervang &lt;accountname&gt; en &lt;accountkey&gt; door de naam en sleutel van uw Azure Storage-account voordat u het bestand opslaat. 

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ``` 
1. Schakel in **Azure PowerShell** over naar de map **ADFGetStartedPSH**.
1. Voer de cmdlet **New-AzDataFactoryLinkedService** uit om de gekoppelde service te maken: **AzureStorageLinkedService**. Voor deze cmdlet en andere Data Factory-cmdlets die u in deze zelfstudie gebruikt, moet u waarden doorgeven voor de parameters **ResourceGroupName** en **DataFactoryName**. U kunt ook het DataFactory-object doorgeven dat door de cdmlet New-AzDataFactory is geretourneerd. U hoeft dan niet telkens wanneer u een cmdlet uitvoert de ResourceGroupName en de DataFactoryName op te geven. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Hier volgt een voorbeeld van uitvoer:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    Een andere manier voor het maken van deze gekoppelde service is de naam van de resourcegroep en de naam van de gegevensfactory op te geven in plaats van het DataFactory-object.  

    ```powershell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-azure-sql-database"></a>Een gekoppelde service maken voor Azure SQL Database
In deze stap koppelt u Azure SQL Database aan uw data factory.

1. Maak een JSON-bestand met de naam AzureSqlLinkedService.json in de map C:\ADFGetStartedPSH. Geef dit bestand de volgende inhoud:

    > [!IMPORTANT]
    > Vervang &lt;servername&gt;, &lt;databasename&gt;, &lt;username@servername&gt; en &lt;password&gt; door de namen van uw server, database, gebruikersaccount en wachtwoord.

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. Voer de volgende opdracht uit om een gekoppelde service te maken:

    ```powershell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Bevestig dat de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor uw server. Voer de volgende stappen uit om dit te controleren en de instelling in te schakelen:

    1. Meld u aan bij [Azure Portal](https://portal.azure.com).
    1. Klik op **Meer services >** aan de linkerzijde en klik op **SQL-servers** in de categorie **DATABASES**.
    1. Selecteer uw server in de lijst met SQL-servers.
    1. Klik op de blade SQL server op de koppeling **Firewall-instellingen weergeven**.
    1. In de blade **Firewallinstellingen** schakelt u **Toegang tot Azure-services toestaan** **in**.
    1. Klik op **Opslaan** op de werkbalk. 

## <a name="create-datasets"></a>Gegevenssets maken
In de vorige stap hebt u gekoppelde services gemaakt om uw Azure-opslagaccount en Azure SQL Database aan de data factory te koppelen. In deze stap definieert u twee gegevenssets, InputDataset en OutputDataset genaamd, die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven waarnaar wordt verwezen door respectievelijk de AzureStorageLinkedService en de AzureSqlLinkedService.

De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. En de blobgegevensset voor invoer (InputDataset) geeft de container en de map met de invoergegevens op.  

Op dezelfde manier geeft de gekoppelde Azure SQL Database-service de verbindingsreeks op die de Data Factory-service in runtime gebruikt om verbinding te maken met uw database. En de uitvoergegevensset van de SQL-tabel (OututDataset) geeft de tabel in de database op waarnaar de gegevens uit de blobopslag worden gekopieerd. 

### <a name="create-an-input-dataset"></a>Een invoergegevensset maken
In deze stap maakt u een gegevensset met de naam InputDataset die verwijst naar een blobbestand (emp.txt) in de hoofdmap van een blobcontainer (adftutorial) in Azure Storage. Deze container wordt vertegenwoordigd door de gekoppelde AzureStorageLinkedService-service. Als u geen waarde voor de fileName hebt opgeven (of hebt overgeslagen), worden gegevens uit alle blobs in de invoermap naar het doel gekopieerd. In deze zelfstudie geeft u een waarde op voor de fileName.  

1. Maak een JSON-bestand met de naam **InputDataset.json** in de map **C:\ADFGetStartedPSH**. Geef dit bestand de volgende inhoud:

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
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
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
    | linkedServiceName | Deze eigenschap verwijst naar de **AzureStorageLinkedService** die u eerder hebt gemaakt. |
    | folderPath | Deze eigenschap verwijst naar de blob **container** en de **map** die de blobs voor invoer bevat. In deze zelfstudie is adftutorial de blobcontainer en is folder de hoofdmap. | 
    | fileName | Deze eigenschap is optioneel. Als u deze eigenschap niet opgeeft, worden alle bestanden uit folderPath gekozen. In deze zelfstudie wordt **emp.txt** opgegeven voor de fileName, zodat alleen dat bestand wordt opgehaald voor de verwerking. |
    | format -> type |Het invoerbestand is in de tekstindeling, zodat we **TextFormat** gebruiken. |
    | columnDelimiter | De kolommen in het invoerbestand worden gescheiden door een **komma (`,`)** . |
    | frequency/interval | Als frequency wordt ingesteld op **Hour** en het interval wordt ingesteld op **1**, betekent dit dat de invoersegmenten één keer per **uur** beschikbaar worden gemaakt. Met andere woorden, de Data Factory-service zoekt elk uur naar invoergegevens in de hoofdmap van de opgegeven blobcontainer (**adftutorial**). Er wordt gezocht naar gegevens binnen de begin- en eindtijd van de pijplijn, niet voor of na deze tijden.  |
    | external | Deze eigenschap wordt ingesteld op **true** als de gegevens niet worden gegenereerd door deze pijplijn. De invoergegevens in deze zelfstudie bevinden zich in het bestand emp.txt, dat niet wordt gegenereerd door deze pijplijn. Daarom stellen we deze eigenschap in op true. |

    Zie het [artikel over Azure Blob-connectoren](data-factory-azure-blob-connector.md#dataset-properties) voor meer informatie over deze JSON-eigenschappen.
1. Voer de volgende opdracht uit om de Data Factory-gegevensset te maken.

    ```powershell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    Hier volgt een voorbeeld van uitvoer:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Een uitvoergegevensset maken
In dit deel van de stap maakt u een uitvoergegevensset met de naam **OutputDataset**. Deze gegevensset wijst naar een SQL-tabel in Azure SQL Database die wordt vertegenwoordigd door **AzureSqlLinkedService**. 

1. Maak een JSON-bestand met de naam **OutputDataset.json** in de map **C:\ADFGetStartedPSH**. Geef dit bestand de volgende inhoud:

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
            "linkedServiceName": "AzureSqlLinkedService",
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
1. Voer de volgende opdracht uit om de data factory-gegevensset te maken.

    ```powershell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijplijn met een **kopieeractiviteit** die gebruikmaakt van **InputDataset** als invoer en **OutputDataset** als uitvoer.

Momenteel is de uitvoergegevensset dat wat de planning aanstuurt. In deze zelfstudie is de uitvoergegevensset geconfigureerd voor het produceren van een segment eenmaal per uur. De pijplijn heeft een begintijd en eindtijd die één dag uit elkaar liggen, ofwel 24 uur. Daarom worden 24 segmenten van de uitvoergegevensset door de pijplijn geproduceerd. 

1. Maak een JSON-bestand met de naam **ADFTutorialPipeline.json** in de map **C:\ADFGetStartedPSH**. Geef dit bestand de volgende inhoud:

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```
    Houd rekening met de volgende punten:

   - In het gedeelte Activiteiten is er slechts één activiteit waarvan **type** is ingesteld op **Copy**. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten. In Data Factory-oplossingen kunt u ook [activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) gebruiken.
   - De invoer voor de activiteit is ingesteld op **InputDataset** en de uitvoer voor de activiteit is ingesteld op **OutputDataset**. 
   - In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een volledige lijst van gegevensarchieven die worden ondersteund door kopieeractiviteiten als bronnen en sinks. Klik op de koppeling in de tabel voor informatie over het gebruik van een specifiek ondersteund gegevensarchief als een bron/sink.  

     Vervang de waarde van de eigenschap **start** door de huidige dag en de waarde **end** door de volgende dag. U hoeft alleen de datum in te vullen en kunt de tijd overslaan. Dit wordt dan bijvoorbeeld 2016-02-03, wat gelijk staat aan 2016-02-03T00:00:00Z

     Zowel de begin- als einddatum en -tijd moeten de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601) hebben. Bijvoorbeeld: 2016-10-14T16:32:41Z. De **eindtijd** is optioneel, maar we gebruiken hem in deze zelfstudie. 

     Als u geen waarde opgeeft voor de eigenschap **end**, wordt automatisch **start + 48 uur** gebruikt. Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**.

     In het voorgaande voorbeeld zijn er 24 gegevenssegmenten omdat er elk uur één gegevenssegment wordt gemaakt.

     Zie het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor beschrijvingen van JSON-eigenschappen in de definitie van een pijplijn. Zie [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voor beschrijvingen van JSON-eigenschappen in de definitie van een kopieeractiviteit. Zie het [artikel over Azure Blob-connectoren](data-factory-azure-blob-connector.md) voor beschrijvingen van JSON-eigenschappen die worden ondersteund door BlobSource. Zie het [artikel over Azure SQL Database-connectoren](data-factory-azure-sql-connector.md) voor beschrijvingen van JSON-eigenschappen die worden ondersteund door SqlSink.
1. Voer de volgende opdracht uit om de data factory-tabel te maken.

    ```powershell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Hier volgt een voorbeeld van uitvoer: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Gefeliciteerd!** U hebt een Azure-gegevensfactory gemaakt met een pijplijn die gegevens van een Azure-blobopslag kopieert naar Azure SQL Database. 

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken
In deze stap gebruikt u Azure PowerShell om te controleren wat er gebeurt in een Azure data factory.

1. Vervang &lt;DataFactoryName&gt; door de naam van uw data factory en voer **Get-AzDataFactory** uit. Wijs de uitvoer toe aan een variabele $df.

    ```powershell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    Bijvoorbeeld:
    ```powershell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```

    Druk vervolgens de inhoud van $df af om de volgende uitvoer te bekijken: 

    ```
    PS C:\ADFGetStartedPSH> $df

    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. Voer **Get-AzDataFactorySlice** uit voor meer informatie over alle segmenten van de **OutputDataset**, de uitvoergegevensset van de pijplijn.  

    ```powershell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Deze instelling moet overeenkomen met de waarde **Start** in de JSON-pijplijn. U ziet 24 segmenten; één voor elk uur vanaf 12:00 uur ‘s nachts vandaag tot 12 uur 's nachts de volgende dag.

   Hier volgen drie voorbeeldsegmenten van de uitvoer: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. Voer **Get-AzDataFactoryRun** uit om gegevens over het uitvoeren van de activiteit op te halen voor een **specifiek** segment. Kopieer de datum-/ tijdwaarde uit de uitvoer van de vorige opdracht om de waarde voor de parameter StartDateTime op te geven. 

    ```powershell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Hier volgt een voorbeeld van uitvoer: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Zie [Naslaginformatie voor Data Factory-cmdlets](/powershell/module/az.datafactory) voor uitgebreide documentatie over Data Factory-cmdlets.

## <a name="summary"></a>Samenvatting
In deze zelfstudie hebt u een data factory in Azure gemaakt om gegevens te kopiëren van een Azure-blob naar Azure SQL Database. U hebt PowerShell gebruikt om de gegevensfactory, gekoppelde services, gegevenssets en pijplijn te maken. In deze zelfstudie hebt u de volgende hoofdstappen uitgevoerd:  

1. U hebt een Azure-**gegevensfactory** gemaakt.
1. U hebt **gekoppelde services** gemaakt:

   a. Een gekoppelde **Azure Storage**-service om uw Azure-opslagaccount te koppelen dat invoergegevens bevat.     
   b. Een gekoppelde **Azure SQL**-service om uw SQL-database te koppelen die uitvoergegevens bevat.
1. U hebt **gegevenssets** gemaakt waarin de invoer- en uitvoergegevens van pijplijnen worden beschreven.
1. U hebt een **pijplijn** gemaakt met **Kopieeractiviteit**, met **BlobSource** als de bron en **SqlSink** als de sink.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u voor een kopieerbewerking Azure-blobopslag gebruikt als brongegevensarchief en Azure SQL Database als doelgegevensarchief. De volgende tabel bevat een lijst met gegevensarchieven die worden ondersteund als bron en doel voor de kopieeractiviteit: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Klik op de koppeling voor de gegevensopslag in de tabel voor meer informatie over het kopiëren van gegevens naar/uit een gegevensarchief. 

