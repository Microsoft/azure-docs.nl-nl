---
title: Analyses op HDInsight Spark met PySpark, scala-team data Science process
description: Voor beelden van het team data Science process dat het gebruik van PySpark en scala op een Azure HDInsight Spark door lopen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320130"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark data Science-scenario's met PySpark en scala op Azure

Deze scenario's maken gebruik van PySpark en scala op een Azure Spark-cluster om predictive analytics uit te voeren. Ze volgen de stappen die worden beschreven in het team data Science process. Zie [Data Science process](overview.md)(Engelstalig) voor een overzicht van het team data Science process. Zie [Inleiding tot Spark in hdinsight](../../hdinsight/spark/apache-spark-overview.md)voor een overzicht van Spark in hdinsight.

Aanvullende data Science-instructies die het team data Science proces uitvoeren, worden gegroepeerd op het **platform** dat ze gebruiken. Zie [de stapsgewijze instructies voor het uitvoeren van het team data Science process](walkthroughs.md) voor een specificatie van deze voor beelden.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Gebruik PySpark in azure Spark om aan de taxi tips te voors pellen

Met behulp van de nieuwe taxi-gegevens van de Utrecht [gebruiken in het scenario voor het gebruik van Spark in azure HDInsight](spark-overview.md) of een tip wordt betaald en het bereik van de verwachte bedragen. In dit voor beeld wordt het team data Science-proces in een scenario gebruikt met behulp van een [Azure HDInsight Spark-cluster](https://azure.microsoft.com/services/hdinsight/) om engineergegevens op te slaan, te verkennen en te functie van de openbaar beschik bare NYCe taxi en ritbedrag-gegevensset. In dit overzichts onderwerp wordt gebruikgemaakt van een HDInsight Spark-cluster en Jupyter PySpark-notebooks. Deze notebooks laten zien hoe u uw gegevens kunt verkennen en hoe u modellen kunt maken en gebruiken. De geavanceerde laptop voor gegevens exploratie en-modellering laat zien hoe u kruis validatie, Hyper-para meters opruimen en model evaluatie kunt gebruiken.

### <a name="data-exploration-and-modeling-with-spark"></a>Gegevens verkennen en model leren met Spark 
Verken de gegevensset en maak, beoordeel en evalueer de machine learning modellen door te werken met behulp van het onderwerp [binaire classificatie maken en regressie modellen voor gegevens met behulp van de Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

### <a name="model-consumption"></a>Model verbruik
Zie voor meer informatie over het beoordelen van de classificatie en regressie modellen die in dit onderwerp zijn gemaakt, de [Score en evalueren van met Spark ontwikkelde machine learning modellen](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kruis validatie en afstemming sweep
Bekijk [geavanceerde gegevens exploratie en model lering met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met kruis validatie en Hyper-para meter sweep.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Gebruik scala in azure Spark om aan de taxi tips te voors pellen

Met het scenario [scala gebruiken met Spark in azure wordt voor](scala-walkthrough.md) speld of een tip wordt betaald en het bereik van de bedragen die naar verwachting worden betaald. Hierin ziet u hoe u scala kunt gebruiken voor onder Super visie machine learning taken met de Spark machine learning Library (MLlib) en SparkML-pakketten op een Azure HDInsight Spark cluster. U wordt begeleid bij de taken die het [Data Science proces](./index.yml)vormen: gegevens opname en exploratie, visualisatie, functie techniek, model lering en model verbruik. De modellen zijn gebaseerd op logistieke en lineaire regressie, wille keurige forests en verlopen structuren.


## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van team data Science process](overview.md)voor een overzicht van het team data Science process.

Zie voor een bespreking van de team data Science proces levenscyclus de [team data Science process Lifecycle](lifecycle.md). Deze levens cyclus bevat een overzicht van de stappen, van begin tot eind, die projecten meestal volgen wanneer ze worden uitgevoerd.