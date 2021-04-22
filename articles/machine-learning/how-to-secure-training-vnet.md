---
title: Trainingsomgevingen beveiligen met virtuele netwerken
titleSuffix: Azure Machine Learning
description: Gebruik een geïsoleerde Azure-Virtual Network om uw Azure Machine Learning te beveiligen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 4b3692884da921eeabcafc5a72419278af2d5440
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888652"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Een Azure Machine Learning-trainingsomgeving beveiligen met virtuele netwerken

In dit artikel leert u hoe u trainingsomgevingen beveiligt met een virtueel netwerk in Azure Machine Learning.

Dit artikel is deel drie van een vijfdelige reeks die u door het beveiligen van een werkstroom Azure Machine Learning helpen. We raden u ten zeerste aan deel [één: VNet-overzicht](how-to-network-security-overview.md) te lezen om eerst inzicht te krijgen in de algehele architectuur. 

Zie de andere artikelen in deze reeks:

[1. VNet-overzicht](how-to-network-security-overview.md)  >  [2. Beveilig de werkruimte](how-to-secure-workspace-vnet.md)  >  **3. Beveilig de trainingsomgeving**  >  [4. Beveilig de deferencingomgeving](how-to-secure-inferencing-vnet.md)   >  [5. Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

In dit artikel leert u hoe u de volgende trainingsrekenbronnen in een virtueel netwerk kunt beveiligen:
> [!div class="checklist"]
> - Azure Machine Learning-rekenclusters
> - Azure Machine Learning-rekeninstantie
> - Azure Databricks
> - Virtuele machine
> - HDInsight-cluster

## <a name="prerequisites"></a>Vereisten

+ Lees het artikel [Overzicht van netwerkbeveiliging](how-to-network-security-overview.md) voor meer informatie over veelvoorkomende scenario's voor virtuele netwerken en de algehele architectuur van virtuele netwerken.

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw rekenbronnen.

+ Als u resources wilt implementeren in een virtueel netwerk of subnet, moet uw gebruikersaccount machtigingen hebben voor de volgende acties in op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC):

    - 'Microsoft.Network/virtualNetworks/*/read' op de resource van het virtuele netwerk.
    - 'Microsoft.Network/virtualNetworks/subnet/join/action' op de subnetresource.

    Zie Ingebouwde netwerkrollen voor meer informatie over Azure RBAC [met netwerken](../role-based-access-control/built-in-roles.md#networking)


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Rekenclusters & exemplaren 

Als u een beheerd [Azure Machine Learning __of__](concept-compute-target.md#azure-machine-learning-compute-managed) een Azure Machine Learning [compute-instantie __in__](concept-compute-instance.md) een virtueel netwerk wilt gebruiken, moet aan de volgende netwerkvereisten worden voldaan:

> [!div class="checklist"]
> * Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio als de Azure Machine Learning-werkruimte.
> * Het subnet dat is opgegeven voor het reken-exemplaar of cluster moet voldoende niet-toegewezen IP-adressen hebben om het aantal VM's te kunnen gebruiken dat is gericht. Als het subnet onvoldoende niet-toegewezen IP-adressen heeft, wordt een rekencluster gedeeltelijk toegewezen.
> * Controleer of uw beveiligingsbeleid of vergrendelingen voor het abonnement of de resourcegroep van het virtuele netwerk de machtigingen voor het beheren van het virtuele netwerk beperken. Als u van plan bent het virtuele netwerk te beveiligen door verkeer te beperken, laat u sommige poorten open voor de rekenservice. Zie de sectie Vereiste [poorten voor meer](#mlcports) informatie.
> * Als u meerdere reken-exemplaren of clusters in één virtueel netwerk wilt zetten, moet u mogelijk een quotumverhoging aanvragen voor een of meer van uw resources.
> * Als de Azure Storage-account(s) voor de werkruimte ook zijn beveiligd in een virtueel netwerk, moeten ze zich in hetzelfde virtuele netwerk en subnet als het Azure Machine Learning reken-exemplaar of cluster. Configureer de firewallinstellingen van uw opslag om communicatie met het virtuele netwerk en het subnet compute toe te staan. Houd er rekening mee dat het selectievakje 'Vertrouwde Microsoft-services toegang tot dit account toestaan' niet voldoende is om communicatie vanuit rekenkracht toe te staan.
> * Zorg ervoor dat websockockecommunicatie niet is uitgeschakeld om de Jupyter-functionaliteit van het reken exemplaar te laten werken. Zorg ervoor dat uw netwerk websocket-verbindingen met *.instances.azureml.net en *.instances.azureml.ms. 
> * Wanneer het reken exemplaar wordt geïmplementeerd in een private link-werkruimte, is deze alleen toegankelijk vanuit een virtueel netwerk. Als u een aangepast DNS- of hosts-bestand gebruikt, voegt u een vermelding toe voor `<instance-name>.<region>.instances.azureml.ms` met een privé-IP-adres van het privé-eindpunt van de werkruimte. Zie het artikel over aangepaste [DNS voor meer](./how-to-custom-dns.md) informatie.
> * Het subnet dat wordt gebruikt om het rekencluster/exemplaar te implementeren, mag niet worden gedelegeerd aan een andere service, zoals ACI
> * Beleid voor service-eindpunten voor virtuele netwerken werkt niet voor opslagaccounts voor rekencluster/exemplaarsysteem

    
> [!TIP]
> De Machine Learning reken-instantie of het cluster wijst automatisch extra netwerkresources toe in de __resourcegroep die het virtuele netwerk bevat.__ Voor elk reken-exemplaar of cluster wijst de service de volgende resources toe:
> 
> * Eén netwerkbeveiligingsgroep
> * Eén openbaar IP-adres. Als u een Azure-beleid hebt dat het maken van openbare IP-adressen verbiedt, mislukt de implementatie van cluster/exemplaren
> * Eén load balancer
> 
> In het geval van clusters worden deze resources steeds verwijderd (en opnieuw gemaakt) wanneer het cluster omlaag wordt geschaald naar 0 knooppunten, maar in een geval worden de resources aan gehouden totdat het exemplaar volledig is verwijderd (met stoppen worden de resources niet verwijderd). 
> De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Als de resourcegroep van het virtuele netwerk is vergrendeld, mislukt het verwijderen van het rekencluster/exemplaar. Load balancer kan pas worden verwijderd als het rekencluster/exemplaar is verwijderd. Zorg er ook voor dat er geen Azure-beleid is dat het maken van netwerkbeveiligingsgroepen verbiedt.


### <a name="required-ports"></a><a id="mlcports"></a> Vereiste poorten

Als u van plan bent het virtuele netwerk te beveiligen door netwerkverkeer van/naar het openbare internet te beperken, moet u inkomende communicatie van de Azure Batch service toestaan.

De Batch-service voegt netwerkbeveiligingsgroepen (NSG's) toe op het niveau van netwerkinterfaces (NIC's) die zijn gekoppeld aan VM's. Met deze netwerkbeveiligingsgroepen worden automatisch binnenkomende en uitgaande regels geconfigureerd om het volgende verkeer toe te staan:

- Inkomende TCP-verkeer op poorten 29876 en 29877 van een __servicetag__ van __BatchNodeManagement__. Verkeer via deze poorten wordt versleuteld en wordt gebruikt door Azure Batch voor scheduler-/knooppuntcommunicatie.

    ![Een binnenkomende regel die gebruikmaakt van de servicetag BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Optioneel) Inkomende TCP-verkeer op poort 22 externe toegang toestaan. Gebruik deze poort alleen als u verbinding wilt maken met behulp van SSH op het openbare IP-adres.

- Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk.

- Uitgaand verkeer op een willekeurige poort naar internet.

- Voor het inkomende TCP-verkeer van het reken exemplaar op poort 44224 van een __servicetag__ __van AzureMachineLearning.__ Verkeer via deze poort wordt versleuteld en wordt gebruikt door Azure Machine Learning voor communicatie met toepassingen die worden uitgevoerd op reken-exemplaren.

> [!IMPORTANT]
> Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als een NSG communicatie naar de rekenknooppunten blokkeert, stelt de rekenservice de status van de rekenknooppunten in op Onbruikbaar.
>
> U hoeft geen NSG's op subnetniveau op te geven, omdat de Azure Batch service zijn eigen NSG's configureert. Als aan het subnet met de Azure Machine Learning compute echter NSG's of een firewall zijn gekoppeld, moet u ook het eerder vermelde verkeer toestaan.

De configuratie van de NSG-regel in Azure Portal wordt weergegeven in de volgende afbeeldingen:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="De inkomende NSG-regels voor Machine Learning Compute" border="true":::



![Inkomende NSG-regels voor Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Uitgaande connectiviteit vanuit het virtuele netwerk beperken

Als u de standaardregels voor uitgaand verkeer niet wilt gebruiken en u de uitgaande toegang tot uw virtuele netwerk wilt beperken, gebruikt u de volgende stappen:

- Uitgaande internetverbinding weigeren met behulp van de NSG-regels.

- __Beperk uitgaand__ verkeer voor een __reken-exemplaar__ of een rekencluster tot de volgende items:
   - Azure Storage servicetag van  __Storage.RegionName.__ Waarbij `{RegionName}` de naam is van een Azure-regio.
   - Azure Container Registry servicetag __van__ __AzureContainerRegistry.RegionName.__ Waarbij `{RegionName}` de naam is van een Azure-regio.
   - Azure Machine Learning, met behulp __van servicetag__ van __AzureMachineLearning__
   - Azure Resource Manager, met behulp van __de servicetag__ __van AzureResourceManager__
   - Azure Active Directory servicetag __van__ __AzureActiveDirectory__ gebruiken

De configuratie van de NSG-regel in Azure Portal wordt weergegeven in de volgende afbeelding:

[![De uitgaande NSG-regels voor Machine Learning Compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Als u van plan bent om standaard Docker-afbeeldingen van Microsoft te gebruiken en door de gebruiker beheerde afhankelijkheden in te stellen, moet u ook de volgende __servicetags gebruiken:__
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Deze configuratie is nodig wanneer u code hebt die vergelijkbaar is met de volgende codefragmenten als onderdeel van uw trainingsscripts:
>
> __RunConfig-training__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Estimator-training__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>Geforceerde tunneling

Als u geforceerd [tunnelen](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) gebruikt met Azure Machine Learning compute, moet u communicatie met het openbare internet toestaan vanuit het subnet dat de rekenresource bevat. Deze communicatie wordt gebruikt voor taakplanning en toegang tot Azure Storage.

Er zijn twee manieren waarop u dit kunt doen:

* Gebruik een [Virtual Network NAT](../virtual-network/nat-overview.md). Een NAT-gateway biedt uitgaande internetverbinding voor een of meer subnetten in uw virtuele netwerk. Zie Virtuele netwerken [ontwerpen met NAT-gatewaybronnen voor meer informatie.](../virtual-network/nat-gateway-resource.md)

* Voeg [door de gebruiker gedefinieerde routes (UDR's) toe](../virtual-network/virtual-networks-udr-overview.md) aan het subnet dat de rekenresource bevat. Stel een UDR in voor elk IP-adres dat wordt gebruikt door de Azure Batch service in de regio waarin uw resources bestaan. Met deze UDR's kan de Batch-service communiceren met rekenknooppunten voor taakplanning. Voeg ook het IP-adres voor de Azure Machine Learning Service, omdat dit vereist is voor toegang tot reken-exemplaren. Wanneer u het IP-adres voor Azure Machine Learning Service toevoegt, moet u het IP-adres voor zowel de primaire als de secundaire __Azure-regio__ toevoegen. De primaire regio is de regio waarin uw werkruimte zich bevindt.

    Zie Ensure business continuity & disaster recovery using Azure Paired Regions (Bedrijfscontinuïteit garanderen & met behulp van [gekoppelde Azure-regio's)](../best-practices-availability-paired-regions.md#azure-regional-pairs)voor informatie over de secundaire regio. Als uw regio zich bijvoorbeeld Azure Machine Learning Service vs - oost 2, is de secundaire regio VS - centraal. 

    Gebruik een van de volgende methoden om een lijst met IP-adressen van de Batch-service en Azure Machine Learning Service op te halen:

    * Download de [Azure IP-adresbereiken en servicetags](https://www.microsoft.com/download/details.aspx?id=56519) en zoek in het bestand naar `BatchNodeManagement.<region>` en , waarbij uw `AzureMachineLearning.<region>` `<region>` Azure-regio is.

    * Gebruik de [Azure CLI om](/cli/azure/install-azure-cli) de informatie te downloaden. In het volgende voorbeeld worden de IP-adresgegevens gedownload en wordt de informatie voor de regio VS - oost 2 (primair) en VS - centraal (secundair) gefilterd:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        # Get primary region IPs
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        # Get secondary region IPs
        az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
        ```

        > [!TIP]
        > Als u de regio's US-Virginia, US-Arizona of China-Oost-2 gebruikt, retourneren deze opdrachten geen IP-adressen. Gebruik in plaats daarvan een van de volgende koppelingen om een lijst met IP-adressen te downloaden:
        >
        > * [Azure IP-adresbereiken en servicetags voor Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure IP-adresbereiken en servicetags voor Azure China](https://www.microsoft.com//download/details.aspx?id=57062)
    
    Wanneer u de UDR's toevoegt, definieert u de route voor elk gerelateerde Batch IP-adres voorvoegsel en stelt __u Volgend hoptype in__ op __Internet__. In de volgende afbeelding ziet u een voorbeeld van deze UDR in de Azure Portal:

    ![Voorbeeld van een UDR voor een adres voorvoegsel](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > De IP-adressen kunnen na een periode veranderen.

    Naast de UDR's die u definieert, moet uitgaand verkeer naar Azure Storage worden toegestaan via uw on-premises netwerkapparaat. De URL's voor dit verkeer hebben de volgende vormen: `<account>.table.core.windows.net` `<account>.queue.core.windows.net` , en `<account>.blob.core.windows.net` . 

    Zie Create an Azure Batch pool in a virtual network (Een virtuele [Azure Batch maken in een virtueel netwerk) voor meer informatie.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Een rekencluster maken in een virtueel netwerk

Gebruik de volgende stappen Machine Learning Compute cluster te maken:

1. Meld u aan [Azure Machine Learning-studio](https://ml.azure.com/)en selecteer vervolgens uw abonnement en werkruimte.

1. Selecteer __Compute__ aan de linkerkant.

1. Selecteer __Trainingsclusters__ in het midden en selecteer vervolgens __+__ .

1. Vouw in __het dialoogvenster Nieuw trainingscluster__ de sectie __Geavanceerde instellingen__ uit.

1. Als u deze rekenresource wilt configureren voor het gebruik van een virtueel netwerk, voert u de volgende acties uit in de __sectie Virtueel netwerk configureren:__

    1. Selecteer in __de vervolgkeuzelijst__ Resourcegroep de resourcegroep die het virtuele netwerk bevat.
    1. Selecteer in __de vervolgkeuzelijst__ Virtueel netwerk het virtuele netwerk dat het subnet bevat.
    1. Selecteer in __de vervolgkeuzelijst Subnet__ het subnet dat u wilt gebruiken.

   ![De instellingen voor virtuele netwerken voor Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

U kunt ook een Machine Learning Compute maken met behulp van de Azure Machine Learning SDK. Met de volgende code maakt u een Machine Learning Compute cluster in het subnet van `default` een virtueel netwerk met de naam `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Wanneer het aanmaakproces is uitgevoerd, traint u uw model met behulp van het cluster in een experiment. Zie Een rekendoel selecteren en gebruiken voor training [voor meer informatie.](how-to-set-up-training-targets.md)

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Toegang tot gegevens in een notebook van een reken-exemplaar

Als u notebooks gebruikt op een Azure Compute-exemplaar, moet u ervoor zorgen dat uw notebook wordt uitgevoerd op een rekenresource achter hetzelfde virtuele netwerk en subnet als uw gegevens. 

U moet uw rekenproces tijdens het maken configureren configureren in hetzelfde virtuele netwerk onder Geavanceerde **instellingen**  >  **Virtueel netwerk configureren.** U kunt geen bestaande rekenkracht toevoegen aan een virtueel netwerk.

## <a name="azure-databricks"></a>Azure Databricks

Als u Azure Databricks in een virtueel netwerk wilt gebruiken met uw werkruimte, moet aan de volgende vereisten worden voldaan:

> [!div class="checklist"]
> * Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio als de Azure Machine Learning-werkruimte.
> * Als de Azure Storage-account(s) voor de werkruimte ook zijn beveiligd in een virtueel netwerk, moeten deze zich in hetzelfde virtuele netwerk als het Azure Databricks cluster.
> * Naast de __databricks-privé-__ en __databricks-openbare__ subnetten die door  Azure Databricks worden gebruikt, is ook het standaardsubnet vereist dat voor het virtuele netwerk is gemaakt.

Zie Deploy Azure Databricks in your Azure Azure Databricks Virtual Network (Implementatie van Azure Databricks in uw Azure-Virtual Network) voor specifieke informatie over het gebruik [van Virtual Network.](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuele machine of HDInsight-cluster

> [!IMPORTANT]
> Azure Machine Learning ondersteunt alleen virtuele machines met Ubuntu.

In deze sectie leert u hoe u een virtuele machine of een Azure HDInsight gebruikt in een virtueel netwerk met uw werkruimte.

### <a name="create-the-vm-or-hdinsight-cluster"></a>De VM of het HDInsight-cluster maken

Maak een VM- of HDInsight-cluster met behulp van de Azure Portal of de Azure CLI en plaats het cluster in een virtueel Azure-netwerk. Raadpleeg voor meer informatie de volgende artikelen:
* [Virtuele Azure-netwerken voor virtuele Linux-machines maken en beheren](../virtual-machines/linux/tutorial-virtual-network.md)

* [HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Netwerkpoorten configureren 

Configureer Azure Machine Learning broninvoer voor de netwerkbeveiligingsgroep om de communicatie met de SSH-poort op de VM of het cluster toe te staan. De SSH-poort is meestal poort 22. Als u verkeer van deze bron wilt toestaan, moet u de volgende acties uitvoeren:

1. Selecteer __servicetag__ in de __vervolgkeuzelijst Bron.__

1. Selecteer in __de vervolgkeuzelijst__ Bronservicetag de optie __AzureMachineLearning.__

    ![Inkomende regels voor het uitvoeren van experimenten op een virtuele machine of HDInsight-cluster binnen een virtueel netwerk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Selecteer in __de vervolgkeuzelijst__ Bronpoortbereiken. __*__

1. Selecteer in __de__ vervolgkeuzelijst Doel de __optie Alle__.

1. Selecteer 22 __in__ de vervolgkeuzelijst __Doelpoortbereiken.__

1. Selecteer __onder Protocol__ de optie __Alle__.

1. Selecteer __onder Actie__ de optie __Toestaan.__

Houd de standaardregels voor uitgaand verkeer voor de netwerkbeveiligingsgroep. Zie de standaardbeveiligingsregels in Beveiligingsgroepen [voor meer informatie.](../virtual-network/network-security-groups-overview.md#default-security-rules)

Als u de standaardregels voor uitgaand verkeer niet wilt gebruiken en u de uitgaande toegang tot uw virtuele netwerk wilt beperken, gaat u naar de sectie Uitgaande connectiviteit vanuit het virtuele [netwerk](#limiting-outbound-from-vnet) beperken.

### <a name="attach-the-vm-or-hdinsight-cluster"></a>De VM of het HDInsight-cluster koppelen

Koppel de VM of het HDInsight-cluster aan uw Azure Machine Learning werkruimte. Zie Rekendoelen instellen voor modeltraining voor [meer informatie.](how-to-set-up-training-targets.md)

## <a name="next-steps"></a>Volgende stappen

Dit artikel is deel drie van een vijfdelige reeks virtuele netwerken. Zie de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 1: Overzicht van virtueel netwerk](how-to-network-security-overview.md)
* [Deel 2: De werkruimtebronnen beveiligen](how-to-secure-workspace-vnet.md)
* [Deel 4: De deferencingomgeving beveiligen](how-to-secure-inferencing-vnet.md)
* [Deel 5: Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

Zie ook het artikel over het gebruik van [aangepaste DNS](how-to-custom-dns.md) voor naamresolutie.
