---
title: 'Zelfstudie: Uw eigen gegevens gebruiken'
titleSuffix: Azure Machine Learning
description: In deel 4 van de aan de slag-serie van Azure ML ziet u hoe u uw eigen gegevens kunt gebruiken in een externe trainingsuitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python
ms.openlocfilehash: ecabfde624ba6d3393bbf6d5480b83dbb5303c5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604553"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Zelfstudie: Uw eigen gegevens gebruiken (Deel 4 van 4)

Deze zelfstudie laat zien hoe u uw eigen gegevens kunt uploaden en gebruiken om machine learning-modellen in Azure Machine Learning te trainen.

Deze zelfstudie is *deel 4 van een vierdelige zelfstudiereeks* waarin u de grondbeginselen van Azure Machine Learning kunt zien en machine learning-taken op basis van taken uitvoert in Azure. Deze zelfstudie borduurt voort op het werk dat u hebt voltooid in [Deel 1: Instellen](tutorial-1st-experiment-sdk-setup-local.md), [Deel 2: 'Hello World!' uitvoeren](tutorial-1st-experiment-hello-world.md) en [Deel 3: Een model trainen](tutorial-1st-experiment-sdk-train.md).

In [Deel 3: Een model trainen](tutorial-1st-experiment-sdk-train.md) zijn gegevens gedownload met behulp van de ingebouwde `torchvision.datasets.CIFAR10`-methode in de PyTorch-API. In veel gevallen wilt u echter uw eigen gegevens gebruiken in een externe trainingsuitvoering. In dit artikel ziet u welke werkstroom u kunt gebruiken om met uw eigen gegevens te werken in Azure Machine Learning.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een trainingsscript configureren voor het gebruik van gegevens in een lokale map.
> * Het trainingsscript lokaal testen.
> * Gegevens uploaden naar Azure.
> * Een besturingsscript maken.
> * Inzicht krijgen in de nieuwe Azure Machine Learning-concepten (parameters doorgeven, gegevenssets, gegevensarchief).
> * Uw trainingsscript verzenden en uitvoeren.
> * De code-uitvoer weergeven in de cloud.

## <a name="prerequisites"></a>Vereisten

U hebt de gegevens en een bijgewerkte versie van de pytorch-omgeving nodig die u in de vorige zelf studie hebt gemaakt.  Zorg ervoor dat u de volgende stappen hebt uitgevoerd:

