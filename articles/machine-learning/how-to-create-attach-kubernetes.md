---
title: Een Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Informatie over het maken van een Azure Kubernetes Service cluster via Azure Machine Learning of het koppelen van een bestaand AKS-cluster aan uw werkruimte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 04/08/2021
ms.openlocfilehash: 1c9434d137114560b5585b081961497412dfbf69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770251"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Een cluster met Azure Kubernetes Service maken en koppelen

Azure Machine Learning kunt getrainde modellen machine learning implementeren in Azure Kubernetes Service. U moet echter  eerst een AKS-cluster (Azure Kubernetes Service) maken vanuit uw  Azure ML-werkruimte of een bestaand AKS-cluster koppelen. Dit artikel bevat informatie over het maken en koppelen van een cluster.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)

- De [Azure CLI-extensie voor Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)of de [Azure Machine Learning Visual Studio Code-extensie](tutorial-setup-vscode-extension.md).

- Als u van plan bent om een Azure-Virtual Network te gebruiken om de communicatie tussen uw Azure ML-werkruimte en het AKS-cluster te beveiligen, leest u het artikel Netwerkisolatie tijdens & [training.](./how-to-network-security-overview.md)

## <a name="limitations"></a>Beperkingen

- Als u een **Standard Load Balancer (SLB)** in uw cluster wilt hebben geïmplementeerd in plaats van een Basic Load Balancer(BLB), maakt u  een cluster in de AKS-portal/CLI/SDK en koppelt u dit vervolgens aan de AML-werkruimte.

- Als u een Azure Policy het maken van openbare IP-adressen beperkt, mislukt het maken van een AKS-cluster. Voor AKS is een openbaar IP-adres vereist [voor het verkeer voorgressie.](../aks/limit-egress-traffic.md) Het artikel voor het verkeer voor wegverkeer bevat ook richtlijnen voor het vergrendelen van het verkeer van het cluster via het openbare IP-adres, met uitzondering van enkele volledig gekwalificeerde domeinnamen. Er zijn twee manieren om een openbaar IP-adres in te stellen:
    - Het cluster kan het openbare IP gebruiken dat standaard is gemaakt met de BLB of SLB, of
    - Het cluster kan worden gemaakt zonder een openbaar IP-adres en vervolgens wordt een openbaar IP-adres geconfigureerd met een firewall met een door de gebruiker gedefinieerde route. Zie Het clusteregress aanpassen met een door de [gebruiker gedefinieerde route voor meer informatie.](../aks/egress-outboundtype.md)
    
    Het AML-besturingsvlak spreekt niet met dit openbare IP-adres. Er wordt gesproken met het AKS-besturingsvlak voor implementaties. 

- Als u **een** AKS-cluster koppelt waarvoor een geautoriseerd IP-bereik is ingeschakeld voor toegang tot de [API-server,](../aks/api-server-authorized-ip-ranges.md)moet u de IP-bereiken van het AML-besturingsvlak inschakelen voor het AKS-cluster. Het AML-besturingsvlak wordt geïmplementeerd in gekoppelde regio's en implementeert de deferentiepods op het AKS-cluster. Zonder toegang tot de API-server kunnen de deference-pods niet worden geïmplementeerd. Gebruik de [IP-adresbereiken](https://www.microsoft.com/download/confirmation.aspx?id=56519) voor beide [gekoppelde regio's](../best-practices-availability-paired-regions.md) bij het inschakelen van de IP-adresbereiken in een AKS-cluster.

    Geautoriseerde IP-adresbereiken werken alleen met Standard Load Balancer.

- Wanneer **u een** AKS-cluster koppelt, moet het zich in hetzelfde Azure-abonnement als uw Azure Machine Learning-werkruimte.

- Als u een privé-AKS-cluster wilt gebruiken (met Azure Private Link), moet u eerst het cluster maken en het vervolgens **koppelen** aan de werkruimte. Zie Een privé-Azure Kubernetes Service [maken voor meer informatie.](../aks/private-clusters.md)

- De rekennaam voor het AKS-cluster MOET uniek zijn binnen uw Azure ML-werkruimte. Het kan letters, cijfers en streepjes bevatten. Deze moet beginnen met een letter, eindigen met een letter of cijfer en tussen de 3 en 24 tekens lang zijn.
 
 - Als u modellen wilt  implementeren op GPU-knooppunten of **FPGA-knooppunten** (of een specifieke SKU), moet u een cluster maken met de specifieke SKU. Er is geen ondersteuning voor het maken van een secundaire knooppuntgroep in een bestaand cluster en het implementeren van modellen in de secundaire knooppuntgroep.
 
