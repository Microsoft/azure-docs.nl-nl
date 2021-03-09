---
title: Een machine learning model implementeren voor het Azure Functions met Azure cache voor redis
description: 'In dit artikel implementeert u een model van Azure Machine Learning als een functie-app in Azure Functions met behulp van een Azure-cache voor redis-instantie. Azure cache voor redis is zeer goed en schaalbaar: wanneer het is gekoppeld aan een Azure Machine Learning model, krijgt u een lage latentie en hoge door Voer in uw toepassing.'
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ec8943bc73cac2020350dd4916f040f031cd842b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499693"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Een machine learning model implementeren voor het Azure Functions met Azure cache voor redis 

In dit artikel implementeert u een model van Azure Machine Learning als een functie-app in Azure Functions met behulp van een Azure-cache voor redis-instantie.  

Azure cache voor redis is zeer goed en schaalbaar: wanneer het is gekoppeld aan een Azure Machine Learning model, krijgt u een lage latentie en hoge door Voer in uw toepassing. Een paar scenario's waarbij een cache bijzonder handig is bij het deleiden van de gegevens en voor het feitelijke model defactory-de resultaten. In beide gevallen worden de meta gegevens of resultaten in het geheugen opgeslagen, wat leidt tot betere prestaties. 

> [!NOTE]
> Hoewel zowel Azure Machine Learning als Azure Functions algemeen beschikbaar zijn, is de mogelijkheid om een model te verpakken van de Machine Learning-service voor functions in de preview-versie.  
>

## <a name="prerequisites"></a>Vereisten
* Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/).
* Een Azure Machine Learning-werkruimte. Zie het artikel [een werk ruimte maken](../machine-learning/how-to-manage-workspace.md) voor meer informatie.
* [Azure CLI](/cli/azure/install-azure-cli).
* Een getraind machine learning model dat is geregistreerd in uw werk ruimte. Als u geen model hebt, gebruikt u de [zelf studie voor installatie kopie classificatie: Train model](../machine-learning/tutorial-train-models-with-aml.md) om er een te trainen en te registreren.

