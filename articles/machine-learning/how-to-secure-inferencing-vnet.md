---
title: Deferencing-omgevingen beveiligen met virtuele netwerken
titleSuffix: Azure Machine Learning
description: Gebruik een geïsoleerde Azure Virtual Network om uw Azure Machine Learning te beveiligen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 610ab82bfc4665fbb30aa3d3bc0448fa9338689c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872547"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Een omgeving Azure Machine Learning met virtuele netwerken beveiligen

In dit artikel leert u hoe u de deferencingomgevingen beveiligt met een virtueel netwerk in Azure Machine Learning.

Dit artikel is deel vier van een vijfdelige reeks die u bij het beveiligen van een Azure Machine Learning werkstroom. We raden u ten zeerste aan deel [één: Overzicht van VNet](how-to-network-security-overview.md) door te nemen om eerst inzicht te krijgen in de algehele architectuur. 

Zie de andere artikelen in deze reeks:

[1. VNet-overzicht](how-to-network-security-overview.md)  >  [De werkruimte](how-to-secure-workspace-vnet.md)  >  [3 beveiligen. Beveilig de trainingsomgeving](how-to-secure-training-vnet.md)  >  **4. Beveilig de deferencing-omgeving**  >  [5. Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

In dit artikel leert u hoe u de volgende deferencing-resources in een virtueel netwerk kunt beveiligen:
> [!div class="checklist"]
> - Standaard Azure Kubernetes Service (AKS)-cluster
> - Privé-AKS-cluster
> - AKS-cluster met private link
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Vereisten

+ Lees het artikel [Overzicht van netwerkbeveiliging voor](how-to-network-security-overview.md) meer informatie over veelvoorkomende scenario's voor virtuele netwerken en de algehele architectuur van virtuele netwerken.

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw rekenbronnen.

+ Als u resources wilt implementeren in een virtueel netwerk of subnet, moet uw gebruikersaccount machtigingen hebben voor de volgende acties in op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC):

    - 'Microsoft.Network/virtualNetworks/join/action' op de resource van het virtuele netwerk.
    - 'Microsoft.Network/virtualNetworks/subnet/join/action' op de subnetresource.

    Zie Ingebouwde rollen voor netwerken voor meer informatie over Azure RBAC [met netwerken](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Als u een AKS-cluster in een virtueel netwerk wilt gebruiken, moet aan de volgende netwerkvereisten worden voldaan:

> [!div class="checklist"]
> * Volg de vereisten in [Geavanceerde netwerken configureren in Azure Kubernetes Service (AKS)](../aks/configure-azure-cni.md#prerequisites).
> * Het AKS-exemplaar en het virtuele netwerk moeten zich in dezelfde regio hebben. Als u de Azure Storage-account(s) beveiligt die worden gebruikt door de werkruimte in een virtueel netwerk, moeten deze zich ook in hetzelfde virtuele netwerk als het AKS-exemplaar hebben.

Als u AKS in een virtueel netwerk wilt toevoegen aan uw werkruimte, gebruikt u de volgende stappen:

1. Meld u aan [Azure Machine Learning-studio](https://ml.azure.com/)en selecteer vervolgens uw abonnement en werkruimte.

1. Selecteer __Compute__ aan de linkerkant.

1. Selecteer __Inference clusters__ in het midden en selecteer vervolgens __+__ .

1. Selecteer in __het dialoogvenster Nieuw deferentiecluster__ de optie __Geavanceerd__ onder __Netwerkconfiguratie.__

1. Voer de volgende acties uit om deze rekenresource te configureren voor het gebruik van een virtueel netwerk:

    1. Selecteer in __de vervolgkeuzelijst__ Resourcegroep de resourcegroep die het virtuele netwerk bevat.
    1. Selecteer in __de vervolgkeuzelijst__ Virtueel netwerk het virtuele netwerk dat het subnet bevat.
    1. Selecteer het __subnet__ in de vervolgkeuzelijst Subnet.
    1. Voer in __het vak Kubernetes Service-adresbereik__ het adresbereik van de Kubernetes-service in. Dit adresbereik maakt gebruik van een IP-adresbereik Inter-Domain CIDR-notatie (Classless Inter-Domain Routing) om de IP-adressen te definiëren die beschikbaar zijn voor het cluster. Deze mag niet overlappen met IP-adresbereiken van subnetten (bijvoorbeeld 10.0.0.0/16).
    1. Voer in het vak IP-adres van __Kubernetes DNS-service__ het IP-adres van de Kubernetes DNS-service in. Dit IP-adres wordt toegewezen aan de Kubernetes DNS-service. Deze moet binnen het adresbereik van de Kubernetes-service (bijvoorbeeld 10.0.0.10) zijn.
    1. Voer in __het adresvak Docker Bridge__ het adres van de Docker-brug in. Dit IP-adres wordt toegewezen aan Docker Bridge. Deze mag zich niet in IP-adresbereiken van het subnet of het adresbereik van de Kubernetes-service (bijvoorbeeld 172.17.0.1/16) vinden.

   ![Azure Machine Learning: Machine Learning Compute virtuele netwerkinstellingen](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Wanneer u een model als een webservice implementeert in AKS, wordt er een score-eindpunt gemaakt om de deferencing-aanvragen te verwerken. Zorg ervoor dat voor de NSG-groep die het virtuele netwerk beheert, een inkomende beveiligingsregel is ingeschakeld voor het IP-adres van het score-eindpunt als u deze van buiten het virtuele netwerk wilt aanroepen.

    Bekijk de scoring-URI voor de geïmplementeerde service om het IP-adres van het scoring-eindpunt te vinden. Zie Consume a model deployed as a web service (Een model gebruiken dat is geïmplementeerd als een webservice) voor meer informatie over het weergeven [van de scoring-URI.](how-to-consume-web-service.md#connection-information)

   > [!IMPORTANT]
   > Houd de standaardregels voor uitgaand verkeer voor de NSG. Zie de standaardbeveiligingsregels in Beveiligingsgroepen [voor meer informatie.](../virtual-network/network-security-groups-overview.md#default-security-rules)

   [![Een beveiligingsregel voor binnenkomende gegevens](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > Het IP-adres dat wordt weergegeven in de afbeelding voor het score-eindpunt is anders voor uw implementaties. Hoewel hetzelfde IP-adres wordt gedeeld door alle implementaties naar één AKS-cluster, heeft elk AKS-cluster een ander IP-adres.

U kunt ook de Azure Machine Learning SDK gebruiken om Azure Kubernetes Service toevoegen aan een virtueel netwerk. Als u al een AKS-cluster in een virtueel netwerk hebt, koppelt u het aan de werkruimte zoals beschreven in Implementeren [naar AKS.](how-to-deploy-and-where.md) Met de volgende code maakt u een nieuw AKS-exemplaar in het `default` subnet van een virtueel netwerk met de naam `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Wanneer het aanmaakproces is voltooid, kunt u de deferentie of modelscore uitvoeren op een AKS-cluster achter een virtueel netwerk. Zie Implementeren naar [AKS voor meer informatie.](how-to-deploy-and-where.md)

Zie Azure RBAC gebruiken Role-Based Access Control [Kubernetes-autorisatie](../aks/manage-azure-rbac.md)voor meer informatie over het gebruik van kubernetes.

## <a name="network-contributor-role"></a>Rol netwerkbijdrager

> [!IMPORTANT]
> Als u een AKS-cluster maakt of koppelt door een virtueel netwerk op te geven dat u eerder  hebt gemaakt, moet u de service-principal (SP) of beheerde identiteit voor uw AKS-cluster de rol Netwerkbijdrager verlenen aan de resourcegroep die het virtuele netwerk bevat.
>
> Als u de identiteit wilt toevoegen als inzender voor netwerken, gebruikt u de volgende stappen:

1. Gebruik de volgende Azure CLI-opdrachten om de service-principal of beheerde identiteits-id voor AKS te vinden. Vervang `<aks-cluster-name>` door de naam van het cluster. Vervang `<resource-group-name>` door de naam van de resourcegroep die het _AKS-cluster bevat:_

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Als deze opdracht een waarde van retourneert, `msi` gebruikt u de volgende opdracht om de principal-id voor de beheerde identiteit te identificeren:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Gebruik de volgende opdracht om de id te vinden van de resourcegroep die uw virtuele netwerk bevat. Vervang `<resource-group-name>` door de naam van de resourcegroep die het virtuele netwerk _bevat:_

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Gebruik de volgende opdracht om de service-principal of beheerde identiteit toe te voegen als een netwerkbijdrager. Vervang `<SP-or-managed-identity>` door de id die wordt geretourneerd voor de service-principal of beheerde identiteit. Vervang `<resource-group-id>` door de id die wordt geretourneerd voor de resourcegroep die het virtuele netwerk bevat:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Zie Use internal load balancer with Azure Kubernetes Service (Interne load balancer gebruiken met AKS) voor meer informatie over het gebruik van de [interne load balancer met AKS.](../aks/internal-lb.md)

## <a name="secure-vnet-traffic"></a>VNet-verkeer beveiligen

Er zijn twee manieren om verkeer van en naar het AKS-cluster naar het virtuele netwerk te isoleren:

* __Privé-AKS-cluster:__ bij deze benadering wordt gebruikgemaakt Azure Private Link communicatie met het cluster te beveiligen voor implementatie-/beheerbewerkingen.
* __Interne AKS load balancer:__ met deze methode configureert u het eindpunt voor uw implementaties naar AKS voor het gebruik van een privé-IP-adres in het virtuele netwerk.

> [!WARNING]
> Interne load balancer werkt niet met een AKS-cluster dat gebruikmaakt van kubenet. Als u tegelijkertijd een interne load balancer en een privé-AKS-cluster wilt gebruiken, configureert u uw persoonlijke AKS-cluster met Azure Container Networking Interface (CNI). Zie Configure Azure CNI networking in Azure Kubernetes Service (Netwerk configureren [in Azure Kubernetes Service) voor Azure Kubernetes Service.](../aks/configure-azure-cni.md)

### <a name="private-aks-cluster"></a>Privé-AKS-cluster

Standaard hebben AKS-clusters een besturingsvlak, of API-server, met openbare IP-adressen. U kunt AKS configureren voor het gebruik van een privébesturingsvlak door een privé-AKS-cluster te maken. Zie Een privé-Azure Kubernetes Service [maken voor meer informatie.](../aks/private-clusters.md)

Nadat u het privé-AKS-cluster hebt gemaakt, [koppelt u het cluster](how-to-create-attach-kubernetes.md) aan het virtuele netwerk voor gebruik met Azure Machine Learning.

> [!IMPORTANT]
> Voordat u een AKS-cluster met private link met Azure Machine Learning gebruikt, moet u een ondersteuningsincident openen om deze functionaliteit in te schakelen. Zie Quota beheren en verhogen [voor meer informatie.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

### <a name="internal-aks-load-balancer"></a>Interne AKS-load balancer

Standaard gebruiken AKS-implementaties een [openbare load balancer](../aks/load-balancer-standard.md). In deze sectie leert u hoe u AKS configureert voor het gebruik van een load balancer. Er wordt een interne (of privé) load balancer gebruikt waarbij alleen privé-IP's zijn toegestaan als front-end. Interne load balancers worden gebruikt voor de load balancer van verkeer binnen een virtueel netwerk

Een privé load balancer wordt ingeschakeld door AKS te configureren voor het gebruik van een _interne load balancer_. 

#### <a name="enable-private-load-balancer"></a>Privé-load balancer

> [!IMPORTANT]
> U kunt geen privé-IP inschakelen bij het maken van Azure Kubernetes Service cluster in Azure Machine Learning-studio. U kunt er een maken met een interne load balancer wanneer u de Python SDK of Azure CLI-extensie voor machine learning.

In de volgende voorbeelden wordt gedemonstreerd hoe u een nieuw AKS-cluster met een __privé-IP/interne load balancer__ met behulp van de SDK en CLI:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> Met de CLI kunt u alleen een AKS-cluster met een interne load balancer. Er is geen az ml-opdracht om een bestaand cluster bij te upgraden voor het gebruik van een interne load balancer.

Zie de naslaginformatie [over az ml computetarget create aks voor meer](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_aks) informatie.

---

Wanneer __u een bestaand cluster aan__ uw werkruimte koppelt, moet u wachten tot na de bewerking koppelen om de configuratie van de load balancer. Zie Attach an existing AKS cluster (Een bestaand [AKS-cluster koppelen) voor](how-to-create-attach-kubernetes.md)meer informatie over het koppelen van een cluster.

Nadat u het bestaande cluster hebt gekoppeld, kunt u het cluster bijwerken om een intern load balancer/privé-IP-adres te gebruiken:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Azure Container Instances (ACI) inschakelen

Azure Container Instances dynamisch worden gemaakt bij het implementeren van een model. Als u Azure Machine Learning ACI in het virtuele netwerk wilt maken, moet u __subnetdelegering__ inschakelen voor het subnet dat door de implementatie wordt gebruikt.

> [!WARNING]
> Wanneer u Azure Container Instances in een virtueel netwerk gebruikt, moet het virtuele netwerk het volgende zijn:
> * In dezelfde resourcegroep als uw Azure Machine Learning werkruimte.
> * Als uw werkruimte een __privé-eindpunt heeft,__ moet het virtuele netwerk dat wordt gebruikt voor Azure Container Instances hetzelfde zijn als het netwerk dat wordt gebruikt door het privé-eindpunt van de werkruimte.
>
> Wanneer u Azure Container Instances in het virtuele netwerk gebruikt, kan de Azure Container Registry (ACR) voor uw werkruimte zich niet in het virtuele netwerk.

Als u ACI in een virtueel netwerk wilt gebruiken voor uw werkruimte, gebruikt u de volgende stappen:

1. Als u subnetdelegering in uw virtuele netwerk wilt inschakelen, gebruikt u de informatie in het artikel [Een subnetdelegering toevoegen](../virtual-network/manage-subnet-delegation.md) of verwijderen. U kunt delegatie inschakelen bij het maken van een virtueel netwerk of toevoegen aan een bestaand netwerk.

    > [!IMPORTANT]
    > Gebruik bij het inschakelen van `Microsoft.ContainerInstance/containerGroups` delegering als __de waarde Subnet aan service delegeren.__

2. Implementeer het model met [AciWebservice.deploy_configuration(),](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)gebruik de `vnet_name` parameters en `subnet_name` . Stel deze parameters in op de naam van het virtuele netwerk en het subnet waar u delegering hebt ingeschakeld.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>Uitgaande connectiviteit vanaf het virtuele netwerk beperken

Als u de standaardregels voor uitgaand verkeer niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, moet u toegang tot de Azure Container Registry. Zorg er bijvoorbeeld voor dat uw netwerkbeveiligingsgroepen (NSG) een regel bevatten die toegang toestaat tot de servicetag __AzureContainerRegistry.RegionName,__ waarbij {RegionName} de naam van een Azure-regio is.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is deel vier van een vijfdelige reeks virtuele netwerken. Zie de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 1: Overzicht van virtueel netwerk](how-to-network-security-overview.md)
* [Deel 2: De werkruimtebronnen beveiligen](how-to-secure-workspace-vnet.md)
* [Deel 3: De trainingsomgeving beveiligen](how-to-secure-training-vnet.md)
* [Deel 5: Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

Zie ook het artikel over het gebruik van [aangepaste DNS](how-to-custom-dns.md) voor naamresolutie.