- Wanneer u een cluster maakt of koppelt, kunt u selecteren of u het cluster wilt maken __voor dev-test__ of __productie.__ Als u een AKS-cluster wilt maken  voor __ontwikkeling,__ validatie en testen in plaats van productie, stelt u het __clusterdoel__ in __op dev-test.__ Als u het clusterdoel niet opgeeft, wordt er __een productiecluster__ gemaakt. 

    > [!IMPORTANT]
    > Een __dev-testcluster__ is niet geschikt voor verkeer op productieniveau en kan de deferentietijden verhogen. Dev/test-clusters bieden ook geen garantie voor fouttolerantie.

- Als het cluster wordt gebruikt voor productie bij het maken of koppelen van een __cluster,__ moet het ten minste 12 virtuele __CPU's bevatten.__ Het aantal virtuele CPU's kan worden berekend door het aantal  knooppunten __in__ het cluster te vermenigvuldigen met het aantal kernen dat wordt geleverd door de geselecteerde VM-grootte. Als u bijvoorbeeld een VM-grootte van 'Standard_D3_v2' gebruikt, die 4 virtuele kernen heeft, moet u 3 of hoger selecteren als het aantal knooppunten.

    Voor een __dev-test-cluster__ stellen we ten minste twee virtuele CPU's op.

- De Azure Machine Learning SDK biedt geen ondersteuning voor het schalen van een AKS-cluster. Als u de knooppunten in het cluster wilt schalen, gebruikt u de gebruikersinterface voor uw AKS-cluster in Azure Machine Learning-studio. U kunt alleen het aantal knooppunt wijzigen, niet de VM-grootte van het cluster. Zie de volgende artikelen voor meer informatie over het schalen van de knooppunten in een AKS-cluster:

    - [Het aantal knooppunt in een AKS-cluster handmatig schalen](../aks/scale-cluster.md)
    - [Automatische schaalset van clusters instellen in AKS](../aks/cluster-autoscaler.md)

- __Werk het cluster niet rechtstreeks bij met behulp van een YAML-configuratie.__ Hoewel Azure Kubernetes Services updates via YAML-configuratie ondersteunt, Azure Machine Learning implementaties uw wijzigingen overschrijven. De enige twee YAML-velden die niet worden overschreven, zijn __aanvraaglimieten__ en __CPU en geheugen.__

- Het maken van een AKS-cluster Azure Machine Learning-studio gebruikersinterface, SDK of CLI-extensie is __niet__ idempotent. Als u de resource opnieuw probeert te maken, t resulteert dit in een foutmelding dat er al een cluster met dezelfde naam bestaat.
    
    - Het gebruik van Azure Resource Manager sjabloon en de resource [Microsoft.MachineLearningServices/workspaces/computes](/azure/templates/microsoft.machinelearningservices/2019-11-01/workspaces/computes) om een AKS-cluster te maken, is ook __niet__ idempotent. Als u de sjabloon opnieuw probeert te gebruiken om een bestaande resource bij te werken, ontvangt u dezelfde foutmelding.

## <a name="azure-kubernetes-service-version"></a>Azure Kubernetes Service-versie

Azure Kubernetes Service kunt u een cluster maken met behulp van verschillende Kubernetes-versies. Zie Ondersteunde [Kubernetes-versies in](../aks/supported-kubernetes-versions.md)Azure Kubernetes Service voor meer informatie over beschikbare Azure Kubernetes Service.

Wanneer **u** een Azure Kubernetes Service cluster maakt met behulp van een van de volgende methoden, hebt u geen keuze in de *versie* van het cluster dat wordt gemaakt:

* Azure Machine Learning-studio of de sectie Azure Machine Learning van de Azure Portal.
* Machine Learning-extensie voor Azure CLI.
* Azure Machine Learning SDK.

Deze methoden voor het maken van een AKS-cluster maken gebruik __van de standaardversie__ van het cluster. *De standaardversie wordt na een periode gewijzigd* naarmate er nieuwe Kubernetes-versies beschikbaar komen.

Bij **het koppelen van** een bestaand AKS-cluster ondersteunen we alle momenteel ondersteunde AKS-versies.

> [!NOTE]
> Er zijn mogelijk randgevallen waarbij u een ouder cluster hebt dat niet meer wordt ondersteund. In dit geval retourneert de attach-bewerking een fout en worden de momenteel ondersteunde versies weergegeven.
>
> U kunt **preview-versies** koppelen. Preview-functionaliteit wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Ondersteuning voor het gebruik van preview-versies is mogelijk beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

### <a name="available-and-default-versions"></a>Beschikbare en standaardversies

