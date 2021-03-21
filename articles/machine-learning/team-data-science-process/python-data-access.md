---
title: Toegang tot gegevens sets met python client library-team data Science process
description: Installeer en gebruik de python-client bibliotheek voor toegang tot en beheer van Azure Machine Learning gegevens veilig vanuit een lokale python-omgeving.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 497b8f5598cf7aa7720f47863d465f5e29789b07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321947"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Toegang tot gegevenssets met Python met behulp van de clientbibliotheek van Azure Machine Learning Python
Met de preview-versie van Microsoft Azure Machine Learning python-client bibliotheek kan beveiligde toegang tot uw Azure Machine Learning gegevens sets worden ingeschakeld vanuit een lokale python-omgeving en kunnen gegevens sets in een werk ruimte worden gemaakt en beheerd.

In dit onderwerp vindt u instructies voor het volgende:

* de Machine Learning python-client bibliotheek installeren
* gegevens sets openen en uploaden, inclusief instructies voor het verkrijgen van autorisatie voor toegang tot Azure Machine Learning gegevens sets vanuit uw lokale python-omgeving
* toegang tot tussenliggende gegevens sets vanuit experimenten
* Gebruik de python-client bibliotheek om gegevens sets op te sommen, toegang tot meta gegevens te krijgen, de inhoud van een gegevensset te lezen, nieuwe gegevens sets te maken en bestaande gegevens sets bij te werken

## <a name="prerequisites"></a><a name="prerequisites"></a>Vereisten
De python-client bibliotheek is getest in de volgende omgevingen:

* Windows, Mac en Linux
* Python 2,7, 3,3 en 3,4

Het is afhankelijk van de volgende pakketten:

* requests
* python-dateutil
* Pandas

