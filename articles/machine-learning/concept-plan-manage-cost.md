---
title: Kosten voorspellen en beheren
titleSuffix: Azure Machine Learning
description: Plan en beheer kosten voor Azure Machine Learning met kostenanalyse in Azure Portal. Meer tips voor kostenbesparingen om uw kosten te verlagen bij het bouwen van ML-modellen.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a52bcc9a2649592bef7b6f8d09cdde96226b77ea
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886114"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Kosten voor Azure Machine Learning

In dit artikel wordt beschreven hoe u kosten voor Azure Machine Learning. Eerst gebruikt u de Azure-prijscalculator om kosten te plannen voordat u resources toevoegt. Als u vervolgens de Azure-resources toevoegt, bekijkt u de geschatte kosten. Gebruik ten slotte tips voor kostenbesparing wanneer u uw model traint met beheerde Azure Machine Learning rekenclusters.

Nadat u uw resources hebt Azure Machine Learning, gebruikt u de functies voor kostenbeheer om budgetten in te stellen en kosten te bewaken. Bekijk ook de geraamde kosten en identificeer bestedingstrends om gebieden te identificeren waar u mogelijk actie wilt ondernemen.

De kosten voor Azure Machine Learning zijn slechts een deel van de maandelijkse kosten in uw Azure-factuur. Als u andere Azure-services gebruikt, wordt u gefactureerd voor alle Azure-services en -resources die worden gebruikt in uw Azure-abonnement, met inbegrip van de services van derden. In dit artikel wordt uitgelegd hoe u kosten voor uw Azure Machine Learning. Nadat u bekend bent met het beheren van de kosten voor Azure Machine Learning, kunt u vergelijkbare methoden toepassen om de kosten te beheren voor alle Azure-services die in uw abonnement worden gebruikt.

Wanneer u uw machine learning-modellen traint, gebruikt u beheerde Azure Machine Learning-rekenclusters om te profiteren van meer kostenbesparende tips:

* Uw trainingsclusters configureren voor automatisch schalen
* Quota instellen voor uw abonnement en werkruimten
* Beëindigingsbeleid instellen voor de trainingsrun
* Virtuele machines met lage prioriteit (VM) gebruiken
* Een gereserveerde VM-instantie van Azure gebruiken

## <a name="prerequisites"></a>Vereisten

Kostenanalyse biedt ondersteuning voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig. 

Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Kosten schatten voordat u Azure Machine Learning

Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) om de kosten te schatten voordat u de resources in een Azure Machine Learning maken. Selecteer aan de linkerkant **AI + Machine Learning** en selecteer vervolgens **Azure Machine Learning** te beginnen.  

In de volgende schermopname ziet u de schatting van de kosten met behulp van de rekenmachine:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kostenraming in Azure-calculator":::

Wanneer u nieuwe resources aan uw werkruimte toevoegt, gaat u terug naar deze calculator en voegt u hier dezelfde resource toe om uw kostenramingen bij te werken.

