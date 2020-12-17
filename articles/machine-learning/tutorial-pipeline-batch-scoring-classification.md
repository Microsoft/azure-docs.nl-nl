---
title: 'Zelfstudie: ML-pijplijnen voor batchgewijs scoren'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie bouwt u een machine learning-pijplijn voor het uitvoeren van batchgewijs scoren. De focus ligt op machine learning in plaats van op infrastructuur en automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperf-fy20q4, devx-track-python
ms.openlocfilehash: b0b415cce37e464abcba9fab5ad4c1196b1b2e1b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033473"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Zelfstudie: Een Azure Machine Learning-pijplijn bouwen voor batchgewijs scoren



In deze geavanceerde zelfstudie leert u hoe u een [Azure Machine Learning-pijplijn](concept-ml-pipelines.md) kunt bouwen om een taak voor batchgewijs scoren uit te voeren. Machine Learning-pijplijnen optimaliseren uw werkstroom met snelheid, draagbaarheid en hergebruik, zodat u zich kunt concentreren op machine learning in plaats van op infrastructuur en automatisering. Nadat u een pijplijn hebt gemaakt en gepubliceerd, configureert u een REST-eindpunt dat u kunt gebruiken om de pijplijn te activeren vanuit elke HTTP-bibliotheek en op elk platform. 

