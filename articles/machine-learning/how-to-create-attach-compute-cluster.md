---
title: Rekenclusters maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van rekenclusters in Azure Machine Learning werkruimte. Gebruik het rekencluster als een rekendoel voor training of de deferie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: f61936e622a539b29c6788f631df5de42bb2f242
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861243"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Een Azure Machine Learning-rekencluster maken

Meer informatie over het maken en beheren van een [rekencluster](concept-compute-target.md#azure-machine-learning-compute-managed) in Azure Machine Learning werkruimte.

U kunt een Azure Machine Learning gebruiken om een training- of batchdeferentieproces te distribueren over een cluster van CPU- of GPU-rekenknooppunten in de cloud. Zie voor GPU geoptimaliseerde VM-grootten voor meer informatie over de VM-grootten met [GPU's.](../virtual-machines/sizes-gpu.md) 

In dit artikel leert u het volgende:

* Een rekencluster maken
* De kosten van uw rekencluster verlagen
* Een beheerde [identiteit instellen](../active-directory/managed-identities-azure-resources/overview.md) voor het cluster

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)

* De [Azure CLI-extensie voor Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)of de [Azure Machine Learning Visual Studio Code-extensie](tutorial-setup-vscode-extension.md).

* Als u de Python-SDK gebruikt, [stelt u uw ontwikkelomgeving in met een werkruimte](how-to-configure-environment.md).  Zodra uw omgeving is ingesteld, koppelt u deze aan de werkruimte in uw Python-script:

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>Wat is een rekencluster?

Azure Machine Learning rekencluster is een beheerde rekeninfrastructuur waarmee u eenvoudig een rekenkracht met één of meerdere knooppunt kunt maken. De berekening wordt binnen uw werkruimteregio gemaakt als een resource die kan worden gedeeld met andere gebruikers in uw werkruimte. De rekenkracht wordt automatisch opgeschaald wanneer een taak wordt verzonden en kan in een Azure-Virtual Network. De berekening wordt uitgevoerd in een containeromgeving en verpakt uw modelafhankelijkheden in een [Docker-container](https://www.docker.com/why-docker).

Rekenclusters kunnen taken veilig uitvoeren in een omgeving met een virtueel [netwerk,](how-to-secure-training-vnet.md)zonder dat ondernemingen SSH-poorten moeten openen. De taak wordt uitgevoerd in een containeromgeving en verpakt uw modelafhankelijkheden in een Docker-container. 

## <a name="limitations"></a>Beperkingen

* Sommige van de scenario's die in dit document worden vermeld, zijn gemarkeerd als __preview.__ Preview-functionaliteit wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

* Momenteel wordt alleen het maken (en niet bijwerken) van clusters ondersteund via ARM-sjablonen [ https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json ]. Voor het bijwerken van de rekenkracht raden we u aan om de SDK, CLI of UX voor nu te gebruiken.

* Azure Machine Learning Compute heeft standaardlimieten, zoals het aantal kernen dat kan worden toegewezen. Zie Quota voor [Azure-resources beheren en aanvragen voor meer informatie.](how-to-manage-quotas.md)

* Met Azure kunt u _vergrendelingen voor_ resources plaatsen, zodat ze niet kunnen worden verwijderd of alleen-lezen zijn. __Pas geen resourcevergrendelingen toe op de resourcegroep die uw werkruimte bevat.__ Door een vergrendeling toe te passen op de resourcegroep die uw werkruimte bevat, voorkomt u schaalbewerkingen voor Azure ML-rekenclusters. Zie Resources vergrendelen om onverwachte wijzigingen te voorkomen voor meer informatie over het [vergrendelen van resources.](../azure-resource-manager/management/lock-resources.md)

> [!TIP]
> Clusters kunnen over het algemeen worden opgeschaald tot 100 knooppunten zolang u voldoende quotum hebt voor het aantal vereiste kernen. Standaard worden clusters ingesteld met communicatie tussen knooppunten ingeschakeld tussen de knooppunten van het cluster om bijvoorbeeld MPI-taken te ondersteunen. U kunt uw clusters echter schalen naar 1000 knooppunten door simpelweg een ondersteuningsticket in te stellen en aan te vragen om uw abonnement of werkruimte of een specifiek cluster toe te staan voor het uitschakelen van communicatie tussen knooppunten. [](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)


## <a name="create"></a>Maken

**Geschatte tijd:** Ongeveer 5 minuten.

Azure Machine Learning Compute kan opnieuw worden gebruikt voor verschillende runs. De rekenkracht kan worden gedeeld met andere gebruikers in de werkruimte en wordt tussen runs bewaard, knooppunten automatisch omhoog of omlaag geschaald op basis van het aantal verzonden runs en de max_nodes ingesteld op uw cluster. De min_nodes bepaalt de minimale beschikbare knooppunten.

De toegewezen kernen per regio per VM-familiequotum en het totale regionale quotum, dat van toepassing is op het maken van rekenclusters, worden samengevoegd en gedeeld met Azure Machine Learning trainingquotum voor rekenproces. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

De rekenkracht wordt automatisch omlaag geschaald naar nul knooppunten wanneer deze niet wordt gebruikt.   Toegewezen VM's worden gemaakt om uw taken naar behoefte uit te voeren.
    
# <a name="python"></a>[Python](#tab/python)


Als u een permanente Azure Machine Learning Compute-resource in Python wilt maken, geeft u de eigenschappen **vm_size** **en max_nodes** op. Azure Machine Learning gebruikt vervolgens slimme standaardwaarden voor de andere eigenschappen.
    
* **vm_size:** de VM-familie van de knooppunten die zijn gemaakt door Azure Machine Learning Compute.
* **max_nodes:** het maximum aantal knooppunten dat automatisch moet worden geschaald wanneer u een taak op Azure Machine Learning Compute.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

U kunt ook verschillende geavanceerde eigenschappen configureren wanneer u een Azure Machine Learning Compute. Met de eigenschappen kunt u een permanent cluster van vaste grootte of binnen een bestaande Azure-Virtual Network maken in uw abonnement.  Zie de [klasse AmlCompute](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) voor meer informatie.


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Zie az [ml computetarget create amlcompute voor meer informatie.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute)

# <a name="studio"></a>[Studio](#tab/azure-studio)

Zie Rekendoelen maken in Azure Machine Learning-studio voor meer informatie over het maken van een [rekencluster in Azure Machine Learning-studio.](how-to-create-attach-compute-studio.md#amlcompute)

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> De kosten van uw rekencluster verlagen

U kunt er ook voor kiezen om [VM's met lage prioriteit te](concept-plan-manage-cost.md#low-pri-vm) gebruiken om een deel van uw workloads of al uw workloads uit te voeren. Deze VM's hebben geen gegarandeerde beschikbaarheid en kunnen tijdens gebruik worden vereendd. U moet een bestaande taak opnieuw starten. 

Gebruik een van deze manieren om een VM met lage prioriteit op te geven:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Stel de `vm-priority` in:
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Kies in de studio **Lage prioriteit wanneer** u een VM maakt.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Beheerde identiteit instellen

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Beheerde identiteit configureren in uw inrichtingsconfiguratie:  

    * Door het systeem toegewezen beheerde identiteit die is gemaakt in een werkruimte met de naam `ws`
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Door de gebruiker toegewezen beheerde identiteit die is gemaakt in een werkruimte met de naam `ws`
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Beheerde identiteit toevoegen aan een bestaand rekencluster met de naam `cpu_cluster`
    
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

* Een nieuw beheerd rekencluster met beheerde identiteit maken

  * Door de gebruiker toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Door het systeem toegewezen beheerde identiteit

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Voeg een beheerde identiteit toe aan een bestaand cluster:

    * Door de gebruiker toegewezen beheerde identiteit
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Door het systeem toegewezen beheerde identiteit

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Zie [Beheerde identiteit instellen in studio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Gebruik van beheerde identiteit

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Er is een kans dat sommige gebruikers die hun Azure Machine Learning-werkruimte hebben gemaakt vanuit de Azure Portal vóór de ga-release mogelijk geen AmlCompute in die werkruimte kunnen maken. U kunt een ondersteuningsaanvraag indienen bij de service of een nieuwe werkruimte maken via de portal of de SDK om uzelf onmiddellijk te deblokkeren.

Als uw Azure Machine Learning-rekencluster lijkt vast te zitten bij het formaat van het knooppunt (0-> 0) voor de knooppunttoestand, kan dit worden veroorzaakt door Azure-resourcevergrendelingen.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Volgende stappen

Gebruik uw rekencluster voor het volgende:

* [Een trainingsrun verzenden](how-to-set-up-training-targets.md) 
* [Voer batchdeferentie uit.](./tutorial-pipeline-batch-scoring-classification.md)
