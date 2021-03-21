---
title: Machine Learning in Azure Synapse Analytics
description: Een overzicht van de machine learning-mogelijkheden in Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222136"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Mogelijkheden voor machine learning in Azure Synapse Analytics

Azure Synapse Analytics biedt verschillende machine learning-mogelijkheden Dit artikel geeft een overzicht van hoe u machine learning kunt toepassen in de context van Azure Synapse.

In dit overzicht worden de verschillende mogelijkheden van Synapse voor machine learning beschreven, vanuit het perspectief van een proces in de gegevenswetenschappen.

Mogelijk weet u al hoe een typisch proces in de gegevenswetenschappen eruitziet. Het gaat om een bekend proces dat de meeste machine learning-projecten volgen.

In grote lijnen bevat het proces de volgende stappen:
* (Inzicht in het bedrijf)
* Gegevens verzamelen en begrijpen
* Modelleren
* Modelimplementatie en -score

In dit artikel worden de machine learning-mogelijkheden van Azure Synapse in verschillende analyse-engines beschreven, vanuit het perspectief van een proces in de gegevenswetenschappen. Voor elke stap in het proces in de gegevenswetenschappen kunnen de nuttige Azure Synapse-mogelijkheden worden samenvatten.

## <a name="azure-synapse-machine-learning-capabilities"></a>Machine learning-mogelijkheden van Azure Synapse

### <a name="data-acquisition-and-understanding"></a>Gegevens verzamelen en begrijpen

De meeste machine learning-projecten omvatten gevestigde stappen, en een van die stappen is om die gegevens to openen en te begrijpen.

#### <a name="data-source-and-pipelines"></a>Gegevensbron en pijplijnen

Dankzij [Azure Data Factory](../../data-factory/introduction.md), een native geïntegreerd onderdeel van Azure Synapse, is er een krachtige set tools beschikbaar voor gegevensopname en pijplijnen voor gegevensindeling. Zo kunt u eenvoudig gegevenspijplijnen maken voor toegang tot en transformatie van de gegevens naar een indeling die kan worden gebruikt voor machine learning. [Meer informatie over gegevenspijplijnen](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) in Synapse. 

#### <a name="data-preparation-and-explorationvisualization"></a>Gegevensvoorbereiding en -ontwikkeling/visualisatie

Een belangrijk onderdeel van het machine learning-proces is om inzicht te krijgen in de gegevens via verkenning en visualisatie.

Afhankelijk van waar de gegevens zijn opgeslagen, biedt Synapse een aantal verschillende hulpmiddelen om deze te verkennen en voor te bereiden voor analyse en machine learning. Een van de snelste manieren om aan de slag te gaan met het verkennen van gegevens, is door serverloze pools van Apache Spark of SQL rechtstreeks te gebruiken op de gegevens in het data lake.

* [Apache Spark voor Azure Synapse](../spark/apache-spark-overview.md) biedt mogelijkheden om uw gegevens op schaal te transformeren, voor te bereiden en te verkennen. Deze Spark-pools bevatten tools zoals PySpark/Python, Scala en .NET voor gegevensverwerking op schaal. Met krachtige visualisatiebibliotheken kunt u de gegevens beter verkennen en begrijpen. [Meer informatie over gegevens verkennen en visualiseren in Synapse met behulp van Spark](../get-started-analyze-spark.md).

* [Serverloze SQL-pools](../sql/on-demand-workspace-overview.md) bieden een manier om gegevens rechtstreeks via het data lake te verkennen met behulp van TSQL. Serverloze SQL-pools bieden ook een aantal ingebouwde visualisaties in Synapse Studio. [Meer informatie over hoe u gegevens kunt verkennen met serverloze SQL-pools](../get-started-analyze-sql-on-demand.md).

### <a name="modeling"></a>Modellen maken

In Azure Synapse kunt u machine learning-modellen trainen op de Apache Spark-pools met tools zoals PySpark/Python, Scala of .NET.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Modellen trainen in Spark-pools met MLlib

Machine learning-modellen kunnen worden getraind met behulp van verschillende algoritmen en bibliotheken. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) bevat schaalbare machine learning-algoritmen die u kunnen helpen om de meeste klassieke machine learning-problemen op te lossen. [In deze zelfstudie](../spark/apache-spark-machine-learning-mllib-notebook.md) wordt beschreven hoe u een model traint met behulp van MLlib in Synapse.

In aanvulling op MLlib kunnen populaire bibliotheken, zoals [Scikit](https://scikit-learn.org/stable/), ook worden gebruikt om modellen te ontwikkelen. Zie [Bibliotheken beheren voor Apache Spark in Azure Synapse Analytics](../spark/apache-spark-azure-portal-add-libraries.md) voor meer informatie over het installeren van bibliotheken in Synapse Spark-pools.

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Modellen trainen met geautomatiseerde ML van Azure Machine Learning

Een andere manier om machine learning-modellen te trainen, waarvoor niet veel voorafgaande kennis van machine learning nodig is, is het gebruik van geautomatiseerde ML. [Geautomatiseerde ML](../../machine-learning/concept-automated-ml.md) is een functie die automatisch een set machine learning-modellen traint, en de gebruiker in staat stelt om het beste model te selecteren op basis van specifieke metrische gegevens. Dankzij een naadloze integratie met Azure Machine Learning van Azure Synapse-notebooks kunnen gebruikers eenvoudig gebruikmaken van geautomatiseerde ML in Synapse met Azure Active Directory-verificatie.  Dit betekent dat u alleen naar uw Azure Machine Learning-werkruimte hoeft te verwijzen en geen referenties moet invoeren. Hier vindt u een [zelfstudie voor geautomatiseerde ML](../spark/apache-spark-azure-machine-learning-tutorial.md) die beschrijft hoe u modellen traint met behulp van Azure Machine Learning geautomatiseerde ML op Synapse Spark-pools.

### <a name="model-deployment-and-scoring"></a>Modelimplementatie en -score

Modellen die binnen of buiten Azure Synapse zijn getraind kunnen eenvoudig worden gebruikt om in batch te scoren. Momenteel zijn er in Synapse twee manieren waarop u in batch kunt scoren.

* U kunt de [TSQL PREDICT-functie](../sql-data-warehouse/sql-data-warehouse-predict.md) gebruiken in Synapse SQL-pools om uw voorspellingen te doen op dezelfde locatie als uw gegevens. Met deze krachtige en schaalbare functie kunt u uw gegevens verrijken zonder dat u gegevens uit uw data warehouse hoeft te verplaatsen. Er is een nieuwe [begeleide machine learning-modelervaring in Synapse Studio](./tutorial-sql-pool-model-scoring-wizard.md) beschikbaar, waarin u een ONNX-model kunt implementeren vanuit het Azure Machine Learning-modelregister in Synapse SQL-pools om in batch te scoren met behulp van PREDICT.

* Een andere optie voor machine Learning-modellen om in batch te scoren in Azure Synapse, is om de Apache Spark-pools voor Azure Synapse te gebruiken. Afhankelijk van de bibliotheken die worden gebruikt voor het trainen van de modellen, kunt u een code-ervaring gebruiken om in batch te scoren.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](../get-started.md)
* [Een werkruimte maken](../get-started-create-workspace.md)
* [Snelstart: Een nieuwe gekoppelde Azure Machine Learning-service maken in Synapse](quickstart-integrate-azure-machine-learning.md)
* [Zelfstudie: Wizard voor scoren van het Machine learning-model - toegewezen SQL-pool](tutorial-sql-pool-model-scoring-wizard.md)