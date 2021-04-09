---
title: Veilige trainings omgevingen met virtuele netwerken
titleSuffix: Azure Machine Learning
description: Gebruik een geïsoleerde Azure-Virtual Network om uw Azure Machine Learning-trainings omgeving te beveiligen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 81a6a6aeb826ba4a61fd4f0a4abdb63fe3674bc4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573724"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Een Azure Machine Learning-trainings omgeving beveiligen met virtuele netwerken

In dit artikel leert u hoe u trainings omgevingen kunt beveiligen met een virtueel netwerk in Azure Machine Learning.

Dit artikel is deel drie van een serie van vijf delen die u begeleidt bij het beveiligen van een Azure Machine Learning werk stroom. We raden u ten zeerste aan om in [deel één het overzicht van VNet](how-to-network-security-overview.md) te lezen om eerst inzicht te krijgen in de algehele architectuur. 

Zie de andere artikelen in deze serie:

[1. VNet-overzicht](how-to-network-security-overview.md)  >  [2. Beveilig de werk ruimte](how-to-secure-workspace-vnet.md)  >  **3. De trainings omgeving**  >  [4 beveiligen. Beveilig de vergoedings omgeving](how-to-secure-inferencing-vnet.md)   >  [5. De functionaliteit van Studio inschakelen](how-to-enable-studio-virtual-network.md)

In dit artikel leert u hoe u de volgende trainings Compute-resources in een virtueel netwerk kunt beveiligen:
> [!div class="checklist"]
> - Azure Machine Learning-rekenclusters
> - Azure Machine Learning-rekeninstantie
> - Azure Databricks
> - Virtuele machine
> - HDInsight-cluster

## <a name="prerequisites"></a>Vereisten

+ Lees het artikel [overzicht van netwerk beveiliging](how-to-network-security-overview.md) voor inzicht in algemene scenario's voor virtuele netwerken en de algehele architectuur van virtuele netwerken.

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw reken resources.

