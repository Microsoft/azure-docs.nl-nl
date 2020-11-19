---
title: Azure Kubernetes-service maken en koppelen
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van een nieuw Azure Kubernetes service-cluster via Azure Machine Learning, of het koppelen van een bestaand AKS-cluster aan uw werk ruimte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: 0f2b9476c9b8c0b5164bfbf29d65d260340effe4
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919757"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken en koppelen

Azure Machine Learning kunt getrainde machine learning modellen implementeren in azure Kubernetes service. U moet echter eerst een AKS-cluster (Azure Kubernetes service) __maken__ op basis van uw Azure ml-werk ruimte of een bestaand AKS-cluster __toevoegen__ . Dit artikel bevat informatie over het maken en koppelen van een cluster.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

- Als u van plan bent om een Azure-Virtual Network te gebruiken om de communicatie tussen uw Azure ML-werk ruimte en het AKS-cluster te beveiligen, leest u de [netwerk isolatie tijdens de training &](./how-to-network-security-overview.md) artikel dezicht.

## <a name="limitations"></a>Beperkingen

- Als u een **Standard Load Balancer (SLB)** hebt geïmplementeerd in uw cluster in plaats van een Basic-Load BALANCER (BLB), maakt u een cluster in de AKS-Portal/cli/SDK en **koppelt** u het vervolgens aan de werk ruimte AML.

- Als u een Azure Policy hebt waarmee het maken van open bare IP-adressen wordt beperkt, mislukt het maken van een AKS-cluster. AKS vereist een openbaar IP-adres voor uitgaand [verkeer](../aks/limit-egress-traffic.md). Het artikel uitgangs verkeer bevat ook richt lijnen voor het vergren delen van uitgaand verkeer van het cluster via het open bare IP-adres, met uitzonde ring van een aantal volledig gekwalificeerde domein namen. Er zijn twee manieren om een openbaar IP-adres in te scha kelen:
    - Het cluster kan gebruikmaken van het open bare IP-adres dat standaard is gemaakt met de BLB of SLB, of
    - Het cluster kan worden gemaakt zonder een openbaar IP-adres en vervolgens moet een openbaar IP-adres met een firewall met een door de gebruiker gedefinieerde route worden geconfigureerd. Zie [cluster uitgang aanpassen met een door de gebruiker gedefinieerde route](../aks/egress-outboundtype.md)voor meer informatie.
    
    Het AML-besturings vlak communiceert niet met dit open bare IP-adres. Het spreekt naar het AKS-besturings vlak voor implementaties. 

- Als u een AKS-cluster **koppelt** , waarvoor een [geautoriseerd IP-bereik is ingeschakeld voor toegang tot de API-server](../aks/api-server-authorized-ip-ranges.md), schakelt u de IP-ADRESBEREIKEN van het AML-besturings vlak in voor het AKS-cluster. Het AML-besturings vlak wordt geïmplementeerd in gepaarde regio's en er wordt een afnemende samen Stel op het AKS-cluster geïmplementeerd. Zonder toegang tot de API-server kan het maken van de deinterferentie niet worden geïmplementeerd. Gebruik de [IP-bereiken](https://www.microsoft.com/download/confirmation.aspx?id=56519) voor beide [regio's](../best-practices-availability-paired-regions.md) bij het inschakelen van de IP-bereiken in een AKS-cluster.

    Geautoriseerde IP-bereiken werken alleen met Standard Load Balancer.

- Wanneer u een AKS-cluster **koppelt** , moet het deel uitmaken van hetzelfde Azure-abonnement als uw Azure machine learning-werk ruimte.

- Als u een persoonlijk AKS-cluster wilt gebruiken (met behulp van een persoonlijke Azure-koppeling), moet u eerst het cluster maken en het vervolgens **koppelen** aan de werk ruimte. Zie [een persoonlijk Azure Kubernetes service-cluster maken](../aks/private-clusters.md)voor meer informatie.

- De naam van de compute voor het AKS-cluster moet uniek zijn binnen uw Azure ML-werk ruimte.
    - De naam is vereist en moet tussen de 3 en 24 tekens lang zijn.
    - Geldige tekens zijn onder andere hoofd letters, cijfers en het teken.
    - De naam moet beginnen met een letter.
    - De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest, niet uniek is.
   
 - Als u modellen wilt implementeren op **GPU** -knoop punten of **FPGA** -knoop punten (of een specifieke SKU), moet u een cluster maken met de specifieke SKU. Er is geen ondersteuning voor het maken van een secundaire knooppunt groep in een bestaand cluster en het implementeren van modellen in de secundaire knooppunt groep.
 
