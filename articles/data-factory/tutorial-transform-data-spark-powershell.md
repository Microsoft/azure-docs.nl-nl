---
title: 'Gegevens transformeren met behulp van Apache Spark in Azure Data Factory '
description: Deze zelfstudie biedt stapsgewijze instructies voor het transformeren van gegevens met behulp van Spark-activiteit in Azure Data Factory.
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: cf425ecb1a8aebf594828bfba50e9aba8ca9dd7e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377615"
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Gegevens transformeren in de cloud met behulp van Spark-activiteit in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie gebruikt u Azure PowerShell om een Data Factory-pijplijn te maken waarmee gegevens worden getransformeerd met behulp van Spark-activiteit en een gekoppelde HDInsight-service op aanvraag. In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken. 
> * Gekoppelde services maakt en implementeert.
> * Een pijplijn maakt en implementeert. 
> * Een pijplijnuitvoering starten.
> * De pijplijnuitvoering controleert.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Een Azure Storage-account**. U maakt een Python-script en een invoerbestand, en upload deze naar de Azure-opslag. De uitvoer van het Spark-programma wordt opgeslagen in dit opslagaccount. Het Spark-cluster op aanvraag gebruikt hetzelfde opslagaccount als de primaire opslag.  
* **Azure PowerShell**. Volg de instructies in [How to install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Python-script uploaden naar het Blob-opslagaccount
1. Maak een Python-bestand met de naam **WordCount_Spark.py** met de volgende inhoud: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Vervang **&lt;storageaccountname&gt;** door de naam van uw Azure Storage-account. Sla het bestand vervolgens op. 
3. Maak in de Azure Blob-opslag een container met de naam **adftutorial** als deze nog niet bestaat. 
4. Maak een map met de naam **spark**.
5. Maak in de map **spark** een submap met de naam **script**. 
6. Upload het bestand **WordCount_Spark.py** naar de submap **script**. 


### <a name="upload-the-input-file"></a>Invoerbestand uploaden
1. Maak een bestand met de naam **minecraftstory.txt** met wat tekst. In het Spark-programma wordt het aantal woorden in deze tekst geteld. 
2. Maak in de map `spark` een submap met de naam `inputfiles`. 
3. Upload de `minecraftstory.txt` naar de submap`inputfiles`. 

## <a name="author-linked-services"></a>Gekoppelde services maken
In deze sectie maakt u twee gekoppelde services: 
    
- Een gekoppelde Azure Storage-service waarmee een Azure Storage-account wordt gekoppeld aan de gegevensfactory. Deze opslag wordt gebruikt voor het HDInsight-cluster op aanvraag. Het bevat ook het Spark-script dat moet worden uitgevoerd. 
- Een gekoppelde HDInsight-service op aanvraag. In Azure Data Factory wordt automatisch een HDInsight-cluster gemaakt, het Spark-programma uitgevoerd, en het HDInsight-cluster vervolgens verwijderd als het gedurende een vooraf geconfigureerde tijd inactief is. 

### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Maak een JSON-bestand met behulp van de gewenste editor, kopieer de volgende JSON-definitie van een gekoppelde Azure Storage-service en sla het bestand op als **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      }
    }
}
```
Werk &lt;storageAccountName&gt; en &lt;storageAccountKey&gt; bij met de naam en de sleutel van uw Azure Storage-account. 


### <a name="on-demand-hdinsight-linked-service"></a>Gekoppelde HDInsight-service op aanvraag
Maak een JSON-bestand met behulp van de gewenste editor, kopieer de volgende JSON-definitie van een gekoppelde Azure HDInsight-service en sla het bestand op als **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Werk de waarden voor de volgende eigenschappen bij in de definitie van de gekoppelde service: 

- **hostSubscriptionId**. Vervang &lt;subscriptionID&gt; door de id van uw Azure-abonnement. Het HDInsight-cluster op aanvraag wordt gemaakt in dit abonnement. 
- **tenant**. Vervang &lt;tenantID&gt; door de id van uw Azure-tenant. 
- **servicePrincipalId**, **servicePrincipalKey**. Vervang &lt;servicePrincipalID&gt; en &lt;servicePrincipalKey&gt; door de id en de sleutel van de service-pincipal in de Azure Active-directory. Deze service-principal moet lid zijn van de rol Inzender van het abonnement of de resourcegroep waarin het cluster is gemaakt. Zie [Een Azure Active Directory-toepassing en service-principal maken](../active-directory/develop/howto-create-service-principal-portal.md) voor details. Het **Service-principal-id** is gelijk aan het *toepassings-id* en een **Service-principalsleutel** is gelijk aan de waarde voor een *Clientgeheim*.
- **clusterResourceGroup**. Vervang &lt;resourceGroupOfHDICluster&gt; door de naam van de resourcegroep waarin de HDInsight-cluster moet worden gemaakt. 

> [!NOTE]
> Voor Azure HDInsight geldt een beperking voor het totale aantal kernen dat u kunt gebruiken in elke Azure-regio die wordt ondersteund. Het HDInsight-cluster voor de gekoppelde HDInsight-service op aanvraag wordt op dezelfde locatie in de Azure Storage-opslag gemaakt die wordt gebruikt als primaire opslag. Zorg ervoor dat u voldoende kerngeheugen hebt om het cluster goed te maken. Zie [Clusters instellen in HDInsight met Hadoop, Spark, Kafka en meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. 


## <a name="author-a-pipeline"></a>Een pijplijn maken 
In deze stap maakt u een nieuwe pijplijn met een Spark-activiteit. De activiteit maakt gebruik van het voorbeeld **Aantal woorden**. Download de inhoud van deze locatie als u dit nog niet hebt gedaan.

Maak een JSON-bestand in de gewenste editor, kopieer de volgende JSON-definitie van een pijplijndefinitie en sla het bestand op als **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Houd rekening met de volgende punten: 

- rootPath verwijst naar de map spark van de container adftutorial. 
- entryFilePath verwijst naar het bestand WordCount_Spark.py in de submap script van de map spark. 


## <a name="create-a-data-factory"></a>Een gegevensfactory maken 
U hebt een gekoppelde service en pijplijndefinities gemaakt in JSON-bestanden. Nu gaan we een data factory maken en de gekoppelde service en de JSON-bestanden van de pijp lijn implementeren met behulp van Power shell-cmdlets. Voer de volgende PowerShell-opdrachten één voor één: 

1. Stel één voor één de variabelen in.

    **Naam resourcegroep**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Naam data factory. De naam moet wereldwijd uniek zijn.** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Naam pijplijn**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Start **PowerShell**. Houd Azure PowerShell geopend tot het einde van deze snelstartgids. Als u het programma sluit en opnieuw opent, moet u de opdrachten opnieuw uitvoeren. Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

    Voer de volgende opdracht uit en geef de gebruikersnaam en het wachtwoord op waarmee u zich aanmeldt bij Azure Portal:
        
    ```powershell
    Connect-AzAccount
    ```        
    Voer de volgende opdracht uit om alle abonnementen voor dit account weer te geven:

    ```powershell
    Get-AzSubscription
    ```
    Voer de volgende opdracht uit om het abonnement te selecteren waarmee u wilt werken. Vervang **SubscriptionId** door de id van uw Azure-abonnement:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Maak de resourcegroep ADFTutorialResourceGroup. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Maak de gegevensfactory. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Voer de volgende opdracht uit om de uitvoer te zien: 

    ```powershell
    $df
    ```
5. Ga naar de map waarin u de JSON-bestanden hebt gemaakt, en voer de volgende opdracht uit om een gekoppelde Azure Storage-service te implementeren: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Voer de volgende opdracht uit om een gekoppelde Spark-service op aanvraag te implementeren: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Voer de volgende opdracht uit om een pijplijn te implementeren: 
       
    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Een pijplijnuitvoering starten en controleren  

1. Een pijplijnuitvoering starten. Ook wordt de id voor de pijplijnuitvoering vastgelegd voor toekomstige controle.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Voer het volgende script uit om continu de status van de pijplijnuitvoering te controleren totdat deze is voltooid.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Hier volgt een voorbeeld van de voorbeelduitvoering: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Bevestig dat er een map met de naam `outputfiles` is gemaakt in de map `spark` van container adftutorial met de uitvoer van het Spark-programma. 


## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. U hebt geleerd hoe u: 

> [!div class="checklist"]
> * Een data factory maken. 
> * Gekoppelde services maakt en implementeert.
> * Een pijplijn maakt en implementeert. 
> * Een pijplijnuitvoering starten.
> * De pijplijnuitvoering controleert.

Ga naar de volgende zelfstudie voor informatie over het transformeren van gegevens door een Hive-script uit te voeren in een Azure HDInsight-cluster in een virtueel netwerk. 

> [!div class="nextstepaction"]
> [Zelfstudie: gegevens transformeren met behulp van Hive in Azure Virtual Network](tutorial-transform-data-hive-virtual-network.md).