+ Als u resources wilt implementeren in een virtueel netwerk of subnet, moet uw gebruikers account over machtigingen beschikken voor de volgende acties in azure op rollen gebaseerd toegangs beheer (Azure RBAC):

    - ' Micro soft. Network/virtualNetworks/lid/Action ' op de virtuele netwerk resource.
    - ' Micro soft. Network/virtualNetworks/subnet/lid/Action ' op de bron van het subnet.

    Zie voor meer informatie over Azure RBAC met netwerken de [ingebouwde rollen voor netwerken](../role-based-access-control/built-in-roles.md#networking)


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>& exemplaren van compute-clusters 

Als u een [beheerd Azure machine learning __Compute-doel__](concept-compute-target.md#azure-machine-learning-compute-managed) of een [Azure machine learning COMPUTE- __exemplaar__](concept-compute-instance.md) in een virtueel netwerk wilt gebruiken, moet aan de volgende netwerk vereisten worden voldaan:

> [!div class="checklist"]
> * Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden als de Azure Machine Learning-werk ruimte.
> * Het subnet dat is opgegeven voor het reken proces of het cluster moet voldoende niet-toegewezen IP-adressen hebben om het aantal Vm's te kunnen ondersteunen. Als het subnet onvoldoende niet-toegewezen IP-adressen heeft, wordt een reken cluster gedeeltelijk toegewezen.
> * Controleer of het beveiligings beleid of de vergren delingen in het abonnement of de resource groep van het virtuele netwerk beperkt zijn tot de machtigingen voor het beheren van het virtuele netwerk. Als u het virtuele netwerk wilt beveiligen door verkeer te beperken, moet u sommige poorten voor de compute-service geopend laten. Zie de sectie [vereiste poorten](#mlcports) voor meer informatie.
> * Als u meerdere reken instanties of clusters in één virtueel netwerk wilt plaatsen, moet u mogelijk een quotum verhoging aanvragen voor een of meer van uw resources.
> * Als de Azure Storage account (s) voor de werk ruimte ook worden beveiligd in een virtueel netwerk, moeten ze zich in hetzelfde virtuele netwerk bevinden als de Azure Machine Learning Reken instantie of het cluster. 
> * Zorg ervoor dat de communicatie tussen websockets niet is uitgeschakeld voor de Jupyter-functionaliteit van reken instanties. Zorg ervoor dat uw netwerk WebSocket-verbindingen toestaat naar *. instances.azureml.net en *. instances.azureml.ms. 
> * Wanneer reken instantie wordt geïmplementeerd in een persoonlijke koppelings werkruimte, kan deze alleen worden geopend vanuit een virtueel netwerk. Als u een aangepast DNS-of hosts-bestand gebruikt, voegt u een vermelding toe voor met het privé `<instance-name>.<region>.instances.azureml.ms` -IP-adres van het persoonlijke eind punt van de werk ruimte. Zie het [aangepaste DNS-](./how-to-custom-dns.md) artikel voor meer informatie.
> * Het subnet dat wordt gebruikt voor het implementeren van het reken cluster/exemplaar, mag niet worden gedelegeerd naar een andere service, zoals ACI
> * Het eindpunt beleid van de virtuele netwerk service werkt niet voor reken cluster-en exemplaar systeem opslag accounts

    
> [!TIP]
> Het Machine Learning Reken exemplaar of cluster wijst automatisch extra netwerk bronnen toe __aan de resource groep die het virtuele netwerk bevat__. Voor elk reken exemplaar of cluster wijst de service de volgende bronnen toe:
> 
> * Eén netwerk beveiligings groep
> * Eén openbaar IP-adres. Als u het maken van een openbaar IP-adres door Azure Policy hebt toegestaan, mislukt de implementatie van het cluster/de exemplaren
> * Een load balancer
> 
> In het geval van clusters worden deze bronnen verwijderd (en opnieuw gemaakt) elke keer dat het cluster wordt geschaald naar 0 knoop punten, maar voor een instantie waarvan de bronnen worden vastgehouden, is het exemplaar volledig verwijderd (stoppen wordt niet verwijderd uit de resources). 
> De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Als de resource groep van het virtuele netwerk is vergrendeld, mislukt de verwijdering van het reken cluster of het exemplaar. De Load Balancer kan pas worden verwijderd als het berekenings cluster/exemplaar is verwijderd. Zorg er ook voor dat er geen Azure-beleid is dat het maken van netwerk beveiligings groepen verhindert.


### <a name="required-ports"></a><a id="mlcports"></a> Vereiste poorten

Als u van plan bent het virtuele netwerk te beveiligen door het netwerk verkeer naar/van het open bare Internet te beperken, moet u binnenkomende communicaties van de Azure Batch-service toestaan.

De batch-service voegt netwerk beveiligings groepen (Nsg's) toe op het niveau van netwerk interfaces (Nic's) die zijn gekoppeld aan Vm's. Met deze netwerkbeveiligingsgroepen worden automatisch binnenkomende en uitgaande regels geconfigureerd om het volgende verkeer toe te staan:

- Binnenkomend TCP-verkeer op de poorten 29876 en 29877 van een __service label__ van __BatchNodeManagement__. Verkeer via deze poorten wordt versleuteld en wordt gebruikt door Azure Batch voor de communicatie van scheduler/knoop punten.

    ![Een regel voor binnenkomende verbindingen die gebruikmaakt van het BatchNodeManagement-service label](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Beschrijving Binnenkomend TCP-verkeer op poort 22 om externe toegang toe te staan. Gebruik deze poort alleen als u verbinding wilt maken met behulp van SSH op het open bare IP-adres.

- Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk.

- Uitgaand verkeer op een willekeurige poort naar internet.

- Voor het binnenkomende TCP-verkeer van reken instanties op poort 44224 van een __service-tag__ van __AzureMachineLearning__. Verkeer via deze poort wordt versleuteld en wordt gebruikt door Azure Machine Learning voor communicatie met toepassingen die worden uitgevoerd op Compute-exemplaren.

> [!IMPORTANT]
> Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als een NSG communicatie met de reken knooppunten blokkeert, stelt de compute-service de status van de reken knooppunten in op onbruikbaar.
>
> U hoeft Nsg's niet op subnetniveau op te geven omdat de Azure Batch-service zijn eigen Nsg's configureert. Als er echter een Nsg's of een firewall is gekoppeld aan het subnet met de Azure Machine Learning compute, moet u ook het verkeer toestaan dat hierboven wordt vermeld.

De NSG-regel configuratie in de Azure Portal wordt weer gegeven in de volgende installatie kopieën:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="De inkomende NSG-regels voor Machine Learning Compute" border="true":::



![Inkomende NSG-regels voor Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Uitgaande connectiviteit vanuit het virtuele netwerk beperken

Als u de standaard regels voor uitgaande verbindingen niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, gebruikt u de volgende stappen:

- Uitgaande Internet verbinding weigeren met behulp van de NSG-regels.

- Beperk het uitgaande verkeer voor een __reken instantie__ of een __berekenings cluster__ tot de volgende items:
   - Azure Storage, door gebruik te maken van de __service tag__ __Storage. regionaam__. Waar `{RegionName}` de naam is van een Azure-regio.
   - Azure Container Registry, met behulp van de __service-tag__ __AzureContainerRegistry. regionaam__. Waar `{RegionName}` de naam is van een Azure-regio.
   - Azure Machine Learning, met behulp van het __service label__ __AzureMachineLearning__
   - Azure Resource Manager, met behulp van het __service label__ __AzureResourceManager__
   - Azure Active Directory, met behulp van het __service label__ __AzureActiveDirectory__

De NSG-regel configuratie in de Azure Portal wordt weer gegeven in de volgende afbeelding:

[![De regels voor uitgaande NSG voor Machine Learning Compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Als u van plan bent om standaard-docker-installatie kopieën te gebruiken die door micro soft worden meegeleverd en door de gebruiker beheerde afhankelijkheden in te scha kelen, moet u ook de volgende __service Tags__ gebruiken:
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Deze configuratie is vereist wanneer u code die vergelijkbaar is met de volgende fragmentten als onderdeel van uw trainings scripts:
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

Als u gebruik wilt maken van [geforceerde tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) met Azure machine learning compute, moet u communicatie met het open bare Internet toestaan vanaf het subnet dat de reken resource bevat. Deze communicatie wordt gebruikt voor het plannen van taken en het openen van Azure Storage.

U kunt dit op twee manieren doen:

* Gebruik een [Virtual Network NAT](../virtual-network/nat-overview.md). Een NAT-gateway biedt een uitgaande Internet verbinding voor een of meer subnetten in het virtuele netwerk. Zie [virtuele netwerken ontwerpen met NAT-gateway bronnen](../virtual-network/nat-gateway-resource.md)voor meer informatie.

* Door de [gebruiker gedefinieerde routes (udr's)](../virtual-network/virtual-networks-udr-overview.md) toevoegen aan het subnet dat de reken resource bevat. Stel een UDR in voor elk IP-adres dat wordt gebruikt door de Azure Batch-service in de regio waar uw resources bestaan. Deze Udr's inschakelen de batch-service om te communiceren met reken knooppunten voor het plannen van taken. Voeg ook het IP-adres voor de Azure Machine Learning-service toe, omdat dit vereist is voor toegang tot reken instanties. Wanneer u het IP-adres voor de Azure Machine Learning-service toevoegt, moet u het IP-adres voor de __primaire en secundaire__ Azure-regio's toevoegen. De primaire regio waarin uw werk ruimte zich bevindt.

    Als u de secundaire regio wilt vinden, raadpleegt u de [bedrijfs continuïteit controleren & herstel na nood geval met behulp van gekoppelde Azure-regio's](../best-practices-availability-paired-regions.md#azure-regional-pairs). Als uw Azure Machine Learning-service bijvoorbeeld in VS-Oost 2 is, is de secundaire regio VS-centraal. 

    Gebruik een van de volgende methoden om een lijst met IP-adressen van de batch-service en Azure Machine Learning-service te verkrijgen:

    * Down load de [Azure IP-bereiken en-service Tags](https://www.microsoft.com/download/details.aspx?id=56519) en zoek het bestand voor `BatchNodeManagement.<region>` en `AzureMachineLearning.<region>` , waar `<region>` is uw Azure-regio.

    * Gebruik de [Azure cli](/cli/azure/install-azure-cli) om de informatie te downloaden. In het volgende voor beeld worden de IP-adres gegevens gedownload en worden de gegevens voor de regio VS-Oost 2 (primair) en het centrale Amerikaanse land (secundair) gefilterd:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        # Get primary region IPs
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        # Get secondary region IPs
        az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
        ```

        > [!TIP]
        > Als u gebruikmaakt van de Amerikaanse Virginia, US-Arizona regio's of China-Oost-2 regio's, retour neren deze opdrachten geen IP-adressen. Gebruik in plaats daarvan een van de volgende koppelingen voor het downloaden van een lijst met IP-adressen:
        >
        > * [IP-adresbereiken en service tags voor Azure voor Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure IP-adresbereiken en service tags voor Azure China](https://www.microsoft.com//download/details.aspx?id=57062)
    
    Wanneer u de Udr's toevoegt, definieert u de route voor elk gerelateerde IP-adres voorvoegsel voor batch en stelt u het __type volgende hop__ in op __Internet__. In de volgende afbeelding ziet u een voor beeld van deze UDR in de Azure Portal:

    ![Voor beeld van een UDR voor een adres voorvoegsel](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > De IP-adressen kunnen na verloop van tijd veranderen.

    Naast de Udr's die u definieert, moet uitgaand verkeer naar Azure Storage worden toegestaan via uw on-premises netwerk apparaat. De Url's voor dit verkeer zijn met name in de volgende formulieren: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` en `<account>.blob.core.windows.net` . 

    Zie [een Azure batch groep maken in een virtueel netwerk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)voor meer informatie.

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Een berekenings cluster maken in een virtueel netwerk

Als u een Machine Learning Compute cluster wilt maken, gebruikt u de volgende stappen:

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com/)en selecteer vervolgens uw abonnement en werk ruimte.

1. Selecteer __Compute__ aan de linkerkant.

1. Selecteer __trainings clusters__ in het midden en selecteer deze __+__ .

1. Vouw in het dialoog venster __nieuw trainings cluster__ het gedeelte __Geavanceerde instellingen__ uit.

1. Als u deze reken resource wilt configureren voor het gebruik van een virtueel netwerk, voert u de volgende acties uit in de sectie __virtueel netwerk configureren__ :

    1. Selecteer de resource groep met het virtuele netwerk in de vervolg keuzelijst __resource groep__ .
    1. Selecteer in de vervolg keuzelijst __virtueel netwerk__ het virtuele netwerk dat het subnet bevat.
    1. Selecteer in de vervolg keuzelijst __subnet__ het subnet dat u wilt gebruiken.

   ![De instellingen van het virtuele netwerk voor Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

U kunt ook een Machine Learning Compute cluster maken met behulp van de Azure Machine Learning SDK. Met de volgende code wordt een nieuw Machine Learning Compute Cluster gemaakt in het `default` subnet van een virtueel netwerk met de naam `mynetwork` :

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

Wanneer het maken van het proces is voltooid, traint u uw model met behulp van het cluster in een experiment. Zie voor meer informatie [een berekenings doel selecteren en gebruiken voor training](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Toegang tot gegevens in een notebook van reken instanties

Als u notitie blokken op een Azure Compute-instantie gebruikt, moet u ervoor zorgen dat uw notitie blok wordt uitgevoerd op een reken resource achter hetzelfde virtuele netwerk en subnet als uw gegevens. 

U moet uw reken instantie configureren zodat deze zich in hetzelfde virtuele netwerk bevindt tijdens het maken van **Geavanceerde instellingen**  >  **virtuele netwerken configureren**. U kunt een bestaand reken exemplaar niet toevoegen aan een virtueel netwerk.

## <a name="azure-databricks"></a>Azure Databricks

Als u Azure Databricks wilt gebruiken in een virtueel netwerk met uw werk ruimte, moet aan de volgende vereisten worden voldaan:

> [!div class="checklist"]
> * Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden als de Azure Machine Learning-werk ruimte.
> * Als de Azure Storage account (s) voor de werk ruimte ook worden beveiligd in een virtueel netwerk, moeten ze zich in hetzelfde virtuele netwerk bevinden als het Azure Databricks-cluster.
> * Naast de __databricks-particuliere__ en __databricks-open bare__ subnetten die worden gebruikt door Azure Databricks, is ook het __standaard__ subnet dat voor het virtuele netwerk is gemaakt, vereist.

Zie [deploying Azure Databricks in uw Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)voor specifieke informatie over het gebruik van Azure Databricks met een virtueel netwerk.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuele machine of HDInsight-cluster

> [!IMPORTANT]
> Azure Machine Learning ondersteunt alleen virtuele machines waarop Ubuntu wordt uitgevoerd.

In deze sectie leert u hoe u een virtuele machine of een Azure HDInsight-cluster in een virtueel netwerk met uw werk ruimte gebruikt.

### <a name="create-the-vm-or-hdinsight-cluster"></a>De virtuele machine of het HDInsight-cluster maken

Maak een virtuele machine of een HDInsight-cluster met behulp van de Azure Portal of de Azure CLI en plaats het cluster in een virtueel Azure-netwerk. Raadpleeg voor meer informatie de volgende artikelen:
* [Virtuele Azure-netwerken voor Linux-Vm's maken en beheren](../virtual-machines/linux/tutorial-virtual-network.md)

* [HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Netwerk poorten configureren 

Azure Machine Learning toestaan te communiceren met de SSH-poort op de virtuele machine of het cluster, configureert u een bron vermelding voor de netwerk beveiligings groep. De SSH-poort is doorgaans poort 22. Voer de volgende acties uit om verkeer van deze bron toe te staan:

1. Selecteer in de vervolg keuzelijst __bron__ de optie __service label__.

1. Selecteer __AzureMachineLearning__ in de vervolg keuzelijst __bron service label__ .

    ![Binnenkomende regels voor het experimenteren op een VM-of HDInsight-cluster in een virtueel netwerk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Selecteer in de vervolg keuzelijst __bron poort bereik__ __*__ .

1. Selecteer in de vervolg keuzelijst __bestemming__ __een__.

1. Selecteer __22__ in de vervolg keuzelijst __doel poort bereik__ .

1. Onder __protocol__ selecteert u __een__.

1. Selecteer onder __actie__ __toestaan__.

Behoud de standaard regels voor uitgaande verbindingen voor de netwerk beveiligings groep. Zie de standaard beveiligings regels in [beveiligings groepen](../virtual-network/network-security-groups-overview.md#default-security-rules)voor meer informatie.

Als u de standaard regels voor uitgaand verkeer niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, raadpleegt u de sectie [uitgaande verbindingen beperken via het virtuele netwerk](#limiting-outbound-from-vnet) .

### <a name="attach-the-vm-or-hdinsight-cluster"></a>De virtuele machine of het HDInsight-cluster koppelen

Koppel de virtuele machine of het HDInsight-cluster aan uw Azure Machine Learning-werk ruimte. Zie [Compute-doelen voor model training instellen](how-to-set-up-training-targets.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is deel drie van een virtuele netwerk reeks van vijf delen. Raadpleeg de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 1: overzicht van virtueel netwerk](how-to-network-security-overview.md)
* [Deel 2: de resources van de werk ruimte beveiligen](how-to-secure-workspace-vnet.md)
* [Deel 4: de omgeving voor het afwijzen van interferentie beveiligen](how-to-secure-inferencing-vnet.md)
* [Deel 5: de functionaliteit van Studio inschakelen](how-to-enable-studio-virtual-network.md)

Zie ook het artikel over het gebruik van [aangepaste DNS](how-to-custom-dns.md) voor naam omzetting.