- Wanneer u een cluster maakt of koppelt, kunt u selecteren of u het cluster wilt maken voor __dev-test__ of __productie__. Als u een AKS-cluster wilt maken voor __ontwikkeling__, __validatie__ en __testen__ in plaats van productie, stelt u het __cluster doel__ in op __dev-test__. Als u het cluster doel niet opgeeft, wordt er een __productie__ cluster gemaakt. 

    > [!IMPORTANT]
    > Een __dev-test-__ cluster is niet geschikt voor verkeer op productie niveau en kan leiden tot meer tijd. Dev/test-clusters garanderen ook geen fout tolerantie.

- Wanneer u een cluster maakt of koppelt en het cluster wordt gebruikt voor __productie__, moet dit ten minste 12 __virtuele cpu's__ bevatten. Het aantal virtuele Cpu's kan worden berekend door het __aantal knoop punten__ in het cluster te vermenigvuldigen met het __aantal kernen dat__ is opgegeven door de geselecteerde VM-grootte. Als u bijvoorbeeld een VM-grootte van ' Standard_D3_v2 ' gebruikt, die vier virtuele kernen heeft, moet u 3 of groter selecteren als het aantal knoop punten.

    Voor een __dev-test__ cluster worden de opdracht mini maal twee virtuele cpu's.

- De Azure Machine Learning SDK biedt geen ondersteuning voor het schalen van een AKS-cluster. Als u de knoop punten in het cluster wilt schalen, gebruikt u de gebruikers interface voor uw AKS-cluster in de Azure Machine Learning Studio. U kunt alleen het aantal knoop punten wijzigen, niet de VM-grootte van het cluster. Raadpleeg de volgende artikelen voor meer informatie over het schalen van de knoop punten in een AKS-cluster:

    - [Het aantal knoop punten in een AKS-cluster hand matig schalen](../aks/scale-cluster.md)
    - [Automatische cluster schaal instellen in AKS](../aks/cluster-autoscaler.md)

## <a name="azure-kubernetes-service-version"></a>Versie van de Azure Kubernetes-service

Met de Azure Kubernetes-service kunt u een cluster maken met behulp van verschillende Kubernetes-versies. Zie [ondersteunde Kubernetes-versies in azure Kubernetes service](../aks/supported-kubernetes-versions.md)voor meer informatie over de beschik bare versies.

Wanneer u een Azure Kubernetes-service cluster **maakt** op basis van een van de volgende methoden, *hebt u geen keuze in de versie* van het cluster dat is gemaakt:

* Azure Machine Learning Studio of de sectie Azure Machine Learning van de Azure Portal.
* Machine Learning-extensie voor Azure CLI.
* Azure Machine Learning SDK.

Deze methoden voor het maken van een AKS-cluster gebruiken de __standaard__ versie van het cluster. *De standaard versie verandert in een periode* als nieuwe Kubernetes-versies beschikbaar zijn.

Wanneer u een bestaand AKS-cluster **koppelt** , worden alle momenteel ondersteunde AKS-versies ondersteund.

> [!NOTE]
> Er zijn mogelijk edge-cases waarin u een ouder cluster hebt dat niet meer wordt ondersteund. In dit geval retourneert de koppelings bewerking een fout en wordt de versie vermeld die momenteel wordt ondersteund.
>
> U kunt **Preview** -versies toevoegen. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Ondersteuning voor het gebruik van Preview-versies kan beperkt zijn. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

### <a name="available-and-default-versions"></a>Beschik bare en standaard versies

Gebruik de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) [-opdracht AZ AKS Get-verse](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions)om de beschik bare en standaard AKS-versies te vinden. De volgende opdracht retourneert bijvoorbeeld de beschik bare versies in de regio vs-West:

