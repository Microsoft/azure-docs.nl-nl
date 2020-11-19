---
title: Modellen trainen en implementeren vanuit de CLI
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de machine learning-extensie voor Azure CLI om een model te trainen, te registreren en te implementeren vanaf de opdrachtregel.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 119f1eaa838b404a3dbdc22f692f559816810dde
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578685"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Zelfstudie: Een model trainen en implementeren vanuit de CLI


In deze zelfstudie gebruikt u de machine learning-extensie voor Azure CLI om een model te trainen, te registreren en te implementeren.

In de Python-trainingsscripts in deze zelfstudie wordt [scikit-learn](https://scikit-learn.org/) gebruikt om een eenvoudig model te trainen. Deze zelfstudie is niet zozeer gericht op de scripts of het model, maar op het proces voor het gebruik van de CLI om met Azure Machine Learning te werken.

U leert hoe u de volgende acties uitvoert:

> [!div class="checklist"]
> * De machine learning-extensie installeren
> * Een Azure Machine Learning-werkruimte maken
> * De rekenresource maken die wordt gebruikt om het model te trainen
> * De gegevensset definiëren en registreren die wordt gebruikt om het model te trainen
> * Een trainingsuitvoering starten
> * Een model registreren en downloaden
> * Het model implementeren als een webservice
> * Scores van gegevens berekenen met behulp van de webservice

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

    Als u [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) gebruikt, opent u de CLI via de browser en bevindt deze zich in de cloud.

## <a name="download-the-example-project"></a>Het voorbeeldproject downloaden

Voor deze zelfstudie downloadt u het project [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps). De bestanden in de map `examples/cli-train-deploy` worden gebruikt voor de stappen in deze zelfstudie.

Als u een lokale kopie van de bestanden wilt ophalen, moet u [een zip-archief downloaden](https://github.com/microsoft/MLOps/archive/master.zip) of de volgende Git-opdracht gebruiken om de opslagplaats te klonen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Trainingsbestanden

De map `examples/cli-train-deploy` van het project bevat de volgende bestanden, die worden gebruikt bij het trainen van een model:

* `.azureml\mnist.runconfig`: Een __configuratiebestand voor de uitvoering__. Dit bestand definieert de runtime-omgeving die nodig is om het model te trainen. In dit voorbeeld koppelt het ook de gegevens die worden gebruikt om het model te trainen in de trainingsomgeving.
* `scripts\train.py`: Het trainingsscript. Met dit bestand wordt het model getraind.
* `scripts\utils.py`: Een helperbestand dat wordt gebruikt door het trainingsscript.
* `.azureml\conda_dependencies.yml`: Dit bestand definieert de software-afhankelijkheden die nodig zijn om het trainingsscript uit te voeren.
* `dataset.json`: De gegevenssetdefinitie. Deze wordt gebruikt om de MNIST-gegevensset te registreren in de Azure Machine Learning-werkruimte.

### <a name="deployment-files"></a>Implementatiebestanden

De opslagplaats bevat de volgende bestanden, die worden gebruikt om het getrainde model te implementeren als een webservice:

* `aciDeploymentConfig.yml`: Een __configuratiebestand voor de implementatie__. Dit bestand definieert de hosting-omgeving die nodig is voor het model.
* `inferenceConfig.json`: Een __configuratiebestand voor de deductie__. Dit bestand definieert de softwareomgeving die door de service wordt gebruikt om de scores voor gegevens in het model te berekenen.
* `score.py`: Een Python-script waarmee inkomende gegevens worden geaccepteerd, scores voor de gegevens worden berekend met behulp van het model, en een antwoord wordt geretourneerd.
* `scoring-env.yml`: De Conda-afhankelijkheden die nodig zijn om het model en het script `score.py` uit te voeren.
* `testdata.json`: Een gegevensbestand dat kan worden gebruikt om de geïmplementeerde webservice te testen.

## <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement

Er zijn verschillende manieren waarop u zich kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De eenvoudigste methode is interactieve verificatie met behulp van een browser. Voor interactieve verificatie opent u een opdrachtregel of terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de aanwijzingen op de opdrachtregel volgen. Dit omvat het bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en het invoeren van een autorisatiecode.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>De machine learning-extensie installeren

Installeer de machine learning-extensie met de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Als er een bericht wordt weergegeven dat de extensie al is geïnstalleerd, moet u deze bijwerken naar nieuwste versie met de volgende opdracht:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een container van resources op het Azure-platform. Wanneer u met Azure Machine Learning werkt, bevat de resourcegroep uw Azure Machine Learning-werkruimte. Deze bevat ook andere Azure-services die worden gebruikt door de werkruimte. Als u bijvoorbeeld uw model traint met behulp van een cloudrekenresource, wordt die resource in de resourcegroep gemaakt.

Met de volgende opdracht kunt u __een nieuwe resourcegroep maken__. Vervang `<resource-group-name>` door de naam die u voor deze resourcegroep wilt gebruiken. Vervang `<location>` door de Azure-regio die u voor deze resourcegroep wilt gebruiken:

> [!TIP]
> Selecteer een regio waarin Azure Machine Learning beschikbaar is. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) voor informatie.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

De respons van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Zie [az group](/cli/azure/group?preserve-view=true&view=azure-cli-latest) voor meer informatie over het werken met resourcegroepen.

## <a name="create-a-workspace"></a>Een werkruimte maken

Gebruik de volgende opdracht om een nieuwe werkruimte te maken. Vervang `<workspace-name>` door de naam die u voor deze werkruimte wilt gebruiken. Vervang `<resource-group-name>` door de naam van de resourcegroep:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Lokaal project verbinden met werkruimte

Voer via een terminal of opdrachtprompt de volgende opdrachten uit om de map te wijzigen in `cli-train-deploy` en maak vervolgens verbinding met uw werkruimte:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Met deze opdracht maakt u het bestand `.azureml/config.json`, met de benodigde informatie om verbinding te maken met uw werkruimte. De rest van de `az ml`-opdrachten die in deze zelfstudie worden gebruikt, maken gebruik van dit bestand, dus u hoeft de werkruimte en resourcegroep niet aan alle opdrachten toe te voegen.

## <a name="create-the-compute-target-for-training"></a>Het rekendoel voor de training maken

In dit voorbeeld wordt een Azure Machine Learning Compute-cluster gebruikt om het model te trainen. Gebruik de volgende opdracht om een nieuw rekencluster te maken:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Met deze opdracht wordt een nieuw rekendoel gemaakt met de naam `cpu-cluster`, met maximaal vier knooppunten. De geselecteerde VM-grootte biedt een virtuele machine met een GPU-resource. Zie [VM-typen en -grootten] voor meer informatie over de grootte van de virtuele machine.

> [!IMPORTANT]
> De naam van het rekendoel (`cpu-cluster` in dit geval) is belangrijk. Er wordt naar verwezen in het bestand `.azureml/mnist.runconfig`, dat in de volgende sectie wordt gebruikt.

## <a name="define-the-dataset"></a>De gegevensset definiëren

Voor het trainen van een model kunt u de trainingsgegevens opgeven met behulp van een gegevensset. Als u een gegevensset wilt maken op basis van de CLI, moet u een bestand voor de gegevenssetdefinitie opgeven. Met het bestand `dataset.json` dat is opgegeven in de opslagplaats maakt u een nieuwe gegevensset met behulp van de MNIST-gegevens. De gegevensset die wordt gemaakt, heeft de naam `mnist-dataset`.

Als u de gegevensset wilt registreren met behulp van het bestand `dataset.json`, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Kopieer de waarde van de `id`-vermelding, zoals deze wordt gebruikt in de volgende sectie.

Als u een uitgebreidere sjabloon voor een gegevensset wilt weergeven, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Verwijzen naar de gegevensset

Als u de gegevensset beschikbaar wilt maken in de trainingsomgeving, moet u ernaar verwijzen vanuit het runconfig-bestand. Het bestand `.azureml/mnist.runconfig` bevat de volgende YAML-vermeldingen:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Wijzig de waarde van de `id`-vermelding zodat deze overeenkomt met de waarde die is geretourneerd bij het registreren van de gegevensset. Deze waarde wordt gebruikt om de gegevens in het rekendoel te laden bij het trainen.

Deze YAML-vermelding resulteert in de volgende acties bij het trainen:

* De gegevensset wordt gekoppeld (op basis van de id van de gegevensset) in de trainingsomgeving en het pad wordt opgeslagen op het koppelpunt in de omgevingsvariabele `mnist`.
* De locatie van de gegevens (koppelpunt) in de trainingsomgeving wordt doorgegeven aan het script met behulp van het argument `--data-folder`.

Het runconfig-bestand bevat ook informatie voor het configureren van de omgeving die wordt gebruikt voor de uitvoering van de training. Als u dit bestand inspecteert, ziet u dat het verwijst naar het rekendoel `cpu-compute` dat u eerder hebt gemaakt. Het bevat ook het aantal knooppunten dat moet worden gebruikt bij het trainen (`"nodeCount": "4"`) en de sectie `"condaDependencies"` met een lijst met de Python-pakketten die nodig zijn om het trainingsscript uit te voeren.

> [!TIP]
> Hoewel het mogelijk is om handmatig een runconfig-bestand te maken, is het bestand in dit voorbeeld gemaakt met behulp van het bestand `generate-runconfig.py` dat is opgenomen in de opslagplaats. Met dit bestand wordt een verwijzing naar de geregistreerde gegevensset opgehaald en programmatisch een configuratie voor de uitvoering gemaakt, die vervolgens wordt opgeslagen in een runconfig-bestand.

Zie [Rekendoelen gebruiken voor het trainen van modellen](how-to-set-up-training-targets.md#whats-a-run-configuration) voor meer informatie over het uitvoeren van runconfig-bestanden. Zie [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) voor een volledige JSON-verwijzing.

## <a name="submit-the-training-run"></a>De uitvoering van de training starten

Gebruik de volgende opdracht om een trainingsuitvoering op het rekendoel `cpu-cluster` te starten:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Met deze opdracht geeft u een naam op voor het experiment (`myexperiment`). In het experiment wordt informatie over deze uitvoering opgeslagen in de werkruimte.

Met de parameter `-c mnist` wordt het bestand `.azureml/mnist.runconfig` opgegeven.

Met de parameter `-t` wordt een verwijzing naar deze uitvoering opgeslagen in een JSON-bestand. Dat wordt in de volgende stappen gebruikt om het model te registreren en te downloaden.

Tijdens de uitvoering van de training wordt informatie van de trainingssessie op de externe rekenresource gestreamd. Een gedeelte van de informatie ziet eruit als de volgende tekst:

```output
Predict the test set
Accuracy is 0.9185
```

Deze tekst is vastgelegd in het logboek van het trainingsscript en geeft de nauwkeurigheid van het model weer. Andere modellen zullen andere metrische gegevens voor de prestaties hebben.

Als u het trainingsscript inspecteert, ziet u dat ook de alpha-waarde wordt gebruikt wanneer het getrainde model wordt opgeslagen in `outputs/sklearn_mnist_model.pkl`.

Het model is opgeslagen in de map `./outputs` op het rekendoel waar het is getraind. In dit geval is dat de Azure Machine Learning Compute-instantie in de Azure-cloud. Het trainingsproces uploadt de inhoud van de map `./outputs` automatisch van het rekendoel waarop de training plaatsvindt naar uw Azure Machine Learning-werkruimte. Het wordt opgeslagen als onderdeel van het experiment (`myexperiment` in dit voorbeeld).

## <a name="register-the-model"></a>Het model registreren

Als u het model rechtstreeks vanuit de opgeslagen versie in uw experiment wilt registreren, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Met deze opdracht wordt het bestand `outputs/sklearn_mnist_model.pkl` dat door de training wordt gemaakt, geregistreerd als een nieuwe modelregistratie met de naam `mymodel`. `--assets-path` verwijst naar een pad in een experiment. In dit geval worden het experiment en de uitvoeringsinformatie geladen vanuit het bestand `runoutput.json` dat is gemaakt met de trainingsopdracht. Met `-t registeredmodel.json` wordt een JSON-bestand gemaakt dat verwijst naar het nieuwe geregistreerde model dat is gemaakt met deze opdracht. Het wordt gebruikt door andere CLI-opdrachten die met geregistreerde modellen werken.

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Versiebeheer model

Noteer het versienummer dat voor het model wordt geretourneerd. Telkens wanneer u een nieuw model met deze naam registreert, wordt het versienummer verhoogd. U kunt het model bijvoorbeeld downloaden en registreren vanuit een lokaal bestand met behulp van de volgende opdrachten:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Met de eerste opdracht wordt het geregistreerde model gedownload naar de huidige map. De bestandsnaam is `sklearn_mnist_model.pkl`. Dit is het bestand waarnaar wordt verwezen wanneer u het model registreert. Met de tweede opdracht wordt het lokale model (`-p "sklearn_mnist_model.pkl"`) geregistreerd met dezelfde naam als het vorige model (`mymodel`). Deze keer worden de JSON-gegevens geretourneerd met het versienummer 2.

## <a name="deploy-the-model"></a>Het model implementeren

Als u een model wilt implementeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Er wordt mogelijk een waarschuwing weergegeven dat het controleren of de lokale webservice bestaat of het maken van de Docker-client is mislukt. U kunt dit veilig negeren, omdat u geen lokale webservice implementeert.

Met deze opdracht wordt een nieuwe service met de naam `myservice` geïmplementeerd, met versie 1 van het model dat u eerder hebt geregistreerd.

Het bestand `inferenceConfig.yml` bevat informatie over hoe u het model gebruikt voor deductie. Er wordt bijvoorbeeld verwezen naar het invoerscript (`score.py`) en software-afhankelijkheden.

Raadpleeg het [configuratieschema voor deductie](reference-azure-machine-learning-cli.md#inference-configuration-schema) voor meer informatie over de structuur van dit bestand. Zie [Modellen implementeren met Azure Machine Learning](how-to-deploy-and-where.md) voor meer informatie over invoerscripts.

In `aciDeploymentConfig.yml` wordt de implementatieomgeving beschreven die wordt gebruikt voor het hosten van de service. De implementatieconfiguratie is specifiek voor het rekentype dat u gebruikt voor de implementatie. In dit geval wordt een Azure-containerinstantie gebruikt. Raadpleeg het [configuratieschema voor implementatie](reference-azure-machine-learning-cli.md#deployment-configuration-schema) voor meer informatie.

Het duurt enkele minuten voordat het implementatieproces is voltooid.

> [!TIP]
> In dit voorbeeld wordt Azure Container Instances gebruikt. Bij implementaties naar ACI wordt de benodigde ACI-resource automatisch gemaakt. Als u in plaats daarvan naar Azure Kubernetes Service wilt implementeren, moet u van tevoren een AKS-cluster maken en dit opgeven als onderdeel van de `az ml model deploy`-opdracht. Zie [Een model implementeren in een Azure Kubernetes Service-cluster](how-to-deploy-azure-kubernetes-service.md) voor een voorbeeld van een implementatie naar AKS.

Na enkele minuten wordt informatie die lijkt op de volgende JSON geretourneerd:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>De scoring-URI

De `scoringUri` die door de implementatie wordt geretourneerd, is het REST-eindpunt voor een model dat is geïmplementeerd als een webservice. U kunt deze URI ook ophalen met behulp van de volgende opdracht:

```azurecli-interactive
az ml service show -n myservice
```

Met deze opdracht wordt hetzelfde JSON-document geretourneerd, met inbegrip van de `scoringUri`.

Het REST-eindpunt kan worden gebruikt om gegevens naar de service te verzenden. Raadpleeg [Een Azure Machine Learning-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md) voor informatie over het maken van een clienttoepassing die gegevens naar de service verzendt

### <a name="send-data-to-the-service"></a>Gegevens naar de service verzenden

Hoewel u een clienttoepassing kunt maken om het eind punt aan te roepen, biedt de machine learning-CLI een hulpprogramma dat kan fungeren als een testclient. Gebruik de volgende opdracht om gegevens in het bestand `testdata.json` naar de service te verzenden:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Als u PowerShell gebruikt, gebruikt u in plaats hiervan de volgende opdracht:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Het antwoord van de opdracht is vergelijkbaar met `[ 3 ]`.

## <a name="clean-up-resources"></a>Resources opschonen

> [!IMPORTANT]
> De resources die u hebt gemaakt kunnen worden gebruikt als de vereisten voor andere Azure Machine Learning-zelfstudies en artikelen met procedures.

### <a name="delete-deployed-service"></a>Geïmplementeerde service verwijderen

Als u van plan bent de Azure Machine Learning-werkruimte te blijven gebruiken, maar de geïmplementeerde service wilt verwijderen om de kosten te verlagen, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml service delete -n myservice
```

Met deze opdracht wordt een JSON-document geretourneerd dat de naam van de verwijderde service bevat. Het kan enkele minuten duren om het service-eindpunt te verwijderen.

### <a name="delete-the-training-compute"></a>Het trainingsrekendoel verwijderen

Als u van plan bent om de Azure Machine Learning-werkruimte te blijven gebruiken, maar u het rekendoel `cpu-cluster` dat voor training is gemaakt, wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Met deze opdracht wordt een JSON-document geretourneerd dat de id van het verwijderde rekendoel bevat. Het kan enkele minuten duren voordat het rekendoel is verwijderd.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om gebruik te maken van de resources die u hebt gemaakt, kunt u ze verwijderen zodat er geen extra kosten voor in rekening worden gebracht.

Als u de resourcegroep en alle Azure-resources die in dit document zijn gemaakt, wilt verwijderen, gebruikt u de volgende opdracht. Vervang `<resource-group-name>` door de naam van de resourcegroep die u eerder hebt gemaakt:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie over Azure Machine Learning hebt u de CLI gebruikt voor de volgende taken:

> [!div class="checklist"]
> * De machine learning-extensie installeren
> * Een Azure Machine Learning-werkruimte maken
> * De rekenresource maken die wordt gebruikt om het model te trainen
> * De gegevensset definiëren en registreren die wordt gebruikt om het model te trainen
> * Een trainingsuitvoering starten
> * Een model registreren en downloaden
> * Het model implementeren als een webservice
> * Scores van gegevens berekenen met behulp van de webservice

Zie [De CLI-extensie voor Azure Machine Learning gebruiken](reference-azure-machine-learning-cli.md) voor meer informatie over het gebruik van de CLI.