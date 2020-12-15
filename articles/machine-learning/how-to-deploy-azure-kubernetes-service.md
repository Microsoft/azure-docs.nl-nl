---
title: ML-modellen implementeren in Kubernetes-service
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning-modellen als een webservice met behulp van de Azure Kubernetes-service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: d7540066ccc0d3a62dbd4012eee100d8e8aea98f
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505083"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Een model implementeren in een Azure Kubernetes service-cluster

Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model als een webservice op Azure Kubernetes service (AKS). De Azure Kubernetes-service is geschikt voor grootschalige productie-implementaties. Gebruik de Azure Kubernetes-service als u een of meer van de volgende mogelijkheden nodig hebt:

- __Snelle reactie tijd__
- Automatisch __schalen__ van de geïmplementeerde service
- __Logboekregistratie__
- __Gegevens verzameling model__
- __Verificatie__
- __TLS-beëindiging__
- __Opties voor hardwareversnelling,__ zoals GPU en Programmeer bare poort matrices (FPGA)

Wanneer u implementeert in azure Kubernetes service, implementeert u naar een AKS-cluster dat is __verbonden met uw werk ruimte__. Zie [een Azure Kubernetes-service cluster maken en koppelen](how-to-create-attach-kubernetes.md)voor meer informatie over het verbinden van een AKS-cluster met uw werk ruimte.

> [!IMPORTANT]
> U wordt aangeraden lokaal fouten op te sporen voordat u de webservice implementeert. Zie voor meer informatie [fouten opsporen in lokaal](./how-to-troubleshoot-deployment-local.md)
>
> U kunt ook verwijzen naar Azure Machine Learning: [Deploy to Local Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local) (Implementeren naar lokale notebook)

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een machine learning model dat in uw werk ruimte is geregistreerd. Als u geen geregistreerd model hebt, raadpleegt u [hoe en hoe u modellen implementeert](how-to-deploy-and-where.md).

- De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

- In de code fragmenten van __python__ in dit artikel wordt ervan uitgegaan dat de volgende variabelen zijn ingesteld:

    * `ws` -Ingesteld op uw werk ruimte.
    * `model` -Ingesteld op uw geregistreerde model.
    * `inference_config` -Stel in op de configuratie voor het afstellen van het model.

    Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

- In het __cli__ -fragment in dit artikel wordt ervan uitgegaan dat u een document hebt gemaakt `inferenceconfig.json` . Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van dit document.

- Een Azure Kubernetes-service cluster dat is verbonden met uw werk ruimte. Zie [een Azure Kubernetes-service cluster maken en koppelen](how-to-create-attach-kubernetes.md)voor meer informatie.

    - Als u modellen wilt implementeren op GPU-knoop punten of FPGA-knoop punten (of een specifieke SKU), moet u een cluster maken met de specifieke SKU. Er is geen ondersteuning voor het maken van een secundaire knooppunt groep in een bestaand cluster en het implementeren van modellen in de secundaire knooppunt groep.

## <a name="understand-the-deployment-processes"></a>Meer informatie over de implementatie processen

Het woord ' implementatie ' wordt gebruikt in zowel Kubernetes als Azure Machine Learning. ' Implementatie ' heeft verschillende betekenissen in deze twee contexten. In Kubernetes is een een `Deployment` concrete entiteit die is opgegeven met een declaratief yaml-bestand. Een Kubernetes `Deployment` heeft een gedefinieerde levens cyclus en concrete relaties met andere Kubernetes-entiteiten, zoals `Pods` en `ReplicaSets` . Meer informatie over Kubernetes van documenten en Video's vindt u op [Wat is Kubernetes?](https://aka.ms/k8slearning).

In Azure Machine Learning wordt ' implementatie ' gebruikt voor een meer algemene indruk van het maken van de beschik baarheid en het opschonen van uw project resources. De stappen die Azure Machine Learning een deel van de implementatie beschouwt zijn:

1. Inpakken de bestanden in de projectmap en negeert deze die zijn opgegeven in. amlignore of. gitignore
1. Uw berekenings cluster omhoog schalen (gekoppeld aan Kubernetes)
1. Het dockerfile maken of downloaden van het reken knooppunt (heeft betrekking op Kubernetes)
    1. Het systeem berekent een hash van: 
        - De basis installatie kopie 
        - Aangepaste stappen voor docker (Zie [een model implementeren met behulp van een aangepaste docker-basis installatie kopie](./how-to-deploy-custom-docker-image.md))
        - De Conda definition YAML (Zie [& software omgevingen maken in azure machine learning](./how-to-use-environments.md))
    1. Het systeem gebruikt deze hash als de sleutel in een zoek opdracht van de werk ruimte Azure Container Registry (ACR)
    1. Als deze niet wordt gevonden, wordt gezocht naar een overeenkomst in de globale ACR
    1. Als deze niet wordt gevonden, bouwt het systeem een nieuwe installatie kopie (die in de cache wordt opgeslagen en gepusht naar de werk ruimte ACR)
