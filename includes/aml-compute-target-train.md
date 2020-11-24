---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: c45b30fb16293652e169b89a6d93520509777a40
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561755"
---
**Rekendoelen kunnen opnieuw worden gebruikt voor trainingstaken.** Als u een externe VM koppelt aan uw werkruimte, kunt u die bijvoorbeeld voor meerdere taken gebruiken. Voor machine learning-pijplijn gebruikt u de juiste [pijplijnstap](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) voor elk rekendoel.

U kunt voor de meeste taken een van de volgende resources gebruiken voor een rekendoel voor trainingsdoeleinden. Niet alle resources kunnen worden gebruikt voor geautomatiseerde machine learning, pijplijnen voor machine learning of de ontwerpfunctie.

|Trainingsdoelen&nbsp;|[Geautomatiseerde Machine Learning](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning-pijplijnen](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning-ontwerpprogramma](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Lokale computer](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Ja | &nbsp; | &nbsp; |
|[Azure Machine Learning-rekenclusters](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Ja | Ja | Ja |
|[Azure Machine Learning-rekeninstantie](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Ja (via de SDK)  | Ja |  |
|[Externe VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Ja  | Ja | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Ja (alleen lokale SDK-modus) | Ja | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Ja | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Ja | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Ja | &nbsp; |