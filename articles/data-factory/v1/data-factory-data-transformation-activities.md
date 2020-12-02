---
title: 'Gegevens transformatie: gegevens verwerken & transformeren '
description: Meer informatie over het transformeren van gegevens of het verwerken van gegevens in Azure Data Factory met Hadoop, Azure Machine Learning Studio (klassiek) of Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 587e8eaf36a8e9d0be86237e2db72f952853a0ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495681"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Gegevens transformeren in Azure Data Factory versie 1
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop-streaming](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (klassiek)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Opgeslagen procedure](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET aangepast](data-factory-use-custom-activities.md)

## <a name="overview"></a>Overzicht
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [activiteiten voor gegevens transformatie in Data Factory](../transform-data.md).

In dit artikel wordt uitgelegd hoe u activiteiten voor gegevens transformatie in Azure Data Factory kunt gebruiken om uw onbewerkte gegevens te transformeren en te verwerken in voor spellingen en inzichten. Een transformatie activiteit wordt uitgevoerd in een computer omgeving zoals Azure HDInsight-cluster of een Azure Batch. Het bevat koppelingen naar artikelen met gedetailleerde informatie over elke transformatie activiteit.

Data Factory ondersteunt de volgende activiteiten voor gegevens transformatie die afzonderlijk kunnen worden toegevoegd aan [pijp lijnen](data-factory-create-pipelines.md) of moeten worden gekoppeld aan een andere activiteit.

> [!NOTE]
> Zie [een pijp lijn maken met hive](data-factory-build-your-first-pipeline.md) -artikel voor een overzicht van stapsgewijze instructies.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-activiteit
De HDInsight Hive-activiteit in een Data Factory pijp lijn voert Hive-query's uit op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie het artikel [Hive-activiteit](data-factory-hive-activity.md) voor meer informatie over deze activiteit. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-activiteit
Met de HDInsight Pig-activiteit in een Data Factory pijp lijn worden Pig-query's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie het artikel [Pig-activiteit](data-factory-pig-activity.md) voor meer informatie over deze activiteit. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-activiteit
Met de HDInsight MapReduce-activiteit in een Data Factory pijp lijn worden MapReduce-Program ma's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie [MapReduce-activiteiten](data-factory-map-reduce.md) artikel voor meer informatie over deze activiteit.

## <a name="hdinsight-streaming-activity"></a>HDInsight streaming-activiteit
Met de activiteit HDInsight streaming in een Data Factory pijp lijn worden Hadoop-streaming-Program ma's uitgevoerd op uw eigen of op aanvraag gebaseerd HDInsight-cluster op basis van Windows/Linux. Zie [HDInsight streaming-activiteit](data-factory-hadoop-streaming-activity.md) voor meer informatie over deze activiteit.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-activiteit
Met de HDInsight Spark-activiteit in een Data Factory pijp lijn worden Spark-Program ma's uitgevoerd op uw eigen HDInsight-cluster. Zie voor meer informatie [aanroepen Spark-Program ma's van Azure Data Factory](data-factory-spark.md). 

## <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning Studio (klassieke) activiteiten
Met Azure Data Factory kunt u eenvoudig pijp lijnen maken die gebruikmaken van een gepubliceerde Azure Machine Learning Studio (klassieke) webservice voor predictive analytics. Met de [activiteit voor batch uitvoering](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) in een Azure Data Factory-pijp lijn kunt u een studio-webservice aanroepen om voor spellingen te maken voor de gegevens in batch.

In de loop van de tijd moeten de voorspellende modellen in de Studio (klassieke) Score experimenten opnieuw worden getraind met nieuwe invoer gegevens sets. Wanneer u klaar bent met opnieuw trainen, wilt u de Score-webservice bijwerken met het opnieuw getrainde machine learning model. U kunt de [activiteit resource bijwerken](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) gebruiken om de webservice bij te werken met het nieuwe getrainde model.  

Zie [Azure machine learning Studio (klassieke) activiteiten gebruiken](data-factory-azure-ml-batch-execution-activity.md) voor meer informatie over deze studio-activiteiten (klassiek). 

## <a name="stored-procedure-activity"></a>Opgeslagen procedure activiteit
U kunt de SQL Server opgeslagen procedure-activiteit in een Data Factory-pijp lijn gebruiken voor het aanroepen van een opgeslagen procedure in een van de volgende gegevens archieven: Azure SQL Database, Azure Synapse Analytics, SQL Server data base in uw onderneming of een Azure-VM. Zie het artikel [opgeslagen procedure-activiteit](data-factory-stored-proc-activity.md) voor meer informatie.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
Met Data Lake Analytics U-SQL-activiteit wordt een U-SQL-script op een Azure Data Lake Analytics cluster uitgevoerd. Zie [het artikel Data Analytics U-SQL-activiteit](data-factory-usql-activity.md) voor meer informatie. 

## <a name="net-custom-activity"></a>Aangepaste .NET-activiteit
Als u gegevens moet transformeren op een manier die niet wordt ondersteund door Data Factory, kunt u een aangepaste activiteit maken met uw eigen gegevens verwerkings logica en de activiteit in de pijp lijn gebruiken. U kunt de aangepaste .NET-activiteit zodanig configureren dat deze wordt uitgevoerd met behulp van een Azure Batch-service of een Azure HDInsight-cluster. Zie het artikel [aangepaste activiteiten gebruiken](data-factory-use-custom-activities.md) voor meer informatie. 

U kunt een aangepaste activiteit maken om R-scripts uit te voeren op uw HDInsight-cluster waarop R is geïnstalleerd. Zie [R-script uitvoeren met behulp van Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Reken omgevingen
U maakt een gekoppelde service voor de compute-omgeving en gebruikt vervolgens de gekoppelde service wanneer u een trans formatie-activiteit definieert. Er zijn twee soorten reken omgevingen die door Data Factory worden ondersteund. 

1. **Op aanvraag**: in dit geval wordt de computer omgeving volledig beheerd door Data Factory. Het wordt automatisch gemaakt door de Data Factory-service voordat een taak wordt verzonden om gegevens te verwerken en te worden verwijderd wanneer de taak is voltooid. U kunt gedetailleerde instellingen van de compute-omgeving op aanvraag voor taak uitvoering, Cluster beheer en acties voor Boots traping configureren en beheren. 
2. **Neem uw eigen** werk: in dit geval kunt u uw eigen computer omgeving (bijvoorbeeld HDInsight-cluster) registreren als een gekoppelde service in Data Factory. De computer omgeving wordt beheerd door u en de Data Factory-service gebruikt deze om de activiteiten uit te voeren. 

Zie het artikel [Compute linked Services](data-factory-compute-linked-services.md) voor meer informatie over Compute-services die door Data Factory worden ondersteund. 

## <a name="summary"></a>Samenvatting
Azure Data Factory ondersteunt de volgende activiteiten voor gegevens transformatie en de reken omgevingen voor de activiteiten. De transformatie activiteiten kunnen afzonderlijk worden toegevoegd aan pijp lijnen of worden gekoppeld aan een andere activiteit.

| Activiteiten voor gegevenstransformatie | Compute-omgeving |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop-streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Azure Machine Learning Studio (klassiek) activiteiten: Batchuitvoering en resource bijwerken](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Opgeslagen procedure](data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics of SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] of Azure Batch |

