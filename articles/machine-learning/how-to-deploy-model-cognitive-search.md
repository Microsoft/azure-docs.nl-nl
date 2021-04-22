---
title: Een model implementeren voor gebruik met Cognitive Search
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik Azure Machine Learning het implementeren van een model voor gebruik met Cognitive Search. Het model wordt gebruikt als een aangepaste vaardigheid om de zoekervaring te verrijken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 5a6fc35db56d016362f006e401ddb156e931440b
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889480"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Een model implementeren voor gebruik met Cognitive Search


In dit artikel leert u hoe u Azure Machine Learning kunt gebruiken om een model te implementeren voor gebruik met [Azure Cognitive Search](../search/search-what-is-azure-search.md).

Cognitive Search voert inhoudsverwerking uit op heterogene inhoud, om query's door mensen of toepassingen mogelijk te maken. Dit proces kan worden verbeterd met behulp van een model dat is geïmplementeerd vanuit Azure Machine Learning.

Azure Machine Learning kunt een getraind model implementeren als een webservice. De webservice wordt vervolgens ingesloten in een Cognitive Search _vaardigheid_, die deel gaat uitmaken van de verwerkingspijplijn.

> [!IMPORTANT]
> De informatie in dit artikel is specifiek voor de implementatie van het model. Het bevat informatie over de ondersteunde implementatieconfiguraties waarmee het model kan worden gebruikt door Cognitive Search.
>
> Zie de zelfstudie Een aangepaste vaardigheid bouwen en implementeren Cognitive Search het geïmplementeerde model voor meer informatie over het configureren Azure Machine Learning het [geïmplementeerde](../search/cognitive-search-tutorial-aml-custom-skill.md) model.
>
> Zie voor het voorbeeld op basis van de [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) zelfstudie.

Wanneer u een model implementeert voor gebruik met Azure Cognitive Search, moet de implementatie voldoen aan de volgende vereisten:

* Gebruik Azure Kubernetes Service om het model te hosten voor de deferie.
* Schakel Transport Layer Security (TLS) in voor de Azure Kubernetes Service. TLS wordt gebruikt om HTTPS-communicatie tussen Cognitive Search en het geïmplementeerde model te beveiligen.
* Het invoerscript moet het pakket `inference_schema` gebruiken om een OpenAPI-schema (Swagger) voor de service te genereren.
* Het invoerscript moet ook JSON-gegevens als invoer accepteren en JSON als uitvoer genereren.


## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)

* Een Python-ontwikkelomgeving met de Azure Machine Learning SDK geïnstalleerd. Zie voor meer informatie [Azure Machine Learning SDK.](/python/api/overview/azure/ml/install)  

