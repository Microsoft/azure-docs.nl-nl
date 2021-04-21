---
title: Metrische gegevens in de ontwerpfunctie logboeken
titleSuffix: Azure Machine Learning
description: Bemonitor uw azure ML Designer-experimenten. Schakel logboekregistratie in met behulp van de module Python-script uitvoeren en bekijk de vastgelegde resultaten in de studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: 13a3b86514428b0219aaf671260c07b4e197d2de
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817291"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Logboekregistratie inschakelen in Azure Machine Learning designer-pijplijnen


In dit artikel leert u hoe u logboekcode kunt toevoegen aan designer-pijplijnen. U leert ook hoe u deze logboeken kunt weergeven met behulp Azure Machine Learning-studio webportal.

Zie Azure [ML-experiment](how-to-log-view-metrics.md)uitvoeren en metrische gegevens bewaken voor meer informatie over het vastleggen van metrische gegevens met behulp van de SDK-ontwerpervaring.

## <a name="enable-logging-with-execute-python-script"></a>Logboekregistratie inschakelen met Python-script uitvoeren

Gebruik de module [Python-script uitvoeren](./algorithm-module-reference/execute-python-script.md) om logboekregistratie in designer-pijplijnen in teschakelen. Hoewel u elke waarde met deze werkstroom kunt bijhouden, is het vooral handig om metrische gegevens uit de module __Evaluate Model__ bij te houden om de modelprestaties voor uitvoeringen bij te houden.

In het volgende voorbeeld ziet u hoe u de gemiddelde kwadraatfout van twee getrainde modellen kunt inloggen met behulp van de modules Evaluate Model en Execute Python Script.

1. Verbind een __module Python-script__ uitvoeren met de uitvoer van de module __Evaluate Model.__

    ![Connect Execute Python Script module to Evaluate Model module (Python-scriptmodule uitvoeren verbinden met de module Evaluate Model)](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. Plak de volgende code in de code-editor __Python-script__ uitvoeren om de gemiddelde absolute fout voor uw getrainde model in een logboek te zetten. U kunt een vergelijkbaar patroon gebruiken om een andere waarde in de ontwerpfunctie vast te maken:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Deze code maakt gebruik van de Azure Machine Learning Python SDK voor het logboeken van waarden. Er wordt Run.get_context() gebruikt om de context van de huidige run op te halen. Vervolgens worden waarden in die context gelogd met de methode run.parent.log(). Hiermee worden waarden bij de bovenliggende pijplijn uitgevoerd in een logboek `parent` in plaats van de module-run.

Zie Logboekregistratie inschakelen in Azure [ML-trainings](how-to-log-view-metrics.md)uitgevoerd voor meer informatie over het gebruik van de Python SDK om waarden te registreren.

## <a name="view-logs"></a>Logboeken weergeven

Nadat de pijplijn is uitgevoerd, ziet u de Mean_Absolute_Error *op* de pagina Experimenten.

1. Navigeer naar **de sectie Experimenten.**
1. Selecteer uw experiment.
1. Selecteer de run in het experiment dat u wilt weergeven.
1. Selecteer **Metrische gegevens**.

    ![Metrische gegevens over uitvoeren weergeven in de studio](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u logboeken gebruikt in de ontwerpfunctie. Zie de volgende verwante artikelen voor de volgende stappen:


* Zie Fouten opsporen in ml-pijplijnen voor meer informatie over het oplossen van problemen met [designer-pijplijnen & oplossen.](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Zie Logboekregistratie inschakelen in Azure ML-trainings uitgevoerd voor meer informatie over het gebruik van de Python SDK voor het vastleggen van metrische gegevens in de [SDK-ontwerpervaring.](how-to-log-view-metrics.md)
* Meer informatie over het gebruik [van Python-script uitvoeren](./algorithm-module-reference/execute-python-script.md) in de ontwerpfunctie.