Gebruik de [Azure CLI-opdracht](/cli/azure/install-azure-cli) [az aks get-versions](/cli/azure/aks#az_aks_get_versions)om de beschikbare en standaard AKS-versies te vinden. De volgende opdracht retourneert bijvoorbeeld de versies die beschikbaar zijn in de regio VS - west:

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

Als u de standaardversie wilt vinden die wordt gebruikt bij **het** maken van een cluster via Azure Machine Learning, kunt u de parameter gebruiken om `--query` de standaardversie te selecteren:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende tekst:

```text
Result
--------
1.16.13
```

Als u de beschikbare versies **programmatisch** wilt controleren, gebruikt u de containerserviceclient - lijst [orchestrators](/rest/api/container-service/container%20service%20client/listorchestrators) REST API. Als u de beschikbare versies wilt vinden, bekijkt u de vermeldingen waarbij `orchestratorType` `Kubernetes` is. De `orchestrationVersion` bijbehorende vermeldingen bevatten de beschikbare versies die aan **uw werkruimte** kunnen worden gekoppeld.

Als u de standaardversie wilt vinden die wordt gebruikt bij **het** maken van een cluster via Azure Machine Learning, gaat u naar de vermelding waarbij is `orchestratorType` en `Kubernetes` `default` `true` is. De `orchestratorVersion` bijbehorende waarde is de standaardversie. In het volgende JSON-fragment ziet u een voorbeeld van een vermelding:

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

**Geschatte tijd:** ongeveer 10 minuten.

Het maken of koppelen van een AKS-cluster is een een time-proces voor uw werkruimte. U kunt dit cluster opnieuw gebruiken voor meerdere implementaties. Als u het cluster of de resourcegroep die het bevat verwijdert, moet u de volgende keer dat u wilt implementeren een nieuw cluster maken. U kunt meerdere AKS-clusters aan uw werkruimte koppelen.

In het volgende voorbeeld wordt gedemonstreerd hoe u een nieuw AKS-cluster maakt met behulp van de SDK en CLI:

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

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Zie de naslaginformatie [over az ml computetarget create aks voor meer](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) informatie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zie Rekendoelen maken in Azure Machine Learning-studio voor meer informatie over het maken van een [AKS-cluster in Azure Machine Learning-studio.](how-to-create-attach-compute-studio.md#inference-clusters)

---

## <a name="attach-an-existing-aks-cluster"></a>Een bestaand AKS-cluster koppelen

**Geschatte tijd:** Ongeveer 5 minuten.

Als u al een AKS-cluster in uw Azure-abonnement hebt, kunt u het gebruiken met uw werkruimte.

> [!TIP]
> Het bestaande AKS-cluster kan zich in een andere Azure-regio dan uw Azure Machine Learning-werkruimte.


> [!WARNING]
> Maak niet meerdere gelijktijdige bijlagen aan hetzelfde AKS-cluster vanuit uw werkruimte. U kunt bijvoorbeeld één AKS-cluster koppelen aan een werkruimte met twee verschillende namen. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage(en).
>
> Als u een AKS-cluster opnieuw wilt koppelen, bijvoorbeeld om TLS of een andere clusterconfiguratie-instelling te wijzigen, moet u eerst de bestaande bijlage verwijderen met behulp van [AksCompute.detach()](/python/api/azureml-core/azureml.core.compute.akscompute#detach--).

Zie de volgende artikelen voor meer informatie over het maken van een AKS-cluster met behulp van de Azure CLI of portal:

* [Een AKS-cluster maken (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az_aks_create)
* [Een AKS-cluster maken (portal)](../aks/kubernetes-walkthrough-portal.md)
* [Een AKS-cluster maken (ARM-sjabloon in Azure-snelstartsjablonen)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

In het volgende voorbeeld ziet u hoe u een bestaand AKS-cluster aan uw werkruimte koppelt:

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

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een bestaand cluster wilt koppelen met behulp van de CLI, moet u de resource-id van het bestaande cluster op halen. Gebruik de volgende opdracht om deze waarde op te halen. Vervang `myexistingcluster` door de naam van uw AKS-cluster. Vervang `myresourcegroup` door de resourcegroep die het cluster bevat:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Deze opdracht retourneert een waarde die vergelijkbaar is met de volgende tekst:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Gebruik de volgende opdracht om het bestaande cluster aan uw werkruimte te koppelen. Vervang `aksresourceid` door de waarde die wordt geretourneerd door de vorige opdracht. Vervang `myresourcegroup` door de resourcegroep die uw werkruimte bevat. Vervang `myworkspace` door de naam van uw werkruimte.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Zie de naslaginformatie [over az ml computetarget attach aks voor meer](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks) informatie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zie Rekendoelen maken in Azure Machine Learning-studio voor meer informatie over het koppelen van een [AKS-cluster in Azure Machine Learning-studio.](how-to-create-attach-compute-studio.md#inference-clusters)

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Een AKS-cluster maken of koppelen met TLS-beëindiging
Wanneer u [een AKS-cluster](how-to-create-attach-kubernetes.md)maakt of koppelt, kunt u TLS-beëindiging inschakelen met **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** en **[AksCompute.attach_configuration()-configuratieobjecten.](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** Beide methoden retourneren een configuratieobject met een **enable_ssl-methode** en u kunt de **enable_ssl** gebruiken om TLS in teschakelen.

In het volgende voorbeeld ziet u hoe u TLS-beëindiging kunt inschakelen met het automatisch genereren en configureren van TLS-certificaten met behulp van een Microsoft-certificaat.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
In het volgende voorbeeld ziet u hoe u TLS-beëindiging kunt inschakelen met een aangepast certificaat en een aangepaste domeinnaam. Met aangepast domein en certificaat moet u uw DNS-record bijwerken om te wijzen naar het IP-adres van het score-eindpunt. Zie [Uw DNS bijwerken](how-to-secure-web-service.md#update-your-dns)

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> Zie TLS gebruiken om een webservice te beveiligen via een Azure Machine Learning voor meer informatie over het beveiligen [van modelimplementatie in een AKS-cluster Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Een AKS-cluster maken of koppelen voor het gebruik van interne Load Balancer privé-IP
Wanneer u een AKS-cluster maakt of koppelt, kunt u het cluster configureren voor het gebruik van een interne Load Balancer. Met een interne Load Balancer gebruiken score-eindpunten voor uw implementaties naar AKS een privé-IP-adres in het virtuele netwerk. De volgende codefragmenten laten zien hoe u een interne Load Balancer voor een AKS-cluster configureert.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning biedt geen ondersteuning voor TLS-beëindiging met interne Load Balancer. Interne Load Balancer een privé-IP-adres heeft en dat privé-IP-adres kan zich in een ander netwerk en het certificaat kunnen opnieuw worden gebruikt. 

>[!NOTE]
> Zie Secure an Azure Machine Learning Inferencing Environment (Een omgeving voor de deferencing beveiligen) voor meer informatie over het beveiligen van de [deferencingomgeving](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>Een AKS-cluster loskoppelen

Als u een cluster wilt loskoppelen van uw werkruimte, gebruikt u een van de volgende methoden:

> [!WARNING]
> Als u de Azure Machine Learning-studio, SDK of de Azure CLI-extensie voor machine learning gebruikt om een AKS-cluster los tekoppelen, wordt het **AKS-cluster niet verwijderd.** Zie De Azure CLI gebruiken met AKS om het cluster [te verwijderen.](../aks/kubernetes-walkthrough.md#delete-the-cluster)

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u het bestaande cluster wilt loskoppelen van uw werkruimte, gebruikt u de volgende opdracht. Vervang `myaks` door de naam die het AKS-cluster als aan uw werkruimte is gekoppeld. Vervang `myresourcegroup` door de resourcegroep die uw werkruimte bevat. Vervang `myworkspace` door de naam van uw werkruimte.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer Azure Machine Learning-studio __Compute,__ __Deference-clusters__ en het cluster dat u wilt verwijderen. Gebruik de __koppeling Loskoppelen__ om het cluster los te koppelen.

---

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="update-the-cluster"></a>Het cluster bijwerken

Updates voor Azure Machine Learning onderdelen die zijn geïnstalleerd in Azure Kubernetes Service cluster, moeten handmatig worden toegepast. 

U kunt deze updates toepassen door het cluster los tekoppelen van Azure Machine Learning werkruimte en het cluster vervolgens opnieuw aan de werkruimte toe te passen. Als TLS is ingeschakeld in het cluster, moet u het TLS/SSL-certificaat en de persoonlijke sleutel leveren bij het opnieuw in deattaching van het cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Als u het TLS/SSL-certificaat en de persoonlijke sleutel niet meer hebt of als u een certificaat gebruikt dat wordt gegenereerd door Azure Machine Learning, kunt u de bestanden ophalen voordat u het cluster loskoppelt door verbinding te maken met het cluster met behulp van en het geheim op te `kubectl` `azuremlfessl` halen.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes slaat de geheimen op in base-64-gecodeerde indeling. U moet base-64 decoderen van de onderdelen en van de geheimen voordat u `cert.pem` `key.pem` ze aan `attach_config.enable_ssl` levert. 

### <a name="webservice-failures"></a>Webservicefouten

Veel webservicefouten in AKS kunnen worden bespord door verbinding te maken met het cluster met behulp van `kubectl` . U kunt de voor `kubeconfig.json` een AKS-cluster op halen door uit te

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Volgende stappen

* [Azure RBAC gebruiken voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)
* [Hoe en waar u een model implementeert](how-to-deploy-and-where.md)
* [Een model implementeren in een Azure Kubernetes Service cluster](how-to-deploy-azure-kubernetes-service.md)