---
title: ML-modellen implementeren in Kubernetes Service
titleSuffix: Azure Machine Learning
description: Leer hoe u uw Azure Machine Learning als een webservice implementeert met behulp van Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 6030462fc7c9678200aa14fa852a82d35f8703b6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877818"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Een model implementeren in een Azure Kubernetes Service cluster

Meer informatie over het gebruik Azure Machine Learning om een model te implementeren als een webservice op Azure Kubernetes Service (AKS). Azure Kubernetes Service is goed voor grootschalige productie-implementaties. Gebruik de Azure Kubernetes-service als u een of meer van de volgende mogelijkheden nodig hebt:

- __Snelle reactietijd__
- __Automatisch schalen van__ de geïmplementeerde service
- __Logboekregistratie__
- __Gegevensverzameling modelleren__
- __Verificatie__
- __TLS-beëindiging__
- __Opties voor__ hardwareversnelling, zoals GPU en field-programmable gate-matrices (FPGA)

Wanneer u implementeert in Azure Kubernetes Service, implementeert u in een AKS-cluster dat is __verbonden met uw werkruimte__. Zie Een AKS-cluster maken en koppelen voor meer informatie over het verbinden van een [AKS-cluster met Azure Kubernetes Service werkruimte.](how-to-create-attach-kubernetes.md)

> [!IMPORTANT]
> U wordt aangeraden lokaal fouten op te sporen voordat u implementeert in de webservice. Zie Lokaal fouten opsporen voor [meer informatie](./how-to-troubleshoot-deployment-local.md)
>
> U kunt ook verwijzen naar Azure Machine Learning: [Deploy to Local Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local) (Implementeren naar lokale notebook)

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)

- Een machine learning dat is geregistreerd in uw werkruimte. Zie Modellen implementeren als u geen geregistreerd model [hebt.](how-to-deploy-and-where.md)

- De [Azure CLI-extensie voor Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)of de [Azure Machine Learning Visual Studio Code-extensie](tutorial-setup-vscode-extension.md).

- In de Python-codefragmenten in dit artikel wordt ervan uitgenomen dat de volgende variabelen zijn ingesteld: 

    * `ws` - Stel in op uw werkruimte.
    * `model` - Ingesteld op uw geregistreerde model.
    * `inference_config` - Stel in op de deference-configuratie voor het model.

    Zie How and where to deploy models (Modellen implementeren) voor meer informatie over het instellen [van deze variabelen.](how-to-deploy-and-where.md)

- In __de CLI-fragmenten__ in dit artikel wordt ervan uitgenomen dat u een document hebt `inferenceconfig.json` gemaakt. Zie How and where to deploy models (Modellen implementeren) voor meer informatie [over het maken van dit document.](how-to-deploy-and-where.md)

- Een Azure Kubernetes Service cluster dat is verbonden met uw werkruimte. Zie Een cluster maken en koppelen voor [Azure Kubernetes Service meer informatie.](how-to-create-attach-kubernetes.md)

    - Als u modellen wilt implementeren op GPU-knooppunten of FPGA-knooppunten (of een specifieke SKU), moet u een cluster maken met de specifieke SKU. Er is geen ondersteuning voor het maken van een secundaire knooppuntgroep in een bestaand cluster en het implementeren van modellen in de secundaire knooppuntgroep.

## <a name="understand-the-deployment-processes"></a>Inzicht in de implementatieprocessen