1. Het gecomprimeerde project bestand downloaden naar de tijdelijke opslag op het reken knooppunt
1. Het project bestand uitgepakt
1. Het reken knooppunt wordt uitgevoerd `python <entry script> <arguments>`
1. Logboeken, model bestanden en andere bestanden die zijn geschreven naar `./outputs` naar het opslag account dat is gekoppeld aan de werk ruimte opslaan
1. Computer omlaag schalen, inclusief het verwijderen van tijdelijke opslag (gekoppeld aan Kubernetes)

### <a name="azure-ml-router"></a>Azure ML-router

Het front-end-onderdeel (azureml-Fe) dat binnenkomende aanvragen voor in-interferentie naar geïmplementeerde Services doorstuurt, wordt automatisch geschaald naar behoefte. Schalen van azureml-Fe is gebaseerd op het cluster doel en de grootte van het AKS (aantal knoop punten). Het cluster doeleinde en knoop punten worden geconfigureerd wanneer u [een AKS-cluster maakt of koppelt](how-to-create-attach-kubernetes.md). Er is één azureml-Fe-service per cluster, die op meerdere peulen kan worden uitgevoerd.

> [!IMPORTANT]
> Wanneer u een cluster gebruikt dat is geconfigureerd als __dev-test__, wordt de zelf schaal **uitgeschakeld**.

Azureml-Fe schaalt beide omhoog (verticaal) om meer kernen te gebruiken en uit (horizon taal) om meer peulen te gebruiken. Wanneer u besluit om omhoog te schalen, wordt de tijd gebruikt die nodig is voor het routeren van binnenkomende aanvragen voor navragen. Als deze tijd de drempel waarde overschrijdt, wordt er een opschalen uitgevoerd. Als de tijd voor het routeren van binnenkomende aanvragen de drempel waarde blijft overschrijden, treedt er een uitschalen op.

Wanneer u omlaag of omlaag schaalt, wordt het CPU-gebruik gebruikt. Als aan de drempel waarde voor het CPU-gebruik wordt voldaan, wordt de front-end eerst omlaag geschaald. Als het CPU-gebruik de drempel waarde voor inschalen inneemt, vindt er een inschaal bewerking plaats. U kunt alleen omhoog en omlaag schalen als er voldoende cluster bronnen beschikbaar zijn.

## <a name="deploy-to-aks"></a>Implementeren naar AKS

Als u een model wilt implementeren in azure Kubernetes service, maakt u een __implementatie configuratie__ waarin de benodigde reken resources worden beschreven. Bijvoorbeeld het aantal kernen en het geheugen. U hebt ook een Afleidings __configuratie__ nodig, waarmee de omgeving wordt beschreven die nodig is voor het hosten van het model en de webservice. Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van de configuratie voor demijnen.

