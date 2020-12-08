---
title: Voorbeelden van Jupyter-notebooks
titleSuffix: Azure Machine Learning
description: Leer hoe u de Juypter-notebooks kunt vinden en gebruiken die zijn ontworpen om u te helpen de SDK te verkennen. Deze dienen als modellen voor uw eigen machine learning-projecten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 38d0c350718480506e1267c161a85505ece4b69d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452260"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Kennismaken met Azure Machine Learning door middel van Jupyter-notebooks

> [!NOTE] 
> Een door de community gestuurde opslagplaats met voorbeelden vindt u op https://github.com/Azure/azureml-examples.

De [voorbeeldopslagplaats van Azure Machine Learning Notebooks](https://github.com/azure/machinelearningnotebooks) bevat de nieuwste Azure Machine Learning Python SDK-voorbeelden. Deze Juypter-notebooks zijn ontworpen om u te helpen de SDK te leren kennen en als modellen te gebruiken voor uw eigen machine learning-projecten.

In dit artikel wordt beschreven hoe u toegang kunt krijgen tot de opslagplaats vanuit de volgende omgevingen:

- [Azure Machine Learning-rekeninstantie](#notebookvm)
- [Bring-Your-Own-notebook-server](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Zodra u de opslagplaats hebt gekloond, vindt u zelfstudienotebooks in de map **tutorials** en functiespecifieke notebooks in de map **how-to-use-azureml**.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Voorbeelden van Azure Machine Learning-rekenprocessen ophalen

De eenvoudigste manier om aan de slag te gaan met de voorbeelden is door voltooiing van de [Zelfstudie: omgeving en werkruimte instellen](tutorial-1st-experiment-sdk-setup.md). Zodra u klaar bent, hebt u een speciale notebookserver die vooraf is geladen met de SDK en een voorbeeldopslagplaats. Er zijn geen downloads of installaties vereist.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Voorbeelden ophalen op uw notebookserver

Voer de volgende stappen uit als u uw eigen notebookserver voor lokale ontwikkeling wilt gebruiken:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

In deze instructies worden de basis-SDK-pakketten geïnstalleerd die nodig zijn voor de quickstart- en zelfstudienotebooks. Voor andere voorbeelden van notebooks moet u mogelijk extra onderdelen installeren. Zie [De Azure Machine Learning-SDK voor Python installeren](/python/api/overview/azure/ml/install) voor meer informatie.

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Voorbeelden ophalen voor DSVM

Data Science Virtual Machine (DSVM) is een aangepaste VM-installatiekopie die speciaal is gebouwd voor datatechnologie. Als u [een DSVM maakt](how-to-configure-environment.md#dsvm), worden de SDK- en notebookserver voor u geïnstalleerd en geconfigureerd. U moet echter nog wel een werkruimte maken en de voorbeeldopslagplaats klonen.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de [voorbeeldnotebooks](https://github.com/Azure/MachineLearningNotebooks) om de mogelijkheden van Azure Machine Learning te leren kennen.

Voor meer voorbeeldprojecten en voorbeelden van GitHub raadpleegt u deze opslagplaatsen:
+ [Azure/azureml-examples](https://github.com/Azure/azureml-examples)
+ [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
+ [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

Volg deze zelfstudies:

- [Een model voor de classificatie van afbeeldingen trainen en implementeren met MNIST](tutorial-train-models-with-aml.md)

- [Gegevens voorbereiden en geautomatiseerde machine learning gebruiken om een regressiemodel met de gegevensset voor taxi's in NYC te trainen](tutorial-auto-train-models.md)