Het woord 'implementatie' wordt gebruikt in zowel Kubernetes als Azure Machine Learning. 'Implementatie' heeft verschillende betekenis in deze twee contexten. In Kubernetes is een `Deployment` een concrete entiteit, opgegeven met een declaratief YAML-bestand. Een Kubernetes `Deployment` heeft een gedefinieerde levenscyclus en concrete relaties met andere Kubernetes-entiteiten, zoals `Pods` en `ReplicaSets` . In Wat [is Kubernetes?](https://aka.ms/k8slearning)vindt u meer informatie over Kubernetes in documenten en video's.

In Azure Machine Learning wordt 'implementatie' in de algemene zin gebruikt om uw projectresources beschikbaar te maken en op te ruimen. De stappen die Azure Machine Learning onderdeel van de implementatie overwegen, zijn:

1. Zing de bestanden in de projectmap en negeer de bestanden die zijn opgegeven in .amlignore of .gitignore
1. Uw rekencluster omhoog schalen (heeft betrekking op Kubernetes)
1. Het dockerfile bouwen of downloaden naar het reken knooppunt (is gerelateerd aan Kubernetes)
    1. Het systeem berekent een hash van: 
        - De basisafbeelding 
        - Aangepaste Docker-stappen (zie [Een model implementeren met behulp van een aangepaste Docker-basisafbeelding](./how-to-deploy-custom-docker-image.md))
        - De YAML van de Conda-definitie (zie [Create & use software environments in Azure Machine Learning](./how-to-use-environments.md))
    1. Het systeem gebruikt deze hash als sleutel in een opzoekactie van de Azure Container Registry (ACR)
    1. Als deze niet wordt gevonden, zoekt het naar een overeenkomst in de globale ACR
    1. Als deze niet wordt gevonden, bouwt het systeem een nieuwe installatie afbeelding (die in de cache wordt opgeslagen en naar de ACR van de werkruimte wordt pushen)
1. Het ingepakte projectbestand downloaden naar tijdelijke opslag op het reken knooppunt
1. Het projectbestand uit kantelen
1. Het reken knooppunt dat wordt uitgevoerd `python <entry script> <arguments>`
1. Logboeken, modelbestanden en andere bestanden opslaan die zijn geschreven naar `./outputs` het opslagaccount dat is gekoppeld aan de werkruimte
1. Omlaag schalen van rekenkracht, inclusief het verwijderen van tijdelijke opslag (heeft betrekking op Kubernetes)

### <a name="azure-ml-router"></a>Azure ML-router

Het front-endonderdeel (azureml-fe) dat binnenkomende deferentieaanvragen naar geïmplementeerde services routeert, wordt automatisch naar behoefte geschaald. Het schalen van azureml-fe is gebaseerd op het doel en de grootte van het AKS-cluster (aantal knooppunten). Het clusterdoel en de knooppunten worden geconfigureerd wanneer u [een AKS-cluster maakt of koppelt.](how-to-create-attach-kubernetes.md) Er is één azureml-fe-service per cluster, die kan worden uitgevoerd op meerdere pods.

> [!IMPORTANT]
> Wanneer u een cluster gebruikt dat is geconfigureerd __als dev-test,__ wordt de self-scaler **uitgeschakeld.**

Azureml-fe schaalt beide omhoog (verticaal) om meer kernen te gebruiken en (horizontaal) om meer pods te gebruiken. Bij het nemen van de beslissing om omhoog te schalen, wordt de tijd gebruikt die nodig is om binnenkomende deferentieaanvragen te routeeren. Als deze tijd de drempelwaarde overschrijdt, wordt omhoog geschaald. Als de tijd voor het doorverzenden van binnenkomende aanvragen de drempel blijft overschrijden, treedt er uitschalen op.

Wanneer u omlaag en inschaalt, wordt het CPU-gebruik gebruikt. Als aan de drempelwaarde voor HET CPU-gebruik wordt voldaan, wordt de front-end eerst omlaag geschaald. Als het CPU-gebruik de drempelwaarde voor inschalen bereikt, vindt er een inschaalbewerking plaats. Omhoog en uitschalen vindt alleen plaats als er voldoende clusterbronnen beschikbaar zijn.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Informatie over de connectiviteitsvereisten voor het AKS-deductiecluster

Wanneer Azure Machine Learning AKS-cluster maakt of koppelt, wordt het AKS-cluster geïmplementeerd met een van de volgende twee netwerkmodellen:
* Kubenet-netwerken: de netwerkbronnen worden doorgaans gemaakt en geconfigureerd wanneer het AKS-cluster wordt geïmplementeerd.
* Azure Container Networking Interface-netwerk (CNI): het AKS-cluster wordt verbonden met resources en configuraties van het bestaande virtuele netwerk.

Voor de eerste netwerkmodus wordt het netwerk correct gemaakt en geconfigureerd voor Azure Machine Learning Service. Voor de tweede netwerkmodus, omdat het cluster is verbonden met een bestaand virtueel netwerk, met name wanneer aangepaste DNS wordt gebruikt voor een bestaand virtueel netwerk, moet de klant extra aandacht besteden aan de connectiviteitsvereisten voor AKS-deferencingcluster en zorgen voor DNS-resolutie en uitgaande connectiviteit voor AKS-deferencing.

Het volgende diagram legt alle connectiviteitsvereisten voor AKS-deferencing vast. Zwarte pijlen vertegenwoordigen de werkelijke communicatie en blauwe pijlen vertegenwoordigen de domeinnamen die door de klant beheerde DNS moeten worden opgelost.

 ![Connectiviteitsvereisten voor AKS-deferencing](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Algemene vereisten voor DNS-resolutie
DNS-resolutie binnen het bestaande VNET staat onder beheer van de klant. De volgende DNS-vermeldingen moeten kunnen worden opgelost:
* AKS API-server in de vorm \<cluster\> van .hcp. \<region\> . azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* De Azure Container Registry (ARC) van de klant in de vorm van \<ACR name\> .azurecr.io
* Azure Storage account in de vorm \<account\> van .table.core.windows.net \<account\> en .blob.core.windows.net
* (Optioneel) Voor AAD-verificatie: api.azureml.ms
* Domeinnaam van score-eindpunt, automatisch gegenereerd door Azure ML of aangepaste domeinnaam. De automatisch gegenereerde domeinnaam ziet er als: \<leaf-domain-label \+ auto-generated suffix\> . \<region\> cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>Connectiviteitsvereisten in chronologische volgorde: van cluster maken tot modelimplementatie

Tijdens het maken of koppelen van AKS wordt de Azure ML-router (azureml-fe) geïmplementeerd in het AKS-cluster. Als u een Azure ML-router wilt implementeren, moet het AKS-knooppunt het volgende kunnen doen:
* DNS voor AKS API-server oplossen
* DNS voor MCR oplossen om Docker-afbeeldingen voor Azure ML-router te downloaden
* Download afbeeldingen van MCR, waarbij uitgaande connectiviteit is vereist

Direct nadat azureml-fe is geïmplementeerd, wordt geprobeerd te starten. Hiervoor is het volgende vereist:
* DNS voor AKS API-server oplossen
* Een query uitvoeren op de AKS API-server om andere exemplaren van zichzelf te ontdekken (het is een service met meerdere pods)
* Verbinding maken met andere exemplaren van zichzelf

Zodra azureml-fe is gestart, hebt u extra connectiviteit nodig om goed te kunnen werken:
* Verbinding maken met Azure Storage dynamische configuratie te downloaden
* Dns voor AAD-verificatieservers oplossen en api.azureml.ms communiceren wanneer de geïmplementeerde service gebruikmaakt van AAD-verificatie.
* Query's uitvoeren op de AKS API-server om geïmplementeerde modellen te ontdekken
* Communiceren met geïmplementeerde model-POD's

Tijdens de modelimplementatie moet het AKS-knooppunt voor een geslaagde modelimplementatie het volgende kunnen doen: 
* DNS voor ACR van klant oplossen
* Afbeeldingen downloaden van de ACR van de klant
* DNS oplossen voor Azure-BLOBs waar het model wordt opgeslagen
* Modellen downloaden van Azure-BLOBs

Nadat het model is geïmplementeerd en de service is gestart, detecteert azureml-fe het automatisch met behulp van de AKS-API en is het klaar om de aanvraag naar het model te sturen. Het moet kunnen communiceren met model-POD's.
>[!Note]
>Als voor het geïmplementeerde model connectiviteit is vereist (bijvoorbeeld het uitvoeren van query's op een externe database of een andere REST-service, het downloaden van een BLOB, enzovoort), moeten zowel DNS-resolutie als uitgaande communicatie voor deze services worden ingeschakeld.

## <a name="deploy-to-aks"></a>Implementeren naar AKS

Als u een model wilt implementeren Azure Kubernetes Service, maakt u een __implementatieconfiguratie__ die de benodigde rekenbronnen beschrijft. Bijvoorbeeld het aantal kernen en het geheugen. U hebt ook een __deferentieconfiguratie nodig,__ die de omgeving beschrijft die nodig is om het model en de webservice te hosten. Zie How and where to deploy models (Modellen implementeren) voor meer informatie over het maken van de [deference-configuratie.](how-to-deploy-and-where.md)

> [!NOTE]
> Het aantal modellen dat moet worden geïmplementeerd, is beperkt tot 1000 modellen per implementatie (per container).

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

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende opdracht om te implementeren met behulp van de CLI. Vervang `myaks` door de naam van het AKS-rekendoel. Vervang `mymodel:1` door de naam en versie van het geregistreerde model. Vervang `myservice` door de naam om deze service te geven:

```azurecli-interactive
az ml model deploy --ct myaks -m mymodel:1 -n myservice --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Zie de naslaginformatie [over az ml model deploy voor meer](/cli/azure/ml/model#az_ml_model_deploy) informatie.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Zie Implementeren in AKS via de VS Code-extensie voor meer informatie over het gebruik [van VS Code.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> Voor implementatie via VS Code moet het AKS-cluster vooraf worden gemaakt of gekoppeld aan uw werkruimte.

---

### <a name="autoscaling"></a>Automatisch schalen

Het onderdeel dat automatisch schalen voor implementaties van Azure ML-modellen verwerkt, is azureml-fe, een router voor slimme aanvragen. Omdat alle deferentieaanvragen worden door middel van deze aanvraag, beschikt u over de benodigde gegevens om de geïmplementeerde modellen automatisch te schalen.

> [!IMPORTANT]
> * **Schakel Kubernetes Horizontal Pod Autoscaler (HPA) niet in voor modelimplementaties.** Dit zou ertoe leiden dat de twee onderdelen voor automatisch schalen met elkaar concurreren. Azureml-fe is ontworpen voor het automatisch schalen van modellen die zijn geïmplementeerd door Azure ML, waarbij HPA het modelgebruik moet raden of benaderen op basis van algemene metrische gegevens zoals CPU-gebruik of een aangepaste metrische configuratie.
> 
> * **Azureml-fe schaalt het aantal knooppunten in een AKS-cluster** niet, omdat dit kan leiden tot onverwachte kostenverhogingen. In plaats **daarvan wordt het aantal replica's voor het model binnen** de fysieke clustergrenzen geschaald. Als u het aantal knooppunten in het cluster wilt schalen, kunt u het cluster handmatig schalen of de automatische schaalvergroting van [het AKS-cluster configureren.](../aks/cluster-autoscaler.md)

Automatisch schalen kan worden beheerd door `autoscale_target_utilization` , en in te stellen voor de `autoscale_min_replicas` `autoscale_max_replicas` AKS-webservice. In het volgende voorbeeld wordt gedemonstreerd hoe u automatisch schalen inschakelen:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Beslissingen voor omhoog/omlaag schalen zijn gebaseerd op het gebruik van de huidige containerreplica's. Het aantal replica's dat bezet is (een aanvraag verwerken) gedeeld door het totale aantal huidige replica's is het huidige gebruik. Als dit aantal groter is dan `autoscale_target_utilization` , worden er meer replica's gemaakt. Als deze lager is, worden de replica's verminderd. Standaard is het doelgebruik 70%.

Beslissingen voor het toevoegen van replica's zijn gretig en snel (ongeveer 1 seconde). Beslissingen voor het verwijderen van replica's zijn voorzichtig (ongeveer 1 minuut).

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

Zie de naslaginformatie over de `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` [AksWebservice-module](/python/api/azureml-core/azureml.core.webservice.akswebservice) voor meer informatie over het instellen van , en .

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellen implementeren in AKS met beheerde implementatie (preview)

Modelversies op een gecontroleerde manier analyseren en promoveren met behulp van eindpunten. U kunt maximaal zes versies achter één eindpunt implementeren. Eindpunten bieden de volgende mogelijkheden:

* Configureer __het percentage scoring-verkeer dat naar elk eindpunt wordt verzonden.__ Routeer bijvoorbeeld 20% van het verkeer naar eindpunt 'test' en 80% naar 'productie'.

    > [!NOTE]
    > Als u geen rekening houdt met 100% van het verkeer, wordt het resterende percentage gerouteerd naar de __standaardversie__ van het eindpunt. Als u bijvoorbeeld eindpuntversie 'test' configureert om 10% van het verkeer op te halen en 'prod' voor 30%, wordt de resterende 60% verzonden naar de standaardversie van het eindpunt.
    >
    > De eerste eindpuntversie die is gemaakt, wordt automatisch geconfigureerd als de standaardversie. U kunt dit wijzigen door deze in te `is_default=True` stellen bij het maken of bijwerken van een eindpuntversie.
     
* Tag een eindpuntversie als controle __of__ __behandeling.__ De huidige versie van het productie-eindpunt kan bijvoorbeeld de controle zijn, terwijl potentiële nieuwe modellen worden geïmplementeerd als behandelversies. Als er na het evalueren van de prestaties van de behandelversies één beter presteert dan de huidige controle, kan dit worden gepromoveerd naar de nieuwe productie/controle.

    > [!NOTE]
    > U kunt slechts één __besturingselement__ hebben. U kunt meerdere behandelingsmethoden hebben.

U kunt app-inzichten inschakelen om operationele metrische gegevens van eindpunten en geïmplementeerde versies weer te geven.

### <a name="create-an-endpoint"></a>Een eindpunt maken
Wanneer u klaar bent om uw modellen te implementeren, maakt u een scoring-eindpunt en implementeert u uw eerste versie. In het volgende voorbeeld ziet u hoe u het eindpunt implementeert en maakt met behulp van de SDK. De eerste implementatie wordt gedefinieerd als de standaardversie, wat betekent dat niet-opgegeven verkeers percentiel voor alle versies naar de standaardversie gaat.  

> [!TIP]
> In het volgende voorbeeld stelt de configuratie de initiële eindpuntversie in om 20% van het verkeer te verwerken. Omdat dit het eerste eindpunt is, is dit ook de standaardversie. En omdat we geen andere versies hebben voor de overige 80% van het verkeer, wordt het ook doorgeleid naar de standaardinstelling. Totdat andere versies worden geïmplementeerd die een percentage van het verkeer nemen, ontvangt deze in effectief 100% van het verkeer.

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

### <a name="update-and-add-versions-to-an-endpoint"></a>Versies bijwerken en toevoegen aan een eindpunt

Voeg nog een versie toe aan uw eindpunt en configureer het percentiel voor het scoren van verkeer dat naar de versie gaat. Er zijn twee typen versies: een besturingselement en een behandelversie. Er kunnen meerdere behandelversies zijn om te vergelijken met één versie van het besturingselement.

> [!TIP]
> De tweede versie, gemaakt door het volgende codefragment, accepteert 10% van het verkeer. De eerste versie is geconfigureerd voor 20%, dus slechts 30% van het verkeer is geconfigureerd voor specifieke versies. De resterende 70% wordt verzonden naar de eerste eindpuntversie, omdat dit ook de standaardversie is.

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

Werk bestaande versies bij of verwijder ze in een eindpunt. U kunt het standaardtype, het besturingstype en het verkeers percentiel van de versie wijzigen. In het volgende voorbeeld verhoogt de tweede versie het verkeer naar 40% en is nu de standaardinstelling.

> [!TIP]
> Na het volgende codefragment is de tweede versie nu standaard. Deze is nu geconfigureerd voor 40%, terwijl de oorspronkelijke versie nog steeds is geconfigureerd voor 20%. Dit betekent dat 40% van het verkeer niet wordt verantwoord door versieconfiguraties. Het verkeer dat overblijft, wordt doorgeleid naar de tweede versie, omdat dit nu standaard is. Het ontvangt effectief 80% van het verkeer.

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

Bij het implementeren naar Azure Kubernetes Service is __verificatie op basis__ van sleutels standaard ingeschakeld. U kunt ook verificatie __op basis van een token__ inschakelen. Voor verificatie op basis van een token moeten clients een Azure Active Directory-account gebruiken om een verificatie-token aan te vragen, dat wordt gebruikt om aanvragen te doen bij de geïmplementeerde service.

Als __u verificatie__ wilt uitschakelen, stelt u de parameter in bij het maken van de `auth_enabled=False` implementatieconfiguratie. In het volgende voorbeeld wordt verificatie met behulp van de SDK uitgeschakeld:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Zie Consume an Azure Machine Learning model deployed as a web service (Een model gebruiken dat is geïmplementeerd als een webservice) voor meer informatie over het authenticeren van [een clienttoepassing.](how-to-consume-web-service.md)

### <a name="authentication-with-keys"></a>Verificatie met sleutels

Als sleutelverificatie is ingeschakeld, kunt u de methode gebruiken om een primaire en `get_keys` secundaire verificatiesleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel opnieuw wilt maken, gebruikt u [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)

### <a name="authentication-with-tokens"></a>Verificatie met tokens

Als u tokenverificatie wilt inschakelen, stelt u `token_auth_enabled=True` de parameter in bij het maken of bijwerken van een implementatie. In het volgende voorbeeld wordt tokenverificatie met behulp van de SDK mogelijk gemaakt:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Als tokenverificatie is ingeschakeld, kunt u de methode gebruiken om een JWT-token en de verlooptijd van dat `get_token` token op te halen:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> U moet na de tijd van het token een nieuw token `refresh_by` aanvragen.
>
> Microsoft raadt u ten zeerste aan om uw werkruimte Azure Machine Learning maken in dezelfde regio als uw Azure Kubernetes Service cluster. Om te verifiëren met een token, roept de webservice de regio aan waarin uw Azure Machine Learning werkruimte is gemaakt. Als de regio van uw werkruimte niet beschikbaar is, kunt u zelfs geen token voor uw webservice ophalen als uw cluster zich in een andere regio dan uw werkruimte. Dit leidt er in wezen toe dat verificatie op basis van een token niet beschikbaar is totdat de regio van uw werkruimte weer beschikbaar is. Bovendien, hoe groter de afstand tussen de regio van uw cluster en de regio van uw werkruimte, hoe langer het duurt om een token op te halen.
>
> Als u een token wilt ophalen, moet u de Azure Machine Learning SDK of de opdracht [az ml service get-access-token](/cli/azure/ml/service#az_ml_service_get_access_token) gebruiken.


### <a name="vulnerability-scanning"></a>Scannen op beveiligingsproblemen

Azure Security Center biedt geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor verschillende hybride cloudworkloads. U moet toestaan Azure Security Center resources te scannen en de aanbevelingen ervan te volgen. Zie Integratie van [Azure Kubernetes Services met Security Center voor meer informatie.](../security-center/defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Volgende stappen

* [Azure RBAC gebruiken voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)
* [Beveiligde deferencingomgeving met Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Problemen met de implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
