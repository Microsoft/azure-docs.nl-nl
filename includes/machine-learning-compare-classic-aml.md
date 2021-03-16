---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563183"
---
De volgende tabel bevat een overzicht van enkele van de belangrijkste verschillen tussen ML Studio (klassiek) en Azure Machine Learning.

| Functie | ML Studio (klassiek) | Azure Machine Learning |
|---| --- | --- |
| Interface met slepen en neerzetten | Klassieke ervaring | Bijgewerkte ervaring - [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)| 
| Code-SDK's | Niet ondersteund | Volledig geïntegreerd met [Azure Machine Learning Python](/python/api/overview/azure/ml/) en [R](https://github.com/Azure/azureml-sdk-for-r) SDK's |
| Experiment | Schaalbaar (max. 10 GB aan trainingsgegevens) | Schalen met rekendoel |
| Rekendoelen voor training | Eigen rekendoel, alleen CPU-ondersteuning | Breed scala aan aanpasbare [rekendoelen voor training](../articles/machine-learning/concept-compute-target.md#train). Inclusief GPU- en CPU-ondersteuning | 
| Rekendoelen voor implementatie | Bedrijfseigen webservice-indeling, niet aanpasbaar | Breed scala aan aanpasbare [rekendoelen voor implementatie](../articles/machine-learning/concept-compute-target.md#deploy). Inclusief GPU- en CPU-ondersteuning |
| ML-pijplijn | Niet ondersteund | Bouw flexibele, modulaire [pijplijnen](../articles/machine-learning/concept-ml-pipelines.md) om werkstromen te automatiseren |
| MLOps | Eenvoudig modellen beheren en implementeren; alleen voor CPU-implementaties | Versiebeheer voor entiteiten (model, gegevens, werkstromen), werkstroomautomatisering, integratie met CICD-hulpprogramma, CPU- en GPU-implementaties [en meer](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Modelindeling | Eigen indeling, alleen Studio (klassiek) | Meerdere ondersteunde indelingen, afhankelijk van het type trainingstaak |
| Geautomatiseerde modeltraining en afstemming van hyperparameters |  Niet ondersteund | [Ondersteund](../articles/machine-learning/concept-automated-ml.md). Opties voor code-eerst en geen-code. | 
| Gegevensdriftdetectie | Niet ondersteund | [Ondersteund](../articles/machine-learning/how-to-monitor-datasets.md) |
| Projecten voor gegevenslabels | Niet ondersteund | [Ondersteund](../articles/machine-learning/how-to-create-labeling-projects.md) |
| RBAC (op rollen gebaseerd toegangsbeheer) | Rol alleen Inzender en eigenaar | [Flexibele functie definitie en RBAC-besturings element](../articles/machine-learning/how-to-assign-roles.md) |
| AI-galerie | Ondersteund ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Niet ondersteund <br><br> Meer informatie over voor [beelden van PYTHON SDK-notebooks](https://github.com/Azure/MachineLearningNotebooks). |