* Een geregistreerd model. Als u geen model hebt, gebruikt u het voorbeeldnoteboek op [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Een algemeen begrip van [hoe en waar modellen moeten worden geïmplementeerd.](how-to-deploy-and-where.md)

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

Een Azure Machine Learning biedt een centrale plaats om te werken met alle artefacten die u maakt wanneer u Azure Machine Learning. De werkruimte houdt een geschiedenis bij van alle trainingsuitvoer, inclusief logboeken, metrische gegevens, uitvoer en een momentopname van uw scripts.

Gebruik de volgende code om verbinding te maken met een bestaande werkruimte:

> [!IMPORTANT]
> Dit codefragment verwacht dat de werkruimteconfiguratie wordt opgeslagen in de huidige of de bovenliggende map. Zie Create and manage Azure Machine Learning workspaces (Werkruimten maken [Azure Machine Learning beheren) voor meer informatie.](how-to-manage-workspace.md) Zie [Een configuratiebestand voor een werkruimte maken](how-to-configure-environment.md#workspace) voor meer informatie over de configuratie als bestand opslaan.

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Een Kubernetes-cluster maken

**Geschatte tijd:** ongeveer 20 minuten.

Een Kubernetes-cluster is een set exemplaren van virtuele machines (knooppunten genoemd) die worden gebruikt voor het uitvoeren van toepassingen in containers.

Wanneer u een model implementeert van Azure Machine Learning naar Azure Kubernetes Service, worden het model en alle assets die nodig zijn om het als een webservice te hosten, verpakt in een Docker-container. Deze container wordt vervolgens geïmplementeerd op het cluster.

De volgende code laat zien hoe u een nieuw AKS Azure Kubernetes Service cluster (AKS) voor uw werkruimte maakt:

> [!TIP]
> U kunt ook een bestaande Azure Kubernetes Service aan uw Azure Machine Learning werkruimte. Zie How to deploy models to Azure Kubernetes Service [(Modellen implementeren naar Azure Kubernetes Service) voor meer Azure Kubernetes Service.](how-to-deploy-azure-kubernetes-service.md)

> [!IMPORTANT]
> U ziet dat de code de `enable_ssl()` methode gebruikt om Transport Layer Security (TLS) voor het cluster in teschakelen. Dit is vereist wanneer u van plan bent het geïmplementeerde model te gebruiken vanuit Cognitive Services.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure factureren u zolang het AKS-cluster bestaat. Zorg ervoor dat u uw AKS-cluster verwijdert wanneer u klaar bent met het cluster.

Zie How to deploy to Azure Kubernetes Service (Implementeren naar een Azure Kubernetes Service) voor meer informatie over het gebruik van AKS [Azure Machine Learning.](how-to-deploy-azure-kubernetes-service.md)

## <a name="write-the-entry-script"></a>Het invoerscript schrijven

Het invoerscript ontvangt gegevens die naar de webservice worden verzonden, geeft deze door aan het model en retourneert de scoreresultaten. Het volgende script laadt het model bij het opstarten en gebruikt vervolgens het model om gegevens te scoren. Dit bestand wordt ook wel `score.py` genoemd.

> [!TIP]
> Het invoerscript is specifiek voor uw model. Het script moet bijvoorbeeld het framework kennen dat moet worden gebruikt met uw model, gegevensindelingen, enzovoort.

> [!IMPORTANT]
> Wanneer u van plan bent het geïmplementeerde model te gebruiken vanuit Azure Cognitive Services, moet u het pakket gebruiken om het genereren van schema's voor `inference_schema` de implementatie mogelijk te maken. Dit pakket biedt verdelers waarmee u de invoer- en uitvoergegevensindeling kunt definiëren voor de webservice die de deferentie uitvoert met behulp van het model.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Zie How and where to deploy (Hoe en waar implementeren) [voor meer informatie over invoerscripts.](how-to-deploy-and-where.md)

## <a name="define-the-software-environment"></a>De softwareomgeving definiëren

De omgevingsklasse wordt gebruikt om de Python-afhankelijkheden voor de service te definiëren. Het bevat afhankelijkheden die vereist zijn voor zowel het model als het invoerscript. In dit voorbeeld worden pakketten geïnstalleerd vanuit de reguliere pypi-index en vanuit een GitHub-opslagplaats. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Zie Omgevingen maken en beheren voor training en implementatie voor meer [informatie over omgevingen.](how-to-use-environments.md)

## <a name="define-the-deployment-configuration"></a>De implementatieconfiguratie definiëren

De implementatieconfiguratie definieert de Azure Kubernetes Service hostomgeving die wordt gebruikt om de webservice uit te voeren.

> [!TIP]
> Als u niet zeker bent over de geheugen-, CPU- of GPU-behoeften van uw implementatie, kunt u profilering gebruiken om deze te leren. Zie How [and where to deploy a model (Een model implementeren) voor meer informatie.](how-to-deploy-and-where.md)

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Zie voor meer informatie de referentiedocumentatie voor [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>De deferentieconfiguratie definiëren

De deferentieconfiguratie wijst naar het invoerscript en het omgevingsobject:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Zie de referentiedocumentatie voor [InferenceConfig voor meer informatie.](/python/api/azureml-core/azureml.core.model.inferenceconfig)

## <a name="deploy-the-model"></a>Het model implementeren

Implementeer het model in uw AKS-cluster en wacht tot het uw service heeft gemaakt. In dit voorbeeld worden twee geregistreerde modellen uit het register geladen en geïmplementeerd in AKS. Na de implementatie worden deze modellen geladen door het bestand in de implementatie `score.py` en gebruikt om deferentie uit te voeren.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Zie de referentiedocumentatie voor Model voor [meer informatie.](/python/api/azureml-core/azureml.core.model.model)

## <a name="issue-a-sample-query-to-your-service"></a>Een voorbeeldquery aan uw service uitgeven

In het volgende voorbeeld worden de implementatiegegevens gebruikt die zijn opgeslagen in `aks_service` de variabele in de vorige codesectie. Deze variabele wordt gebruikt om de scoring-URL en het verificatie token op te halen die nodig zijn om te communiceren met de service:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

Het resultaat dat door de service wordt geretourneerd, is vergelijkbaar met de volgende JSON:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Verbinding maken met Cognitive Search

Zie de zelfstudie Een aangepaste vaardigheid bouwen Cognitive Search implementeren met Azure Machine Learning model voor meer informatie over het [gebruik van Azure Machine Learning.](../search/cognitive-search-tutorial-aml-custom-skill.md)

## <a name="clean-up-the-resources"></a>Resources opschonen

Als u het AKS-cluster specifiek voor dit voorbeeld hebt gemaakt, verwijdert u uw resources nadat u klaar bent met het testen met Cognitive Search.

> [!IMPORTANT]
> Azure befacturen u op basis van hoe lang het AKS-cluster wordt geïmplementeerd. Zorg ervoor dat u het opschoont nadat u klaar bent.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste vaardigheid bouwen en implementeren met Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)