> [!NOTE]
> Het aantal modellen dat moet worden geïmplementeerd, is beperkt tot 1.000 modellen per implementatie (per container).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute?preserve-view=true&view=azure-ml-py)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?preserve-view=true&view=azure-ml-py)
* [Model. implementeren](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt implementeren met behulp van de CLI, gebruikt u de volgende opdracht. Vervang door `myaks` de naam van het AKS Compute-doel. Vervang door `mymodel:1` de naam en versie van het geregistreerde model. Vervang door `myservice` de naam om deze service te verlenen:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Zie voor meer informatie de referentie [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Zie voor meer informatie over het gebruik van VS code [implementeren naar AKS via de VS code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Voor de implementatie via VS code moet het AKS-cluster vooraf worden gemaakt of aan uw werk ruimte zijn gekoppeld.

---

### <a name="autoscaling"></a>Automatisch schalen

Het onderdeel dat automatisch schalen afhandelt voor Azure ML-model implementaties is azureml-Fe, een Smart Request-router. Omdat alle aanvragen voor navragen worden uitgevoerd, heeft het de benodigde gegevens om de geïmplementeerde model (s) automatisch te schalen.

> [!IMPORTANT]
> * **Schakel Kubernetes Horizontal pod autoscaler (HPA) niet in voor model implementaties**. Als dit het geval is, worden de twee onderdelen voor automatisch schalen met elkaar in concurrentie gebracht. Azureml-Fe is ontworpen voor het automatisch schalen van modellen die door Azure ML zijn geïmplementeerd, waarbij HPA het model gebruik van een algemene metriek zoals CPU-gebruik of een aangepaste metrische configuratie zou moeten raden of benaderen.
> 
> * Met **Azureml-Fe wordt het aantal knoop punten in een AKS-cluster niet geschaald**, omdat dit tot onverwachte kosten verhogingen kan leiden. In plaats daarvan **wordt het aantal replica's voor het model** binnen de grenzen van het fysieke cluster geschaald. Als u het aantal knoop punten in het cluster wilt schalen, kunt u het cluster hand matig schalen of [de AKS-cluster-automatische schaalr configureren](../aks/cluster-autoscaler.md).

Automatisch schalen kan worden bepaald door de instelling `autoscale_target_utilization` , `autoscale_min_replicas` en `autoscale_max_replicas` voor de AKS-webservice. In het volgende voor beeld ziet u hoe u automatisch schalen inschakelt:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Beslissingen voor omhoog/omlaag schalen is gebaseerd op het gebruik van de huidige container replica's. Het huidige gebruik is het aantal replica's dat bezig is met het verwerken van een aanvraag, gedeeld door het totale aantal huidige replica's. Als dit aantal groter is dan `autoscale_target_utilization` , worden er meer replica's gemaakt. Als deze lager is, worden de replica's gereduceerd. Het doel gebruik is standaard 70%.

Beslissingen voor het toevoegen van replica's zijn nu en snel (rond 1 seconde). Beslissingen voor het verwijderen van replica's zijn conservatief (ongeveer 1 minuut).

U kunt de vereiste replica's berekenen met behulp van de volgende code:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

`autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` Zie de naslag gids voor [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) voor meer informatie over het instellen van en.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellen implementeren op AKS met behulp van gecontroleerde implementatie (preview-versie)

Analyseer en promoot model versies op een gecontroleerde manier met behulp van eind punten. U kunt Maxi maal zes versies achter één eind punt implementeren. Eind punten bieden de volgende mogelijkheden:

* Het __percentage score verkeer configureren dat naar elk eind punt wordt verzonden__. Route bijvoorbeeld 20% van het verkeer naar eind punt ' test ' en 80% naar ' productie '.

    > [!NOTE]
    > Als u geen account voor 100% van het verkeer hebt, wordt het resterende percentage doorgestuurd naar de __standaard__ eindpunt versie. Als u bijvoorbeeld eind punt versie ' test ' configureert om 10% van het verkeer te krijgen, en ' Prod ' voor 30%, wordt de resterende 60% naar de standaard eindpunt versie verzonden.
    >
    > De eerste versie van het eind punt die wordt gemaakt, wordt automatisch geconfigureerd als standaard. U kunt dit wijzigen door in te stellen `is_default=True` Wanneer u een eindpunt versie maakt of bijwerkt.
     
* Een eindpunt versie als een __controle__ of __behandeling__ labelen. Zo kan de huidige versie van het productie-eind punt het besturings element zijn, terwijl mogelijke nieuwe modellen worden geïmplementeerd als behandelings versies. Na het evalueren van de prestaties van de behandelings versies, kan deze worden gepromoveerd tot de nieuwe productie/het andere besturings element.

    > [!NOTE]
    > U kunt slechts __één__ besturings element hebben. U kunt meerdere behandelingen hebben.

U kunt app Insights inschakelen om operationele metrische gegevens van eind punten en geïmplementeerde versies weer te geven.

### <a name="create-an-endpoint"></a>Een eindpunt maken
Wanneer u klaar bent om uw modellen te implementeren, maakt u een score-eind punt en implementeert u uw eerste versie. In het volgende voor beeld ziet u hoe u het eind punt implementeert en maakt met behulp van de SDK. De eerste implementatie wordt gedefinieerd als de standaard versie, wat betekent dat niet-opgegeven verkeers percentiel in alle versies naar de standaard versie gaat.  

> [!TIP]
> In het volgende voor beeld wordt met de configuratie de eerste eindpunt versie ingesteld voor het verwerken van 20% van het verkeer. Aangezien dit het eerste eind punt is, is het ook de standaard versie. Omdat we geen andere versies hebben voor de andere 80% van het verkeer, wordt deze ook doorgestuurd naar de standaard waarde. Totdat andere versies die een percentage van het verkeer uitvoeren, worden geïmplementeerd, ontvangt deze een effectief 100% van het verkeer.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Versies bijwerken en toevoegen aan een eind punt

Voeg nog een versie toe aan uw eind punt en configureer het Score verkeer percentiel naar de versie. Er zijn twee soorten versies, een besturings element en een behandelings versie. Er kunnen meerdere behandelings versies zijn om te vergelijken met één versie van het besturings element.

> [!TIP]
> De tweede versie, gemaakt door het volgende code fragment, accepteert 10% van het verkeer. De eerste versie is geconfigureerd voor 20%, dus slechts 30% van het verkeer is geconfigureerd voor specifieke versies. De resterende 70% wordt verzonden naar de eerste eindpunt versie, omdat het ook de standaard versie is.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Bestaande versies bijwerken of verwijderen in een eind punt. U kunt het standaard type van de versie, het type besturings element en het verkeers percentiel wijzigen. In het volgende voor beeld verhoogt de tweede versie het verkeer naar 40% en is nu de standaard waarde.

> [!TIP]
> Na het volgende code fragment is de tweede versie nu standaard. Het is nu geconfigureerd voor 40%, terwijl de oorspronkelijke versie nog steeds is geconfigureerd voor 20%. Dit betekent dat 40% van het verkeer niet is verwerkt door versie configuraties. Het overgebleven verkeer wordt doorgestuurd naar de tweede versie, omdat dit nu standaard is. Het ontvangt effectief 80% van het verkeer.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Web-serviceverificatie

Bij het implementeren naar de Azure Kubernetes-service is verificatie op __basis van een sleutel__ standaard ingeschakeld. U kunt ook verificatie __op basis van tokens__ inschakelen. Voor verificatie op basis van tokens moeten clients een Azure Active Directory-account gebruiken om een verificatie token aan te vragen, dat wordt gebruikt om aanvragen te doen voor de geïmplementeerde service.

Als u de verificatie wilt __uitschakelen__ , stelt u de `auth_enabled=False` para meter bij het maken van de implementatie configuratie in. In het volgende voor beeld wordt de verificatie uitgeschakeld met de SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Zie voor meer informatie over het verifiëren van een client toepassing het [model een Azure machine learning gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Verificatie met sleutels

Als sleutel verificatie is ingeschakeld, kunt u de `get_keys` methode gebruiken om een primaire en secundaire verificatie sleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel opnieuw moet genereren, gebruikt u [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Verificatie met tokens

Als u token verificatie wilt inschakelen, stelt `token_auth_enabled=True` u de para meter in wanneer u een implementatie maakt of bijwerkt. In het volgende voor beeld wordt token verificatie met behulp van de SDK ingeschakeld:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Als token verificatie is ingeschakeld, kunt u de `get_token` methode gebruiken om een JWT-token op te halen en de verval tijd van dat token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet een nieuw token aanvragen na de tijd van de token `refresh_by` .
>
> Micro soft raadt u ten zeerste aan om uw Azure Machine Learning-werk ruimte te maken in dezelfde regio als uw Azure Kubernetes service-cluster. Als u wilt verifiëren met een token, wordt er door de webservice een aanroep naar de regio waarin uw Azure Machine Learning-werk ruimte is gemaakt. Als de regio van uw werk ruimte niet beschikbaar is, kunt u geen token voor uw webservice ophalen, zelfs als uw cluster zich in een andere regio bevindt dan uw werk ruimte. Dit leidt ertoe dat op tokens gebaseerde verificatie niet beschikbaar is totdat de regio van de werk ruimte weer beschikbaar is. Daarnaast is de afstand tussen de regio van uw cluster en de regio van uw werk ruimte groter, hoe langer het duurt om een token op te halen.
>
> Als u een token wilt ophalen, moet u de Azure Machine Learning SDK of de opdracht [AZ ml service Get-access-token](/cli/azure/ext/azure-cli-ml/ml/service?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) gebruiken.


### <a name="vulnerability-scanning"></a>Scannen op beveiligingsproblemen

Azure Security Center biedt geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor verschillende hybride cloudworkloads. U moet Azure Security Center toestaan om uw resources te scannen en de aanbevelingen te volgen. Zie [integratie van Azure Kubernetes Services met Security Center](../security-center/defender-for-kubernetes-introduction.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Azure RBAC gebruiken voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)
* [Veilige omgeving voor het afwijzen van interferentie met Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
