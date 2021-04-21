---
title: 'Zelfstudie over classificatie van afbeeldingen: Modellen trainen'
titleSuffix: Azure Machine Learning
description: Gebruik Azure Machine Learning om een model voor het classificeren van afbeeldingen te trainen met scikit-learn in een Python Jupyter-notebook. Deze zelfstudie is deel één van een serie van twee.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 6c5691759983d8ec40598834e5dbcd507ccf00cf
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816868"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Zelfstudie: Modellen voor de classificatie van afbeeldingen trainen met MNIST-gegevens en scikit-learn 


In deze zelfstudie gaat u een machine learning-model trainen op externe rekenresources. U gebruikt de werkstroom voor training en implementatie voor Azure Machine Learning in een Python Jupyter-notebook.  Vervolgens kunt u het notebook gebruiken als een sjabloon voor het trainen van uw eigen machine learning-model met uw eigen gegevens. Deze zelfstudie is **deel één van een serie van twee**.  

In deze zelfstudie traint u een eenvoudig logistieke regressiemodel met behulp van de gegevensset [MNIST](http://yann.lecun.com/exdb/mnist/) en [scikit-learn](https://scikit-learn.org) met Azure Machine Learning. MNIST is een populaire gegevensset die bestaat uit 70.000 afbeeldingen in grijstinten. Elke afbeelding is een handgeschreven cijfer van 28 x 28 pixels, dat een getal tussen 0-9 vertegenwoordigt. Het doel is om een classificatiemechanisme met meerdere klassen te maken om het cijfer te identificeren dat een bepaalde afbeelding vertegenwoordigt.

U leert hoe u de volgende acties uitvoert:

> [!div class="checklist"]
> * De ontwikkelomgeving instellen.
> * De gegevens downloaden en controleren.
> * U gaat een eenvoudig logistiek regressiemodel trainen op een extern cluster.
> * Trainingsresultaten bekijken en het beste model registreren.

In [deel twee van deze zelfstudie](tutorial-deploy-models-with-aml.md) leert u hoe u een model selecteert en dit implementeert.

Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

>[!NOTE]
> Code in dit artikel is getest met versie 1.13.0 van de [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro).

## <a name="prerequisites"></a>Vereisten

* U moet de [Zelfstudie: Ga aan de slag met het maken van uw eerste Azure ML-experiment](tutorial-1st-experiment-sdk-setup.md) en doe het volgende:
    * Een werkruimte maken
    * Kloon het zelfstudie-notebook naar uw map in de werkruimte.
    * Maak een cloudgebaseerd rekeninstantie.

* Open in uw gekloonde map *tutorials/image-classification-mnist-data* de notebook *img-classification-part1-training.ipynb*. 


De zelfstudie en het bijbehorende bestand **utils.py** zijn ook beschikbaar op [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) als u deze wilt gebruiken in uw eigen [lokale omgeving](how-to-configure-environment.md#local). Voer `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` uit om afhankelijkheden te installeren voor deze zelfstudie.

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als die u ziet in de notebook.  
>
> Schakel nu over naar de Jupyter-notebook als u wilt meelezen tijdens het uitvoeren van de code. 
> Als u één codecel in een notebook wilt uitvoeren, klikt u op de codecel en drukt u op **Shift+Enter**. U kunt ook de hele notebook uitvoeren door **Alle uitvoeren** te kiezen op de bovenste werkbalk.

## <a name="set-up-your-development-environment"></a><a name="start"></a>De ontwikkelomgeving instellen

De configuratie van uw ontwikkelomgeving kan worden uitgevoerd met een Python-notebook. De configuratie bestaat uit de volgende acties:

* Python-pakketten importeren.
* Verbinding maken met een werkruimte, zodat uw lokale computer met externe bronnen kan communiceren.
* Een experiment maken om alle uitvoeringen (runs) bij te houden.
* Een extern rekendoel voor training maken.

### <a name="import-packages"></a>Pakketten importeren

Importeer de Python-pakketten die u nodig hebt in deze sessie. Controleer ook wat de versie is van de SDK voor Azure Machine Learning:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Verbinding maken met een werkruimte

Maak een werkruimte-object van de bestaande werkruimte. `Workspace.from_config()` leest het bestand **config.json** en laadt de gegevens in een object met de naam `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> U wordt mogelijk gevraagd om u te verifiëren bij uw werkruimte wanneer u de volgende code de eerste keer hebt uitgevoerd. Volg de instructies op het scherm.

### <a name="create-an-experiment"></a>Een experiment maken

Maak een experiment voor het volgen van de runs in uw werkruimte. Een werkruimte kan meerdere experimenten bevatten:

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Een bestaand rekendoel maken of koppelen

Met Azure Machine Learning Compute, een beheerde service, kunnen gegevenswetenschappers Machine Learning-modellen trainen op clusters met virtuele Azure-machines. Voorbeelden hiervan zijn virtuele machines met GPU-ondersteuning. In deze zelfstudie maakt u Azure Machine Learning Compute als uw trainingsomgeving. Verderop in de zelfstudie gaat u de Python-code verzenden om deze op deze VM uit te voeren. 

Met de onderstaande code wordt het rekencluster voor u gemaakt als dat nog niet in uw werkruimte bestaat. Er wordt een cluster ingesteld dat omlaag wordt geschaald naar 0 wanneer het niet in gebruik is en dat kan worden geschaald naar maximaal vier knooppunten. 

 **Het maken van het rekendoel duurt ongeveer vijf minuten.** Als de rekenresource al aanwezig is in de werkruimte, wordt het cluster door de code gebruikt en wordt er geen nieuw cluster gemaakt.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

U beschikt nu over de vereiste pakketten en rekenresources voor het trainen van een model in de cloud. 

## <a name="explore-data"></a>Gegevens verkennen

Voordat u een model gaat trainen, is het belangrijk dat u de gegevens begrijpt die u gebruikt voor het trainen. In dit gedeelte leert u het volgende:

* De MNIST-gegevensset downloaden.
* Enkele voorbeeldafbeeldingen weergeven.

### <a name="download-the-mnist-dataset"></a>De MNIST-gegevensset downloaden

Gebruik Azure Open Datasets om de onbewerkte MNIST-gegevensbestanden op te halen. [Azure Open Datasets](../open-datasets/overview-what-are-open-datasets.md) zijn samengestelde openbare gegevenssets die u kunt gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Elke gegevensset heeft een bijbehorende klasse, in dit geval `MNIST`, om de gegevens op verschillende manieren op te halen.

Met deze code worden de gegevens opgehaald als een `FileDataset`-object, dat een subklasse van `Dataset` is. Een `FileDataset` verwijst naar een of meer bestanden met elke indeling in uw gegevensarchieven of openbare URL's. De klasse biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw rekenresource door een verwijzing naar de locatie van de gegevensbron te maken. Daarnaast registreert u de gegevensset bij uw werkruimte, zodat u deze eenvoudig kunt ophalen tijdens de training.

Volg de [Uitleg](how-to-create-register-datasets.md) voor meer informatie over gegevenssets en hun gebruik in de SDK.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Enkele voorbeeldafbeeldingen weergeven

Laad de gecomprimeerde bestanden in `numpy`-matrices. Gebruik vervolgens `matplotlib` om 30 willekeurige afbeeldingen uit de gegevensset te tekenen, met de bijbehorende labels erboven. Voor deze stap hebt u een `load_data`-functie nodig die is opgenomen in een `utils.py`-bestand. Dit bestand staat in de map met voorbeelden. Zorg ervoor dat u het bestand in de map met dit notebook zet. De functie `load_data` parseert de gecomprimeerde bestanden simpelweg in numpy-matrices.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Er wordt een steekproef van afbeeldingen weergegeven:

![Steekproef van afbeeldingen](./media/tutorial-train-models-with-aml/digits.png)

U hebt nu een beter beeld van hoe deze afbeeldingen eruit zien en wat u voor resultaat kunt verwachten van de voorspelling.

## <a name="train-on-a-remote-cluster"></a>Trainen op een extern cluster

Voor deze taak verstuurt u de taak om uit te voeren op het cluster voor externe training dat u eerder hebt ingesteld.  Om een taak te verzenden, moet u het volgende doen:
* Een map maken
* Een trainingsscript maken
* Een configuratie voor het uitvoeren van een script maken
* De taak verzenden

### <a name="create-a-directory"></a>Een map maken

Maak een map om de benodigde code vanaf uw computer aan te bieden aan de externe resource.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Een trainingsscript maken

Als u de taak naar het cluster wilt verzenden, moet u eerst een trainingsscript maken. Voer de volgende code uit om in de map die u net hebt gemaakt een trainingsscript te maken met de naam `train.py`.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

U ziet hoe met het script gegevens worden opgehaald en modellen worden opgeslagen:

+ Met het trainingsscript leest u een argument om de map met de gegevens te vinden. Als u de taak later verstuurt, wijst u naar het gegevensarchief voor dit argument: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Het trainingsscript slaat uw model op in een map met de naam **outputs**. Alles gegevens die naar deze map worden geschreven, worden automatisch geüpload naar uw werkruimte. Verderop in de zelfstudie gaat u dit model openen vanuit deze map. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Voor het trainingsscript is het bestand `utils.py` vereist om de gegevensset juist te laden. Met de volgende code kopieert u `utils.py` naar `script_folder`, zodat het bestand samen met het trainingsscript vanaf de externe resource kan worden geopend.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>De trainingstaak configureren

Maak een [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)-object om de configuratiegegevens van uw trainingstaak op te geven, inclusief het trainingsscript, de omgeving die u wilt gebruiken en het rekendoel om uit te voeren. Configureer de ScriptRunConfig door het volgende op te geven:

* De map met uw scripts. Alle bestanden in deze map worden naar de clusterknooppunten geüpload voor uitvoering.
* Het rekendoel. In dit geval gebruikt u het Azure Machine Learning-rekencluster dat u hebt gemaakt.
* De naam van het trainingsscript, **train.py**.
* Een omgeving met de bibliotheken die nodig zijn om het script uit te voeren.
* Vereiste argumenten uit het trainingsscript.

In deze zelfstudie bestaat dit doel uit AmlCompute. Alle bestanden in de scriptmap worden naar de clusterknooppunten geüpload om te worden uitgevoerd. De **--data_folder** is ingesteld om de gegevensset te gebruiken.

Maak eerst de omgeving die het volgende bevat: de bibliotheek scikit-learn, azureml-dataset-runtime die is vereist voor toegang tot de gegevensset, en azureml-defaults die de afhankelijkheden voor het vastleggen van metrische gegevens bevat. De azureml-defaults bevat ook de afhankelijkheden die nodig zijn om in deel 2 van de zelfstudie het model te implementeren als een webservice.

Nadat de omgeving is gedefinieerd, registreert u deze bij de werkruimte om deze omgeving opnieuw te gebruiken in deel 2 van de zelfstudie.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Maak vervolgens de ScriptRunConfig door het trainingsscript, het rekendoel en de omgeving op te geven.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>De taak verzenden naar het cluster

Voer het experiment uit door het ScriptRunConfig-object in te dienen:

```python
run = exp.submit(config=src)
run
```

Aangezien de aanroep asynchroon is, retourneert deze een status **Preparing** of **Running** zodra de taak is gestart.

## <a name="monitor-a-remote-run"></a>Een externe run controleren

In totaal duurt de eerste run **ongeveer tien minuten**. Voor latere runs wordt dezelfde afbeelding echter opnieuw gebruikt indien de scriptafhankelijkheden niet zijn gewijzigd. Hierdoor is de opstarttijd van de container veel korter.

Terwijl u wacht, gebeurt het volgende:

- **Afbeelding maken**: Er wordt een Docker-afbeelding gemaakt die overeenkomt met de Python-omgeving die is opgegeven in de Azure ML-omgeving. De afbeelding wordt naar de werkruimte geüpload. Het maken en uploaden van de afbeelding duurt **circa vijf minuten**.

  Deze fase vindt eenmaal plaats voor elke Python-omgeving, aangezien de container voor volgende runs in de cache wordt opgeslagen. Tijdens het maken van de afbeelding, worden er logboeken gestreamd naar de uitvoeringsgeschiedenis. U kunt de voortgang van het maken van afbeeldingen volgen aan de hand van deze logboeken.

- **Schalen**: Als voor het externe cluster meer knooppunten zijn vereist voor het uitvoeren van de run dan er momenteel beschikbaar zijn, worden er automatisch extra knooppunten toegevoegd. Het schalen duurt meestal **ongeveer vijf minuten.**

- **Uitvoeren**: In deze fase worden de noodzakelijke scripts en bestanden verzonden naar het rekendoel. Vervolgens worden gegevensarchieven gekoppeld of gekopieerd. Ten slotte wordt het **entry_script** uitgevoerd. Terwijl de taak wordt uitgevoerd, worden **stdout** en de map **./logs** naar de uitvoeringsgeschiedenis gestreamd. U kunt de voortgang van de run volgen aan de hand van deze logboeken.

- **Nabewerken**: De map **./outputs** van de run wordt naar de uitvoeringsgeschiedenis in uw werkruimte gekopieerd, zodat u deze resultaten kunt bekijken.

U kunt de voortgang van een actieve taak op verschillende manieren controleren. In deze zelfstudie wordt gebruikgemaakt van een Jupyter-widget en de methode `wait_for_completion`.

### <a name="jupyter-widget"></a>Jupyter-widget

U kunt de voortgang van de run volgen met een [Jupyter-widget](/python/api/azureml-widgets/azureml.widgets). Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10 tot 15 seconden live updates totdat de taak is voltooid:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Aan het einde van de training ziet de widget er als volgt uit:

![Notebook-widget](./media/tutorial-train-models-with-aml/widget.png)

Als u een uitvoering wilt annuleren, kunt u [deze instructies](./how-to-track-monitor-analyze-runs.md) volgen.

### <a name="get-log-results-upon-completion"></a>Resultaten van logboeken weergeven bij voltooiing

Het trainen en controleren van het model gebeurt op de achtergrond. Wacht totdat het trainen van het model is voltooid voordat u andere code uitvoert. Gebruik `wait_for_completion` om weer te geven wanneer het trainen van het model is voltooid:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Resultaten van run weergeven

U hebt nu een model dat is getraind op een extern cluster. De nauwkeurigheid van het model opvragen:

```python
print(run.get_metrics())
```

De uitvoer toont dat het externe model een nauwkeurigheid van 0,9204 heeft:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

In de volgende zelfstudie wordt dit model in meer detail besproken.

## <a name="register-model"></a>Model registreren

Met de laatste stap in het trainingsscript wordt het bestand `outputs/sklearn_mnist_model.pkl` weggeschreven naar een map met de naam `outputs` op de VM van het cluster waarop de taak wordt uitgevoerd. `outputs` is een speciale map omdat alle inhoud in deze map automatisch wordt geüpload naar uw werkruimte. Deze inhoud wordt opgenomen in de uitvoeringsrecord in het experiment, onder uw werkruimte. Hierdoor is het modelbestand nu ook beschikbaar in uw werkruimte.

U kunt zien welke bestanden aan die run zijn gekoppeld:

```python
print(run.get_file_names())
```

Registreer het model in de werkruimte, zodat u of anderen query's op het model kunnen uitvoeren, of het model kunnen onderzoeken en implementeren:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

U kunt ook gewoon alleen het Azure Machine Learning Compute-cluster verwijderen. Automatische schaalaanpassing is ingeschakeld en het clusterminimum is 0. Hierdoor worden er voor deze specifieke resource geen extra rekenkosten in rekening gebracht wanneer deze niet in gebruik is:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie over Azure Machine Learning hebt u Python gebruikt voor de volgende taken:

> [!div class="checklist"]
> * De ontwikkelomgeving instellen.
> * De gegevens downloaden en controleren.
> * Meerdere modellen trainen op een extern cluster met behulp van de populaire scikit-learn machine learning-bibliotheek
> * Trainingsgegevens bekijken en het beste model registreren.

U kunt dit geregistreerde model nu implementeren aan de hand van de instructies in het volgende deel van de reeks zelfstudies:

> [!div class="nextstepaction"]
> [Zelfstudie 2: Modellen implementeren](tutorial-deploy-models-with-aml.md)
