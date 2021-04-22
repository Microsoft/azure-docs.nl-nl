---
title: ML-modellen implementeren in Azure Functions Apps (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik Azure Machine Learning voor het verpakken en implementeren van een model als een webservice in Azure Functions app.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: how-to
ms.custom: racking-python, devx-track-azurecli
ms.openlocfilehash: bdeecf02e81ba3c8143f707896bbf3e5c36cf212
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885394"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Een machine learning model implementeren naar Azure Functions (preview)


Meer informatie over het implementeren van een model Azure Machine Learning als een functie-app in Azure Functions.

> [!IMPORTANT]
> Hoewel zowel Azure Machine Learning als Azure Functions algemeen beschikbaar zijn, is de mogelijkheid om een model te verpakken vanuit de Machine Learning service for Functions in preview.

Met Azure Machine Learning kunt u Docker-afbeeldingen maken van getrainde machine learning modellen. Azure Machine Learning beschikt nu over de preview-functionaliteit voor het bouwen van deze machine learning-modellen in functie-apps, die kunnen [worden geïmplementeerd in Azure Functions](../azure-functions/functions-deployment-technologies.md#docker-container).

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel Een werkruimte maken [voor meer](how-to-manage-workspace.md) informatie.
* De [Azure CLI](/cli/azure/install-azure-cli).
* Een getraind machine learning dat is geregistreerd in uw werkruimte. Als u geen model hebt, gebruikt u de zelfstudie [Afbeeldingsclassificatie: model trainen om](tutorial-train-models-with-aml.md) een model te trainen en te registreren.

    > [!IMPORTANT]
    > In de codefragmenten in dit artikel wordt ervan uitgenomen dat u de volgende variabelen hebt ingesteld:
    >
    > * `ws` - Uw Azure Machine Learning werkruimte.
    > * `model` - Het geregistreerde model dat wordt geïmplementeerd.
    > * `inference_config` - De deferentieconfiguratie voor het model.
    >
    > Zie Modellen implementeren met Azure Machine Learning voor meer informatie [over het instellen van deze variabelen.](how-to-deploy-and-where.md)

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden

Voordat u implementeert, moet u definiëren wat er nodig is om het model uit te voeren als een webservice. In de volgende lijst worden de belangrijkste items beschreven die nodig zijn voor een implementatie:

* Een __invoerscript__. Dit script accepteert aanvragen, scoort de aanvraag met behulp van het model en retourneert de resultaten.

    > [!IMPORTANT]
    > Het invoerscript is specifiek voor uw model; Het moet de indeling begrijpen van de binnenkomende aanvraaggegevens, de indeling van de gegevens die door uw model worden verwacht en de indeling van de gegevens die naar clients worden geretourneerd.
    >
    > Als de aanvraaggegevens een indeling hebben die niet kan worden gebruikt door uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook het antwoord transformeren voordat het naar de client terugkeert.
    >
    > Bij het verpakken van functies wordt de invoer standaard behandeld als tekst. Als u geïnteresseerd bent in het gebruik van de onbewerkte bytes van de invoer (bijvoorbeeld voor Blob-triggers), moet u [AMLRequest](./how-to-deploy-advanced-entry-script.md#binary-data)gebruiken om onbewerkte gegevens te accepteren.

Zie Scorecode definiëren voor meer informatie [over invoerscript](./how-to-deploy-and-where.md#define-an-entry-script)

* **Afhankelijkheden,** zoals helperscripts of Python/Conda-pakketten die vereist zijn om het invoerscript of -model uit te voeren

Deze entiteiten worden ingekapseld in een __deference-configuratie__. De deductieconfiguratie verwijst naar het invoerscript en andere afhankelijkheden.

> [!IMPORTANT]
> Wanneer u een deferentieconfiguratie maakt voor gebruik met Azure Functions, moet u een [Environment-object](/python/api/azureml-core/azureml.core.environment%28class%29) gebruiken. Als u een aangepaste omgeving definieert, moet u azureml-defaults toevoegen met versie >= 1.0.45 als pip-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als webservice te hosten. In het volgende voorbeeld wordt gedemonstreerd hoe u een omgevingsobject maakt en gebruikt met een deferentieconfiguratie:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Zie Omgevingen maken en beheren voor training en implementatie voor meer informatie [over omgevingen.](how-to-use-environments.md)

Zie Modellen implementeren met de Azure Machine Learning voor meer informatie [over de deferentieconfiguratie.](how-to-deploy-and-where.md)

> [!IMPORTANT]
> Wanneer u implementeert in Functions, hoeft u geen implementatieconfiguratie __te maken.__

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Het SDK-previewpakket installeren voor ondersteuning van functies

Als u pakketten voor Azure Functions wilt maken, moet u het SDK-previewpakket installeren.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>De installatiekopie maken

Als u de Docker-afbeelding wilt maken die is geïmplementeerd in Azure Functions, gebruikt u [azureml.contrib.functions.package](/python/api/azureml-contrib-functions/azureml.contrib.functions) of de specifieke pakketfunctie voor de trigger die u wilt gebruiken. In het volgende codefragment wordt gedemonstreerd hoe u een nieuw pakket maakt met een blobtrigger van het model en de deference-configuratie:

> [!NOTE]
> In het codefragment wordt ervan uitgenomen dat een geregistreerd model bevat en dat de `model` `inference_config` configuratie voor de deferentieomgeving bevat. Zie Modellen implementeren met [Azure Machine Learning voor meer Azure Machine Learning.](how-to-deploy-and-where.md)

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Wanneer `show_output=True` wordt de uitvoer van het Docker-buildproces weergegeven. Zodra het proces is afgelopen, is de afbeelding gemaakt in de Azure Container Registry voor uw werkruimte. Zodra de afbeelding is gemaakt, wordt de locatie in uw Azure Container Registry weergegeven. De geretourneerde locatie heeft de indeling `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Verpakking voor functies ondersteunt momenteel HTTP-triggers, blobtriggers en Service Bus-triggers. Zie bindingen voor meer informatie [Azure Functions triggers.](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns)

> [!IMPORTANT]
> Sla de locatiegegevens op, omdat deze worden gebruikt bij het implementeren van de -afbeelding.

## <a name="deploy-image-as-a-web-app"></a>Een afbeelding implementeren als een web-app

1. Gebruik de volgende opdracht om de aanmeldingsreferenties op te halen voor de Azure Container Registry die de afbeelding bevat. Vervang `<myacr>` door de waarde die eerder is geretourneerd door `blob.location` : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    De uitvoer van deze opdracht is vergelijkbaar met het volgende JSON-document:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Sla de waarde voor __gebruikersnaam en__ een van de __wachtwoorden op.__

1. Als u nog geen resourcegroep of App Service-plan hebt om de service te implementeren, laten de volgende opdrachten zien hoe u beide maakt:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In dit voorbeeld wordt een _Linux Basic-prijscategorie_ ( `--sku B1` ) gebruikt.

    > [!IMPORTANT]
    > Afbeeldingen die zijn gemaakt Azure Machine Learning maken gebruik van Linux, dus u moet de `--is-linux` parameter gebruiken.

1. Maak het opslagaccount dat u wilt gebruiken voor de web jobopslag en haal de opslag connection string. Vervang `<webjobStorage>` door de naam die u wilt gebruiken.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Gebruik de volgende opdracht om de functie-app te maken. Vervang `<app-name>` door de naam die u wilt gebruiken. Vervang `<acrinstance>` en door de waarden die eerder zijn `<imagename>` `package.location` geretourneerd. Vervang `<webjobStorage>` door de naam van het opslagaccount uit de vorige stap:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Op dit moment is de functie-app gemaakt. Omdat u de connection string voor de blobtrigger of referenties niet hebt opgegeven voor de Azure Container Registry die de afbeelding bevat, is de functie-app echter niet actief. In de volgende stappen geeft u de connection string en de verificatiegegevens voor het containerregister op. 

1. Maak het opslagaccount dat u wilt gebruiken voor de blobtriggeropslag en haal de connection string. Vervang `<triggerStorage>` door de naam die u wilt gebruiken.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Neem deze connection string de functie-app op te geven. We gebruiken deze later wanneer we om vragen `<triggerConnectionString>`

1. Maak de containers voor de invoer en uitvoer in het opslagaccount. Vervang `<triggerConnectionString>` door de connection string eerder is geretourneerd:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Gebruik de volgende opdracht om connection string trigger te koppelen aan de functie-app. Vervang `<app-name>` door de naam van de functie-app. Vervang `<triggerConnectionString>` door de connection string eerder geretourneerd:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. U moet de tag ophalen die is gekoppeld aan de gemaakte container met behulp van de volgende opdracht. Vervang `<username>` door de gebruikersnaam die eerder uit het containerregister is geretourneerd:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Sla de geretourneerde waarde op. Deze wordt gebruikt `imagetag` als de in de volgende stap.

1. Gebruik de volgende opdracht om de functie-app te voorzien van de referenties die nodig zijn voor toegang tot het containerregister. Vervang `<app-name>` door de naam van de functie-app. Vervang `<acrinstance>` en door de waarden van de AZ `<imagetag>` CLI-aanroep in de vorige stap. Vervang `<username>` en door de `<password>` ACR-aanmeldingsgegevens die u eerder hebt opgehaald:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Deze opdracht retourneert informatie die lijkt op het volgende JSON-document:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Op dit moment begint de functie-app met het laden van de afbeelding.

> [!IMPORTANT]
> Het kan enkele minuten duren voordat de afbeelding is geladen. U kunt de voortgang bewaken met behulp van De Azure-portal.

## <a name="test-the-deployment"></a>De implementatie testen

Zodra de afbeelding is geladen en de app beschikbaar is, gebruikt u de volgende stappen om de app te activeren:

1. Maak een tekstbestand met de gegevens die het score.py verwacht. Het volgende voorbeeld werkt met een score.py een matrix van 10 getallen verwacht:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > De indeling van de gegevens is afhankelijk van wat uw score.py en het model verwacht.

2. Gebruik de volgende opdracht om dit bestand te uploaden naar de invoercontainer in de eerder gemaakte blob voor triggeropslag. Vervang `<file>` door de naam van het bestand met de gegevens. Vervang `<triggerConnectionString>` door de connection string eerder is geretourneerd. In dit voorbeeld `input` is de naam van de invoercontainer die u eerder hebt gemaakt. Als u een andere naam hebt gebruikt, vervangt u deze waarde:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Als u de uitvoer wilt weergeven die door de functie wordt geproduceerd, gebruikt u de volgende opdracht om de gegenereerde uitvoerbestanden weer te geven. Vervang `<triggerConnectionString>` door de connection string eerder is geretourneerd. In dit voorbeeld `output` is de naam van de uitvoercontainer die u eerder hebt gemaakt. Als u een andere naam hebt gebruikt, vervangt u deze waarde:

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    De uitvoer van deze opdracht is vergelijkbaar met `sample_input_out.json` .

4. Gebruik de volgende opdracht om het bestand te downloaden en de inhoud te controleren. Vervang `<file>` door de bestandsnaam die wordt geretourneerd door de vorige opdracht. Vervang `<triggerConnectionString>` door de connection string eerder is geretourneerd: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Zodra de opdracht is voltooid, opent u het bestand. Het bevat de gegevens die door het model worden geretourneerd.

Zie het artikel Een functie maken die wordt geactiveerd door Azure Blob Storage voor meer informatie over het gebruik van [blobtriggers.](../azure-functions/functions-create-storage-blob-triggered-function.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het configureren van uw Functions-app in de [Functions-documentatie.](../azure-functions/functions-create-function-linux-custom-image.md)
* Meer informatie over Blob Storage-triggers [voor Azure Blob Storage-bindingen.](../azure-functions/functions-bindings-storage-blob.md)
* [Implementeer uw model in Azure App Service](how-to-deploy-app-service.md).
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Naslaginformatie voor API](/python/api/azureml-contrib-functions/azureml.contrib.functions)
