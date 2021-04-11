---
title: Uw eerste gegevensfactory bouwen (REST)
description: In deze zelfstudie maakt u een Azure Data Factory-voorbeeldpijplijn met behulp van de Data Factory-REST API.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.date: 11/01/2017
ms.openlocfilehash: eb9ce65985521fb3f82032f04aec62538a157639
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785459"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Zelfstudie: Uw eerste Azure-gegevensfactory bouwen met de Data Factory-REST API
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sjabloon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [Quickstart: een gegevensfactory maken met Azure Data Factory](../quickstart-create-data-factory-rest-api.md).

In dit artikel gebruikt u de Data Factory-REST API voor het maken van uw eerste Azure-gegevensfactory. Als u de zelfstudie wilt volgen met andere hulpprogramma's/SDK's, selecteert u een van de opties uit de vervolgkeuzelijst.

De pijplijn in deze zelfstudie heeft één activiteit: **HDInsight Hive-activiteit**. Deze activiteit voert een Hive-script uit op een Azure HDInsight-cluster dat invoergegevens transformeert om uitvoergegevens te produceren. De pijplijn is gepland on één keer per maand tussen de opgegeven begin- en eindtijd te worden uitgevoerd.

> [!NOTE]
> Dit artikel omvat niet alles over de REST API. Zie [Naslaginformatie voor Data Factory-REST API](/rest/api/datafactory/) voor uitgebreide documentatie over REST API.
> 
> Een pijplijn kan meer dan één activiteit hebben. Ook kunt u twee activiteiten koppelen (de ene activiteit na de andere laten uitvoeren) door de uitvoergegevensset van één activiteit in te stellen als invoergegevensset voor een andere activiteit. Zie [Planning en uitvoering in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) voor meer informatie.


## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Lees het artikel [Overzicht van de zelfstudie](data-factory-build-your-first-pipeline.md) en voer de **vereiste** stappen uit.
* Installeer [Curl](https://curl.haxx.se/dlwiz/) op uw computer. U kunt in het hulpprogramma CURL REST-opdrachten gebruiken om een gegevensfactory te maken.
* Volg de instructies in [dit artikel](../../active-directory/develop/howto-create-service-principal-portal.md) voor het volgende:
  1. Maak een webtoepassing met de naam **ADFGetStartedApp** in Azure Active Directory.
  2. Haal de **client-id** en **geheime sleutel** op.
  3. Haal de **tenant-id** op.
  4. Wijs de **ADFGetStartedApp**-toepassing toe aan de rol **Inzender Data Factory**.
* Installeer [Azure PowerShell](/powershell/azure/).
* Start **PowerShell** en voer de volgende opdracht uit. Houd Azure PowerShell open tot het einde van deze zelfstudie. Als u het programma sluit en opnieuw opent, moet u de opdrachten opnieuw uitvoeren.
  1. Voer **Connect-AzAccount** uit en geef de gebruikersnaam en het wachtwoord op die u gebruikt om u aan te melden bij de Azure-portal.
  2. Voer **Get-AzSubscription** uit om alle abonnementen voor dit account weer te geven.
  3. Voer **Get-AzSubscription -SubscriptionName NameOfAzureSubscription | Set AzContext** uit om het abonnement te selecteren waarmee u wilt werken. Vervang **NameOfAzureSubscription** door de naam van uw Azure-abonnement.
* Maak een Azure-resourcegroep met de naam **ADFTutorialResourceGroup** door de volgende opdracht uit te voeren in PowerShell:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

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
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Vervang **accountname** en **accountkey** door de naam en sleutel van uw Azure Storage-account. Zie [Toegangssleutels voor opslagaccounts beheren](../../storage/common/storage-account-keys-manage.md) voor informatie over het ophalen van uw toegangssleutel voor opslag.
>
>

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

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

```JSON
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

| Eigenschap | Beschrijving |
|:--- |:--- |
| clusterSize |Grootte van het HDInsight-cluster. |
| timeToLive |Geeft aan hoelang het HDInsight-cluster inactief moet zijn voordat het wordt verwijderd. |
| linkedServiceName |Geeft het opslagaccount aan dat wordt gebruikt voor het opslaan van de logboeken die door HDInsight worden gegenereerd |

Houd rekening met de volgende punten:

* Met de bovenstaande JSON maakt Data Factory voor u een HDInsight-cluster **op basis van Linux**. Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie.
* U kunt **uw eigen HDInsight-cluster** gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) voor meer informatie.
* Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit gedrag is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment wordt verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid.

    Naarmate er meer segmenten worden verwerkt, verschijnen er meer containers in uw Azure-blobopslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers worden als volgt opgebouwd: adf **naamvanuwgegevensfactory**-**naamvangekoppeldeservice**-datum-/tijdstempel. Gebruik hulpprogramma's zoals [Microsoft Azure Storage Explorer](https://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.

Zie [Gekoppelde on-demand HDInsight-service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie.

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

Met de JSON wordt een gegevensset gedefinieerd met de naam **AzureBlobInput**. Deze bevat de invoergegevens voor een activiteit in de pijplijn. Bovendien wordt hiermee aangegeven dat de invoergegevens zich bevinden in de blobcontainer met de naam **adfgetstarted** en in de map met de naam **inputdata**

De volgende tabel bevat beschrijvingen van de JSON-eigenschappen die in het codefragment worden gebruikt:

| Eigenschap | Beschrijving |
|:--- |:--- |
| type |De eigenschap type wordt ingesteld op AzureBlob, omdat de gegevens zich in de Azure-blobopslag bevinden. |
| linkedServiceName |Deze eigenschap verwijst naar de StorageLinkedService die u eerder hebt gemaakt. |
| fileName |Deze eigenschap is optioneel. Als u deze eigenschap niet opgeeft, worden alle bestanden uit folderPath gekozen. In dit geval wordt alleen input.log verwerkt. |
| type |Omdat de logboekbestanden tekstbestanden zijn, gebruiken we TextFormat. |
| columnDelimiter |Kolommen in de logboekbestanden worden gescheiden door een komma (,) |
| frequency/interval |Als frequency wordt ingesteld op Month en de interval 1 is, betekent dat dat de invoersegmenten één keer per maand beschikbaar worden gemaakt. |
| external |Deze eigenschap wordt ingesteld op true als de invoergegevens niet worden gegenereerd door de Data Factory-service. |

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Met de JSON wordt een gegevensset gedefinieerd met de naam **AzureBlobOutput**. Deze bevat de uitvoergegevens voor een activiteit in de pijplijn. Bovendien wordt hiermee aangegeven dat de resultaten worden opgeslagen in de blobcontainer met de naam **adfgetstarted** en in de map met de naam **partitioneddata**. In het gedeelte **availability** wordt opgegeven dat de uitvoergegevensset op maandelijkse basis wordt geproduceerd.

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> Vervang **storageaccountname** door de naam van uw Azure Storage-account.
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "HDInsightOnDemandLinkedService"
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

In het JSON-fragment maakt u een pijplijn die bestaat uit een enkele activiteit waarvoor gebruik wordt gemaakt van Hive om gegevens in een HDInsight-cluster te verwerken.

Het Hive-scriptbestand **partitionweblogs.hql** wordt opgeslagen in het Azure-opslagaccount (opgegeven door de scriptLinkedService met de naam **StorageLinkedService**) en in de map **script** van de container **adfgetstarted**.

In het gedeelte **defines** worden de runtime-instellingen aangegeven die als Hive-configuratiewaarden worden doorgegeven aan het Hive-script (bijvoorbeeld ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

Met de eigenschappen **start** en **end** van de pijplijn wordt opgegeven in welke periode de pijplijn actief is.

In de activiteits-JSON geeft u op dat het Hive-script wordt uitgevoerd in de berekening die is opgegeven door de **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [!NOTE]
> Zie 'Pijplijn JSON' in [Pijplijnen en activiteiten in Azure Data Factory](data-factory-create-pipelines.md) voor meer informatie over de JSON-eigenschappen die in het voorgaande voorbeeld worden gebruikt.
>
>

## <a name="set-global-variables"></a>Globale variabelen instellen
Voer in Azure PowerShell de volgende opdrachten uit nadat u de waarden hebt vervangen door uw eigen waarden:

> [!IMPORTANT]
> Zie de sectie [Vereisten](#prerequisites) voor instructies over het verkrijgen van de client-id, het clientgeheim, de tenant-id en de abonnements-id.
>
>

```powershell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>Verifiëren met AAD

```powershell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>Een gegevensfactory maken
In deze stap maakt u een Azure-gegevensfactory met de naam **FirstDataFactoryREST**. Een gegevensfactory kan één of meer pijplijnen hebben. Een pijplijn kan één of meer activiteiten bevatten. Bijvoorbeeld een kopieeractiviteit om gegevens van een bron- naar een doelgegevensopslagplaats te kopiëren en een HDInsight Hive-activiteit om een Hive-script uit te voeren voor het transformeren van gegevens. Voer de volgende opdracht uit om de gegevensfactory te maken:

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    Bevestig dat de naam van de gegevensfactory die u hier opgeeft (ADFCopyTutorialDF) overeenkomt met de naam die is opgegeven in de **datafactory.json**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensfactory is gemaakt, ziet u de JSON voor de gegevensfactory in de **resultaten**. Anders wordt er een foutbericht weergegeven.

    ```powershell
    Write-Host $results
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de volgende fout in de resultaten ziet: **Data factory name “FirstDataFactoryREST” is not available** in de resultaten ziet, voert u de volgende stappen uit:
  1. Wijzig de naam (bijvoorbeeld uwnaamFirstDataFactoryREST) in het bestand **datafactory.json**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  2. In de eerste opdracht waar aan de **$cmd**-variabele een waarde is toegewezen, vervangt u FirstDataFactoryREST door de nieuwe naam en voert u de opdracht uit.
  3. Voer de volgende twee opdrachten uit voor het aanroepen van de REST API om de gegevensfactory te maken en de resultaten van de bewerking af te drukken.
* Als u Data Factory-exemplaren wilt maken, moet u bijdrager/beheerder zijn van het Azure-abonnement
* De naam van de gegevensfactory wordt in de toekomst mogelijk geregistreerd als DNS-naam en wordt daarmee ook voor iedereen zichtbaar.
* Als de volgende foutmelding wordt weergegeven: '**Dit abonnement is niet geregistreerd voor gebruik van de naamruimte Microsoft.DataFactory**', voert u een van de volgende stappen uit en probeert u opnieuw te publiceren:

  * Voer in Azure PowerShell de volgende opdracht uit om de Data Factory-provider te registreren:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      U kunt de volgende opdracht uitvoeren om te bevestigen dat de Data Factory-provider is geregistreerd:
    ```powershell
    Get-AzResourceProvider
    ```
  * Meld u bij de [Azure Portal](https://portal.azure.com) aan met behulp van het Azure-abonnement en navigeer naar een Data Factory-blade of maak een gegevensfactory in de Azure Portal. Door deze actie wordt de provider automatisch voor u geregistreerd.

Voordat u een pijplijn maakt, moet u eerst enkele Data Factory-entiteiten maken. U maakt eerst gekoppelde services om gegevensopslag/berekeningen te koppelen aan uw gegevensopslag, vervolgens definieert u welke invoer- en uitvoergegevenssets de gegevens in de gekoppelde gegevensopslag vertegenwoordigen.

## <a name="create-linked-services"></a>Gekoppelde services maken
In deze stap koppelt u uw Azure Storage-account en een on-demand Azure HDInsight-cluster aan uw gegevensfactory. Het Azure Storage-account bevat de in- en uitvoergegevens van de pijplijn in dit voorbeeld. De gekoppelde HDInsight-service wordt gebruikt om een Hive-script uit te voeren dat is opgegeven in de activiteit van de pijplijn in dit voorbeeld.

### <a name="create-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken
In deze stap koppelt u uw Azure Storage-account aan uw gegevensfactory. Bij deze zelfstudie gebruikt u hetzelfde Azure Storage-account om invoer- en uitvoergegevens en het HQL-scriptbestand op te slaan.

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gekoppelde service is gemaakt, ziet u de JSON voor de gekoppelde service in de **resultaten**. Anders wordt er een foutbericht weergegeven.

    ```powershell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Een gekoppelde HDInsight-service maken
In deze stap koppelt u een on-demand HDInsight-cluster aan uw gegevensfactory. Het HDInsight-cluster wordt automatisch gemaakt tijdens runtime en wordt verwijderd wanneer het verwerken is voltooid en het cluster gedurende een opgegeven tijdsperiode niet actief is geweest. U kunt uw eigen HDInsight-cluster gebruiken in plaats van een on-demand HDInsight-cluster. Zie [Gekoppelde services berekenen](data-factory-compute-linked-services.md) voor meer informatie.

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gekoppelde service is gemaakt, ziet u de JSON voor de gekoppelde service in de **resultaten**. Anders wordt er een foutbericht weergegeven.

    ```powershell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Gegevenssets maken
In deze stap maakt u gegevenssets die de invoer- en uitvoergegevens voor Hive-verwerking vertegenwoordigen. Deze gegevenssets verwijzen naar de **StorageLinkedService** die u eerder in deze zelfstudie hebt gemaakt. De gekoppelde service verwijst naar een Azure-opslagaccount en in de gegevenssets vindt u de container, map en bestandsnaam in de opslag van de invoer- en uitvoergegevens.

### <a name="create-input-dataset"></a>Invoergegevensset maken
In deze stap maakt u de invoergegevensset die staat voor invoergegevens die worden opgeslagen in de Azure-blobopslag.

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensset is gemaakt, ziet u de JSON voor de gegevensset in de **resultaten**. Anders wordt er een foutbericht weergegeven.

    ```powershell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Uitvoergegevensset maken
In deze stap maakt u de uitvoergegevensset die staat voor uitvoergegevens die worden opgeslagen in de Azure-blobopslag.

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensset is gemaakt, ziet u de JSON voor de gegevensset in de **resultaten**. Anders wordt er een foutbericht weergegeven.

    ```powershell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>Pijplijn maken
In deze stap maakt u uw eerste pijplijn met een **HDInsightHive**-activiteit. Het invoersegment is maandelijks beschikbaar (frequentie: Maand, interval: 1), evenals het uitvoersegment. Ook de plannereigenschap van de activiteit wordt op maandelijks ingesteld. De instellingen voor de uitvoergegevensset en de activiteitenplanner moeten overeenkomen. Op dit moment wordt de planning gebaseerd op de uitvoergegevensset. Daarom moet u ook een uitvoergegevensset maken als er tijdens de activiteit geen uitvoer wordt geproduceerd. Als er voor de activiteit geen invoer nodig is, kunt u het maken van de invoergegevensset overslaan.

Controleer of u het bestand **input.log** ziet in de map **adfgetstarted/inputdata** in de Azure-blobopslag en voer de volgende opdracht uit om de pijplijn te implementeren. Aangezien de tijden voor **start** en **end** in het verleden vallen en **isPaused** is ingesteld op false, wordt de pijplijn (activiteit in de pijplijn) direct na het implementeren uitgevoerd.

1. Wijs de opdracht toe aan de variabele met de naam **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Voer de opdracht uit via **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Bekijk de resultaten. Als de gegevensset is gemaakt, ziet u de JSON voor de gegevensset in de **resultaten**. Anders wordt er een foutbericht weergegeven.

    ```powershell
    Write-Host $results
    ```
4. U hebt uw eerste pijplijn gemaakt met Azure PowerShell!

## <a name="monitor-pipeline"></a>De pijplijn bewaken
In deze stap gebruikt u Data Factory-REST API voor het bewaken van segmenten die worden geproduceerd door de pijplijn.

```powershell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> Het maken van een on-demand HDInsight-cluster duurt normaal gesproken enige tijd (ongeveer 20 minuten). Daarom kunt u ervan uitgaan dat het **ongeveer 30 minuten** duurt voordat het segment in de pijplijn is verwerkt.
>
>

Voer de Invoke-Command en de volgende uit totdat u het segment in de status **Gereed** of **Mislukt** ziet. Wanneer het segment de status Gereed heeft, controleert u de map **partitioneddata** in de container **adfgetstarted** in uw blobopslag voor de uitvoergegevens.  Het maken van een on-demand HDInsight-cluster duurt normaal gesproken enige tijd.

![Uitvoergegevens](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> Het invoerbestand wordt verwijderd zodra het segment is verwerkt. Als u het segment dus opnieuw wilt uitvoeren of als u de zelfstudie opnieuw wilt doorlopen, uploadt u het invoerbestand (input.log) naar de map met invoergegevens van de container adfgetstarted.
>
>

U kunt ook Azure Portal gebruiken voor het bewaken van segmenten en om eventuele problemen op te lossen. Zie voor meer informatie [Pijplijnen bewaken met de Azure Portal](data-factory-monitor-manage-pipelines.md).

## <a name="summary"></a>Samenvatting
In deze zelfstudie hebt u een Azure-gegevensfactory gemaakt voor het verwerken van gegevens. Dit hebt u gedaan door Hive-script uit te voeren op een HDInsight Hadoop-cluster. U hebt in de Azure Portal de Data Factory-editor gebruikt om de volgende stappen uit te voeren:

1. U hebt een Azure-**gegevensfactory** gemaakt.
2. U hebt twee **gekoppelde services** gemaakt:
   1. Een gekoppelde **Azure Storage**-service om te koppelen aan de Azure-blobopslag die de invoer-/uitvoerbestanden van de gegevensfactory bevat.
   2. Een gekoppelde on-demand **Azure HDInsight**-service om te koppelen aan een on-demand HDInsight Hadoop-cluster van de gegevensfactory. Azure Data Factory maakt op tijd een HDInsight Hadoop-cluster om invoergegevens te verwerken en uitvoergegevens te produceren.
3. U hebt twee **gegevenssets** gemaakt, waarin de invoer- en uitvoergegevens van de HDInsight Hive-activiteit in de pijplijn worden beschreven.
4. U hebt een **pijplijn** gemaakt met een **HDInsight Hive**-activiteit.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een pijplijn gemaakt met een transformatieactiviteit (HDInsight-activiteit) waarvoor een Hive-script wordt uitgevoerd op een on-demand Azure HDInsight-cluster. Meer informatie over het gebruiken van een kopieeractiviteit om gegevens van een Azure-blob te kopiëren naar Azure SQL vindt u in [Zelfstudie: gegevens van een Azure-blob kopiëren naar Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Zie ook
| Onderwerp | Beschrijving |
|:--- |:--- |
| [Naslaginformatie over de REST-API voor Data Factory](/rest/api/datafactory/) |Zie de uitgebreide documentatie over Data Factory-cmdlets |
| [Pijplijnen](data-factory-create-pipelines.md) |Met behulp van dit artikel krijgt u inzicht in de pijplijnen en activiteiten in Azure Data Factory en in de wijze waarop u deze kunt gebruiken om end-to-end gegevensgestuurde werkstromen te maken voor uw scenario of bedrijf. |
| [Gegevenssets](data-factory-create-datasets.md) |Op basis van dit artikel krijgt u inzicht in de gegevenssets in Azure Data Factory. |
| [Plannen en uitvoeren](data-factory-scheduling-and-execution.md) |In dit artikel wordt uitleg gegeven over de plannings- en uitvoeringsaspecten van het Azure Data Factory-toepassingsmodel. |
| [Pijplijnen bewaken en beheren met de app voor bewaking en beheer](data-factory-monitor-manage-app.md) |In dit artikel wordt beschreven hoe u pijplijnen bewaakt en beheert en hoe u fouten hierin oplost met de app voor bewaking en beheer. |