In het voorbeeld wordt gebruikgemaakt van een vooraf getraind convolutioneel neuraal netwerkmodel [Inception-V3](https://arxiv.org/abs/1512.00567) dat is geïmplementeerd in Tensorflow om ongelabelde afbeeldingen te classificeren. 

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Werkruimte configureren 
> * Voorbeeldgegevens downloaden en opslaan
> * Gegevensobjecten maken om gegevens op te halen en uit te voeren
> * Het model downloaden, voorbereiden en registreren in uw werkruimte
> * Rekendoelen inrichten en een scoringscript maken
> * De klasse `ParallelRunStep` voor asynchroon batchgewijs scoren gebruiken
> * Een pijplijn bouwen, uitvoeren en publiceren
> * Een REST-eindpunt inschakelen voor de pijplijn

Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Vereisten

* Doorloop [deel 1 van de installatiezelfstudie](tutorial-1st-experiment-sdk-setup.md) als u nog geen Azure Machine Learning-werkruimte of virtuele notebook-machine hebt.
* Wanneer u de installatiezelfstudie hebt voltooid, gebruikt u dezelfde notebook-server om de notebook *tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb* te openen.

Als u de installatiehandleiding wilt uitvoeren in uw eigen [lokale omgeving](how-to-configure-environment.md#local), kunt u de zelfstudie op [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) openen. Voer `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` uit om de vereiste pakketten te downloaden.

## <a name="configure-workspace-and-create-a-datastore"></a>Een werkruimte configureren en een gegevensopslag maken

Maak een werkruimteobject op basis van de bestaande Azure Machine Learning-werkruimte.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> Dit codefragment verwacht dat de werkruimteconfiguratie wordt opgeslagen in de huidige of de bovenliggende map. Zie [Azure Machine Learning-werkruimten maken en beheren](how-to-manage-workspace.md) voor meer informatie over het maken van een werkruimte. Zie [Een configuratiebestand voor een werkruimte maken](how-to-configure-environment.md#workspace) voor meer informatie over de configuratie als bestand opslaan.

## <a name="create-a-datastore-for-sample-images"></a>Een gegevensarchief maken voor voorbeeldafbeeldingen

Haal op het `pipelinedata`-account het voorbeeld van de openbare gegevens van de ImageNet-evaluatie op uit de openbare blob-container van `sampledata`. Roep `register_azure_blob_container()` aan om de gegevens beschikbaar te maken voor de werkruimte onder de naam `images_datastore`. Stel vervolgens het standaard gegevensarchief voor de werkruimte in als het gegevensarchief voor uitvoer. Gebruik het gegevensarchief voor uitvoer om uitvoer in de pijplijn te scoren.

Zie [Toegang krijgen tot gegevens](./how-to-access-data.md) voor meer informatie over toegang tot gegevens.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Gegevenssetobjecten maken

Wanneer u pijplijnen bouwt, worden `Dataset`-objecten gebruikt voor het lezen van gegevens uit gegevensarchieven van werkruimtes en worden `PipelineData`-objecten gebruikt voor het overzetten van tussenliggende gegevens tussen pijplijnstappen.

> [!Important]
> In het voorbeeld voor batchgewijs scoren in deze zelfstudie wordt slechts één pijplijnstap gebruikt. In gebruiksvoorbeelden met meerdere stappen bevat de typische stroom de volgende stappen:
>
> 1. Gebruik `Dataset`-objecten als *invoer* om onbewerkte gegevens op te halen, een transformatie uit te voeren en een `PipelineData`-object *uit te voeren*.
>
> 2. Gebruik het *uitvoerobject* `PipelineData` in de vorige stap als een *invoerobject*. Herhaal dit voor de volgende stappen.

In dit scenario maakt u `Dataset`-objecten die overeenkomen met de gegevensopslagmappen voor zowel de invoerafbeeldingen als de classificatielabels (y-test waarden). U maakt ook een `PipelineData`-object voor de uitvoergegevens voor batchgewijs scoren.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Registreer de gegevenssets in de werkruimte als u deze later opnieuw wilt gebruiken. Deze stap is optioneel.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Het model downloaden en registreren

Download het vooraf getrainde Tensorflow-model om het te gebruiken voor batchgewijs scoren in een pijplijn. Maak eerst een lokale map waarin u het model opslaat. Vervolgens downloadt en extraheert u het model.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Registreer vervolgens het model in uw werkruimte, zodat u het model eenvoudig kunt ophalen in het pijplijnproces. In de statische functie `register()` is de parameter `model_name` de sleutel die u gebruikt om uw model in de SDK te vinden.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Het externe rekendoel maken en koppelen

Machine learning-pijplijnen kunnen niet lokaal worden uitgevoerd, dus u kunt ze uitvoeren op cloudresources of *externe rekendoelen*. Een extern rekendoel is een herbruikbare virtuele rekenomgeving waarin u experimenten en machine learning-werkstromen kunt uitvoeren. 

Voer de volgende code uit om een [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py)-doel met GPU te maken en deze vervolgens aan uw werkruimte te koppelen. Zie het [conceptuele artikel](./concept-compute-target.md) voor meer informatie over rekendoelen.


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Een scorescript schrijven

Als u wilt scoren, maakt u een script voor batchgewijs scoren met de naam `batch_scoring.py` en schrijft u het naar de huidige map. Het script gebruikt invoerafbeeldingen, past het classificatiemodel toe en voert vervolgens de voorspellingen uit naar een bestand met resultaten.

Het script `batch_scoring.py` gebruikt de volgende parameters, die worden doorgegeven aan de `ParallelRunStep` die u later maakt:

- `--model_name`: De naam van het model dat wordt gebruikt.
- `--labels_dir`: De locatie van het bestand `labels.txt`.

De pijplijninfrastructuur gebruikt de klasse `ArgumentParser` om parameters door te geven aan pijplijnstappen. In de volgende code krijgt het eerste argument `--model_name` bijvoorbeeld de eigenschaps-id `model_name`. In de functie `init()` wordt `Model.get_model_path(args.model_name)` gebruikt om toegang te krijgen tot deze eigenschap.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> De pijplijn in deze zelfstudie heeft slechts één stap en schrijft de uitvoer naar een bestand. Voor pijplijnen met meerdere stappen gebruikt u ook `ArgumentParser` om een map te definiëren voor het schrijven van uitvoergegevens voor invoer naar volgende stappen. Zie de [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) voor een voorbeeld van het doorgeven van gegevens tussen meerdere pijplijnstappen met behulp van het `ArgumentParser`-ontwerppatroon.

## <a name="build-the-pipeline"></a>De pijplijn bouwen

Voordat u de pijplijn uitvoert, maakt u een object dat de Python-omgeving definieert en maakt u de afhankelijkheden die uw `batch_scoring.py`-script nodig heeft. De belangrijkste vereiste afhankelijkheid is Tensorflow, maar u installeert ook `azureml-core` en `azureml-dataprep[fuse]` die vereist zijn voor ParallelRunStep. Geef ook Docker- en Docker-GPU-ondersteuning op.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>De configuratie maken om het script te verpakken

Maak de pijplijnstap met het script, de omgevingsconfiguratie en de parameters. Geef het rekendoel op dat u al aan uw werkruimte hebt gekoppeld.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>De pijplijnstap maken

Een pijplijnstap is een object dat alles inkapselt dat u nodig hebt om een pijplijn uit te voeren, waaronder:

* Omgevings- en afhankelijkheidsinstellingen
* De rekenresource waarmee de pijplijn moet worden uitgevoerd
* Invoer- en uitvoergegevens en eventuele aangepaste parameters
* Verwijzing naar een script of SDK-logica om uit te voeren tijdens de stap

Meerdere klassen worden overgenomen van de bovenliggende klasse [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py). U kunt klassen kiezen om specifieke frameworks of stacks te gebruiken om een stap te maken. In dit voorbeeld gebruikt u de klasse `ParallelRunStep` om uw staplogica te definiëren met behulp van een aangepast Python-script. Als een argument voor uw script een invoer of een uitvoer is van de stap, moet het argument worden gedefinieerd in *zowel* de `arguments`-matrix *als* in de `input`- of `output`-parameter. 

In scenario's waarin er meer dan één stap is, wordt een objectverwijzing in de matrix `outputs` beschikbaar als *invoer* voor een volgende pijplijnstap.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Voor een lijst met alle klassen die u voor verschillende typen stappen kunt gebruiken, raadpleegt u het [stappenpakket](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py).

## <a name="submit-the-pipeline"></a>De pijplijn indienen

Voer nu de pijplijn uit. Maak eerst een `Pipeline`-object met behulp van uw werkruimtereferentie en de pijplijnstap die u hebt gemaakt. De `steps`-parameter is een matrix van stappen. In dit geval is er slechts één stap voor batchgewijs scoren. Als u pijplijnen met meerdere stappen wilt bouwen, plaatst u de stappen in volgorde in deze matrix.

Gebruik vervolgens de functie `Experiment.submit()` om de pijplijn te verzenden voor uitvoering. De functie `wait_for_completion` voert logboeken uit tijdens het bouwen van de pijplijn. U kunt de logboeken gebruiken om de huidige voortgang te bekijken.

> [!IMPORTANT]
> De eerste pijplijnuitvoering duurt ongeveer *15 minuten*. Alle afhankelijkheden moeten worden gedownload, een Docker-kopie wordt gemaakt en de Python-omgeving wordt ingericht en gemaakt. Het opnieuw uitvoeren van de pijplijn vergt aanzienlijk minder tijd, omdat deze resources opnieuw worden gebruikt in plaats van worden gemaakt. De totale runtime voor de pijplijn is echter afhankelijk van de werkbelasting van uw scripts en de processen die in elke pijplijnstap worden uitgevoerd.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Uitvoer downloaden en bekijken

Voer de volgende code uit om het uitvoerbestand te downloaden dat is gemaakt op basis van het `batch_scoring.py`-script. Bekijk vervolgens de scoreresultaten.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publiceren en uitvoeren vanaf een REST-eindpunt

Voer de volgende code uit om de pijplijn te publiceren naar uw werkruimte. In uw werkruimte in Azure Machine Learning Studio kunt u metagegevens voor de pijplijn zien, inclusief uitvoeringsgeschiedenis en duur. U kunt de pijplijn ook handmatig uitvoeren vanuit de Studio.

Als u de pijplijn publiceert, wordt een REST-eindpunt ingeschakeld dat u kunt gebruiken om de pijplijn uit te voeren vanuit elke HTTP-bibliotheek op elk platform.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Als u de pijplijn vanuit het REST-eindpunt wilt uitvoeren, hebt u een OAuth2-verificatieheader nodig. In het volgende voorbeeld wordt gebruikgemaakt van interactieve verificatie (ter illustratie), maar voor de meeste productiescenario's waarvoor geautomatiseerde of headless verificatie is vereist, gebruikt u verificatie met de service-principal, zoals [beschreven in dit artikel](how-to-setup-authentication.md).

Voor verificatie met de service-principal moet u een *app-registratie* maken in *Azure Active Directory*. Eerst genereert u een clientgeheim en vervolgens verleent u uw service-principal *roltoegang* tot uw machine learning-werkruimte. Gebruik de [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py)-klasse om uw verificatiestroom te beheren. 

Zowel [`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?preserve-view=true&view=azure-ml-py) als `ServicePrincipalAuthentication` worden overgenomen van `AbstractAuthentication`. In beide gevallen gebruikt u de functie [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-authentication-header--) op dezelfde manier om de header op te halen:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Haal de REST-URL op uit de eigenschap `endpoint` van het gepubliceerde pijplijnobject. U kunt de REST-URL ook vinden in uw werkruimte in Azure Machine Learning Studio. 

Bouw een HTTP POST-aanvraag voor het eindpunt. Geef uw verificatieheader op in de aanvraag. Voeg een JSON-payload-object toe met de naam van het experiment.

Maak de aanvraag om de uitvoering te activeren. Neem code op om toegang te krijgen tot de `Id`-sleutel vanuit de antwoordbibliotheek om de waarde van de uitvoer-id op te halen.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Gebruik de uitvoer-id om de status van de nieuwe uitvoering te controleren. De nieuwe uitvoering heeft nog 10 tot 15 minuten nodig om te voltooien. 

De nieuwe uitvoering ziet er ongeveer hetzelfde uit als de pijplijn die u eerder in de zelfstudie hebt uitgevoerd. U kunt ervoor kiezen om niet de volledige uitvoer weer te geven.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Resources opschonen

Voltooi deze sectie niet als u van plan bent andere Azure Machine Learning-zelfstudies uit te voeren.

### <a name="stop-the-compute-instance"></a>Het rekenproces stoppen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om gebruik te maken van de resources die u hebt gemaakt, kunt u ze verwijderen zodat er geen kosten voor in rekening worden gebracht:

1. Selecteer **Resourcegroepen** in het linkermenu van de Azure-portal.
1. Selecteer in de lijst met resourcegroepen de resourcegroep die u hebt gemaakt.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Bekijk de eigenschappen van de werkruimte en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie over machine learning-pijplijnen hebt u het volgende gedaan:

> [!div class="checklist"]
> * U hebt een pijplijn met omgevingsafhankelijkheden gebouwd om te worden uitgevoerd op een externe GPU-rekenresource.
> * U hebt een scoringscript gemaakt voor het uitvoeren van batchgewijze voorspellingen met behulp van een vooraf getraind Tensorflow-model.
> * U hebt een pijplijn gepubliceerd en deze ingeschakeld om te worden uitgevoerd vanaf een REST-eindpunt.

Voor meer voorbeelden van het bouwen van pijplijnen met behulp van de machine learning-SDK, raadpleegt u de [notebook-opslagplaats](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
