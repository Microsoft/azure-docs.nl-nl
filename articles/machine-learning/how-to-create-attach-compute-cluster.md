---
title: Reken clusters maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van reken clusters in uw Azure Machine Learning-werk ruimte. Gebruik het berekenings cluster als een reken doel voor training of deinterferentie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 0bbf70016dc9b93120b3158e8954c336095ea211
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832684"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Een Azure Machine Learning-rekencluster maken

Meer informatie over het maken en beheren van een [berekenings cluster](concept-compute-target.md#azure-machine-learning-compute-managed) in uw Azure machine learning-werk ruimte.

U kunt Azure Machine Learning Compute-cluster gebruiken voor het distribueren van een training of batch-deprocessor proces over een cluster van CPU-of GPU-reken knooppunten in de Cloud. Zie [grootten geoptimaliseerd voor virtuele machines](../virtual-machines/sizes-gpu.md)voor meer informatie over de VM-grootten die GPU bevatten. 

In dit artikel leert u het volgende:

* Een rekencluster maken
* De kosten voor reken clusters verlagen
* Een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) voor het cluster instellen

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

* De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

## <a name="what-is-a-compute-cluster"></a>Wat is een berekenings cluster?

Azure Machine Learning Compute-Cluster is een beheerde infra structuur voor beheer, waarmee u eenvoudig een berekening met één of meerdere knoop punten kunt maken. De berekening wordt binnen uw werkruimte regio gemaakt als een resource die kan worden gedeeld met andere gebruikers in uw werk ruimte. De berekening wordt automatisch omhoog geschaald wanneer een taak wordt verzonden en kan in een Azure-Virtual Network worden geplaatst. De berekening wordt uitgevoerd in een omgeving met containers en verpakt uw model afhankelijkheden in een [docker-container](https://www.docker.com/why-docker).

Reken clusters kunnen taken veilig uitvoeren in een [virtuele netwerk omgeving](how-to-secure-training-vnet.md), zonder dat ondernemingen de SSH-poorten hoeven te openen. De taak wordt uitgevoerd in een omgeving met containers en verpakt uw model afhankelijkheden in een docker-container. 

## <a name="limitations"></a>Beperkingen

* **Maak niet meerdere gelijktijdige bijlagen met dezelfde reken kracht** vanuit uw werk ruimte. U kunt bijvoorbeeld één reken cluster koppelen aan een werk ruimte met behulp van twee verschillende namen. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

    Als u een compute-doel opnieuw wilt koppelen, bijvoorbeeld om de cluster configuratie-instellingen te wijzigen, moet u eerst de bestaande bijlage verwijderen.

* Enkele van de scenario's die in dit document worden vermeld, zijn gemarkeerd als __Preview__. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

* Azure Machine Learning Compute heeft standaard limieten, zoals het aantal kernen dat kan worden toegewezen. Zie voor meer informatie [beheer en aanvragen van quota's voor Azure-resources](how-to-manage-quotas.md).

* Met Azure kunt u _vergren delingen_ op resources plaatsen, zodat ze niet kunnen worden verwijderd of alleen-lezen zijn. __Pas geen resource vergrendeling toe op de resource groep die uw werk ruimte bevat__. Het Toep assen van een vergren deling op de resource groep die uw werk ruimte bevat, voor komt dat er schaal bewerkingen voor Azure ML-reken clusters worden uitgevoerd. Zie [resources vergren delen om onverwachte wijzigingen](../azure-resource-manager/management/lock-resources.md)te voor komen voor meer informatie over het vergren delen van resources.

> [!TIP]
> Clusters kunnen over het algemeen tot 100 knoop punten worden geschaald zolang u voldoende quota hebt voor het vereiste aantal kernen. Standaard worden clusters ingesteld met communicatie tussen knoop punten die is ingeschakeld tussen de cluster knooppunten voor de ondersteuning van MPI-taken. U kunt uw clusters echter naar duizenden knoop punten schalen door simpelweg [een ondersteunings ticket te verhogen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)en u aan te vragen om een lijst met uw abonnement of werk ruimte of een specifiek cluster te maken voor het uitschakelen van communicatie tussen knoop punten. 


## <a name="create"></a>Maken

**Geschatte tijd**: ongeveer 5 minuten.

Azure Machine Learning Compute kan worden hergebruikt in uitvoeringen. De compute kan worden gedeeld met andere gebruikers in de werk ruimte en wordt bewaard tussen uitvoeringen, waarbij knoop punten automatisch omhoog of omlaag worden geschaald op basis van het aantal verzonden uitvoeringen en de max_nodes die in uw cluster zijn ingesteld. Met de instelling min_nodes bepaalt u de mini maal beschik bare knoop punten.

Het quotum toegewezen kernen per regio per VM-serie en het totale regionale quotum, dat van toepassing is op het maken van een compute-cluster, is Unified en gedeeld met Azure Machine Learning quota voor reken instanties. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

De berekening wordt automatisch geschaald naar nul knoop punten wanneer deze niet wordt gebruikt.   Er zijn specifieke Vm's gemaakt om uw taken naar behoefte uit te voeren.
    
# <a name="python"></a>[Python](#tab/python)

Als u een permanente Azure Machine Learning Compute-resource in python wilt maken, geeft u de **vm_size** en **max_nodes** eigenschappen op. Azure Machine Learning gebruikt vervolgens slimme standaard instellingen voor de andere eigenschappen. 
    
* **vm_size**: de VM-familie van de knoop punten die door Azure machine learning Compute zijn gemaakt.
* **max_nodes**: het maximum aantal knoop punten waarmee automatisch kan worden geschaald wanneer u een taak op Azure machine learning Compute uitvoert.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

U kunt ook verschillende geavanceerde eigenschappen configureren wanneer u Azure Machine Learning Compute maakt. Met de eigenschappen kunt u een permanent cluster met een vaste grootte of binnen een bestaand Azure-Virtual Network in uw abonnement maken.  Zie de [klasse AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) voor meer informatie.


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Zie [AZ ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true)voor meer informatie.

# <a name="studio"></a>[Studio](#tab/azure-studio)

Zie [Compute-doelen maken in azure machine learning Studio](how-to-create-attach-compute-studio.md#amlcompute)voor meer informatie over het maken van een berekenings cluster in de Studio.

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> De kosten voor reken clusters verlagen

U kunt ook [virtuele machines met lage prioriteit](concept-plan-manage-cost.md#low-pri-vm) gebruiken om enkele of alle werk belastingen uit te voeren. Deze Vm's hebben geen gegarandeerde Beschik baarheid en kunnen worden afgebroken tijdens het gebruik. Een afgebroken taak wordt opnieuw gestart, niet hervat. 

Gebruik een van de volgende manieren om een VM met lage prioriteit op te geven:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Stel het `vm-priority` volgende in:
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Kies in de Studio **lage prioriteit** bij het maken van een virtuele machine.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Beheerde identiteit instellen

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Beheerde identiteit configureren in uw inrichtings configuratie:  

    * Door het systeem toegewezen beheerde identiteit:
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * Door de gebruiker toegewezen beheerde identiteit:
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Beheerde identiteit toevoegen aan een bestaand reken cluster 
    
    * Door het systeem toegewezen beheerde identiteit:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Door de gebruiker toegewezen beheerde identiteit:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

* Een nieuw beheerd reken cluster maken met beheerde identiteit

  * Door een gebruiker toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Door het systeem toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Een beheerde identiteit toevoegen aan een bestaand cluster:

    * Door een gebruiker toegewezen beheerde identiteit
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Door het systeem toegewezen beheerde identiteit

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Zie [beheerde identiteit instellen in Studio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Gebruik van beheerde identiteit

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="next-steps"></a>Volgende stappen

Gebruik uw berekenings cluster voor het volgende:

* [Een trainings uitvoering verzenden](how-to-set-up-training-targets.md) 
* [Batch](./tutorial-pipeline-batch-scoring-classification.md)afleiding uitvoeren.
