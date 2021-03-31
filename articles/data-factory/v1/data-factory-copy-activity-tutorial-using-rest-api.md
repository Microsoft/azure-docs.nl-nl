---
title: 'Zelfstudie: REST-API gebruiken voor het maken van een Azure Data Factory-pijplijn '
description: In deze zelfstudie gebruikt u REST API om een Azure Data Factory-pijplijn te maken met een kopieeractiviteit om gegevens uit een Azure-blobopslag naar Azure SQL Database te kopiëren.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7488834252dcd4e231c2d91a1435838befe7b1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377020"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Zelfstudie: REST-API gebruiken voor het maken van een Azure Data Factory-pijplijn om gegevens te kopiëren 
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
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-rest-api.md). 

In dit artikel leert u hoe u REST API kunt gebruiken om een data factory te maken met een pijplijn waarmee gegevens worden gekopieerd van een Azure-blobopslag naar Azure SQL Database. Als u niet bekend bent met Azure Data Factory, lees dan het artikel [Inleiding tot Azure Data Factory](data-factory-introduction.md) voordat u deze zelfstudie volgt.   

In deze zelfstudie maakt u een pijplijn met één activiteit erin: kopieeractiviteit. De kopieeractiviteit in Data Factory kopieert gegevens uit een ondersteund gegevensarchief naar een ondersteund sinkgegevensarchief. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink. De activiteit wordt mogelijk gemaakt door een wereldwijd beschikbare service waarmee gegevens veilig, betrouwbaar en schaalbaar kunnen worden gekopieerd tussen verschillende gegevensarchieven. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten.

Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Meerdere activiteiten in een pijplijn](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie.

> [!NOTE]
> Dit artikel omvat niet alle Data Factory-REST API. Zie [Naslaginformatie voor Data Factory-REST API](/rest/api/datafactory/) voor uitgebreide documentatie over Data Factory-cmdlets.
>  
> In de gegevenspijplijn in deze zelfstudie worden gegevens van een brongegevensarchief gekopieerd naar een doelgegevensarchief. Meer informatie over het transformeren van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: een pijplijn bouwen om gegevens te transformeren met een Hadoop-cluster](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Neem het artikel [Overzicht van de zelfstudie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) door en voer de **vereiste** stappen uit.
* Installeer [Curl](https://curl.haxx.se/dlwiz/) op uw computer. U kunt in het hulpprogramma Curl REST-opdrachten gebruiken om een gegevensfactory te maken. 
* Volg de instructies in [dit artikel](../../active-directory/develop/howto-create-service-principal-portal.md) voor het volgende: 
  1. Maak een webtoepassing met de naam **ADFCopyTutorialApp** in Azure Active Directory.
  2. Haal de **client-id** en **geheime sleutel** op. 
  3. Haal de **tenant-id** op. 
  4. Wijs de toepassing **ADFCopyTutorialApp** toe aan de rol **Inzender Data Factory**.  
* Installeer [Azure PowerShell](/powershell/azure/).  
* Start **PowerShell** en voer de volgende stappen uit. Houd Azure PowerShell open tot het einde van deze zelfstudie. Als u het programma sluit en opnieuw opent, moet u de opdrachten opnieuw uitvoeren.
  
  1. Voer de volgende opdracht uit en geef de gebruikersnaam en het wachtwoord op waarmee u zich aanmeldt bij Azure Portal:
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. Voer de volgende opdracht uit om alle abonnementen voor dit account weer te geven:

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. Voer de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken. Vervang **&lt;NameOfAzureSubscription**&gt; door de naam van uw Azure-abonnement. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. Maak een Azure-resourcegroep met de naam **ADFTutorialResourceGroup** door de volgende opdracht uit te voeren in PowerShell:  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      Als de resourcegroep al bestaat, geeft u aan of u deze wilt bijwerken (Y) of ongewijzigd wilt laten (N). 
     
      Voor sommige van de stappen in deze zelfstudie wordt ervan uitgegaan dat u de resourcegroep met de naam ADFTutorialResourceGroup gebruikt. Als u een andere resourcegroep gebruikt, moet u voor deze zelfstudie de naam van uw resourcegroep gebruiken in plaats van ADFTutorialResourceGroup.

## <a name="create-json-definitions"></a>JSON-definities maken
Maak de volgende JSON-bestanden in de map waar curl.exe staat. 

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Naam moet uniek zijn, dus het is raadzaam om voor- en/of achtervoegsels te gebruiken bij ADFCopyTutorialDF, zodat het een unieke naam wordt. 
> 
> 

```JSON
{  
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Vervang **accountname** en **accountkey** door de naam en sleutel van uw Azure Storage-account. Zie [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md) voor informatie over het ophalen van uw toegangssleutel voor opslag.

```JSON
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

Zie [Gekoppelde Azure Storage-service](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor meer informatie over de JSON-eigenschappen.

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.json
> [!IMPORTANT]
> Vervang **servername**, **databasename**, **username** en **password** door de naam van uw server, de naam van de SQL-database, het gebruikersaccount en het wachtwoord van het account.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Zie [Gekoppelde Azure SQL-service](data-factory-azure-sql-connector.md#linked-service-properties) voor meer informatie over de JSON-eigenschappen.

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
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
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
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

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
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

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL Database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
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
- De invoer voor de activiteit is ingesteld op **AzureBlobInput** en de uitvoer voor de activiteit is ingesteld op **AzureSqlOutput**. 
- In het gedeelte **typeProperties** is **BlobSource** opgegeven als het brontype en **SqlSink** als het sink-type. Zie [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een volledige lijst van gegevensarchieven die worden ondersteund door kopieeractiviteiten als bronnen en sinks. Klik op de koppeling in de tabel voor informatie over het gebruik van een specifiek ondersteund gegevensarchief als een bron/sink.  
 
Vervang de waarde van de eigenschap **start** door de huidige dag en de waarde **end** door de volgende dag. U hoeft alleen de datum in te vullen en kunt de tijd overslaan. Dit wordt dan bijvoorbeeld 2017-02-03, wat gelijk staat aan 2017-02-03T00:00:00Z
 
Zowel de begin- als einddatum en -tijd moeten de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601) hebben. Bijvoorbeeld: 2016-10-14T16:32:41Z. De **eindtijd** is optioneel, maar we gebruiken hem in deze zelfstudie. 
 
Als u geen waarde opgeeft voor de eigenschap **end**, wordt automatisch **start + 48 uur** gebruikt. Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9999-09-09** op als waarde voor de eigenschap **end**.
 
In het voorgaande voorbeeld zijn er 24 gegevenssegmenten omdat er elk uur één gegevenssegment wordt gemaakt.

Zie het artikel [Pijplijnen maken](data-factory-create-pipelines.md) voor beschrijvingen van JSON-eigenschappen in de definitie van een pijplijn. Zie [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) voor beschrijvingen van JSON-eigenschappen in de definitie van een kopieeractiviteit. Zie het [artikel over Azure Blob-connectoren](data-factory-azure-blob-connector.md) voor beschrijvingen van JSON-eigenschappen die worden ondersteund door BlobSource. Zie het [artikel over Azure SQL Database-connectoren](data-factory-azure-sql-connector.md) voor beschrijvingen van JSON-eigenschappen die worden ondersteund door SqlSink.

## <a name="set-global-variables"></a>Globale variabelen instellen
Voer in Azure PowerShell de volgende opdrachten uit nadat u de waarden hebt vervangen door uw eigen waarden:

> [!IMPORTANT]
> Zie de sectie [Vereisten](#prerequisites) voor instructies over het verkrijgen van de client-id, het clientgeheim, de tenant-id en de abonnements-id.   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Voer de volgende opdracht uit na het bijwerken van de naam van de gegevensfactory die u gebruikt: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Verifiëren met AAD
Voer de volgende opdracht uit om te verifiëren met Azure Active Directory (AAD): 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Een gegevensfactory maken
In deze stap maakt u een Azure-gegevensfactory met de naam **ADFCopyTutorialDF**. Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens te kopiëren van een brongegevensarchief naar een doelgegevensarchief. Een HDInsight Hive-activiteit om een Hive-script uit te voeren dat invoergegevens omzet in productuitvoergegevens. Voer de volgende opdracht uit om de gegevensfactory te maken: 

1. Wijs de opdracht toe aan de variabele met de naam **cmd**. 
   
    > [!IMPORTANT]
    > Bevestig dat de naam van de gegevensfactory die u hier opgeeft (ADFCopyTutorialDF) overeenkomt met de naam die is opgegeven in de **datafactory.json**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensfactory is gemaakt, ziet u de JSON voor de gegevensfactory in de **resultaten**. Anders wordt er een foutbericht weergegeven.  
   
    ```
    Write-Host $results
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de fout in de resultaten ziet: **Data Factory name "ADFCopyTutorialDF" is niet beschikbaar**, voert u de volgende stappen uit:  
  
  1. Wijzig de naam (bijvoorbeeld uwnaamADFCopyTutorialDF) in het **datafactory.json**-bestand.
  2. In de eerste opdracht waar de **$cmd**-variabele een waarde is toegewezen, vervangt u ADFCopyTutorialDF door de nieuwe naam en voert u de opdracht uit. 
  3. Voer de volgende twee opdrachten uit voor het aanroepen van de REST API om de gegevensfactory te maken en de resultaten van de bewerking af te drukken. 
     
     Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
* Als u Data Factory-exemplaren wilt maken, moet u bijdrager/beheerder zijn van het Azure-abonnement
* De naam van de gegevensfactory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.
* Als de volgende foutmelding wordt weergegeven: '**Dit abonnement is niet geregistreerd voor gebruik van de naamruimte Microsoft.DataFactory**', voert u een van de volgende stappen uit en probeert u opnieuw te publiceren: 
  
  * Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-provider te registreren: 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    U kunt de volgende opdracht uitvoeren om te bevestigen dat de Data Factory-provider is geregistreerd. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Meld u bij de [Azure Portal](https://portal.azure.com) aan met behulp van het Azure-abonnement en navigeer naar een Data Factory-blade of maak een gegevensfactory in de Azure Portal. Door deze actie wordt de provider automatisch voor u geregistreerd.

Voordat u een pijplijn maakt, moet u eerst enkele Data Factory-entiteiten maken. U maakt eerst gekoppelde services om de bron- en doelgegevensarchieven te koppelen aan uw gegevensarchief. Vervolgens definieert u in- en uitvoergegevenssets die de gegevens in de gekoppelde gegevensarchieven vertegenwoordigen. Ten slotte maakt u de pijplijn met een activiteit die deze gegevenssets gebruikt.

## <a name="create-linked-services"></a>Gekoppelde services maken
U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze zelfstudie gebruikt u niet een willekeurige compute-service, zoals Azure HDInsight of Azure Data Lake Analytics. U gebruikt twee gegevensarchieven van het type Azure Storage (bron) en Azure SQL Database (doel). Daarom maakt u twee gekoppelde services met de naam AzureStorageLinkedService en AzureSqlLinkedService van de typen AzureStorage en AzureSqlDatabase.  

De AzureStorageLinkedService koppelt uw Azure-opslagaccount aan de gegevensfactory. Dit opslagaccount is het account waarin u een container hebt gemaakt en gegevens hebt geüpload als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Met AzureSqlLinkedService wordt Azure SQL Database aan de data factory gekoppeld. De gegevens die worden gekopieerd uit de blobopslag worden opgeslagen in deze database. Als onderdeel van de [vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hebt u de emp-tabel in deze database gemaakt.  

### <a name="create-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken
In deze stap koppelt u uw Azure Storage-account aan uw gegevensfactory. In deze sectie geeft u de naam en sleutel van uw Azure Storage-account op. Zie [Een gekoppelde Azure Storage-service](data-factory-azure-blob-connector.md#azure-storage-linked-service) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een gekoppelde Azure Storage-service.  

1. Wijs de opdracht toe aan de variabele met de naam **cmd**. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gekoppelde service is gemaakt, ziet u de JSON voor de gekoppelde service in de **resultaten**. Anders wordt er een foutbericht weergegeven.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Gekoppelde Azure SQL-service maken
In deze stap koppelt u Azure SQL Database aan uw data factory. U geeft in deze sectie de logische SQL-servernaam, -databasenaam, -gebruikersnaam en -wachtwoord op. Zie [Een gekoppelde Azure SQL-service](data-factory-azure-sql-connector.md#linked-service-properties) voor meer informatie over de JSON-eigenschappen die worden gebruikt voor het definiëren van een gekoppelde Azure SQL-service.

1. Wijs de opdracht toe aan de variabele met de naam **cmd**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azuresqllinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gekoppelde service is gemaakt, ziet u de JSON voor de gekoppelde service in de **resultaten**. Anders wordt er een foutbericht weergegeven.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Gegevenssets maken
In de vorige stap hebt u gekoppelde services gemaakt om uw Azure-opslagaccount en Azure SQL Database aan uw data factory te koppelen. In deze stap definieert u twee gegevenssets, AzureBlobInput en AzureSqlOutput genaamd, die staan voor de invoer- en uitvoergegevens die zijn opgeslagen in de gegevensarchieven waarnaar wordt verwezen door respectievelijk de AzureStorageLinkedService en de AzureSqlLinkedService.

De gekoppelde Azure Storage-service geeft de verbindingsreeks op die de Data Factory-service tijdens runtime gebruikt om verbinding te maken met uw Azure-opslagaccount. En de blobgegevensset voor invoer (AzureBlobInput) geeft de container en de map met de invoergegevens op.  

Op dezelfde manier geeft de gekoppelde Azure SQL Database-service de verbindingsreeks op die de Data Factory-service in runtime gebruikt om verbinding te maken met Azure SQL Database. En de uitvoergegevensset van de SQL-tabel (OututDataset) geeft de tabel in de database op waarnaar de gegevens uit de blobopslag worden gekopieerd. 

### <a name="create-input-dataset"></a>Invoergegevensset maken
In deze stap maakt u een gegevensset met de naam AzureBlobInput die verwijst naar een blobbestand (emp.txt) in de hoofdmap van een blobcontainer (adftutorial) in Azure Storage. Deze container wordt vertegenwoordigd door de gekoppelde AzureStorageLinkedService-service. Als u geen waarde voor de fileName hebt opgeven (of hebt overgeslagen), worden gegevens uit alle blobs in de invoermap naar het doel gekopieerd. In deze zelfstudie geeft u een waarde op voor de fileName. 

1. Wijs de opdracht toe aan de variabele met de naam **cmd**. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensset is gemaakt, ziet u de JSON voor de gegevensset in de **resultaten**. Anders wordt er een foutbericht weergegeven.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Uitvoergegevensset maken
De gekoppelde Azure SQL Database-service geeft de verbindingsreeks op die de Data Factory-service in runtime gebruikt om verbinding te maken met Azure SQL Database. De uitvoergegevensset van de SQL-tabel (OututDataset) die u in deze stap hebt gemaakt, geeft de tabel in de database op waarnaar de gegevens uit de blobopslag worden gekopieerd.

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensset is gemaakt, ziet u de JSON voor de gegevensset in de **resultaten**. Anders wordt er een foutbericht weergegeven.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>Pijplijn maken
In deze stap maakt u een pijplijn met een **kopieeractiviteit** die gebruikmaakt van **AzureBlobInput** als invoer en **AzureSqlOutput** als uitvoer.

Momenteel is de uitvoergegevensset dat wat de planning aanstuurt. In deze zelfstudie is de uitvoergegevensset geconfigureerd voor het produceren van een segment eenmaal per uur. De pijplijn heeft een begintijd en eindtijd die één dag uit elkaar liggen, ofwel 24 uur. Daarom worden 24 segmenten van de uitvoergegevensset door de pijplijn geproduceerd. 

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensset is gemaakt, ziet u de JSON voor de gegevensset in de **resultaten**. Anders wordt er een foutbericht weergegeven.  

    ```PowerShell   
    Write-Host $results
    ```

**Gefeliciteerd!** U hebt een Azure-data factory gemaakt met een pijplijn die gegevens van Azure-blobopslag kopieert naar de Azure SQL Database.

## <a name="monitor-pipeline"></a>De pijplijn bewaken
In deze stap gebruikt u Data Factory-REST API voor het bewaken van segmenten die worden geproduceerd door de pijplijn.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Controleer of de begin- en eindtijden die zijn opgegeven in de volgende opdracht overeenkomen met de begin- en eindtijden van de pijplijn. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Voer de Invoke-Command en de volgende uit totdat u het segment in de status **Gereed** of **Mislukt** ziet. Als het segment de status Gereed heeft, controleert u de uitvoergegevens in de **emp**-tabel in Azure SQL Database. 

Voor elk segment worden twee rijen gegevens uit het bronbestand gekopieerd naar de emp-tabel in Azure SQL Database. U ziet daarom 24 nieuwe records in de emp-tabel wanneer alle segmenten zijn verwerkt (in de status Gereed). 

## <a name="summary"></a>Samenvatting
In deze zelfstudie hebt u REST API gebruikt om een Azure-data factory te maken voor het kopiëren van gegevens van een Azure-blob naar Azure SQL Database. In deze zelfstudie hebt u de volgende hoofdstappen uitgevoerd:  

1. U hebt een Azure-**gegevensfactory** gemaakt.
2. U hebt **gekoppelde services** gemaakt:
   1. Een gekoppelde Azure Storage-service om uw Azure Storage-account te koppelen dat invoergegevens bevat.     
   2. Een gekoppelde Azure SQL-service om uw database te koppelen die de uitvoergegevens bevat. 
3. U hebt **gegevenssets** gemaakt waarin de invoer- en uitvoergegevens van pijplijnen worden beschreven.
4. U hebt een **pijplijn** gemaakt met een kopieeractiviteit met BlobSource als bron en SqlSink als sink. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u voor een kopieerbewerking Azure-blobopslag gebruikt als brongegevensarchief en Azure SQL Database als doelgegevensarchief. De volgende tabel bevat een lijst met gegevensarchieven die worden ondersteund als bron en doel voor de kopieeractiviteit: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Klik op de koppeling voor de gegevensopslag in de tabel voor meer informatie over het kopiëren van gegevens naar/uit een gegevensarchief.
