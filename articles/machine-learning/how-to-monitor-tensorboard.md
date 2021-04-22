---
title: Experimenten visualiseren met TensorBoard
titleSuffix: Azure Machine Learning
description: Start TensorBoard om geschiedenissen van experimentruns te visualiseren en potentiële gebieden te identificeren voor het afstemmen en opnieuw trainen van hyperparameters.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 4d440742e9cfe9aadb63ed31c113879c4478dc82
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888760"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Experiment-runs en metrische gegevens visualiseren met TensorBoard en Azure Machine Learning


In dit artikel leert u hoe u uw experiment-runs en metrische gegevens in TensorBoard kunt weergeven met behulp van het pakket [in `tensorboard` ](/python/api/azureml-tensorboard/) de Azure Machine Learning SDK. Nadat u de uitvoeringen van uw experiment hebt geïnspecteerd, kunt u uw modellen beter afstemmen en machine learning trainen.

[TensorBoard](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) is een suite met webtoepassingen voor het inspecteren en begrijpen van de structuur en prestaties van uw experiment.

Hoe u TensorBoard start met Azure Machine Learning experimenten is afhankelijk van het type experiment:
+ Als met uw experiment logboekbestanden worden uitgevoerd die kunnen worden gebruikt door TensorBoard, zoals PyTorch-, Chainer- en TensorFlow-experimenten, kunt u [TensorBoard](#launch-tensorboard) rechtstreeks vanuit de uitvoergeschiedenis van het experiment starten. 

+ Voor experimenten die niet standaard TensorBoard-bestanden uitvoeren, zoals Scikit-learn- of Azure Machine Learning-experimenten, gebruikt u de methode om de uitvoergeschiedenissen als TensorBoard-logboeken te exporteren en TensorBoard van hieruit te starten. [ `export_to_tensorboard()` ](#export) 

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevenswetenschappers en ontwikkelaars die het modeltrainingsproces willen bewaken. Als u een beheerder bent die geïnteresseerd is in het bewaken van resourcegebruik en gebeurtenissen van Azure Machine Learning, zoals quota, voltooide trainingsuitvoer of voltooide modelimplementaties, zie [Bewaking](monitor-azure-machine-learning.md)van Azure Machine Learning .

## <a name="prerequisites"></a>Vereisten

* Als u TensorBoard wilt starten en de geschiedenis van de uitvoering van uw experiment wilt weergeven, moeten uw experimenten eerder logboekregistratie hebben ingeschakeld om de metrische gegevens en prestaties bij te houden.  
* De code in dit document kan worden uitgevoerd in een van de volgende omgevingen: 
    * Azure Machine Learning rekenproces : er zijn geen downloads of installatie nodig
        * Voltooi de [zelfstudie: Omgeving en werkruimte instellen om](tutorial-1st-experiment-sdk-setup.md) een toegewezen notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
        * Zoek in de map met voorbeelden op de notebookserver twee voltooide en uitgebreide notebooks door naar deze mappen te navigeren:
            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**
            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard > tensorboard > tensorboard.ipynb**
    * Uw eigen Juptyer-notebookserver
       * [Installeer de Azure Machine Learning SDK](/python/api/overview/azure/ml/install) met de `tensorboard` extra
        * [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)  
        * [Maak een configuratiebestand voor de werkruimte.](how-to-configure-environment.md#workspace)

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Optie 1: de geschiedenis van de run rechtstreeks weergeven in TensorBoard

Deze optie werkt voor experimenten die systeemeigen logboekbestanden uitvoeren die door TensorBoard kunnen worden gebruikt, zoals PyTorch-, Chainer- en TensorFlow-experimenten. Als dat niet het geval is van uw experiment, gebruikt u [in plaats daarvan `export_to_tensorboard()` de](#export) methode .

De volgende voorbeeldcode maakt gebruik van het [MNIST-demo-experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) uit de opslagplaats van TensorFlow in een extern rekendoel, Azure Machine Learning Compute. Vervolgens configureren en starten we een uitvoering voor het trainen van het TensorFlow-model en vervolgens starten we TensorBoard voor dit TensorFlow-experiment.

### <a name="set-experiment-name-and-create-project-folder"></a>De naam van het experiment instellen en de projectmap maken

Hier noemen we het experiment en maken we de map. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow-demo-experimentcode downloaden

De opslagplaats van TensorFlow heeft een MNIST-demo met uitgebreide TensorBoard-instrumentatie. We hoeven geen code van deze demo te wijzigen om deze te laten werken met Azure Machine Learning. In de volgende code downloaden we de MNIST-code en slaan we deze op in onze zojuist gemaakte experimentmap.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
In het MNIST-codebestand, mnist_with_summaries.py, ziet u dat er regels zijn die `tf.summary.scalar()` ,  `tf.summary.histogram()` , enzovoort `tf.summary.FileWriter()` aanroepen. Met deze methoden worden belangrijke metrische gegevens van uw experimenten gegroepeerd, gelogd en gelabeld in de geschiedenis van de run. De is vooral belangrijk omdat hiermee de gegevens uit uw geregistreerde metrische gegevens voor experimenten worden geser serialiseert, waardoor `tf.summary.FileWriter()` TensorBoard er visualisaties van kan genereren.

 ### <a name="configure-experiment"></a>Experiment configureren

In het volgende configureren we ons experiment en stellen we de directories voor logboeken en gegevens in. Deze logboeken worden geüpload naar de geschiedenis van de run, die later wordt gebruikt door TensorBoard.

> [!Note]
> Voor dit TensorFlow-voorbeeld moet u TensorFlow installeren op uw lokale computer. Bovendien moet de TensorBoard-module (dat wil zeggen, de module die is opgenomen in TensorFlow) toegankelijk zijn voor de kernel van dit notebook, omdat TensorBoard wordt uitgevoerd op de lokale computer.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Een cluster maken voor uw experiment
We maken een AmlCompute-cluster voor dit experiment, maar uw experimenten kunnen in elke omgeving worden gemaakt en u kunt TensorBoard nog steeds starten op de geschiedenis van de experimentrun. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Trainingsrun configureren en verzenden

Configureer een trainings job door een ScriptRunConfig-object te maken.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>TensorBoard starten

U kunt TensorBoard starten tijdens de run of nadat deze is voltooid. In het volgende maken we een TensorBoard-objectexe instance, , waarmee de geschiedenis van de experimentrun wordt geladen in de , en `tb` `run` tensorBoard vervolgens wordt gelanceerd met de `start()` methode . 
  
De [TensorBoard-constructor](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) neemt een matrix met runs. Zorg er dus voor dat u deze als een matrix met één element doordeelt.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> Hoewel in dit voorbeeld TensorFlow wordt gebruikt, kan TensorBoard net zo eenvoudig worden gebruikt met PyTorch of Chainer. TensorFlow moet beschikbaar zijn op de computer met TensorBoard, maar is niet nodig op de computer die PyTorch- of Chainer-berekeningen doet. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Optie 2: De geschiedenis exporteren als logboek om weer te bieden in TensorBoard

Met de volgende code wordt een voorbeeldexperiment gemaakt, wordt het logboekregistratieproces gestart met behulp van de Azure Machine Learning-API's voor de uitvoergeschiedenis en wordt de uitvoergeschiedenis van het experiment naar logboeken exporteert die door TensorBoard kunnen worden gebruikt voor visualisatie. 

### <a name="set-up-experiment"></a>Experiment instellen

Met de volgende code stelt u een nieuw experiment in en geeft u de map run de naam `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Hier laden we de diabetes-gegevensset, een ingebouwde kleine gegevensset die wordt geleverd met scikit-learn, en splitsen we deze op in testsets en trainingssets.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Metrische gegevens voor experimenten en logboeken uitvoeren

Voor deze code trainen we een lineair regressiemodel en logboeksleutelmetrieken, de alfacoëfficiënt, en de gemiddelde `alpha` kwadraatfout, `mse` , in de rungeschiedenis.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportuitvoer naar TensorBoard

Met de [methode export_to_tensorboard()](/python/api/azureml-tensorboard/azureml.tensorboard.export) van de SDK kunnen we de uitvoergeschiedenis van ons Azure machine learning-experiment exporteren naar TensorBoard-logboeken, zodat we deze kunnen bekijken via TensorBoard.  

In de volgende code maken we de map `logdir` in onze huidige werkmap. In deze map exporteren we de geschiedenis en logboeken van de experimentuitvoer `root_run` en markeren we die run als voltooid. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

> [!Note]
> U kunt ook een bepaalde run exporteren naar TensorBoard door de naam van de run op te geven  `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>TensorBoard starten en stoppen
Zodra onze uitvoergeschiedenis voor dit experiment is geëxporteerd, kunnen we TensorBoard starten met de [methode start().](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Wanneer u klaar bent, moet u de [methode stop()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#stop--) van het TensorBoard-object aanroepen. Anders blijft TensorBoard worden uitgevoerd totdat u de notebookkernel hebt afgesloten. 

```python
tb.stop()
```

## <a name="next-steps"></a>Volgende stappen

In deze how-to-you hebt u twee experimenten gemaakt en geleerd hoe u TensorBoard kunt starten op basis van hun rungeschiedenissen om gebieden te identificeren voor mogelijke afstemming en opnieuw trainen. 

* Als u tevreden bent met uw model, gaat u naar ons artikel Een [model](how-to-deploy-and-where.md) implementeren. 
* Meer informatie over [het afstemmen van hyperparameters.](how-to-tune-hyperparameters.md)
