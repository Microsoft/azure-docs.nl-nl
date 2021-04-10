---
title: Een model implementeren voor gebruik met Cognitive Search
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model voor gebruik met Cognitive Search. Het model wordt gebruikt als aangepaste vaardigheid voor het verrijken van de zoek ervaring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 22c8880cbcde1f1a55fa66beee0323e0348e1164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149610"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Een model implementeren voor gebruik met Cognitive Search


In dit artikel leert u hoe u Azure Machine Learning kunt gebruiken om een model te implementeren voor gebruik met [Azure Cognitive Search](../search/search-what-is-azure-search.md).

Cognitive Search voert inhouds verwerking uit via heterogene-inhoud, zodat deze kan worden doorzocht door mensen of toepassingen. Dit proces kan worden uitgebreid met behulp van een model dat is geïmplementeerd vanuit Azure Machine Learning.

Azure Machine Learning kunt een getraind model implementeren als een webservice. De webservice wordt vervolgens Inge sloten in een Cognitive Search _vaardigheid_, die onderdeel wordt van de verwerkings pijplijn.

> [!IMPORTANT]
> De informatie in dit artikel is specifiek voor de implementatie van het model. Het bevat informatie over de ondersteunde implementatie configuraties waarmee het model door Cognitive Search kan worden gebruikt.
>
> Zie voor meer informatie over het configureren van Cognitive Search voor het gebruik van het geïmplementeerde model de zelf studie [een aangepaste vaardigheid bouwen en implementeren met Azure machine learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .
>
> Zie voor het voor beeld waarop de zelf studie is gebaseerd [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

Bij het implementeren van een model voor gebruik met Azure Cognitive Search moet de implementatie voldoen aan de volgende vereisten:

* Gebruik de Azure Kubernetes-service om het model te hosten voor demijnen.
* Schakel trans port Layer Security (TLS) in voor de Azure Kubernetes-service. TLS wordt gebruikt voor het beveiligen van HTTPS-communicatie tussen Cognitive Search en het geïmplementeerde model.
* Het invoer script moet het `inference_schema` pakket gebruiken om een OpenAPI-schema (Swagger) te genereren voor de service.
* Het invoer script moet ook JSON-gegevens als invoer accepteren en JSON als uitvoer genereren.


## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

* Een python-ontwikkel omgeving met de Azure Machine Learning SDK geïnstalleerd. Zie [Azure machine learning SDK](/python/api/overview/azure/ml/install)voor meer informatie.  

* Een geregistreerd model. Als u geen model hebt, gebruikt u het voorbeeld notitieblok op [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill) .

* Algemene informatie [over hoe en waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

Een Azure Machine Learning-werk ruimte biedt een centrale locatie voor het werken met alle artefacten die u maakt wanneer u Azure Machine Learning gebruikt. De werk ruimte houdt een geschiedenis bij van alle uitvoeringen van de training, met inbegrip van Logboeken, metrische gegevens, uitvoer en een moment opname van uw scripts.

Gebruik de volgende code om verbinding te maken met een bestaande werk ruimte:

> [!IMPORTANT]
> Dit codefragment verwacht dat de werkruimteconfiguratie wordt opgeslagen in de huidige of de bovenliggende map. Zie [Azure machine learning-werk ruimten maken en beheren](how-to-manage-workspace.md)voor meer informatie. Zie [Een configuratiebestand voor een werkruimte maken](how-to-configure-environment.md#workspace) voor meer informatie over de configuratie als bestand opslaan.

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

**Geschatte tijd**: circa 20 minuten.

Een Kubernetes-cluster is een set virtuele-machine-instanties (knoop punten genaamd) die worden gebruikt voor het uitvoeren van container toepassingen.

Wanneer u een model implementeert van Azure Machine Learning naar de Azure Kubernetes-service, worden het model en alle assets die nodig zijn voor het hosten van de service als een webservice, verpakt in een docker-container. Deze container wordt vervolgens geïmplementeerd op het cluster.

De volgende code laat zien hoe u een nieuw Azure Kubernetes service (AKS)-cluster maakt voor uw werk ruimte:

> [!TIP]
> U kunt ook een bestaande Azure Kubernetes-service toevoegen aan uw Azure Machine Learning-werk ruimte. Zie [modellen implementeren in azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md)voor meer informatie.

> [!IMPORTANT]
> U ziet dat de code de `enable_ssl()` methode gebruikt om TLS (trans port Layer Security) voor het cluster in te scha kelen. Dit is vereist wanneer u van plan bent het geïmplementeerde model van Cognitive Services te gebruiken.

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
> In azure wordt u gefactureerd zolang het AKS-cluster bestaat. Zorg ervoor dat u uw AKS-cluster verwijdert wanneer u klaar bent.

Zie [How to Deploy to Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md)(Engelstalig) voor meer informatie over het gebruik van AKS met Azure machine learning.

## <a name="write-the-entry-script"></a>Het invoer script schrijven

Het invoer script ontvangt gegevens die zijn verzonden naar de webservice, geeft deze door aan het model en retourneert de Score resultaten. Met het volgende script wordt het model bij het opstarten geladen en wordt het model vervolgens gebruikt voor het beoordelen van gegevens. Dit bestand wordt ook wel genoemd `score.py` .

> [!TIP]
> Het invoerscript is specifiek voor uw model. Het script moet bijvoorbeeld weten dat het Framework kan worden gebruikt met uw model, gegevens indelingen, enzovoort.

> [!IMPORTANT]
> Wanneer u van plan bent het geïmplementeerde model van Azure Cognitive Services te gebruiken, moet u het `inference_schema` pakket gebruiken om het genereren van schema's voor de implementatie in te scha kelen. Dit pakket biedt onderdelen waarmee u de invoer-en uitvoer gegevens indeling kunt definiëren voor de webservice die gebruikmaakt van het model.

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

Voor meer informatie over invoer scripts, Zie [hoe en waar u wilt implementeren](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>De software omgeving definiëren

De omgevings klasse wordt gebruikt voor het definiëren van de python-afhankelijkheden voor de service. Het bevat afhankelijkheden die worden vereist door het model en het script. In dit voor beeld installeert het pakketten van de gewone pypi-index, evenals van een GitHub opslag plaats. 

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

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over omgevingen.

## <a name="define-the-deployment-configuration"></a>De implementatie configuratie definiëren

De implementatie configuratie definieert de Azure Kubernetes service-hosting omgeving die wordt gebruikt om de webservice uit te voeren.

> [!TIP]
> Als u niet zeker weet wat het geheugen, de CPU of GPU nodig heeft voor uw implementatie, kunt u een profile ring gebruiken om deze informatie te lezen. Zie [hoe en wanneer u een model implementeert](how-to-deploy-and-where.md)voor meer informatie.

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

Zie de referentie documentatie voor [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)voor meer informatie.

## <a name="define-the-inference-configuration"></a>De configuratie voor het afstellen van interferentie definiëren

Het afwijzen van de configuratie punten voor het inbrengen van het script en het omgevings object:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Zie de referentie documentatie voor [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)voor meer informatie.

## <a name="deploy-the-model"></a>Het model implementeren

Implementeer het model op uw AKS-cluster en wacht tot het uw service heeft gemaakt. In dit voor beeld worden twee geregistreerde modellen geladen uit het REGI ster en geïmplementeerd in AKS. Na de implementatie worden `score.py` deze modellen door het bestand in de implementatie geladen en gebruikt om een deinterferentie uit te voeren.

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

Zie de referentie documentatie voor [model](/python/api/azureml-core/azureml.core.model.model)voor meer informatie.

## <a name="issue-a-sample-query-to-your-service"></a>Een voorbeeld query voor uw service uitgeven

In het volgende voor beeld wordt gebruikgemaakt van de implementatie-informatie die is opgeslagen in de `aks_service` variabele door de vorige code sectie. Deze variabele wordt gebruikt om de Score-URL en het verificatie token op te halen die nodig zijn om te communiceren met de service:

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

Zie voor meer informatie over het gebruik van dit model van Cognitive Search de zelf studie [een aangepaste vaardigheid bouwen en implementeren met Azure machine learning](../search/cognitive-search-tutorial-aml-custom-skill.md) .

## <a name="clean-up-the-resources"></a>Resources opschonen

Als u het AKS-cluster specifiek voor dit voor beeld hebt gemaakt, verwijdert u uw resources Nadat u deze hebt getest met Cognitive Search.

> [!IMPORTANT]
> Azure factureert u op basis van hoe lang het AKS-cluster wordt geïmplementeerd. Zorg ervoor dat u het opschoont nadat u klaar bent.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste vaardigheid bouwen en implementeren met Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)