1. [Het trainingsscript maken](tutorial-1st-experiment-sdk-train.md#create-training-scripts)
1. [Een nieuwe python-omgeving maken](tutorial-1st-experiment-sdk-train.md#environment)
1. [Lokaal testen](tutorial-1st-experiment-sdk-train.md#test-local)
1. [Het Conda-omgevingsbestand bijwerken](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)

## <a name="adjust-the-training-script"></a>Het trainingsscript aanpassen

U hebt nu uw trainingsscript (tutorial/src/train.py) aangezet in Azure Machine Learning en kunt de modelprestaties bewaken. We gaan de parameters van het trainingsscript bepalen door argumenten te introduceren. Door argumenten te gebruiken, kunt u eenvoudig verschillende hyperparameters vergelijken.

Het trainingsscript is nu ingesteld op het downloaden van de CIFAR10-gegevensset voor elke uitvoering. De volgende Python-code is zo aangepast dat de gegevens uit een map worden gelezen.

>[!NOTE] 
> Als u `argparse` gebruikt, worden parameters bepaald voor het script.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>De codewijzigingen begrijpen

Voor de code in `train.py` is de bibliotheek `argparse` gebruikt om `data_path`, `learning_rate` en `momentum` in te stellen.

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

Daarnaast is het `train.py`-script aangepast om de optimizer bij te werken voor het gebruik van door de gebruiker gedefinieerde parameters:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

> [!div class="nextstepaction"]
> [Ik heb het trainingsscript aangepast](?success=adjust-training-script#test-locally) [Er is een probleem opgetreden](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> Het script lokaal testen

Uw script accepteert nu _gegevenspad_ als een argument. U moet het als eerste lokaal testen. Voeg een map met de naam `data` toe aan de mapstructuur van uw zelfstudie. De mapstructuur moet er als volgt uitzien:

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="In de mapstructuur worden weer gegeven. azureml-, gegevens-en src-submappen":::

1. Sluit de huidige omgeving af.

    ```bash
    conda deactivate

1. Now create and activate the new environment.  This will rebuild the pytorch-aml-env with the [updated environment file](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)


    ```bash
    conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
    ```

    ```bash
    conda activate pytorch-aml-env          # activate new conda environment
    ```

1. Ten slotte voert u het gewijzigde trainings script lokaal uit.

    ```bash
    python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
    ```

U moet voorkomen dat u de CIFAR10-gegevensset downloadt door een lokaal pad naar de gegevens op te geven. U kunt ook experimenteren met verschillende waarden voor de hyperparameters voor _leersnelheid_ en _momentum_ zonder ze in het trainingsscript te hoeven coderen.

> [!div class="nextstepaction"]
> [Ik heb het script lokaal getest](?success=test-locally#upload) [Er is een probleem opgetreden](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> De gegevens uploaden naar Azure

Als u dit script in Azure Machine Learning wilt uitvoeren, moet u uw trainingsgegevens beschikbaar maken in Azure. Uw Azure Machine Learning-werkruimte is voorzien van een _standaard_ gegevensopslag. Dit is een Azure Blob Storage-account waarin u uw trainingsgegevens kunt opslaan.

>[!NOTE] 
> Met Azure Machine Learning kunt u verbinding maken met andere gegevensarchieven in de cloud die uw gegevens opslaan. Zie [Documentatie over gegevensarchieven](./concept-data.md) voor meer informatie.  

Maak een nieuw Python-besturingsscript met de naam `05-upload-data.py` in de `tutorial`-map:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

De waarde `target_path` geeft het pad op naar het gegevensarchief waarnaar de CIFAR10-gegevens worden geüpload.

>[!TIP] 
> Terwijl u Azure Machine Learning gebruikt om de gegevens te uploaden, kunt u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om ad-hocbestanden te uploaden. Als u een ETL-hulpprogramma nodig hebt, kunt u [Azure Data Factory](../data-factory/introduction.md) gebruiken om uw gegevens in Azure op te nemen.

Voer, in het venster met de geactiveerde Conda-omgeving van *tutorial1*, het Python-bestand uit om de gegevens te uploaden. (De upload zou minder dan 60 seconden moeten duren.)

```bash
python 05-upload-data.py
```

U ziet de volgende standaarduitvoer:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [Ik heb de gegevens geüpload](?success=upload-data#control-script) [Er is een probleem opgetreden](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Een besturingsscript maken

Net zoals u eerder hebt gedaan, maakt u een nieuw Python-besturingsscript met de naam `06-run-pytorch-data.py`:

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>De codewijzigingen begrijpen

Het besturingsscript is vergelijkbaar met dat van [Deel 3 van deze serie](tutorial-1st-experiment-sdk-train.md) met de volgende nieuwe regels:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Een [gegevensset](/python/api/azureml-core/azureml.core.dataset.dataset) wordt gebruikt om te verwijzen naar de gegevens die u hebt geüpload naar Azure Blob Storage. Gegevenssets zijn een abstracte laag bovenop uw gegevens die is ontworpen voor de verbetering van de betrouwbaarheid.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) is gewijzigd en bevat een lijst met argumenten die worden doorgegeven aan `train.py`. Het argument `dataset.as_named_input('input').as_mount()` betekent dat de opgegeven map wordt _gekoppeld_ aan het rekendoel.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Ik heb het besturingsscript gemaakt](?success=control-script#submit-to-cloud) [Er is een probleem opgetreden](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> De uitvoering versturen naar Microsoft Azure Machine Learning

Verzend de uitvoering nu opnieuw om de nieuwe configuratie te gebruiken:

```bash
python 06-run-pytorch-data.py
```

Met deze code wordt een URL naar het experiment afgedrukt in Azure Machine Learning Studio. Als u naar deze link gaat, kunt u zien dat de code wordt uitgevoerd.

> [!div class="nextstepaction"]
> [Ik heb de uitvoering opnieuw verzonden](?success=submit-to-cloud#inspect-log) [Er is een probleem opgetreden](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Het logboekbestand controleren

Ga in Studio naar de experimentuitvoering (door de vorige URL-uitvoer te selecteren), gevolgd door **Uitvoer en logboeken**. Selecteer het bestand `70_driver_log.txt`. U moet de volgende uitvoer zien:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Kennisgeving:

- Azure Machine Learning heeft Blob Storage automatisch gekoppeld aan het rekencluster.
- De ``dataset.as_named_input('input').as_mount()`` die wordt gebruikt in het besturingsscript, wordt omgezet naar het koppelpunt.

> [!div class="nextstepaction"]
> [Ik heb het logboekbestand gecontroleerd](?success=inspect-log#clean-up-resources) [Er is een probleem opgetreden](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Bekijk de eigenschappen van de werkruimte en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebben we gezien hoe we gegevens naar Azure moeten uploaden met `Datastore`. Het gegevensarchief wordt geleverd als cloudopslag voor uw werkruimte, waardoor u een permanente en flexibele plaats hebt om uw gegevens te bewaren.

U hebt gezien hoe u uw trainingsscript kunt aanpassen om een gegevenspad te accepteren via de opdrachtregel. Met `Dataset` hebt u een map gekoppeld aan de externe uitvoering. 

Nu u een model hebt, leert u het volgende:

* [Modellen implementeren met Azure Machine Learning](how-to-deploy-and-where.md).