U kunt het beste een python-distributie gebruiken, zoals [Anaconda](https://www.anaconda.com/) of [Canopy](https://store.enthought.com/downloads/), die wordt geleverd met python, IPython en de drie bovenstaande pakketten die hierboven zijn geïnstalleerd. Hoewel IPython niet strikt vereist is, is het een fantastische omgeving voor het interactief bewerken en visualiseren van gegevens.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>De Azure Machine Learning python-client bibliotheek installeren
Installeer de Azure Machine Learning python-client bibliotheek om de taken uit te voeren die in dit onderwerp worden beschreven. Deze bibliotheek is beschikbaar vanuit de [python-pakket index](https://pypi.python.org/pypi/azureml). Als u deze wilt installeren in uw python-omgeving, voert u de volgende opdracht uit vanuit uw lokale python-omgeving:

```console
pip install azureml
```

U kunt ook downloaden en installeren vanuit de bronnen op [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

```console
python setup.py install
```

Als Git op uw computer is geïnstalleerd, kunt u PIP gebruiken om rechtstreeks vanuit de Git-opslag plaats te installeren:

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Code fragmenten gebruiken voor toegang tot gegevens sets
De python-client bibliotheek geeft u programmatische toegang tot uw bestaande gegevens sets van experimenten die zijn uitgevoerd.

Vanuit de webinterface Azure Machine Learning Studio (klassiek) kunt u code fragmenten genereren die alle benodigde informatie bevatten voor het downloaden en deserialiseren van gegevens sets als Panda data frame-objecten op uw lokale computer.

### <a name="security-for-data-access"></a><a name="security"></a>Beveiliging voor gegevens toegang
De code fragmenten die worden geleverd door Azure Machine Learning Studio (klassiek) voor gebruik met de python-client bibliotheek bevatten uw werk ruimte-ID en autorisatie token. Deze bieden volledige toegang tot uw werk ruimte en moeten worden beveiligd, zoals een wacht woord.

Uit veiligheids overwegingen is de functie code fragment alleen beschikbaar voor gebruikers die hun rol hebben ingesteld als **eigenaar** voor de werk ruimte. Uw rol wordt weer gegeven in Azure Machine Learning Studio (klassiek) op de pagina **gebruikers** onder **instellingen**.

![Scherm afbeelding toont de instellingen op de pagina gebruikers van Azure Machine Learning Studio.][security]

Als uw rol niet is ingesteld als **eigenaar**, kunt u aanvragen om opnieuw te worden uitgenodigd als eigenaar of vraagt u de eigenaar van de werk ruimte u het code fragment te geven.

Als u het autorisatie token wilt ophalen, kunt u een van de volgende opties kiezen:

* Vraag een token aan bij een eigenaar. Eigen aren hebben toegang tot hun autorisatie tokens op de pagina instellingen van hun werk ruimte in Azure Machine Learning Studio (klassiek). Selecteer **instellingen** in het linkerdeel venster en klik op **autorisatie tokens** om de primaire en secundaire tokens weer te geven. Hoewel de primaire of de secundaire autorisatie tokens in het code fragment kunnen worden gebruikt, is het raadzaam dat de eigen aars alleen de secundaire autorisatie tokens delen.

   ![Autorisatie tokens](./media/python-data-access/ml-python-access-settings-tokens.png)

* Vraag om te worden gepromoveerd tot rol van eigenaar: een huidige eigenaar van de werk ruimte moet u eerst uit de werk ruimte verwijderen en vervolgens als eigenaar uitnodigen.

Zodra ontwikkel aars de werk ruimte-ID en het autorisatie token hebben verkregen, kunnen ze toegang krijgen tot de werk ruimte met behulp van het code fragment, ongeacht hun rol.

Verificatie tokens worden beheerd op de pagina **autorisatie tokens** onder **instellingen**. U kunt ze opnieuw genereren, maar met deze procedure wordt de toegang tot het vorige token ingetrokken.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Gegevens sets openen vanuit een lokale python-toepassing
1. Klik in Machine Learning Studio (klassiek) op **gegevens sets** in de navigatie balk aan de linkerkant.
2. Selecteer de gegevensset die u wilt openen. U kunt een wille keurig gegevens sets selecteren in de lijst met **mijn gegevens sets** of in de lijst met voor **beelden** .
3. Klik in de onderste werk balk op **gegevens toegangs code genereren**. Als de gegevens een indeling hebben die incompatibel is met de python-client bibliotheek, wordt deze knop uitgeschakeld.
   
    ![In de scherm opname worden gegevens sets weer gegeven met de toegangs CODE genereren.][datasets]
4. Selecteer het code fragment in het venster dat wordt weer gegeven en kopieer het naar het klem bord.
   
    ![Knop voor gegevens toegangs code genereren][dataset-access-code]
5. Plak de code in het notitie blok van uw lokale python-toepassing.
   
    ![Code in het notitie blok plakken][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Toegang tot tussenliggende gegevens sets vanuit Machine Learning experimenten
Nadat een experiment is uitgevoerd in Machine Learning Studio (klassiek), is het mogelijk om toegang te krijgen tot de tussenliggende gegevens sets van de uitvoer knooppunten van modules. Tussenliggende data sets zijn gegevens die zijn gemaakt en gebruikt voor tussenliggende stappen wanneer een model hulpprogramma is uitgevoerd.

Tussenliggende gegevens sets kunnen worden geopend, zolang de Data-indeling compatibel is met de python-client bibliotheek.

De volgende indelingen worden ondersteund (constanten voor deze indelingen bevinden zich in de `azureml.DataTypeIds` klasse):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

U kunt de indeling bepalen door boven een module-uitvoer knooppunt te bewegen. Het wordt samen met de naam van het knoop punt weer gegeven in de knop info.

Sommige modules, zoals de [gesplitste][split] module, worden uitgevoerd naar een indeling met `Dataset` de naam, die niet wordt ondersteund door de python-client bibliotheek.

![Indeling van gegevensset][dataset-format]

U moet een conversie module, zoals [converteren naar CSV][convert-to-csv], gebruiken om een uitvoer in een ondersteunde indeling te krijgen.

![GenericCSV-indeling][csv-format]

In de volgende stappen ziet u een voor beeld waarin een experiment wordt gemaakt, wordt uitgevoerd en de tussenliggende gegevensset wordt geopend.

1. Een nieuw experiment maken.
2. Voeg een module voor de **binaire classificatie van de volwassen telling** van de inkomsten toe.
3. Voeg een [Splits][split] module in en verbind de invoer met de module uitvoer van de gegevensset.
4. Voeg een [conversie naar CSV][convert-to-csv] -module toe en verbind de invoer met een van de [gesplitste][split] module-uitvoer.
5. Sla het experiment op, voer het uit en wacht totdat de taak is voltooid.
6. Klik op het uitvoer knooppunt in de module [converteren naar CSV][convert-to-csv] .
7. Wanneer het context menu wordt weer gegeven, selecteert u **gegevens toegangs code genereren**.
   
    ![Contextmenu][experiment]
8. Selecteer het code fragment en kopieer het naar het klem bord vanuit het venster dat wordt weer gegeven.
   
    ![Toegangs code genereren in het context menu][intermediate-dataset-access-code]
9. Plak de code in uw notitie blok.
   
    ![Code in notitie blok plakken][ipython-intermediate-dataset]
10. U kunt de gegevens visualiseren met behulp van matplotlib. Dit wordt weer gegeven in een histogram voor de kolom leeftijd:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>De Machine Learning python-client bibliotheek gebruiken voor het openen, lezen, maken en beheren van gegevens sets
### <a name="workspace"></a>Werkruimte
De werk ruimte is het toegangs punt voor de python-client bibliotheek. Geef de `Workspace` klasse op met uw werk ruimte-id en autorisatie token om een instantie te maken:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Gegevens sets opsommen
Alle gegevens sets in een bepaalde werk ruimte opsommen:

```python
for ds in ws.datasets:
    print(ds.name)
```

Als u alleen de door de gebruiker gemaakte gegevens sets wilt inventariseren:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Als u alleen de voorbeeld gegevens sets wilt inventariseren:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

U kunt een gegevensset openen op naam (dit is hoofdletter gevoelig):

```python
ds = ws.datasets['my dataset name']
```

Of u kunt de app openen via index:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Metagegevens
Data sets bevatten naast inhoud ook meta gegevens. (Tussenliggende gegevens sets vormen een uitzonde ring op deze regel en bevatten geen meta gegevens.)

Sommige meta gegevens waarden worden tijdens het maken toegewezen door de gebruiker:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Andere zijn waarden die worden toegewezen door Azure ML:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

Zie de `SourceDataset` klasse voor meer informatie over de beschik bare meta gegevens.

### <a name="read-contents"></a>Inhoud lezen
De code fragmenten die worden verschaft door Machine Learning Studio (klassiek), downloaden en deserialiseren de gegevensset automatisch naar een Panda data frame-object. Dit wordt gedaan met de- `to_dataframe` methode:

```python
frame = ds.to_dataframe()
```

Als u liever de onbewerkte gegevens downloadt en de deserialisatie zelf uitvoert, is dit een optie. Op dit moment is dit de enige optie voor indelingen zoals ' ARFF ', die de python-client bibliotheek niet kan deserialiseren.

De inhoud als tekst lezen:

```python
text_data = ds.read_as_text()
```

De inhoud als binair lezen:

```python
binary_data = ds.read_as_binary()
```

U kunt ook gewoon een stroom naar de inhoud openen:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Een nieuwe gegevensset maken
Met de python-client bibliotheek kunt u gegevens sets uploaden vanuit uw python-programma. Deze gegevens sets zijn vervolgens beschikbaar voor gebruik in uw werk ruimte.

Als u uw gegevens in een Panda data frame hebt, gebruikt u de volgende code:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Als uw gegevens al zijn geserialiseerd, kunt u het volgende gebruiken:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

De python-client bibliotheek kan een Panda data frame serialiseren met de volgende indelingen (constanten voor deze bevinden zich in de `azureml.DataTypeIds` klasse):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Een bestaande gegevensset bijwerken
Als u probeert een nieuwe gegevensset te uploaden met een naam die overeenkomt met een bestaande gegevensset, krijgt u een conflict fout te zien.

Als u een bestaande gegevensset wilt bijwerken, moet u eerst een verwijzing naar de bestaande gegevensset ophalen:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Vervolgens gebruikt `update_from_dataframe` u om de inhoud van de gegevensset in azure te serialiseren en te vervangen:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Als u de gegevens wilt serialiseren naar een andere indeling, geeft u een waarde op voor de optionele `data_type_id` para meter.

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

U kunt desgewenst een nieuwe beschrijving instellen door een waarde voor de para meter op te geven `description` .

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

U kunt desgewenst een nieuwe naam instellen door een waarde voor de para meter op te geven `name` . Vanaf nu haalt u de gegevensset op met behulp van de nieuwe naam. Met de volgende code worden de gegevens, naam en beschrijving bijgewerkt.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

De `data_type_id` `name` `description` para meters en zijn optioneel en zijn standaard ingesteld op de vorige waarde. De `dataframe` para meter is altijd vereist.

Als uw gegevens al zijn geserialiseerd, gebruikt u `update_from_raw_data` in plaats van `update_from_dataframe` . Als u zojuist doorgeeft in `raw_data` plaats van  `dataframe` , werkt het op een vergelijk bare manier.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: /azure/machine-learning/studio-module-reference/convert-to-csv
[split]: /azure/machine-learning/studio-module-reference/split-data