---
title: Continu Azure Machine Learning implementeren
titleSuffix: Azure Machine Learning
description: Leer hoe u continu modellen implementeert met de Azure Machine Learning DevOps-extensie. Automatisch nieuwe modelversies controleren en implementeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: tracking-python, deploy
ms.openlocfilehash: aae0194269d79371189998268bd87b9ea4a6b4d4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885538"
---
# <a name="continuously-deploy-models"></a>Continu modellen implementeren

In dit artikel wordt beschreven hoe u continue implementatie in Azure DevOps gebruikt om automatisch te controleren op nieuwe versies van geregistreerde modellen en deze nieuwe modellen in productie te pushen.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgenomen dat u al een model hebt geregistreerd in Azure Machine Learning werkruimte. Zie [deze zelfstudie](how-to-train-scikit-learn.md) voor een voorbeeld van het trainen en registreren van een scikit-learn-model.

## <a name="continuously-deploy-models"></a>Continu modellen implementeren

U kunt continu modellen implementeren met behulp van de Machine Learning-extensie voor [Azure DevOps.](https://azure.microsoft.com/services/devops/) U kunt de Machine Learning-extensie voor Azure DevOps gebruiken om een implementatiepijplijn te activeren wanneer een nieuw machine learning-model wordt geregistreerd in een Azure Machine Learning werkruimte.

1. Meld u aan [voor Azure Pipelines,](/azure/devops/pipelines/get-started/pipelines-sign-up)waardoor continue integratie en levering van uw toepassing op elk platform of in de cloud mogelijk is. (Houd er rekening mee dat Azure Pipelines niet hetzelfde is als [Machine Learning pijplijnen](concept-ml-pipelines.md#compare).)

1. [Maak een Azure DevOps-project.](/azure/devops/organizations/projects/create-project)

1. Installeer de [Machine Learning-extensie voor Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Gebruik serviceverbindingen om een service-principalverbinding in te stellen met Azure Machine Learning werkruimte, zodat u toegang hebt tot uw artefacten. Ga naar projectinstellingen, selecteer **Serviceverbindingen** en selecteer vervolgens **Azure Resource Manager**:

    [![Selecteer Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Selecteer in **de lijst Bereikniveau** **de optie AzureMLWorkspace** en voer de rest van de waarden in:

    ![Selecteer AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Als u uw machine learning wilt implementeren met behulp van Azure Pipelines, selecteert u onder Pijplijnen de **optie Release**. Voeg een nieuw artefact toe en selecteer vervolgens het **artefact AzureML-model** en de serviceverbinding die u eerder hebt gemaakt. Selecteer het model en de versie om een implementatie te activeren:

    [![AzureML-model selecteren](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Schakel de modeltrigger in voor uw modelartefact. Wanneer u de trigger inschakelen, wordt telkens wanneer de opgegeven versie (dat wil zeggen, de nieuwste versie) van dat model geregistreerd in uw werkruimte, een Azure DevOps-release-pijplijn geactiveerd.

    [![De modeltrigger inschakelen](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk de onderstaande projecten op GitHub voor meer voorbeelden van continue implementatie voor ML-modellen.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)