```azurecli-interactive
az aks get-versions -l westus -o table
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende tekst:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Als u de standaard versie wilt vinden die wordt gebruikt bij **het maken** van een cluster via Azure machine learning, kunt u de `--query` para meter gebruiken om de standaard versie te selecteren:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende tekst:

```text
Result
--------
1.16.13
```

Als u **de beschik bare versies programmatisch wilt controleren**, gebruikt u de rest API van de [Container Service-client lijst](/rest/api/container-service/container%20service%20client/listorchestrators) . Als u de beschik bare versies wilt vinden, bekijkt u de vermeldingen in `orchestratorType` `Kubernetes` . De bijbehorende `orchestrationVersion` vermeldingen bevatten de beschik bare versies die aan uw werk ruimte kunnen worden **gekoppeld** .

Als u de standaard versie wilt vinden die wordt gebruikt bij **het maken** van een cluster via Azure machine learning, zoekt u de vermelding waar `orchestratorType` `Kubernetes` en `default` is `true` . De gekoppelde `orchestratorVersion` waarde is de standaard versie. In het volgende JSON-fragment ziet u een voorbeeld vermelding:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Een nieuw AKS-cluster maken

**Geschatte tijd**: ongeveer 10 minuten.

Het maken of koppelen van een AKS-cluster is een eenmalig proces voor uw werk ruimte. U kunt dit cluster hergebruiken voor meerdere implementaties. Als u het cluster of de resource groep verwijdert die het bevat, moet u de volgende keer dat u moet implementeren een nieuw cluster maken. Er kunnen meerdere AKS-clusters aan uw werk ruimte zijn gekoppeld.

In het volgende voor beeld ziet u hoe u een nieuw AKS-cluster maakt met behulp van de SDK en CLI:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Zie voor meer informatie de referentie [AZ ml computetarget Create AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zie [Compute-doelen maken in azure machine learning Studio](how-to-create-attach-compute-studio.md#inference-clusters)voor meer informatie over het maken van een AKS-cluster in de portal.

---

## <a name="attach-an-existing-aks-cluster"></a>Een bestaand AKS-cluster koppelen

**Geschatte tijd:** Ongeveer 5 minuten.

Als u al een AKS-cluster in uw Azure-abonnement hebt en versie 1,17 of lager is, kunt u het gebruiken om uw installatie kopie te implementeren.

> [!TIP]
> Het bestaande AKS-cluster kan zich in een andere Azure-regio bevinden dan uw Azure Machine Learning-werk ruimte.


> [!WARNING]
> Maak vanuit uw werk ruimte niet meerdere gelijktijdige bijlagen op hetzelfde AKS-cluster. U kunt bijvoorbeeld een AKS-cluster koppelen aan een werk ruimte met behulp van twee verschillende namen. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).
>
> Als u een AKS-cluster opnieuw wilt koppelen, bijvoorbeeld om TLS of een andere cluster configuratie-instelling te wijzigen, moet u eerst de bestaande bijlage verwijderen met behulp van [AksCompute. Detach ()](/python/api/azureml-core/azureml.core.compute.akscompute?preserve-view=true&view=azure-ml-py#detach--).

Raadpleeg de volgende artikelen voor meer informatie over het maken van een AKS-cluster met behulp van de Azure CLI of portal:

* [Een AKS-cluster maken (CLI)](/cli/azure/aks?bc=%252fazure%252fbread%252ftoc.json&preserve-view=true&toc=%252fazure%252faks%252fTOC.json&view=azure-cli-latest#az-aks-create)
* [Een AKS-cluster maken (Portal)](../aks/kubernetes-walkthrough-portal.md?preserve-view=true&view=azure-cli-latest)
* [Een AKS-cluster maken (ARM-sjabloon in azure Quick Start-sjablonen)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

In het volgende voor beeld ziet u hoe u een bestaand AKS-cluster koppelt aan uw werk ruimte:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute. attach](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een bestaand cluster wilt koppelen met behulp van de CLI, moet u de bron-ID van het bestaande cluster ophalen. Gebruik de volgende opdracht om deze waarde op te halen. Vervang door `myexistingcluster` de naam van uw AKS-cluster. Vervang door `myresourcegroup` de resource groep die het cluster bevat:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Gebruik de volgende opdracht om het bestaande cluster aan uw werk ruimte te koppelen. Vervang door de `aksresourceid` waarde die door de vorige opdracht is geretourneerd. Vervang door `myresourcegroup` de resource groep die uw werk ruimte bevat. Vervang door `myworkspace` de naam van uw werk ruimte.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Zie voor meer informatie de referentie [AZ ml computetarget attach AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zie [Compute-doelen maken in azure machine learning Studio](how-to-create-attach-compute-studio.md#inference-clusters)voor meer informatie over het koppelen van een AKS-cluster in de portal.

---

## <a name="detach-an-aks-cluster"></a>Een AKS-cluster ontkoppelen

Gebruik een van de volgende methoden om een cluster los te koppelen van uw werk ruimte:

> [!WARNING]
> Als u de Azure Machine Learning Studio, SDK of de Azure CLI-extensie gebruikt voor machine learning om een AKS-cluster los te koppelen, **wordt het AKS-cluster niet verwijderd**. Zie [de Azure CLI gebruiken met AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster)voor het verwijderen van het cluster.

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende opdracht om het bestaande cluster los te koppelen aan uw werk ruimte. Vervang door `myaks` de naam die het AKS-cluster aan uw werk ruimte is gekoppeld. Vervang door `myresourcegroup` de resource groep die uw werk ruimte bevat. Vervang door `myworkspace` de naam van uw werk ruimte.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in Azure Machine Learning Studio __Compute__, __clusters__ afstellen en het cluster dat u wilt verwijderen. Gebruik de koppeling __loskoppelen__ om het cluster los te koppelen.

## <a name="next-steps"></a>Volgende stappen

* [Azure RBAC gebruiken voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)
* [Hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md)
* [Een model implementeren in een Azure Kubernetes service-cluster](how-to-deploy-azure-kubernetes-service.md)