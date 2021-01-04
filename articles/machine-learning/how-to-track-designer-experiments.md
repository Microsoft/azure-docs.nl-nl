---
title: Metrische gegevens in de ontwerp functie vastleggen
titleSuffix: Azure Machine Learning
description: Bewaak uw experimenten voor Azure ML Designer. Schakel logboek registratie in met de script module python uitvoeren en Bekijk de geregistreerde resultaten in de Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 11/25/2020
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: 29d83f4acddfce6294457f87519d62e35f52bf15
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/21/2020
ms.locfileid: "97709415"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Logboek registratie inschakelen in Azure Machine Learning Designer-pijp lijnen


In dit artikel leert u hoe u logboek registratie code kunt toevoegen aan Designer-pijp lijnen. U leert ook hoe u deze logboeken kunt weer geven met behulp van de Azure Machine Learning Studio-webportal.

Voor meer informatie over het vastleggen van metrische gegevens met behulp van de SDK-ontwerp functie raadpleegt u [Azure ml experimenten en metrische gegevens bewaken](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Logboek registratie inschakelen met python-script uitvoeren

Gebruik de __script module python uitvoeren__ om logboek registratie in Designer-pijp lijnen in te scha kelen. Hoewel u elke wille keurige waarde kunt registreren bij deze werk stroom, is het vooral nuttig om metrische gegevens vast te leggen in de module __Evaluate model__ om de model prestaties voor uitvoeringen bij te houden.

In het volgende voor beeld ziet u hoe u de gemiddelde fout van twee getrainde modellen kunt vastleggen met behulp van het model evalueren en python-script modules uitvoeren.

1. Verbind een script module voor het __uitvoeren van python__ met de uitvoer van de module __Evaluate model__ .

    ![Python-script module voor het uitvoeren van verbinding maken om de model module te evalueren](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Plak de volgende code in de script code-editor voor __python uitvoeren__ om de gemiddelde absolute fout voor uw getrainde model te registreren. U kunt een vergelijkbaar patroon gebruiken om een andere waarde in de ontwerp functie te registreren:

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
        # Log right output port result of Evaluate Model.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Deze code maakt gebruik van de Azure Machine Learning python SDK om waarden te registreren. Er wordt gebruikgemaakt van Run.get_context () om de context van de huidige uitvoering te verkrijgen. Vervolgens worden waarden in deze context geregistreerd met de methode Run. Parent. log (). Het gebruikt `parent` om waarden te registreren voor de bovenliggende pijplijn uitvoering in plaats van dat de module wordt uitgevoerd.

Zie voor meer informatie over het gebruik van de python-SDK voor het vastleggen van waarden [logboek registratie inschakelen in training van Azure ml](how-to-track-experiments.md).

## <a name="view-logs"></a>Logboeken weergeven

Nadat de uitvoering van de pijp lijn is voltooid, kunt u de *Mean_Absolute_Error* zien op de pagina experimenten.

1. Navigeer naar het gedeelte **experimenten** .
1. Selecteer uw experiment.
1. Selecteer de uitvoering in het experiment dat u wilt weer geven.
1. Selecteer **Metrische gegevens**.

    ![Metrische uitvoerings gegevens weer geven in de Studio](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Logboeken in de ontwerp functie kunt gebruiken. Zie voor de volgende stappen deze gerelateerde artikelen:


* Meer informatie over het oplossen van problemen met Designer-pijp lijnen vindt u in [Debug & Troubleshooting ml-pijp lijnen oplossen](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Meer informatie over het gebruik van de python-SDK voor het vastleggen van metrische gegevens in de ontwikkel ervaring van de SDK raadpleegt u [logboek registratie inschakelen in azure ml-trainings uitvoeringen](how-to-track-experiments.md).
