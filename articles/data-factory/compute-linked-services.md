---
title: Reken omgevingen die worden ondersteund door Azure Data Factory
description: Reken omgevingen die kunnen worden gebruikt met Azure Data Factory pijp lijnen (zoals Azure HDInsight) om gegevens te transformeren of te verwerken.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.date: 05/08/2019
ms.openlocfilehash: 2aa7afebede3759221674c48dd66256e740575a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565942"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Reken omgevingen die worden ondersteund door Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden verschillende reken omgevingen uitgelegd die u kunt gebruiken om gegevens te verwerken of te transformeren. Ook vindt u hier informatie over verschillende configuraties (op aanvraag versus uw eigen configuratie) die door Data Factory worden ondersteund bij het configureren van gekoppelde services die deze reken omgevingen koppelen aan een Azure data factory.

De volgende tabel bevat een lijst met reken omgevingen die worden ondersteund door Data Factory en de activiteiten die hierop kunnen worden uitgevoerd. 

| Compute-omgeving                                          | Activiteiten                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Hdinsight-cluster op aanvraag](#azure-hdinsight-on-demand-linked-service) of [uw eigen hdinsight-cluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [varken](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop-streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Aangepast](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio (klassiek)](#azure-machine-learning-studio-classic-linked-service) | [Machine Learning Studio (klassieke) activiteiten: batch uitvoering en resource bijwerken](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Pijp lijn Azure Machine Learning uitvoeren](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Opgeslagen procedure](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [jar](transform-data-databricks-jar.md), [python](transform-data-databricks-python.md) |
| [Azure-functie](#azure-function-linked-service)         | [Azure function-activiteit](control-flow-azure-function-activity.md)
>  

## <a name="hdinsight-compute-environment"></a>HDInsight Compute-omgeving

Raadpleeg de onderstaande tabel voor meer informatie over de ondersteunde opslag typen die worden ondersteund door de service voor configuratie in on-demand en BYOC (uw eigen Compute-omgeving meenemen).

| In Compute-gekoppelde service | Eigenschapsnaam                | Description                                                  | Blob | ADLS Gen2 | Azure SQL Database | ADLS Gen 1 |
| ------------------------- | ---------------------------- | ------------------------------------------------------------ | ---- | --------- | ------------ | ---------- |
| Op aanvraag                 | linkedServiceName            | Azure Storage gekoppelde service die moet worden gebruikt door het cluster op aanvraag om gegevens op te slaan en te verwerken. | Ja  | Ja       | Nee           | Nee         |
|                           | additionalLinkedServiceNames | Hiermee worden extra opslag accounts voor de gekoppelde HDInsight-service opgegeven, zodat de Data Factory-service namens u kan worden geregistreerd. | Ja  | Nee        | Nee           | Nee         |
|                           | hcatalogLinkedServiceName    | De naam van de gekoppelde Azure SQL-service die verwijst naar de HCatalog-data base. Het HDInsight-cluster op aanvraag wordt gemaakt met behulp van de Azure-SQL database als de meta Store. | Nee   | Nee        | Ja          | Nee         |
| BYOC                      | linkedServiceName            | De verwijzing naar de Azure Storage gekoppelde service.                | Ja  | Ja       | Nee           | Nee         |
|                           | additionalLinkedServiceNames | Hiermee worden extra opslag accounts voor de gekoppelde HDInsight-service opgegeven, zodat de Data Factory-service namens u kan worden geregistreerd. | Nee   | Nee        | Nee           | Nee         |
|                           | hcatalogLinkedServiceName    | Een verwijzing naar de gekoppelde Azure SQL-service die verwijst naar de HCatalog-data base. | Nee   | Nee        | Nee           | Nee         |

### <a name="azure-hdinsight-on-demand-linked-service"></a>Een gekoppelde Azure HDInsight-service op aanvraag

In dit type configuratie wordt de computer omgeving volledig beheerd door de Azure Data Factory-service. Het wordt automatisch gemaakt door de Data Factory-service voordat een taak wordt verzonden om gegevens te verwerken en te worden verwijderd wanneer de taak is voltooid. U kunt een gekoppelde service maken voor de berekenings omgeving op aanvraag, deze configureren en gedetailleerde instellingen beheren voor taak uitvoering, Cluster beheer en acties voor Boots trap.

> [!NOTE]
> De configuratie op aanvraag wordt momenteel alleen ondersteund voor Azure HDInsight-clusters. Azure Databricks biedt ook ondersteuning voor taken op aanvraag met behulp van taak clusters. Zie [gekoppelde Azure databricks-service](#azure-databricks-linked-service)voor meer informatie.

De Azure Data Factory-service kan automatisch een HDInsight-cluster op aanvraag maken voor het verwerken van gegevens. Het cluster wordt gemaakt in dezelfde regio als het opslag account (linkedServiceName-eigenschap in de JSON) die aan het cluster is gekoppeld. Het opslag account `must` is een standaard Azure Storage-account voor algemeen gebruik. 

Houd rekening met de volgende **belang rijke** punten over gekoppelde HDInsight-service op aanvraag:

* Het HDInsight-cluster op aanvraag wordt gemaakt onder uw Azure-abonnement. U kunt het cluster in uw Azure Portal zien wanneer het cluster actief is. 
* De logboeken voor taken die worden uitgevoerd op een HDInsight-cluster op aanvraag worden gekopieerd naar het opslag account dat is gekoppeld aan het HDInsight-cluster. De clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword die in de definitie van de gekoppelde service zijn gedefinieerd, worden gebruikt voor aanmelding bij het cluster voor uitgebreidere probleem oplossing tijdens de levens cyclus van het cluster. 
* Er worden alleen kosten in rekening gebracht voor het moment waarop het HDInsight-cluster taken uitvoert.
* U kunt een **script actie** gebruiken met de gekoppelde Azure HDInsight-service op aanvraag.  

> [!IMPORTANT]
> Het vergt meestal **20 minuten** of meer om een Azure HDInsight-cluster op aanvraag in te richten.

#### <a name="example"></a>Voorbeeld

De volgende JSON definieert een gekoppelde HDInsight-service op aanvraag van Linux. De Data Factory-service maakt automatisch een HDInsight-cluster op **basis van Linux** om de vereiste activiteit te verwerken. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Het HDInsight-cluster maakt een **standaardcontainer** in de blobopslag die u hebt opgegeven in de JSON (**linkedServiceName**). HDInsight verwijdert deze container niet wanneer het cluster wordt verwijderd. Dit gedrag is standaard. Met een gekoppelde on-demand HDInsight-service wordt er steeds een HDInsight-cluster gemaakt wanneer er een segment moet worden verwerkt, tenzij er een bestaand livecluster is (**timeToLive**). Het cluster wordt verwijderd wanneer het verwerken is voltooid. 
>
> Naarmate er meer activiteit wordt uitgevoerd, ziet u veel containers in uw Azure Blob-opslag. Als u deze niet nodig hebt voor het oplossen van problemen met taken, kunt u ze verwijderen om de opslagkosten te verlagen. De namen van deze containers volgen een patroon: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Gebruik hulpprogramma's zoals [Microsoft Azure Storage Explorer](https://storageexplorer.com/) om containers in uw Azure-blobopslag te verwijderen.

#### <a name="properties"></a>Eigenschappen

| Eigenschap                     | Beschrijving                              | Vereist |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | De eigenschap type moet worden ingesteld op **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Aantal werk-en gegevens knooppunten in het cluster. Het HDInsight-cluster wordt gemaakt met 2 hoofd knooppunten, samen met het aantal worker-knoop punten dat u voor deze eigenschap opgeeft. De knoop punten zijn van grootte Standard_D3 met 4 kern geheugens. een cluster van 4 worker-knoop punten neemt dus 24 kernen (4 \* 4 = 16 kernen voor werk knooppunten, plus 2 \* 4 = 8 kernen voor hoofd knooppunten). Zie [clusters in HDInsight instellen met Hadoop, Spark, Kafka en meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. | Yes      |
| linkedServiceName            | Azure Storage gekoppelde service die moet worden gebruikt door het cluster op aanvraag om gegevens op te slaan en te verwerken. Het HDInsight-cluster wordt gemaakt in dezelfde regio als deze Azure Storage-account. Voor Azure HDInsight geldt een beperking voor het totale aantal kernen dat u kunt gebruiken in elke Azure-regio die wordt ondersteund. Zorg ervoor dat de Azure-regio voldoende kern quota heeft om aan de vereiste clusterSize te voldoen. Raadpleeg [clusters in HDInsight instellen met Hadoop, Spark, Kafka en meer](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie<p>Op dit moment kunt u geen HDInsight-cluster op aanvraag maken dat gebruikmaakt van een Azure Data Lake Storage (Gen 2) als opslag. Als u de resultaat gegevens van HDInsight-verwerking wilt opslaan in een Azure Data Lake Storage (Gen 2), gebruikt u een Kopieer activiteit om de gegevens van de Azure-Blob Storage te kopiëren naar de Azure Data Lake Storage (Gen 2). </p> | Yes      |
| clusterResourceGroup         | Het HDInsight-cluster wordt gemaakt in deze resource groep. | Yes      |
| timetolive                   | De toegestane tijd niet-actief voor het HDInsight-cluster op aanvraag. Hiermee geeft u op hoe lang het HDInsight-cluster op aanvraag actief blijft na voltooiing van een uitvoering van een activiteit als er geen andere actieve taken in het cluster zijn. De mini maal toegestane waarde is 5 minuten (00:05:00).<br/><br/>Als bijvoorbeeld het uitvoeren van een activiteit zes minuten duurt en timetolive is ingesteld op 5 minuten, blijft het cluster 5 minuten na de 6 minuten van verwerking van de uitvoering van de activiteit actief. Als er een andere uitvoering van de activiteit wordt uitgevoerd met het 6-minuten venster, wordt deze verwerkt door hetzelfde cluster.<br/><br/>Het maken van een HDInsight-cluster op aanvraag is een dure bewerking (kan enige tijd duren). Gebruik deze instelling daarom zo nodig om de prestaties van een data factory te verbeteren door opnieuw gebruik te maken van een on-demand HDInsight-cluster.<br/><br/>Als u timetolive waarde instelt op 0, wordt het cluster verwijderd zodra de uitvoering van de activiteit is voltooid. Als u een hoge waarde instelt, kan het cluster niet actief blijven om u aan te melden voor het oplossen van problemen, maar dit kan leiden tot hoge kosten. Daarom is het belang rijk dat u de juiste waarde instelt op basis van uw behoeften.<br/><br/>Als de waarde van de eigenschap timetolive is ingesteld op de juiste wijze, kunnen meerdere pijp lijnen het exemplaar van het HDInsight-cluster op aanvraag delen. | Yes      |
| clusterType                  | Het type HDInsight-cluster dat moet worden gemaakt. Toegestane waarden zijn ' Hadoop ' en ' Spark '. Als niet wordt opgegeven, is de standaard waarde Hadoop. Enterprise Security Package ingeschakeld cluster kan niet op aanvraag worden gemaakt. gebruik in plaats daarvan een [bestaand cluster/zet uw eigen reken kracht](#azure-hdinsight-linked-service). | No       |
| versie                      | De versie van het HDInsight-cluster. Als u niets opgeeft, wordt de huidige standaard versie van HDInsight gebruikt. | No       |
| hostSubscriptionId           | De ID van het Azure-abonnement dat wordt gebruikt om een HDInsight-cluster te maken. Als u niets opgeeft, wordt de abonnements-ID van uw Azure-aanmeldings context gebruikt. | No       |
| clusterNamePrefix           | Het voor voegsel van de HDI-cluster naam, een tijds tempel wordt automatisch toegevoegd aan het einde van de cluster naam| No       |
| sparkVersion                 | De versie van Spark als het cluster type Spark is | No       |
| additionalLinkedServiceNames | Hiermee worden extra opslag accounts voor de gekoppelde HDInsight-service opgegeven, zodat de Data Factory-service namens u kan worden geregistreerd. Deze opslag accounts moeten zich in dezelfde regio bevinden als het HDInsight-cluster, dat wordt gemaakt in dezelfde regio als het opslag account dat is opgegeven door linkedServiceName. | No       |
| osType                       | Type besturings systeem. Toegestane waarden zijn: Linux en Windows (alleen voor HDInsight 3,3). De standaard waarde is Linux. | No       |
| hcatalogLinkedServiceName    | De naam van de gekoppelde Azure SQL-service die verwijst naar de HCatalog-data base. Het HDInsight-cluster op aanvraag wordt gemaakt met behulp van de Azure SQL Database als de meta Store. | No       |
| connectVia                   | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde HDInsight-service. Voor de gekoppelde on-demand HDInsight-service ondersteunt deze alleen Azure Integration Runtime. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No       |
| clusterUserName                   | De gebruikers naam voor toegang tot het cluster. | No       |
| clusterPassword                   | Het wacht woord in het type beveiligde teken reeks voor toegang tot het cluster. | No       |
| clusterSshUserName         | De gebruikers naam voor SSH is extern verbonden met het knoop punt van het cluster (voor Linux). | No       |
| clusterSshPassword         | Het wacht woord in het type beveiligde teken reeks naar SSH extern verbinding maken met het knoop punt van het cluster (voor Linux). | No       |
| scriptActions | Geef het script voor [aanpassingen van HDInsight-clusters](../hdinsight/hdinsight-hadoop-customize-cluster-linux.md) op tijdens het maken van een cluster op aanvraag. <br />Het hulp programma voor het ontwerpen van gebruikers interfaces van Azure Data Factory biedt momenteel ondersteuning voor het opgeven van slechts 1 script actie, maar u kunt deze beperking door lopen in de JSON (meerdere script acties in de JSON opgeven). | No |


> [!IMPORTANT]
> HDInsight ondersteunt meerdere Hadoop-cluster versies die kunnen worden geïmplementeerd. Met elke versie keuze maakt u een specifieke versie van de HDP-distributie (Hortonworks data platform) en een set onderdelen die zijn opgenomen in die distributie. De lijst met ondersteunde HDInsight-versies blijft bijgewerkt met de nieuwste Hadoop ecosysteem-onderdelen en-oplossingen. Raadpleeg altijd de nieuwste informatie over de [ondersteunde hdinsight-versie en het type besturings systeem](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) om ervoor te zorgen dat u een ondersteunde versie van hdinsight gebruikt. 
>
> [!IMPORTANT]
> Op dit moment biedt HDInsight gekoppelde services geen ondersteuning voor HBase, interactieve Query's (Hive LLAP), storm. 

* additionalLinkedServiceNames JSON-voor beeld

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

#### <a name="service-principal-authentication"></a>Verificatie van service-principal

Voor de gekoppelde on-demand HDInsight-service is een Service-Principal-verificatie vereist voor het maken van HDInsight-clusters in uw naam. Als u Service-Principal-verificatie wilt gebruiken, registreert u een toepassings entiteit in Azure Active Directory (Azure AD) en verleent u deze de rol **Inzender** van het abonnement of de resource groep waarin het HDInsight-cluster wordt gemaakt. Zie [Portal gebruiken om een Azure Active Directory toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md)voor gedetailleerde stappen. Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

- Toepassings-id
- Toepassings sleutel 
- Tenant-id

Gebruik Service-Principal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap                | Beschrijving                              | Vereist |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Geef de client-ID van de toepassing op.     | Yes      |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op.           | Yes      |
| **tenant**              | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Yes      |

#### <a name="advanced-properties"></a>Geavanceerde eigenschappen

U kunt ook de volgende eigenschappen opgeven voor de gedetailleerde configuratie van het HDInsight-cluster op aanvraag.

| Eigenschap               | Beschrijving                              | Vereist |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Hiermee geeft u de para meters voor de kern configuratie op (zoals in core-site.xml) voor het HDInsight-cluster dat moet worden gemaakt. | No       |
| hBaseConfiguration     | Hiermee geeft u de HBase-configuratie parameters (hbase-site.xml) voor het HDInsight-cluster. | No       |
| hdfsConfiguration      | Hiermee geeft u de HDFS-configuratie parameters (hdfs-site.xml) voor het HDInsight-cluster op. | No       |
| hiveConfiguration      | Hiermee geeft u de Hive-configuratie parameters (hive-site.xml) voor het HDInsight-cluster. | No       |
| mapReduceConfiguration | Hiermee geeft u de MapReduce-configuratie parameters (mapred-site.xml) voor het HDInsight-cluster. | No       |
| oozieConfiguration     | Hiermee geeft u de Oozie-configuratie parameters (oozie-site.xml) voor het HDInsight-cluster. | No       |
| stormConfiguration     | Hiermee geeft u de Storm-configuratie parameters (storm-site.xml) voor het HDInsight-cluster. | No       |
| yarnConfiguration      | Hiermee geeft u de garen configuratie parameters (yarn-site.xml) voor het HDInsight-cluster. | No       |

* Voor beeld: HDInsight-cluster configuratie op aanvraag met geavanceerde eigenschappen

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

#### <a name="node-sizes"></a>Knooppunt grootten
U kunt de grootte van de hoofd-, gegevens-en Zookeeper-knoop punten opgeven met behulp van de volgende eigenschappen: 

| Eigenschap          | Beschrijving                              | Vereist |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Hiermee wordt de grootte van het hoofd knooppunt opgegeven. De standaard waarde is: Standard_D3. Zie de sectie **knooppunt grootten opgeven** voor meer informatie. | No       |
| dataNodeSize      | Hiermee wordt de grootte van het gegevens knooppunt opgegeven. De standaard waarde is: Standard_D3. | No       |
| zookeeperNodeSize | Hiermee geeft u de grootte van het Zoo keeper-knoop punt. De standaard waarde is: Standard_D3. | No       |

* Het opgeven van de grootte van knoop punten ziet u de [grootten van virtual machines](../virtual-machines/sizes.md) artikel voor teken reeks waarden die u moet opgeven voor de eigenschappen die worden vermeld in de vorige sectie. De waarden moeten voldoen aan de **cmdlets & api's** waarnaar in het artikel wordt verwezen. Zoals u in het artikel kunt zien, heeft het gegevens knooppunt van groot (standaard) een capaciteit van 7 GB. Dit is mogelijk niet voldoende voor uw scenario. 

Als u de grootte van het hoofd knooppunt en werk knooppunten van D4 wilt maken, geeft u **Standard_D4** op als de waarde voor de eigenschappen HeadNodeSize en dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Als u een verkeerde waarde voor deze eigenschappen opgeeft, wordt mogelijk de volgende fout weer gegeven **:** kan het cluster niet maken. Uitzondering: Kan het cluster niet maken. Bewerking is mislukt met code 400. Cluster heeft status: 'Fout'. Bericht: ' PreClusterCreationValidationFailure '. Wanneer u dit fout bericht ontvangt, moet u ervoor zorgen dat u de **CMDLET-&-api's** naam uit de tabel gebruikt in de [grootte van virtual machines](../virtual-machines/sizes.md) artikel.

### <a name="bring-your-own-compute-environment"></a>Uw eigen reken omgeving meenemen
In dit type configuratie kunnen gebruikers een reeds bestaande computer omgeving registreren als een gekoppelde service in Data Factory. De computer omgeving wordt beheerd door de gebruiker en de Data Factory-service gebruikt deze om de activiteiten uit te voeren.

Dit type configuratie wordt ondersteund voor de volgende reken omgevingen:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure Synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Gekoppelde Azure HDInsight-service
U kunt een gekoppelde Azure HDInsight-service maken om uw eigen HDInsight-cluster te registreren bij Data Factory.

### <a name="example"></a>Voorbeeld

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                                                  | Vereist |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | De eigenschap type moet worden ingesteld op **HDInsight**.            | Yes      |
| clusterUri        | De URI van het HDInsight-cluster.                            | Yes      |
| gebruikersnaam          | Geef de naam op van de gebruiker die moet worden gebruikt om verbinding te maken met een bestaand HDInsight-cluster. | Yes      |
| wachtwoord          | Geef het wacht woord voor het gebruikers account op.                       | Yes      |
| linkedServiceName | De naam van de gekoppelde Azure Storage-service die verwijst naar de Azure Blob-opslag die wordt gebruikt door het HDInsight-cluster. <p>Op dit moment kunt u geen gekoppelde Azure Data Lake Storage-service (Gen 2) opgeven voor deze eigenschap. Als het HDInsight-cluster toegang heeft tot de Data Lake Store, kunt u toegang krijgen tot gegevens in de Azure Data Lake Storage (Gen 2) van Hive-en Pig-scripts. </p> | Yes      |
| isEspEnabled      | Geef *waar* op als het HDInsight-cluster [Enterprise Security Package](../hdinsight/domain-joined/apache-domain-joined-architecture.md) ingeschakeld. De standaard waarde is *False*. | No       |
| connectVia        | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. <br />Voor Enterprise Security Package (ESP) ingeschakeld HDInsight-cluster maakt gebruik van een zelf-hostende Integration runtime, die een regel van het gezichts punt naar het cluster heeft of moet worden geïmplementeerd in dezelfde Virtual Network als het ESP HDInsight-cluster. | No       |

> [!IMPORTANT]
> HDInsight ondersteunt meerdere Hadoop-cluster versies die kunnen worden geïmplementeerd. Met elke versie keuze maakt u een specifieke versie van de HDP-distributie (Hortonworks data platform) en een set onderdelen die zijn opgenomen in die distributie. De lijst met ondersteunde HDInsight-versies blijft bijgewerkt met de nieuwste Hadoop ecosysteem-onderdelen en-oplossingen. Raadpleeg altijd de nieuwste informatie over de [ondersteunde hdinsight-versie en het type besturings systeem](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) om ervoor te zorgen dat u een ondersteunde versie van hdinsight gebruikt. 
>
> [!IMPORTANT]
> Op dit moment biedt HDInsight gekoppelde services geen ondersteuning voor HBase, interactieve Query's (Hive LLAP), storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Azure Batch gekoppelde service

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt een Azure Batch gekoppelde service maken om een batch-pool van virtuele machines (Vm's) te registreren bij een data factory. U kunt aangepaste activiteit uitvoeren met behulp van Azure Batch.

Zie de volgende artikelen als u geen ervaring hebt met Azure Batch-service:

* [Azure batch basis principes](../batch/batch-technical-overview.md) voor een overzicht van de Azure batch-service.
* De cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) om een Azure batch-account (of) te maken [Azure Portal](../batch/batch-account-create-portal.md) het Azure batch-account te maken met behulp van Azure Portal. Zie het artikel over het [beheren van Azure batch-account met](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) behulp van Power shell voor gedetailleerde instructies voor het gebruik van de cmdlet.
* De cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) om een Azure batch groep te maken.

> [!IMPORTANT]
> Bij het maken van een nieuwe Azure Batch pool moet ' VirtualMachineConfiguration ' worden gebruikt en niet ' CloudServiceConfiguration '. Raadpleeg de [richt lijnen](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md)voor de migratie van Azure batch voor groepen voor meer informatie. 

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Eigenschappen
| Eigenschap          | Beschrijving                              | Vereist |
| ----------------- | ---------------------------------------- | -------- |
| type              | De eigenschap type moet worden ingesteld op **AzureBatch**. | Yes      |
| accountName       | De naam van het Azure Batch-account.         | Yes      |
| accessKey         | De toegangs sleutel voor het Azure Batch-account.  | Yes      |
| batchUri          | URL naar uw Azure Batch-account, in de indeling https://*batchaccountname. Region*. batch.Azure.com. | Yes      |
| poolName          | De naam van de pool van virtuele machines.    | Yes      |
| linkedServiceName | De naam van de Azure Storage gekoppelde service die is gekoppeld aan deze Azure Batch gekoppelde service. Deze gekoppelde service wordt gebruikt voor tijdelijke bestanden die vereist zijn om de activiteit uit te voeren. | Yes      |
| connectVia        | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No       |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Gekoppelde service Azure Machine Learning Studio (klassiek)
U maakt een gekoppelde Azure Machine Learning Studio-Service (klassiek) om een Machine Learning Studio (klassiek) batch Score-eind punt te registreren voor een data factory.

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Eigenschappen
| Eigenschap               | Beschrijving                              | Vereist                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | De eigenschap type moet worden ingesteld op: **AzureML**. | Yes                                      |
| mlEndpoint             | De batch Score-URL.                   | Yes                                      |
| apiKey                 | De API van het gepubliceerde werkruimte model.     | Yes                                      |
| updateResourceEndpoint | De bron-URL van de update voor een webservice-eind punt voor Azure Machine Learning Studio (klassiek) dat wordt gebruikt om de voorspellende webservice bij te werken met het getrainde model bestand | No                                       |
| servicePrincipalId     | Geef de client-ID van de toepassing op.     | Vereist als updateResourceEndpoint is opgegeven |
| servicePrincipalKey    | Geef de sleutel van de toepassing op.           | Vereist als updateResourceEndpoint is opgegeven |
| tenant                 | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Vereist als updateResourceEndpoint is opgegeven |
| connectVia             | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No                                       |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning gekoppelde service
U maakt een Azure Machine Learning gekoppelde service om een Azure Machine Learning werk ruimte te verbinden met een data factory.

> [!NOTE]
> Momenteel wordt alleen Service-Principal-verificatie ondersteund voor de Azure Machine Learning gekoppelde service.

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Eigenschappen

| Eigenschap               | Beschrijving                              | Vereist                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | De eigenschap type moet worden ingesteld op: **AzureMLService**. | Yes                                      |
| subscriptionId         | Azure-abonnements-ID              | Yes                                      |
| resourceGroupName      | naam | Yes                                      |
| mlWorkspaceName        | Naam van Azure Machine Learning werkruimte | Yes  |
| servicePrincipalId     | Geef de client-ID van de toepassing op.     | No |
| servicePrincipalKey    | Geef de sleutel van de toepassing op.           | No |
| tenant                 | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Vereist als updateResourceEndpoint is opgegeven |
| connectVia             | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics gekoppelde service
U maakt een **Azure data Lake Analytics** gekoppelde service om een Azure data Lake Analytics compute-service aan een Azure Data Factory te koppelen. De Data Lake Analytics U-SQL-activiteit in de pijp lijn verwijst naar deze gekoppelde service. 

### <a name="example"></a>Voorbeeld

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Eigenschappen

| Eigenschap             | Beschrijving                              | Vereist                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | Azure Data Lake Analytics account naam.  | Yes                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics-URI.           | No                                       |
| subscriptionId       | Azure-abonnements-ID                    | No                                       |
| resourceGroupName    | Naam van Azure-resourcegroep                | No                                       |
| servicePrincipalId   | Geef de client-ID van de toepassing op.     | Yes                                      |
| servicePrincipalKey  | Geef de sleutel van de toepassing op.           | Yes                                      |
| tenant               | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | Yes                                      |
| connectVia           | Het Integration Runtime dat moet worden gebruikt voor het verzenden van de activiteiten naar deze gekoppelde service. U kunt Azure Integration Runtime of zelf-hostende Integration Runtime gebruiken. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks gekoppelde service
U kunt **Azure Databricks gekoppelde service** maken om de Databricks-werk ruimte te registreren die u gebruikt om de Databricks-workloads (notebook, jar, python) uit te voeren. 
> [!IMPORTANT]
> Databricks gekoppelde services ondersteunen [instantie groepen](https://aka.ms/instance-pools) & door het systeem toegewezen beheerde identiteits verificatie.

### <a name="example---using-new-job-cluster-in-databricks"></a>Voor beeld: een nieuw taak cluster gebruiken in Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Voor beeld-een bestaand interactief cluster gebruiken in Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Eigenschappen

| Eigenschap             | Beschrijving                              | Vereist                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| naam                 | De naam van de gekoppelde service               | Yes   |
| type                 | De eigenschap type moet worden ingesteld op: **Azure Databricks**. | Yes                                      |
| domein               | Geef de Azure-regio op op basis van de regio van de Databricks-werk ruimte. Voorbeeld: https://eastus.azuredatabricks.net | Yes                                 |
| accessToken          | Er is een toegangs token vereist om Data Factory te verifiëren bij Azure Databricks. Het toegangs token moet worden gegenereerd op basis van de databricks-werk ruimte. Meer gedetailleerde stappen om het toegangs token te vinden, vindt u [hier](/azure/databricks/dev-tools/api/latest/authentication#generate-token)  | No                                       |
| MSI          | Gebruik de beheerde identiteit van Data Factory (systeem toegewezen) voor verificatie bij Azure Databricks. U hebt geen toegangs token wanneer u MSI-verificatie gebruikt  | No                                       |
| existingClusterId    | De cluster-ID van een bestaand cluster voor het uitvoeren van alle taken. Dit moet een al gemaakt interactief cluster zijn. Mogelijk moet u het cluster hand matig opnieuw opstarten als het niet meer reageert. Databricks suggereert het uitvoeren van taken op nieuwe clusters voor een grotere betrouw baarheid. U vindt de cluster-ID van een interactief cluster op Databricks werkruimte-> clusters-> interactieve cluster naam-> configuratie->-Tags. [Meer Details](https://docs.databricks.com/user-guide/clusters/tags.html) | No 
| instancePoolId    | ID van de exemplaar groep van een bestaande groep in de databricks-werk ruimte.  | No  |
| newClusterVersion    | De Spark-versie van het cluster. Er wordt een taak cluster gemaakt in databricks. | No  |
| newClusterNumOfWorker| Het aantal worker-knoop punten dat dit cluster moet hebben. Een cluster heeft één Spark-stuur programma en num_workers-uitvoerders voor een totaal van num_workers + 1 Spark-knoop punten. Een teken reeks met de notatie Int32, zoals ' 1 ' betekent dat numOfWorker 1 of ' 1:10 ' betekent dat automatisch wordt geschaald van 1 tot en met 10 als Max.  | No                |
| newClusterNodeType   | Dit veld codeert, via één waarde, de bronnen die beschikbaar zijn voor elk van de Spark-knoop punten in dit cluster. De Spark-knoop punten kunnen bijvoorbeeld worden ingericht en geoptimaliseerd voor geheugen of computerintensieve werk belastingen. Dit veld is vereist voor het nieuwe cluster                | No               |
| newClusterSparkConf  | een set optionele, door de gebruiker opgegeven Spark-configuratie sleutel-waardeparen. Gebruikers kunnen ook een reeks extra JVM-opties door geven aan het stuur programma en de uitvoerendeers via Spark. driver. extraJavaOptions en spark.executor. extraJavaOptions respectievelijk. | No  |
| newClusterInitScripts| een set optionele, door de gebruiker gedefinieerde initialisatie scripts voor het nieuwe cluster. Het DBFS-pad naar de init-scripts opgeven. | No  |


## <a name="azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service

U maakt een gekoppelde Azure SQL-service en gebruikt deze met de [opgeslagen procedure activiteit](transform-data-using-stored-procedure.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie het artikel over [Azure SQL connector](connector-azure-sql-database.md#linked-service-properties) voor meer informatie over deze gekoppelde service.

## <a name="azure-synapse-analytics-linked-service"></a>Gekoppelde Azure Synapse Analytics-Service

U maakt een gekoppelde Azure Synapse Analytics-service en gebruikt deze met de [opgeslagen procedure activiteit](transform-data-using-stored-procedure.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie het artikel over [Azure Synapse Analytics-connector](connector-azure-sql-data-warehouse.md#linked-service-properties) voor meer informatie over deze gekoppelde service.

## <a name="sql-server-linked-service"></a>SQL Server gekoppelde service

U maakt een SQL Server gekoppelde service en gebruikt deze met de [opgeslagen procedure activiteit](transform-data-using-stored-procedure.md) om een opgeslagen procedure vanuit een Data Factory pijp lijn aan te roepen. Zie [SQL Server connector](connector-sql-server.md#linked-service-properties) -artikel voor meer informatie over deze gekoppelde service.

## <a name="azure-function-linked-service"></a>Gekoppelde Azure-functie Service

U maakt een gekoppelde Azure-functie service en gebruikt deze met de [activiteit Azure function](control-flow-azure-function-activity.md) om Azure functions uit te voeren in een Data Factory-pijp lijn. Het retour type van de Azure-functie moet geldig zijn `JObject` . (Houd er wel bij [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) dat JArray *geen* is `JObject` .) Een ander retour type dan `JObject` mislukt en de inhoud van het fout bericht van de gebruiker wordt gegenereerd, *is geen geldige JObject*.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| type   | De eigenschap type moet worden ingesteld op: **AzureFunction** | ja |
| URL van de functie-app | De URL voor de Azure-functie-app. Indeling is `https://<accountname>.azurewebsites.net` . Deze URL is de waarde onder **URL** -gedeelte bij het weer geven van uw functie-app in de Azure Portal  | ja |
| functie toets | Toegangs sleutel voor de Azure-functie. Klik op de sectie **beheren** voor de betreffende functie en kopieer de **functie sleutel** of de host- **sleutel**. Meer informatie vindt u hier: [Azure functions HTTP-triggers en-bindingen](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="next-steps"></a>Volgende stappen

Zie [gegevens transformeren](transform-data.md)voor een lijst van de transformatie activiteiten die door Azure Data Factory worden ondersteund.