Zie prijzen Azure Machine Learning [voor meer informatie.](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Inzicht in het volledige factureringsmodel voor Azure Machine Learning

Azure Machine Learning wordt uitgevoerd op een Azure-infrastructuur die samen met de kosten Azure Machine Learning u de nieuwe resource implementeert. Het is belangrijk om te begrijpen dat extra infrastructuur kosten kan genereren. U moet die kosten beheren wanneer u geïmplementeerde resources wijzigt. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Kosten die doorgaans worden gemaakt met Azure Machine Learning

Wanneer u resources maakt voor een Azure Machine Learning werkruimte, worden er ook resources voor andere Azure-services gemaakt. Dit zijn:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Basisaccount
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (algemeen gebruik v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>De kosten kunnen toenemen na het verwijderen van een resource

Wanneer u een werkruimte Azure Machine Learning in de Azure Portal of met Azure CLI verwijdert, blijven de volgende resources bestaan. Ze blijven kosten maken totdat u ze verwijdert.

* Azure Container Registry
* Azure Block Blob Storage
* Key Vault
* Application Insights

Als u de werkruimte samen met deze afhankelijke resources wilt verwijderen, gebruikt u de SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Als u een Azure Kubernetes Service (AKS) in uw werkruimte maakt of als u rekenbronnen aan uw werkruimte koppelt, moet u deze afzonderlijk verwijderen in [Azure Portal](https://portal.azure.com).

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure-vooruitbetalingstegoed gebruiken met Azure Machine Learning

U kunt betalen voor Azure Machine Learning met uw Azure-vooruitbetalingstegoed (voorheen financiële toezegging genoemd). U kunt azure-vooruitbetaling echter niet gebruiken om te betalen voor kosten voor producten en services van derden, inclusief die van de Azure Marketplace.


## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en -resourcegroepen, zodat ze nuttig zijn als onderdeel van een algehele strategie voor kostenbewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in Azure als u meer granulariteit in uw bewaking wilt hebben. Filters zorgen ervoor dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie Groeps- en filteropties voor meer informatie over de filteropties wanneer u een budget [maakt.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kostengegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslagaccount. Dit is handig wanneer u of anderen extra gegevensanalyses moeten uitvoeren voor kosten. Een financieel team kan de gegevens bijvoorbeeld analyseren met Behulp van Excel of Power BI. U kunt uw kosten exporteren volgens een dagelijks, wekelijks of maandelijks schema en een aangepast datumbereik instellen. Het exporteren van kostengegevens is de aanbevolen manier om kostengegevenssets op te halen.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Andere manieren om de kosten voor uw Azure Machine Learning

Gebruik deze tips voor het bevatten van kosten voor uw machine learning rekenresources.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Een Azure Machine Learning gebruiken (AmlCompute)

Met gegevens die voortdurend veranderen, hebt u snelle en gestroomlijnde modeltraining en hertraining nodig om nauwkeurige modellen te onderhouden. Continue training gaat echter ten koste, met name voor Deep Learning-modellen op GPU's. 

Azure Machine Learning kunnen het beheerde Azure Machine Learning compute-cluster, ook wel AmlCompute genoemd, gebruiken. AmlCompute biedt ondersteuning voor tal van GPU- en CPU-opties. AmlCompute wordt intern namens uw abonnement gehost door Azure Machine Learning. Het biedt dezelfde beveiliging, naleving en governance op enterprise-niveau op Azure IaaS-cloudschaal.

Omdat deze rekenpools zich in de IaaS-infrastructuur van Azure kunnen nesten, kunt u uw training implementeren, schalen en beheren met dezelfde beveiligings- en nalevingsvereisten als de rest van uw infrastructuur.  Deze implementaties worden uitgevoerd in uw abonnement en voldoen aan uw governanceregels. Meer informatie over [Azure Machine Learning compute.](how-to-create-attach-compute-cluster.md)

### <a name="configure-training-clusters-for-autoscaling"></a>Trainingsclusters configureren voor automatisch schalen

Clusters automatisch schalen op basis van de vereisten van uw workload helpen uw kosten te verlagen, zodat u alleen gebruikt wat u nodig hebt.

AmlCompute-clusters zijn ontworpen om dynamisch te schalen op basis van uw workload. Het cluster kan worden geschaald tot het maximum aantal knooppunten dat u configureert. Wanneer elke run is voltooid, zal het cluster knooppunten vrijgeven en schalen naar het geconfigureerde minimale aantal knooppunten.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

U kunt ook de hoeveelheid tijd configureren dat het knooppunt inactief is voordat u omlaag schaalt. Niet-actieve tijd vóór omlaag schalen is standaard ingesteld op 120 seconden.

+ Als u minder iteratief experimenteert, vermindert u deze tijd om kosten te besparen.
+ Als u een zeer iteratieve dev/test-experimenten hebt uitgevoerd, moet u mogelijk de tijd verhogen zodat u niet betaalt voor constant omhoog en omlaag schalen na elke wijziging in uw trainingsscript of -omgeving.

AmlCompute-clusters kunnen worden geconfigureerd voor uw veranderende workloadvereisten in Azure Portal, met behulp van de [AmlCompute SDK-klasse](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), [AmlCompute CLI](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute), met de [REST API's](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Quota voor resources instellen

AmlCompute wordt geleverd met een [quotumconfiguratie (of limiet).](how-to-manage-quotas.md#azure-machine-learning-compute) Dit quotum is per VM-serie (bijvoorbeeld de Dv2-serie, NCv3-serie) en varieert per regio voor elk abonnement. Abonnementen beginnen met kleine standaardinstellingen om u op weg te helpen, maar gebruik deze instelling om te bepalen hoeveel Amlcompute-resources er beschikbaar zijn voor gebruik in uw abonnement. 

Configureer ook [het quotum op werkruimteniveau per VM-familie](how-to-manage-quotas.md#workspace-level-quotas)voor elke werkruimte binnen een abonnement. Als u dit doet, hebt u gedetailleerdere controle over de kosten die voor elke werkruimte kunnen worden in rekening brengen en kunt u bepaalde VM-families beperken. 

Als u quota wilt instellen op het niveau van de werkruimte, begint u in [Azure Portal](https://portal.azure.com).  Selecteer een werkruimte in uw abonnement en selecteer **Gebruik en quota** in het linkerdeelvenster. Selecteer vervolgens het tabblad **Quota configureren** om de quota te bekijken. U hebt bevoegdheden voor het abonnementsbereik nodig om het quotum in te stellen, omdat het een instelling is die van invloed is op meerdere werkruimten.

### <a name="set-run-autotermination-policies"></a>Beleid voor automatische beëindiging uitvoeren instellen 

In sommige gevallen moet u uw trainings runs configureren om de duur ervan te beperken of ze vroegtijdig te beëindigen. Wanneer u bijvoorbeeld de ingebouwde hyperparameterafstemming of geautomatiseerde Azure Machine Learning van de machine learning.

Hier zijn enkele opties die u hebt:
* Definieer een parameter met de naam in uw RunConfiguration om te bepalen hoe lang een run maximaal kan duren voor de rekenkracht die u kiest (lokale of `max_run_duration_seconds` externe cloudcomputing).
* Voor [het afstemmen van hyperparameters](how-to-tune-hyperparameters.md#early-termination)definieert u een beleid voor vroegtijdige beëindiging van een Bandit-beleid, een mediaan-stopbeleid of een selectiebeleid voor afbrekingen. Als u hyperparameters verder wilt controleren, gebruikt u parameters zoals `max_total_runs` of `max_duration_minutes` .
* Voor [automatische machine learning](how-to-configure-auto-train.md#exit)stelt u vergelijkbare beëindigingsbeleidsregels in met behulp van de vlag  `enable_early_stopping` . Gebruik ook eigenschappen zoals `iteration_timeout_minutes` en om de maximale duur van een run of voor het hele experiment te `experiment_timeout_minutes` bepalen.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Virtuele VM's met lage prioriteit gebruiken

Met Azure kunt u overtollige niet-beschikbare capaciteit gebruiken als Low-Priority virtuele machines in virtuele-machineschaalsets, Batch en de Machine Learning service. Deze toewijzingen zijn preventief, maar hebben een lagere prijs in vergelijking met toegewezen VM's. Over het algemeen raden we u aan om Low-Priority te gebruiken voor Batch-workloads. U moet ze ook gebruiken wanneer onderbrekingen kunnen worden hersteld via opnieuwubmits (voor Batch-deferencing) of via opnieuw opstarten (voor deep learning-training met controlepunten).

Low-Priority VM's hebben één quotum dat is gescheiden van de toegewezen quotumwaarde, die per VM-familie is. Meer [informatie over AmlCompute-quota.](how-to-manage-quotas.md)

 Low-Priority VM's werken niet voor reken-exemplaren, omdat ze interactieve notebookervaringen moeten ondersteunen.

### <a name="use-reserved-instances"></a>Gereserveerde instanties gebruiken

Een andere manier om geld te besparen op rekenbronnen is een gereserveerde VM-instantie van Azure. Met deze aanbieding gaat u een termijn van één of drie jaar na. Deze kortingen variëren tot 72% van de betalen per gebruik-prijzen en worden rechtstreeks toegepast op uw maandelijkse Azure-factuur.

Azure Machine Learning Compute ondersteunt inherent gereserveerde instanties. Als u een gereserveerde instantie van één of drie jaar aanschaft, wordt automatisch korting toegepast op uw Azure Machine Learning beheerde rekenkracht.


## <a name="next-steps"></a>Volgende stappen

- Meer [informatie over het optimaliseren van uw cloudinvestering met Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het voorkomen [van onverwachte kosten.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de begeleide cursus.
