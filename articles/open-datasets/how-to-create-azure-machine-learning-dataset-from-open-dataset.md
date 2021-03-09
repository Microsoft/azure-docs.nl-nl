---
title: Gegevens sets maken met Azure open gegevens sets
titleSuffix: Azure Open Datasets
description: Meer informatie over het maken van een Azure Machine Learning gegevensset van Azure open gegevens sets.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: dd1440cd7bda8d40a81290cd9f633264b9641dc5
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502549"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Machine Learning gegevens sets maken op basis van Azure open gegevens sets

In dit artikel leert u hoe u verrijkings gegevens kunt meenemen in uw lokale of externe machine learning experimenten met [Azure machine learning](../machine-learning/overview-what-is-azure-ml.md) gegevens sets en [Azure open gegevens sets](./index.yml). 

Door een [Azure machine learning-gegevensset](../machine-learning/how-to-create-register-datasets.md)te maken, maakt u een verwijzing naar de locatie van de gegevens bron, samen met een kopie van de meta gegevens ervan. Omdat gegevens sets worden geëvalueerd als vertraagd, en de huidige locatie blijft bestaan, kunt u
* Geen extra opslag kosten in rekening gebracht.
* Geen risico dat u onbedoeld uw oorspronkelijke gegevens bronnen wijzigt. 
* Verbeter de prestaties van de werk stroom snelheid.

Zie het artikel over [beveiligde toegang](../machine-learning/concept-data.md#data-workflow) als u wilt weten waar gegevens sets passen in de algehele werk stroom van Azure machine learning Data Access.

Azure open gegevens sets zijn alle open bare gegevens sets die u kunt gebruiken om scenario's met specifieke functies toe te voegen om uw voorspellende oplossingen te verrijken en de nauw keurigheid te verbeteren. Raadpleeg de [catalogus](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) met gegevens sets voor open bare domeinen waarmee u machine learning modellen kunt trainen, zoals:

* [meteorologische](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [inventaris](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [reizen](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [open bare veiligheid](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn opgenomen in de [Azure machine learning PYTHON SDK](#create-datasets-with-the-sdk) en de [Azure machine learning Studio](#create-datasets-with-the-studio).


## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](../machine-learning/how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](/python/api/overview/azure/ml/install), inclusief het `azureml-datasets` pakket.

    * Maak een [Azure machine learning Compute-exemplaar](../machine-learning/how-to-create-manage-compute-instance.md), een volledig geconfigureerde en beheerde ontwikkel omgeving met daarin geïntegreerde notebooks en de SDK die al is geïnstalleerd.

    **OF**

    * Werk aan uw eigen python-omgeving en installeer zelf de SDK met [deze instructies](/python/api/overview/azure/ml/install).

> [!NOTE]
> Sommige dataset-klassen hebben afhankelijkheden van het pakket voor [azureml-dataprep](/python/api/azureml-dataprep/) . Dit is alleen compatibel met 64-bits python. Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) en CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Gegevens sets maken met de SDK

Als u Azure Machine Learning gegevens sets wilt maken via Azure-klassen voor het openen van gegevens sets in de python-SDK, moet u ervoor zorgen dat u het pakket met hebt geïnstalleerd `pip install azureml-opendatasets` . Elke afzonderlijke gegevensset wordt vertegenwoordigd door een eigen klasse in de SDK en bepaalde klassen zijn beschikbaar als een Azure machine learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)of beide. Zie de [referentie documentatie](/python/api/azureml-opendatasets/azureml.opendatasets) voor een volledige lijst met `opendatasets` klassen.

U kunt bepaalde `opendatasets` klassen ophalen als ofwel een `TabularDataset` of `FileDataset` , waarmee u de bestanden rechtstreeks kunt bewerken en/of downloaden. Andere klassen kunnen **alleen** een gegevensset ophalen met behulp `get_tabular_dataset()` `get_file_dataset()` van de functies of van de `Dataset` klasse in de python-SDK.

In de volgende code ziet u dat de MNIST- `opendatasets` klasse een of kan retour neren `TabularDataset` `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

In dit voor beeld is de `opendatasets` klasse diabetes alleen beschikbaar als een `TabularDataset` , dus het gebruik van `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Gegevens sets registreren

Registreer een Azure Machine Learning gegevensset bij uw werk ruimte, zodat u deze kunt delen met anderen en ze opnieuw moet gebruiken voor experimenten in uw werk ruimte. Wanneer u een Azure Machine Learning gegevensset registreert die is gemaakt op basis van open gegevens sets, worden er geen gegevens direct gedownload, maar worden de gegevens later weer gegeven wanneer daarom wordt gevraagd (bijvoorbeeld tijdens training) vanuit een centrale opslag locatie.

Als u uw gegevens sets wilt registreren bij een werk ruimte, gebruikt u de- [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#register-workspace--name--description-none--tags-none--create-new-version-false-) methode. 

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Gegevens sets maken met de Studio

U kunt ook Azure Machine Learning gegevens sets maken op basis van Azure open gegevens sets met de [Azure machine learning Studio](https://ml.azure.com), een geconsolideerde webinterface die machine learning hulpprogram ma's bevat voor het uitvoeren van data Science-scenario's voor data Wetenschappen van alle vaardigheids niveaus.

> [!Note]
> Gegevens sets die zijn gemaakt via Azure Machine Learning Studio, worden automatisch geregistreerd bij de werk ruimte.

1. Selecteer in uw werk ruimte het tabblad **gegevens sets** onder **assets**. Selecteer in de vervolg keuzelijst **gegevensset maken** de optie **uit geopende gegevens sets**.

    ![Gegevensset openen met de gebruikers interface](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Selecteer een gegevensset door de tegel ervan te selecteren. (U hebt de mogelijkheid om te filteren met behulp van de zoek balk.) Selecteer **volgende**.

    ![Gegevensset kiezen](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Kies een naam waaronder u de gegevensset wilt registreren en de gegevens eventueel kunt filteren met behulp van de beschik bare filters. In dit geval filtert u voor de gegevensset van de **open bare feest dagen** de periode tot één jaar en de land code naar alleen de Verenigde Staten. Zie de [catalogus Azure open data sets](https://azure.microsoft.com/services/open-datasets/catalog) voor gegevens details, zoals veld beschrijvingen en datumbereiken. Selecteer **Maken**.

    ![Gegevensset-para meters instellen en gegevensset maken](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    De gegevensset is nu beschikbaar in uw werk ruimte onder **gegevens sets**. U kunt deze op dezelfde manier gebruiken als andere gegevens sets die u hebt gemaakt.


## <a name="access-datasets-for-your-experiments"></a>Toegang tot gegevens sets voor uw experimenten

Gebruik uw gegevens sets in uw machine learning experimenten voor trainings ML-modellen. Meer [informatie over het trainen van gegevens sets](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Voorbeeldnotebooks

Voor voor beelden en demonstraties van de functionaliteit van open gegevens sets raadpleegt u deze [voorbeeld notitieblokken](samples.md).

## <a name="next-steps"></a>Volgende stappen

* [Train uw eerste ml-model](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* [Train met gegevens sets](../machine-learning/how-to-train-with-datasets.md).

* [Een Azure machine learning-gegevensset maken](../machine-learning/how-to-create-register-datasets.md).
