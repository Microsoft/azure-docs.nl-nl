---
title: Gegevens splitsingen en kruis validatie in geautomatiseerde machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over het configureren van gegevensset-splitsingen en kruis validatie voor automatische machine learning experimenten
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 2e26bfa484d573c0158e518b31087fb10bdcdfb9
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185679"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Gegevenssplitsingen en kruisvalidatie configureren in geautomatiseerde machine learning

In dit artikel vindt u informatie over de verschillende opties voor het configureren van de splitsingen van trainings-en validatie gegevens en kruis validatie voor uw geautomatiseerde machine learning, automatische ML, experimenten.

Wanneer u in Azure Machine Learning gebruikmaakt van automatische ML om meerdere ML-modellen te bouwen, moet elke onderliggende uitvoering het gerelateerde model valideren door de gegevens over de kwaliteit van het model te berekenen, zoals nauw keurigheid of AUC gewogen. Deze metrische gegevens worden berekend door de voor spellingen te vergelijken die zijn gemaakt met elk model met echte labels uit eerdere waarnemingen in de validatie gegevens. 

Automatische ML experimenten voeren automatisch model validatie uit. In de volgende secties wordt beschreven hoe u validatie-instellingen verder kunt aanpassen met behulp van de [Azure machine learning PYTHON SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Zie [uw geautomatiseerde machine learning experimenten in azure machine learning Studio maken](how-to-use-automated-ml-for-ml-models.md)voor een programma met weinig code of zonder code. 

> [!NOTE]
> De Studio ondersteunt momenteel de splitsingen van trainings-en validatie gegevens en opties voor kruis validatie, maar biedt geen ondersteuning voor het opgeven van afzonderlijke gegevens bestanden voor uw validatieset. 

## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u het volgende nodig:

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor het maken van de werk ruimte.

* Vertrouwd met het instellen van een automatische machine learning experiment met de Azure Machine Learning SDK. Volg de [zelf studie](tutorial-auto-train-models.md) of Lees [hoe](how-to-configure-auto-train.md) u de ontwerp patronen voor het basisautomatische machine learning experiment kunt zien.

* Een goed idee van de splitsing van Train/validatie gegevens en kruis validatie als machine learning concepten. Voor een uitleg op hoog niveau

    * [Over Train-, validatie-en test sets in Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Kruis validatie begrijpen in machine learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default-data-splits-and-cross-validation"></a>Standaard gegevens splitsingen en kruis validatie

Gebruik het object [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) om uw instellingen voor experimenteren en trainingen te definiëren. In het volgende code fragment ziet u dat alleen de vereiste para meters zijn gedefinieerd. Dit zijn de para meters voor `n_cross_validation` of `validation_ data` worden **niet** opgenomen.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Als u een `validation_data` of `n_cross_validation` -para meter niet expliciet opgeeft, past AutoML standaard technieken toe, afhankelijk van het aantal rijen in de opgegeven gegevensset `training_data` :

|Grootte van de trainings &nbsp; gegevens &nbsp;| Validatie techniek |
|---|-----|
|**Meer &nbsp; dan &nbsp; 20.000 &nbsp; rijen**| De splitsing van Train/validatie gegevens wordt toegepast. De standaard instelling is om 10% van de eerste set met trainings gegevens te nemen als de validatieset. Deze validatieset wordt vervolgens gebruikt voor de berekening van metrische gegevens.
|**Kleiner &nbsp; dan &nbsp; 20.000 &nbsp; rijen**| De methode voor kruis validatie wordt toegepast. Het standaard aantal vouwen is afhankelijk van het aantal rijen. <br> **Als de gegevensset kleiner is dan 1.000 rijen**, worden er 10 vouwen gebruikt. <br> **Als de rijen tussen 1.000 en 20.000** liggen, worden er drie vouwen gebruikt.

## <a name="provide-validation-data"></a>Validatie gegevens opgeven

In dit geval kunt u beginnen met één gegevens bestand en deze splitsen in trainings-en validatie sets, of u kunt een afzonderlijk gegevens bestand voor de validatieset opgeven. In beide gevallen `validation_data` wijst de para meter in uw `AutoMLConfig` object welke gegevens moeten worden gebruikt als uw validatieset. Deze para meter accepteert alleen gegevens sets in de vorm van een [Azure machine learning dataset](how-to-create-register-datasets.md) of een Panda data frame.   

In het volgende code voorbeeld wordt expliciet aangegeven welk gedeelte van de gegeven gegevens in `dataset` moet worden gebruikt voor training en validatie.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Grootte van validatieset opgeven

In dit geval wordt er slechts één gegevensset voor het experiment gegeven. Dat wil zeggen, de `validation_data` para meter is **niet** opgegeven en de opgegeven gegevensset wordt toegewezen aan de  `training_data` para meter.  In uw `AutoMLConfig` object kunt u de `validation_size` para meter instellen om een deel van de trainings gegevens voor validatie op te slaan. Dit betekent dat de validatieset wordt gesplitst door AutoML van de oorspronkelijke `training_data` waarde. Deze waarde moet tussen 0,0 en 1,0 niet inclusief (bijvoorbeeld 0,2 betekent dat er 20% van de gegevens voor validatie gegevens wordt uitgecheckt).

Zie het volgende code voorbeeld:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>Het aantal Kruis validaties instellen

Als u kruis validatie wilt uitvoeren, neemt u de `n_cross_validations` para meter op en stelt u deze in op een waarde. Deze para meter bepaalt hoeveel Kruis validaties worden uitgevoerd op basis van hetzelfde aantal vouwen.

In de volgende code worden vijf vouwen voor kruis validatie gedefinieerd. Daarom gebruiken vijf verschillende trainingen, elke training met 4/5 van de gegevens en elke validatie met behulp van 1/5 van de gegevens met een andere evaluatie vouwen.

Als gevolg hiervan worden metrische gegevens berekend met het gemiddelde van de vijf validatie-metrische gegevens.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Aangepaste Vouw gegevens voor kruis validatie opgeven

U kunt ook uw eigen CV-gegevens vouwen (kruis validatie) opgeven. Dit wordt beschouwd als een geavanceerd scenario omdat u opgeeft welke kolommen u wilt splitsen en gebruiken voor validatie.  Voeg aangepaste AVK Splits kolommen toe aan uw trainings gegevens en geef op welke kolommen u wilt gebruiken om de kolom namen in de para meter in te vullen `cv_split_column_names` . Elke kolom vertegenwoordigt een kruis validatie gesplitst en wordt gevuld met gehele getallen 1 of 0--waarbij 1 aangeeft dat de rij moet worden gebruikt voor de training en 0 geeft aan dat de rij moet worden gebruikt voor validatie.

Het volgende code fragment bevat Bank marketing gegevens met twee gesplitste kolommen CV1 en CV2.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Als u `cv_split_column_names` met en wilt gebruiken, moet u `training_data` `label_column_name` een upgrade uitvoeren van uw Azure machine learning python SDK-versie 1.6.0 of hoger. Raadpleeg voor eerdere SDK-versies `cv_splits_indices` , maar houd er rekening mee dat deze alleen wordt gebruikt in combi natie met `X` en alleen de invoer van de `y` gegevensset. 

## <a name="next-steps"></a>Volgende stappen

* [Geen gebalanceerde gegevens en overmontage](concept-manage-ml-pitfalls.md).
* [Zelf studie: automatische machine learning gebruiken om de sectie gegevens over de taxi tarieven te voors pellen](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Het [automatisch trainen van een time-series-prognose model](how-to-auto-train-forecast.md).