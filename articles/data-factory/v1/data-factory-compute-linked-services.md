---
title: Reken omgevingen die worden ondersteund door Azure Data Factory versie 1
description: Meer informatie over reken omgevingen die u in Azure Data Factory-pijp lijnen (zoals Azure HDInsight) kunt gebruiken om gegevens te transformeren of te verwerken.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5f6fbcb73b4139c0a80ea8352071d8683c401d6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782942"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Reken omgevingen die worden ondersteund door Azure Data Factory versie 1
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gekoppelde services berekenen in](../compute-linked-services.md).

In dit artikel worden de reken omgevingen uitgelegd die u kunt gebruiken voor het verwerken of transformeren van gegevens. Ook vindt u hier informatie over verschillende configuraties (op aanvraag versus uw eigen) die Data Factory ondersteunt wanneer u gekoppelde services configureert die deze reken omgevingen koppelen aan een Azure data factory.

De volgende tabel bevat een lijst met reken omgevingen die worden ondersteund door Data Factory en de activiteiten die hierop kunnen worden uitgevoerd. 

| Compute-omgeving                      | Activiteiten                               |
| ---------------------------------------- | ---------------------------------------- |
| [Azure hdinsight-cluster op aanvraag](#azure-hdinsight-on-demand-linked-service) of [uw eigen HDInsight-cluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop-streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klassiek)](#azure-machine-learning-studio-classic-linked-service) | [Studio-activiteiten (klassiek): batch uitvoering en resource bijwerken](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Opgeslagen procedureactiviteit](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>HDInsight-versies die worden ondersteund in Data Factory
Azure HDInsight ondersteunt meerdere Hadoop-cluster versies die u op elk gewenst moment kunt implementeren. Elke ondersteunde versie maakt een specifieke versie van de HDP-distributie (Hortonworks data platform) en een set onderdelen in de distributie. 

Micro soft werkt de lijst met ondersteunde HDInsight-versies bij met de nieuwste Hadoop ecosysteem-onderdelen en-oplossingen. Zie [ondersteunde HDInsight-versies](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)voor gedetailleerde informatie.

> [!IMPORTANT]
> HDInsight-versie 3,3 voor Linux is buiten gebruik gesteld op 31 juli 2017. Data Factory versie 1-gekoppelde services van HDInsight op aanvraag zijn ontvangen tot 15 december 2017, om een nieuwere versie van HDInsight te testen en bij te werken. HDInsight op basis van Windows wordt buiten gebruik gesteld op 31 juli 2018.
>
> 

### <a name="after-the-retirement-date"></a>Na de datum van pensionering 

Na 15 december 2017:

- U kunt geen clusters van versie 3,3 (of lager) op basis van Linux maken met behulp van een gekoppelde on-demand HDInsight-service in Data Factory versie 1. 
- Als de [eigenschappen **OsType** en **Version**](#azure-hdinsight-on-demand-linked-service) niet expliciet zijn opgegeven in de JSON-definitie voor een bestaande Data Factory versie 1-gekoppelde HDInsight-service op aanvraag, wordt de standaard waarde gewijzigd van **versie = 3.1, osType = Windows** naar **versie = \<latest HDI default version\> ( https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) , osType = Linux**.

Na 31 juli 2018:

- U kunt geen versie van op Windows gebaseerde HDInsight-clusters meer maken met behulp van een gekoppelde on-demand HDInsight-service in Data Factory versie 1. 

### <a name="recommended-actions"></a>Aanbevolen acties

- Om ervoor te zorgen dat u de nieuwste Hadoop ecosysteem-onderdelen en-oplossingen kunt gebruiken, werkt u de [ **osType** -en **versie** -eigenschappen](#azure-hdinsight-on-demand-linked-service) bij van de betrokken Data Factory versie 1 van de on-demand hdinsight-service definities op aanvraag naar nieuwere op Linux gebaseerde hdinsight-versies (hdinsight 3,6). 
- Vóór 15 december 2017 kunt u de activiteiten versie 1 Hive, Pig, MapReduce en Hadoop streaming Data Factory testen die verwijzen naar de betreffende gekoppelde service. Zorg ervoor dat deze compatibel zijn met de nieuwe standaard waarden voor **osType** en **Version** (**versie = 3.6**, **osType = Linux**) of de expliciete HDInsight-versie en het type besturings systeem waarnaar u een upgrade uitvoert. 
  Zie voor meer informatie over compatibiliteit [migreren van een HDInsight-cluster op basis van Windows naar een Linux-cluster](../../hdinsight/index.yml) en [Wat zijn de Hadoop-onderdelen en versies die beschikbaar zijn met HDInsight?](../../hdinsight/hdinsight-component-versioning.md). 
- Als u wilt door gaan met het gebruik van een Data Factory versie 1 gekoppelde HDInsight-service op aanvraag voor het maken van op Windows gebaseerde HDInsight-clusters, stelt u vóór 15 december 2017 expliciet **osType** in op **Windows** . We raden u aan om te migreren naar HDInsight-clusters op basis van Linux vóór 31 juli 2018. 
- Als u een gekoppelde HDInsight-service op aanvraag gebruikt voor het uitvoeren van Data Factory versie 1 DotNet aangepaste activiteit, werkt u de JSON-definitie van de DotNet-aangepaste activiteit bij in plaats daarvan een Azure Batch gekoppelde service te gebruiken. Zie [aangepaste activiteiten gebruiken in een Data Factory pijp lijn](./data-factory-use-custom-activities.md)voor meer informatie. 

> [!Note]
> Als u uw bestaande, voor uw eigen cluster HDInsight gekoppelde apparaat gebruikt in Data Factory versie 1 of een aan de gekoppelde on-demand HDInsight-service op aanvraag in Azure Data Factory, hoeft u geen actie te ondernemen. In deze scenario's wordt het meest recente versie-ondersteunings beleid van HDInsight-clusters al afgedwongen. 
>
> 


## <a name="on-demand-compute-environment"></a>Compute-omgeving op aanvraag
In een configuratie op aanvraag, Data Factory volledig beheer van de reken omgeving. Data Factory maakt automatisch de compute-omgeving voordat een taak wordt verzonden voor het verwerken van gegevens. Wanneer de taak is voltooid, wordt de berekenings omgeving door Data Factory verwijderd. 

U kunt een gekoppelde service maken voor een compute-omgeving op aanvraag. Gebruik de gekoppelde service om de compute-omgeving te configureren en om gedetailleerde instellingen te beheren voor het uitvoeren van taken, Cluster beheer en acties voor Boots trap.

> [!NOTE]
> Momenteel wordt de configuratie op aanvraag alleen ondersteund voor HDInsight-clusters.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Een gekoppelde Azure HDInsight-service op aanvraag
Data Factory kunt automatisch een HDInsight-cluster op basis van Windows of Linux maken voor het verwerken van gegevens. Het cluster wordt gemaakt in dezelfde regio als het opslag account dat is gekoppeld aan het cluster. Gebruik de eigenschap JSON **linkedServiceName** om het cluster te maken.

Houd rekening met de volgende *belang rijke* punten over gekoppelde HDInsight-service op aanvraag:

* Het HDInsight-cluster op aanvraag wordt niet weer gegeven in uw Azure-abonnement. De Data Factory-service beheert het HDInsight-cluster op aanvraag namens u.
* De logboeken voor taken die worden uitgevoerd op een HDInsight-cluster op aanvraag, worden gekopieerd naar het opslag account dat is gekoppeld aan het HDInsight-cluster. Als u deze logboeken wilt openen, gaat u in het Azure Portal naar het deel venster **Details van activiteit uitvoeren** . Zie [pijp lijnen bewaken en beheren](data-factory-monitor-manage-pipelines.md)voor meer informatie.
* Er worden alleen kosten in rekening gebracht voor het tijdstip waarop het HDInsight-cluster taken uitvoert.

> [!IMPORTANT]
> Het inrichten van een on-demand HDInsight-cluster duurt meestal *20 minuten* of langer.
>
> 

### <a name="example"></a>Voorbeeld
De volgende JSON definieert een gekoppelde HDInsight-service op aanvraag van Linux. Data Factory maakt automatisch een HDInsight *-cluster op basis van Linux* wanneer het een gegevens segment verwerkt. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Het HDInsight-cluster maakt een *standaard container* in de Azure Blob-opslag die u opgeeft in de JSON **linkedServiceName** -eigenschap. Standaard verwijdert HDInsight deze container niet wanneer het cluster wordt verwijderd. In een gekoppelde HDInsight-service op aanvraag wordt een HDInsight-cluster gemaakt wanneer een segment moet worden verwerkt, tenzij er een bestaand Live cluster (**timeToLive**) is. Het cluster wordt verwijderd wanneer de verwerking is voltooid. 
>
> Naarmate er meer segmenten worden verwerkt, ziet u veel containers in uw Blob-opslag. Als u de containers voor het oplossen van problemen niet nodig hebt, kunt u de containers verwijderen om de opslag kosten te verlagen. De namen van deze containers volgen een patroon: `adf<your Data Factory name>-<linked service name>-<date and time>`. U kunt een hulp programma als [Microsoft Azure Storage Explorer](https://storageexplorer.com/) gebruiken om containers in Blob Storage te verwijderen.
>
> 

### <a name="properties"></a>Eigenschappen
| Eigenschap                     | Beschrijving                              | Vereist |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Stel de eigenschap type in op **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Het aantal werk-en gegevens knooppunten in het cluster. Het HDInsight-cluster wordt gemaakt met twee hoofd knooppunten, naast het aantal worker-knoop punten dat u voor deze eigenschap opgeeft. De knoop punten hebben een grootte Standard_D3, die vier kernen heeft. Een cluster van 4 worker-knoop punten neemt 24 kernen (4 \* 4 = 16 kernen voor worker-knoop punten, plus 2 \* 4 = 8 kernen voor hoofd knooppunten). Zie op [Linux gebaseerde Hadoop-clusters maken in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over de laag Standard_D3. | Yes      |
| timeToLive                   | De toegestane tijd niet-actief voor het HDInsight-cluster op aanvraag. Hiermee geeft u op hoelang het HDInsight-cluster op aanvraag actief blijft wanneer de uitvoering van een activiteit is voltooid, als er geen andere actieve taken in het cluster zijn.<br /><br />Als bijvoorbeeld het uitvoeren van een activiteit zes minuten duurt en **timeToLive** is ingesteld op 5 minuten, blijft het cluster 5 minuten na de 6 minuten van verwerking van de uitvoering van de activiteit actief. Als er een andere uitvoering van de activiteit wordt uitgevoerd in het venster van 6 minuten, wordt deze verwerkt door hetzelfde cluster.<br /><br />Het maken van een HDInsight-cluster op aanvraag is een dure bewerking (dit kan enige tijd duren). Gebruik deze instelling als dat nodig is om de prestaties van een data factory te verbeteren door opnieuw gebruik te maken van een on-demand HDInsight-cluster.<br /><br />Als u de waarde **timeToLive** instelt op **0**, wordt het cluster verwijderd zodra de uitvoering van de activiteit is voltooid. Als u echter een hoge waarde instelt, kan het cluster niet-actief blijven, wat leidt tot hoge kosten. Het is belang rijk dat u de juiste waarde instelt op basis van uw behoeften.<br /><br />Als de **timeToLive** -waarde op de juiste wijze is ingesteld, kunnen meerdere pijp lijnen het exemplaar van het HDInsight-cluster op aanvraag delen. | Yes      |
| versie                      | De versie van het HDInsight-cluster. Zie [ondersteunde hdinsight-versies](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)voor toegestane hdinsight-versies. Als deze waarde niet is opgegeven, wordt de [meest recente HDI-standaard versie](../../hdinsight/hdinsight-component-versioning.md) gebruikt. | No       |
| linkedServiceName            | De Azure Storage gekoppelde service die moet worden gebruikt door het cluster op aanvraag om gegevens op te slaan en te verwerken. Het HDInsight-cluster wordt gemaakt in dezelfde regio als dit opslag account.<p>Op dit moment kunt u geen HDInsight-cluster op aanvraag maken dat gebruikmaakt van Azure Data Lake Store als opslag. Als u de resultaat gegevens van HDInsight-verwerking in Data Lake Store wilt opslaan, gebruikt u Kopieer activiteit om de gegevens uit de Blobopslag te kopiëren naar Data Lake Store. </p> | Yes      |
| additionalLinkedServiceNames | Hiermee geeft u extra opslag accounts voor de gekoppelde HDInsight-service op. Data Factory de opslag accounts namens u registreren. Deze opslag accounts moeten zich in dezelfde regio bevinden als het HDInsight-cluster. Het HDInsight-cluster wordt gemaakt in dezelfde regio als het opslag account dat is opgegeven door de eigenschap **linkedServiceName** . | No       |
| osType                       | Het type besturings systeem. Toegestane waarden zijn **Linux** en **Windows**. Als deze waarde niet is opgegeven, wordt **Linux** gebruikt.  <br /><br />We raden u ten zeerste aan Linux gebaseerde HDInsight-clusters te gebruiken. De datum van pensionering voor HDInsight in Windows is 31 juli 2018. | No       |
| hcatalogLinkedServiceName    | De naam van de gekoppelde Azure SQL-service die verwijst naar de HCatalog-data base. Het HDInsight-cluster op aanvraag wordt gemaakt met behulp van de SQL database als de meta Store. | No       |

#### <a name="example-linkedservicenames-json"></a>Voor beeld: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Geavanceerde eigenschappen
Voor een gedetailleerde configuratie van het HDInsight-cluster op aanvraag kunt u de volgende eigenschappen opgeven:

| Eigenschap               | Beschrijving                              | Vereist |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Hiermee geeft u de para meters voor de kern configuratie (core-site.xml) op voor het HDInsight-cluster dat moet worden gemaakt. | No       |
| hBaseConfiguration     | Hiermee geeft u de HBase-configuratie parameters (hbase-site.xml) voor het HDInsight-cluster. | No       |
| hdfsConfiguration      | Hiermee geeft u de HDFS-configuratie parameters (hdfs-site.xml) voor het HDInsight-cluster op. | No       |
| hiveConfiguration      | Hiermee geeft u de Hive-configuratie parameters (hive-site.xml) voor het HDInsight-cluster. | No       |
| mapReduceConfiguration | Hiermee geeft u de MapReduce-configuratie parameters (mapred-site.xml) voor het HDInsight-cluster. | No       |
| oozieConfiguration     | Hiermee geeft u de Oozie-configuratie parameters (oozie-site.xml) voor het HDInsight-cluster. | No       |
| stormConfiguration     | Hiermee geeft u de Storm-configuratie parameters (storm-site.xml) voor het HDInsight-cluster. | No       |
| yarnConfiguration      | Hiermee geeft u de garen configuratie parameters (yarn-site.xml) voor het HDInsight-cluster. | No       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Voor beeld: HDInsight-cluster configuratie op aanvraag met geavanceerde eigenschappen

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Knooppunt grootten
Als u de grootte van de hoofd-, gegevens-en ZooKeeper-knoop punten wilt opgeven, gebruikt u de volgende eigenschappen: 

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Hiermee stelt u de grootte van het hoofd knooppunt. De standaard waarde is **Standard_D3**. Zie [grootte van knoop punten opgeven](#specify-node-sizes)voor meer informatie. | No       |
| dataNodeSize      | Hiermee stelt u de grootte van het gegevens knooppunt in. De standaard waarde is **Standard_D3**. | No       |
| zookeeperNodeSize | Hiermee stelt u de grootte van het ZooKeeper-knoop punt. De standaard waarde is **Standard_D3**. | No       |

#### <a name="specify-node-sizes"></a>Knooppunt groottes opgeven
Zie [grootten van virtuele machines](../../virtual-machines/sizes.md)voor teken reeks waarden die u moet opgeven voor de eigenschappen die in de voor gaande sectie worden beschreven. De waarden moeten voldoen aan de cmdlets en Api's waarnaar wordt verwezen in de [grootte van virtuele machines](../../virtual-machines/sizes.md). De grote (standaard) gegevens knooppunt grootte heeft 7 GB geheugen. Dit is mogelijk niet voldoende voor uw scenario. 

Als u hoofd knooppunten en worker-knoop punten in D4-grootte wilt maken, geeft u **Standard_D4** op als de waarde voor de eigenschappen **headNodeSize** en **dataNodeSize** : 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Als u een onjuiste waarde voor deze eigenschappen instelt, ziet u mogelijk het volgende bericht:

  Kan het cluster niet maken. Uitzondering: Kan het cluster niet maken. Bewerking is mislukt met code 400. Cluster heeft status: 'Fout'. Bericht: ' PreClusterCreationValidationFailure '. 
  
Als u dit bericht ziet, moet u ervoor zorgen dat u de cmdlet-en API-namen gebruikt uit de tabel in de grootte van de [virtuele machine](../../virtual-machines/sizes.md).  

> [!NOTE]
> Data Factory biedt momenteel geen ondersteuning voor HDInsight-clusters die gebruikmaken van Data Lake Store als primair archief. Gebruik Azure Storage als de primaire opslag voor HDInsight-clusters. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Uw eigen Compute-omgeving
U kunt een bestaande Compute-omgeving registreren als een gekoppelde service in Data Factory. U beheert de compute-omgeving. De Data Factory-service gebruikt de compute-omgeving om activiteiten uit te voeren.

Dit type configuratie wordt ondersteund voor de volgende reken omgevingen:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (klassiek)
* Azure Data Lake Analytics
* Azure SQL Database, Azure Synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Gekoppelde Azure HDInsight-service
U kunt een gekoppelde HDInsight-service maken om uw eigen HDInsight-cluster te registreren bij Data Factory.

### <a name="example"></a>Voorbeeld

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| type              | Stel de eigenschap type in op **HDInsight**. | Yes      |
| clusterUri        | De URI van het HDInsight-cluster.        | Yes      |
| gebruikersnaam          | De naam van het gebruikers account dat moet worden gebruikt om verbinding te maken met een bestaand HDInsight-cluster. | Yes      |
| wachtwoord          | Het wacht woord voor het gebruikers account.   | Yes      |
| linkedServiceName | De naam van de gekoppelde opslag service die verwijst naar de Blob-opslag die wordt gebruikt door het HDInsight-cluster. <p>Op dit moment kunt u geen Data Lake Store gekoppelde service opgeven voor deze eigenschap. Als het HDInsight-cluster toegang heeft tot Data Lake Store, kunt u toegang krijgen tot gegevens in Data Lake Store van Hive-of Pig-scripts. </p> | Yes      |

## <a name="azure-batch-linked-service"></a>Azure Batch gekoppelde service
U kunt een batch-gekoppelde service maken om een batch-pool van virtuele machines (Vm's) te registreren bij een data factory. U kunt Microsoft .NET aangepaste activiteiten uitvoeren met behulp van batch of HDInsight.

Als u geen ervaring hebt met het gebruik van de batch-service:

* Meer informatie over de [basis principes van Azure batch](../../azure-sql/database/sql-database-paas-overview.md).
* Meer informatie over de cmdlet [New-AzureBatchAccount](/previous-versions/azure/mt125880(v=azure.100)) . Gebruik deze cmdlet om een batch-account te maken. U kunt ook het batch-account maken met behulp van de [Azure Portal](../../batch/batch-account-create-portal.md). Voor gedetailleerde informatie over het gebruik van de cmdlet raadpleegt u [Power shell gebruiken voor het beheren van een batch-account](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account).
* Meer informatie over de cmdlet [New-AzureBatchPool](/previous-versions/azure/mt125936(v=azure.100)) . Gebruik deze cmdlet om een batch-pool te maken.

### <a name="example"></a>Voorbeeld

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Voor de eigenschap **AccountName** , toevoegen **. \<region name\>** naar de naam van uw batch-account. Bijvoorbeeld:

```json
"accountName": "mybatchaccount.eastus"
```

Een andere optie is om het **batchUri** -eind punt op te geven. Bijvoorbeeld:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| type              | Stel de eigenschap type in op **AzureBatch**. | Yes      |
| accountName       | De naam van het batch-account.         | Yes      |
| accessKey         | De toegangs sleutel voor het batch-account.  | Yes      |
| poolName          | De naam van de groep Vm's.    | Yes      |
| linkedServiceName | De naam van de gekoppelde opslag service die is gekoppeld aan deze batch gekoppelde service. Deze gekoppelde service wordt gebruikt voor tijdelijke bestanden die nodig zijn om de activiteit uit te voeren en om uitvoerings logboeken voor de activiteit op te slaan. | Yes      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Gekoppelde service Azure Machine Learning Studio (klassiek)
U kunt een gekoppelde Azure Machine Learning Studio-Service (klassiek) maken om een studio (klassiek) batch Score-eind punt te registreren voor een data factory.

### <a name="example"></a>Voorbeeld

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Eigenschappen
| Eigenschap   | Beschrijving                              | Vereist |
| ---------- | ---------------------------------------- | -------- |
| Type       | Stel de eigenschap type in op **AzureML**. | Yes      |
| mlEndpoint | De batch Score-URL.                   | Yes      |
| apiKey     | De API van het gepubliceerde werkruimte model.     | Yes      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics gekoppelde service
U kunt een Data Lake Analytics gekoppelde service maken om een Data Lake Analytics compute-service aan een Azure-data factory te koppelen. De Data Lake Analytics U-SQL-activiteit in de pijp lijn verwijst naar deze gekoppelde service. 

In de volgende tabel worden de algemene eigenschappen beschreven die worden gebruikt in de JSON-definitie:

| Eigenschap                 | Beschrijving                              | Vereist                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Stel de eigenschap type in op **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | De naam van het Data Lake Analytics-account.  | Yes                                      |
| dataLakeAnalyticsUri | De Data Lake Analytics-URI.           | No                                       |
| subscriptionId       | De ID van het Azure-abonnement.                    | No<br /><br />(Als dit niet is opgegeven, wordt het data factory-abonnement gebruikt.) |
| resourceGroupName    | De naam van de Azure-resource groep.                | No<br /><br /> (Indien niet opgegeven, wordt de data factory resource groep gebruikt.) |

### <a name="authentication-options"></a>Verificatieopties
Voor uw Data Lake Analytics gekoppelde service kunt u kiezen tussen verificatie met behulp van een service-principal of een gebruikers referentie.

#### <a name="service-principal-authentication-recommended"></a>Service-Principal-verificatie (aanbevolen)
Als u Service-Principal-verificatie wilt gebruiken, moet u een toepassings entiteit registreren in Azure Active Directory (Azure AD). Ken vervolgens toegang tot Azure AD toe aan Data Lake Store. Zie [service-to-service-verificatie](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:
* Toepassings-id
* Toepassings sleutel 
* Tenant-id

Gebruik Service-Principal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap                | Beschrijving                              | Vereist |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | De client-ID van de toepassing.     | Yes      |
| servicePrincipalKey | De sleutel van de toepassing.           | Yes      |
| tenant              | De Tenant gegevens (domein naam of Tenant-ID) waar uw toepassing zich bevindt. Als u deze informatie wilt ophalen, houdt u de muis aanwijzer in de rechter bovenhoek van de Azure Portal. | Yes      |

**Voor beeld: Service-Principal-verificatie**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Verificatie van gebruikers referenties
Geef voor de verificatie van de gebruikers referenties voor Data Lake Analytics de volgende eigenschappen op:

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| autorisatie | In Data Factory editor selecteert u de knop **autoriseren** . Geef de referentie op waarmee de automatisch gegenereerde autorisatie-URL wordt toegewezen aan deze eigenschap. | Yes      |
| sessionId     | De OAuth-sessie-ID van de OAuth-autorisatie sessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u Data Factory editor gebruikt. | Yes      |

**Voor beeld: verificatie van de gebruikers referenties**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token verloop tijd
De autorisatie code die u hebt gegenereerd door de knop **autoriseren** te selecteren, verloopt na een ingesteld interval. 

Mogelijk wordt het volgende fout bericht weer gegeven wanneer het verificatie token verloopt: 

  Referentie bewerkings fout: invalid_grant-AADSTS70002: fout bij het valideren van referenties. AADSTS70008: de verleende toegangs toekenning is verlopen of ingetrokken. Tracerings-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 correlatie-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7-tijds tempel: 2015-12-15 21:09:31Z

De volgende tabel toont verval datums per gebruikers account type: 

| Gebruikers type                                | Verloopt na                            |
| :--------------------------------------- | :--------------------------------------- |
| Gebruikers accounts die *niet* worden beheerd door Azure AD (Hotmail, Live, enzovoort) | 12 uur.                                 |
| Gebruikers accounts die *worden* beheerd door Azure AD | 14 dagen na de laatste uitvoering van het segment. <br /><br />90 dagen: als een segment dat is gebaseerd op een op OAuth gebaseerde gekoppelde service ten minste één keer per 14 dagen wordt uitgevoerd. |

Als u deze fout wilt voor komen of oplossen, moet u autoriseren door de knop **autoriseren** te selecteren wanneer het token verloopt. Implementeer vervolgens de gekoppelde service opnieuw. U kunt ook met behulp van de volgende code waarden genereren voor de eigenschappen **SessionID** en **Authorization** via een programma:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Zie voor meer informatie over de Data Factory klassen die in dit code voorbeeld worden gebruikt:
* [Klasse AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [Klasse AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [Klasse AuthorizationSessionGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

Voeg een verwijzing naar Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll toe voor de klasse **WindowsFormsWebAuthenticationDialog** . 

## <a name="azure-sql-linked-service"></a>Een gekoppelde Azure SQL-service
U kunt een gekoppelde SQL-service maken en deze gebruiken met de [opgeslagen procedure activiteit](data-factory-stored-proc-activity.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie [Azure SQL-connector](data-factory-azure-sql-connector.md#linked-service-properties)voor meer informatie.

## <a name="azure-synapse-analytics-linked-service"></a>Gekoppelde Azure Synapse Analytics-Service
U kunt een gekoppelde Azure Synapse Analytics-service maken en deze gebruiken met de [opgeslagen procedure activiteit](data-factory-stored-proc-activity.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie [Azure Synapse Analytics connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)voor meer informatie.

## <a name="sql-server-linked-service"></a>SQL Server gekoppelde service
U kunt een SQL Server gekoppelde service maken en deze gebruiken met de [opgeslagen procedure activiteit](data-factory-stored-proc-activity.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie [SQL Server-connector](data-factory-sqlserver-connector.md#linked-service-properties)voor meer informatie.