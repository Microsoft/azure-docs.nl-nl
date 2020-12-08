---
title: Wat is Apache Spark?
description: Dit artikel bevat een inleiding tot Apache Spark in Azure Synapse Analytics en de verschillende scenario's waarin Spark kan worden gebruikt.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b31fe5daaa0882dc0927c1340902b20df56eb6b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450439"
---
# <a name="apache-spark-in-azure-synapse-analytics"></a>Apache Spark in Azure Synapse Analytics

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in Azure Synapse Analytics is een van de implementaties van Apache Spark van Microsoft in de cloud. Met Azure Synapse kunt u eenvoudig een serverloze Apache Spark-pool maken en configureren in Azure. Spark-pools in Azure Synapse zijn compatibel met opslag van de tweede generatie in Azure Storage en Azure Data Lake. Dit betekent dat u Spark-pools kunt gebruiken om gegevens te verwerken die zijn opgeslagen in Azure.

![Spark: een geünificeerd framework](./media/apache-spark-overview/spark-overview.png)

## <a name="what-is-apache-spark"></a>Wat is Apache Spark?

Apache Spark biedt primitieve typen voor in-memory clustercomputing. Een Spark-taak kan gegevens laden en in het geheugen cachen en er herhaaldelijk query’s op uitvoeren. In-memory computing is veel sneller dan op schijven gebaseerde toepassingen. Apache Spark kan ook worden geïntegreerd met meerdere programmeertalen, zodat u gedistribueerde gegevenssets zoals lokale verzamelingen kunt bewerken. Het is niet nodig om alles te structureren als toewijzings- en verminderingsbewerkingen.