> [!IMPORTANT]
> In de code fragmenten in dit artikel wordt ervan uitgegaan dat u de volgende variabelen hebt ingesteld:
>
> * `ws` -Uw Azure Machine Learning-werk ruimte.
> * `model` -Het geregistreerde model dat wordt geïmplementeerd.
> * `inference_config` -De configuratie voor het afwachten van het model.
>
> Zie [modellen implementeren met Azure machine learning](../machine-learning/how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

## <a name="create-an-azure-cache-for-redis-instance"></a>Een instantie van Azure Cache voor Redis maken 
U kunt een machine learning model implementeren om te Azure Functions met een basis-, standaard-of Premium-cache-exemplaar. Volg deze stappen om een cache-exemplaar te maken.  

1. Ga naar de Azure Portal start pagina of open het menu Sidebar en selecteer vervolgens **een resource maken**. 
   
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecteer Azure Cache voor Redis.":::
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor de nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Open de vervolgkeuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Prijscategorie** | Open de vervolgkeuzelijst en selecteer een [Prijscategorie](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |

1. Selecteer het tabblad **Netwerken** of klik op de knop **Netwerken** onderaan de pagina.

1. Selecteer uw verbindingsmethode op het tabblad **Netwerk**.

1. Selecteer het tabblad **Volgende: Geavanceerd** of klik op de knop **Volgende: Geavanceerd** onderaan de pagina.

1. Selecteer in het tabblad **Geavanceerd** voor een basic of standard cache-exemplaar de schakeloptie inschakelen als u een niet-TLS-poort wilt inschakelen.

1. Configureer in het tabblad **Geavanceerd** voor premium cache-exemplaar de instellingen voor een niet-TLS-poort, clustering en gegevenspersistentie.

1. Selecteer het tabblad **Volgende: Tags** of klik op de knop **Volgende: Tags** onderaan de pagina.

1. Voer desgewenst in het tabblad **Tags** de naam en waarde in om de resource te categoriseren. 

1. Selecteer **Controleren + maken**. Het tabblad Beoordelen + maken wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Selecteer **Maken** nadat het groene bericht Validatie geslaagd verschijnt.

Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken. 

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden

Voordat u implementeert, moet u definiëren wat er nodig is om het model als een webservice uit te voeren. In de volgende lijst worden de belangrijkste items beschreven die nodig zijn voor een implementatie:

* Een __invoer script__. Met dit script worden aanvragen geaccepteerd, wordt de aanvraag met het model gescoord en worden de resultaten geretourneerd.

    > [!IMPORTANT]
    > Het invoer script is specifiek voor uw model. het moet inzicht krijgen in de indeling van de gegevens van de inkomende aanvraag, de indeling van de gegevens die worden verwacht door uw model en de indeling van de gegevens die aan clients worden geretourneerd.
    >
    > Als de gegevens van de aanvraag een indeling hebben die niet kan worden gebruikt door uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook de reactie transformeren voordat deze wordt geretourneerd naar de client.
    >
    > Wanneer de functie wordt ingepakt, wordt de invoer standaard beschouwd als tekst. Als u geïnteresseerd bent in het verbruiken van de onbewerkte bytes van de invoer (bijvoorbeeld voor BLOB-triggers), moet u [AMLRequest gebruiken om onbewerkte gegevens te accepteren](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data).

Zorg ervoor dat de functie voor uitvoeren verbinding maakt met een redis-eind punt.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Zie [Score code definiëren](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script) voor meer informatie over het invoer script.

* **Afhankelijkheden**, zoals hulp scripts of python/Conda-pakketten die zijn vereist voor het uitvoeren van het script of model van de vermelding

Deze entiteiten worden ingekapseld in een Afleidings __configuratie__. De deductieconfiguratie verwijst naar het invoerscript en andere afhankelijkheden.

> [!IMPORTANT]
> Wanneer u een configuratie voor afwijzen maakt voor gebruik met Azure Functions, moet u een [omgevings](/python/api/azureml-core/azureml.core.environment%28class%29) object gebruiken. Houd er rekening mee dat als u een aangepaste omgeving definieert, de standaard waarden van azureml en versie >= 1.0.45 als een PIP-afhankelijkheid worden toegevoegd. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten. In het volgende voor beeld ziet u hoe u een omgevings object maakt en dit gebruikt met een configuratie voor ingaand gebruik:
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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Zie [omgevingen maken en beheren voor training en implementatie](../machine-learning/how-to-use-environments.md)voor meer informatie over omgevingen.

Zie [modellen implementeren met Azure machine learning](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration)voor meer informatie over het afnemen van de configuratie.

> [!IMPORTANT]
> Wanneer u naar functies implementeert, hoeft u geen __implementatie configuratie__ te maken.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Het SDK-preview-pakket voor functie-ondersteuning installeren

Als u pakketten voor Azure Functions wilt maken, moet u het SDK-preview-pakket installeren.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>De installatiekopie maken

Als u de docker-installatie kopie wilt maken die is geïmplementeerd op Azure Functions, gebruikt u [azureml. contrib. functions. package](/python/api/azureml-contrib-functions/azureml.contrib.functions) of de specifieke pakket functie voor de trigger die u wilt gebruiken. In het volgende code fragment ziet u hoe u een nieuw pakket maakt met een HTTP-trigger uit het model en de configuratie voor afleiden:

> [!NOTE]
> In het code fragment wordt ervan uitgegaan dat het `model` een geregistreerd model bevat en dat `inference_config` de configuratie voor de afnemende omgeving bevat. Zie [modellen implementeren met Azure machine learning](../machine-learning/how-to-deploy-and-where.md)voor meer informatie.

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Wanneer `show_output=True` wordt de uitvoer van het docker-bouw proces weer gegeven. Zodra het proces is voltooid, is de afbeelding gemaakt in de Azure Container Registry voor uw werk ruimte. Zodra de installatie kopie is gemaakt, wordt de locatie in uw Azure Container Registry weer gegeven. De geretourneerde locatie heeft de indeling `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> De functie voor verpakking voor functions ondersteunt momenteel HTTP-triggers, Blob-triggers en service bus-triggers. Zie [Azure functions-bindingen](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns)voor meer informatie over triggers.

> [!IMPORTANT]
> Sla de locatie gegevens op, zoals deze worden gebruikt bij het implementeren van de installatie kopie.

## <a name="deploy-image-as-a-web-app"></a>Een installatie kopie implementeren als een web-app

1. Gebruik de volgende opdracht om de aanmeldings referenties op te halen voor de Azure Container Registry die de installatie kopie bevat. Vervang door `<myacr>` de waarde die u eerder hebt geretourneerd van `package.location` : 

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

    Sla de waarde voor de __gebruikers naam__ en een van de __wacht woorden__ op.

1. Als u nog geen resource groep of app service-plan hebt voor het implementeren van de service, demonstreert de volgende opdrachten hoe u beide maakt:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In dit voor beeld wordt een _Linux Basic_ -prijs categorie ( `--sku B1` ) gebruikt.

    > [!IMPORTANT]
    > Installatie kopieën die zijn gemaakt door Azure Machine Learning Linux gebruiken, dus u moet de `--is-linux` para meter gebruiken.

1. Maak het opslag account dat moet worden gebruikt voor de opslag van de Web-taak en ontvang het connection string. Vervang door `<webjobStorage>` de naam die u wilt gebruiken.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Als u de functie-app wilt maken, gebruikt u de volgende opdracht. Vervang door `<app-name>` de naam die u wilt gebruiken. Vervang `<acrinstance>` en door `<imagename>` de waarden die eerder zijn geretourneerd `package.location` . Vervang door `<webjobStorage>` de naam van het opslag account uit de vorige stap:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Op dit moment is de functie-app gemaakt. Omdat u echter geen connection string hebt ingevoerd voor de HTTP-trigger of referenties voor de Azure Container Registry die de installatie kopie bevatten, is de functie-app niet actief. In de volgende stappen geeft u de connection string en de verificatie gegevens voor het container register. 

1. Als u de functie-app met de referenties die nodig zijn voor toegang tot het container register, wilt opgeven, gebruikt u de volgende opdracht. Vervang door `<app-name>` de naam van de functie-app. Vervang `<acrinstance>` en `<imagetag>` door de waarden van de AANROEP AZ cli in de vorige stap. Vervang `<username>` en door `<password>` de ACR-aanmeldings gegevens die u eerder hebt opgehaald:

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

Op dit punt begint de functie-app het laden van de installatie kopie.

> [!IMPORTANT]
> Het kan enkele minuten duren voordat de installatie kopie is geladen. U kunt de voortgang bewaken met behulp van de Azure Portal.

## <a name="test-azure-functions-http-trigger"></a>Azure Functions HTTP-trigger testen 

We gaan nu onze Azure Functions HTTP-trigger uitvoeren en testen.

1. Ga naar de functie-app in het Azure Portal.
1. Selecteer onder ontwikkelaar **code + test**. 
1. Klik aan de rechter kant op het tabblad **invoer** . 
1. Klik op de knop **uitvoeren** om de Azure functions http-trigger te testen. 

U hebt nu een model geïmplementeerd van Azure Machine Learning als een functie-app met behulp van een Azure-cache voor redis-exemplaar. Meer informatie over Azure cache voor redis door te navigeren naar de koppelingen in de onderstaande sectie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u verder wilt gaan met de volgende zelfstudie, kunt u de resources die u in deze snelstart hebt gemaakt, behouden en opnieuw gebruiken.

Als u klaar bent met de Snelstartgids, kunt u de Azure-resources die u in deze Quick Start hebt gemaakt, verwijderen om kosten te voor komen. 

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. Wanneer u een resourcegroep verwijdert, worden alle resources in de groep definitief verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. Als u de resources voor het hosten van dit voorbeeld in een bestaande resourcegroep hebt gemaakt en deze groep ook resources bevat die u wilt behouden, kunt u elke resource afzonderlijk verwijderen via hun respectievelijke blade.

### <a name="to-delete-a-resource-group"></a>Een resourcegroep verwijderen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer vervolgens **Resourcegroepen**.

2. Typ in het vak **Filteren op naam...** de naam van de resourcegroep. Selecteer in de resourcegroep, in de resultatenlijst, de optie **...** . Selecteer vervolgens **Resourcegroep verwijderen**.

U wordt gevraagd om het verwijderen van de resourcegroep te bevestigen. Typ ter bevestiging de naam van de resourcegroep. Selecteer vervolgens **Verwijderen**.

Na enkele ogenblikken worden de resourcegroep en alle bijbehorende resources verwijderd.

## <a name="next-steps"></a>Volgende stappen 

* Meer informatie over [Azure cache voor redis](./cache-overview.md)
* Meer informatie over het configureren van uw functie-app in de [functions](../azure-functions/functions-create-function-linux-custom-image.md) -documentatie.
* [Naslaginformatie voor API](/python/api/azureml-contrib-functions/azureml.contrib.functions) 
* Een [python-app maken die gebruikmaakt van Azure cache voor redis](./cache-python-get-started.md)