![Traditionele MapReduce vergeleken met Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Spark-pools in Azure Synapse bieden een volledig beheerde Spark-service. De voordelen van het maken van een Spark-pool in Azure Synapse Analytics worden hier beschreven.

| Functie | Beschrijving |
| --- | --- |
| Snelheid en efficiëntie |Spark-exemplaren starten in ongeveer 2 minuten voor minder dan 60 knooppunten en in ongeveer 5 minuten voor meer dan 60 knooppunten. Het exemplaar wordt standaard 5 minuten na de laatste uitgevoerde taak afgesloten, tenzij het actief wordt gehouden door een notebook-verbinding. |
| Eenvoudig te maken |U kunt binnen enkele minuten een nieuwe Spark-pool in Azure Synapse maken met de Azure-portal, Azure PowerShell of de .NET-SDK voor Synapse Analytics. Zie [Get started with Spark pools in Azure Synapse Analytics](../quickstart-create-apache-spark-pool-studio.md) (Aan de slag met Spark-pools in Azure Synapse Analytics). |
| Gebruiksgemak |Synapse Analytics bevat een aangepaste notebook die is afgeleid van [Nteract](https://nteract.io/). U kunt deze notebooks gebruiken voor interactieve gegevensverwerking en visualisatie.|
| REST-API’s |Apache Spark in Azure Synapse Analytics bevat [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), een op REST API gebaseerde Apache Spark-taakserver om op afstand taken te verzenden en te controleren. |
| Ondersteuning voor Azure Data Lake Storage, tweede generatie| Spark-pools in Azure Synapse kunnen Azure Data Lake Storage van de tweede generatie en blob-opslag gebruiken. Zie [Overzicht van Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md) voor meer informatie over Data Lake Storage. |
| Integratie met IDE's van derden | Azure Synapse biedt een IDE-invoegtoepassing voor [JetBrains' IntelliJ IDEA](https://www.jetbrains.com/idea/) die nuttig is om toepassingen te maken en in te dienen bij een Spark-pool. |
| Vooraf geladen Anaconda-bibliotheken |Spark-pools in Azure Synapse worden geleverd met Anaconda-bibliotheken die vooraf zijn geïnstalleerd. [Anaconda](https://docs.continuum.io/anaconda/) voorziet in bijna 200 bibliotheken voor machine learning, data-analyse, visualisatie, enzovoort. |
| Schaalbaarheid | Apache Spark-pools in Azure Synapse kunnen automatisch schalen, als u deze functie inschakelt, zodat pools naar wens omhoog en omlaag kunnen worden geschaald door knooppunten toe te voegen of te verwijderen. Bovendien kunnen Spark-pools zonder gegevensverlies worden afgesloten, omdat alle gegevens zijn opgeslagen in Azure Storage of Data Lake Storage. |

De volgende onderdelen zijn standaard beschikbaar voor Spark-pools in Azure Synapse.

- [Spark Core](https://spark.apache.org/docs/2.4.5/). Omvat Spark Core, Spark SQL, GraphX en MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract-notebook](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Architectuur van Spark-pool

De onderdelen van Apache Spark zijn gemakkelijker te begrijpen als u weet hoe Apache Spark wordt uitgevoerd in Azure Synapse Analytics.

Spark-toepassingen worden als onafhankelijke sets processen uitgevoerd in een pool, gecoördineerd door het SparkContext-object in uw hoofdprogramma (het zogenaamde stuurprogramma).

De SparkContext kan verbinding maken met de clusterbeheerder, die resources toewijst aan toepassingen. De clusterbeheerder is [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Als er verbinding is, verkrijgt Spark executors op knooppunten in de pool. Dit zijn processen die berekeningen uitvoeren en gegevens opslaan voor uw toepassing. Vervolgens wordt de code van uw toepassing (gedefinieerd in JAR- of Python-bestanden die worden doorgegeven aan SparkContext) verstuurd naar de executor. Ten slotte verzendt SparkContext taken naar de executors om uit te voeren.

SparkContext voert de hoofdfunctie van de gebruiker uit en voert de verschillende parallelle bewerkingen uit op de knooppunten. Vervolgens verzamelt SparkContext de resultaten van de bewerkingen. De knooppunten lezen en schrijven gegevens van en naar het bestandssysteem. De knooppunten plaatsen getransformeerde gegevens ook in het geheugen als RDD's (Resilient Distributed Datasets).

SparkContext maakt verbinding met de Spark-pool en converteert een toepassing naar een Directed Acyclic Graph (DAG). De graaf bestaat uit afzonderlijke taken die worden uitgevoerd in een uitvoerproces op de knooppunten. Elke toepassing krijgt zijn eigen executorprocessen, die voor de duur van de gehele toepassing blijven en taken uitvoeren in meerdere threads.

## <a name="apache-spark-in-azure-synapse-analytics-use-cases"></a>Gebruiksvoorbeelden van Apache Spark in Azure Synapse Analytics

Apache Spark-pools in Azure Synapse Analytics maken de volgende belangrijke scenario's mogelijk:

### <a name="data-engineeringdata-preparation"></a>Data engineering/gegevensvoorbereiding

Apache Spark bevat veel taalfuncties die het voorbereiden en verwerken van grote hoeveelheden gegevens ondersteunen, zodat er waarde aan kan worden toegevoegd en de gegevens vervolgens kunnen worden verwerkt door andere services binnen Azure Synapse Analytics. Dit wordt mogelijk gemaakt door meerdere talen (C#, Scala, PySpark, Spark SQL) en meegeleverde bibliotheken voor de verwerking en connectiviteit.

### <a name="machine-learning"></a>Machine Learning

Apache Spark wordt geleverd met [MLlib](https://spark.apache.org/mllib/), een bibliotheek voor machine learning die boven op Apache Spark is gebouwd. U kunt deze bibliotheek gebruiken vanuit een Apache Spark-pool in Azure Synapse Analytics. Apache Spark-pools in Azure Synapse Analytics bevatten ook Anaconda, een Python-distributie met verschillende pakketten voor data science, waaronder machine learning. Gecombineerd met ingebouwde ondersteuning voor notebooks beschikt u hiermee over een omgeving voor het maken van machine learning-toepassingen.

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

Raadpleeg de volgende artikelen voor meer informatie over Apache Spark in Azure Synapse Analytics:

- [Snelstart: Een Spark-pool maken in Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
- [Snelstart: Een Apache Spark-notebook maken](../quickstart-apache-spark-notebook.md)
- [Zelfstudie: Machine learning met Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Officiële documentatie voor Apache Spark](https://spark.apache.org/docs/2.4.5/)

> [!NOTE]
> Een deel van de officiële Apache Spark-documentatie is gebaseerd op het gebruik van de Spark-console, die echter niet beschikbaar is in Azure Synapse Spark. In plaats daarvan kunt u de notebook of IntelliJ gebruiken.

## <a name="next-steps"></a>Volgende stappen

In dit overzicht hebt u algemene informatie gekregen over het gebruik van Apache Spark in Azure Synapse Analytics. Ga naar het volgende artikel voor meer informatie over het maken van een Spark-pool in Azure Synapse Analytics:

- [Een Spark-pool maken in Azure Synapse](../quickstart-create-apache-spark-pool-portal.md)
