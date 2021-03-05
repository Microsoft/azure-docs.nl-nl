---
title: Kosten voorspellen en beheren
titleSuffix: Azure Machine Learning
description: Plan en beheer kosten voor Azure Machine Learning met kosten analyse in Azure Portal. Meer informatie over tips voor het besparen van kosten voor het verlagen van de kosten bij het bouwen van ML-modellen.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: be8b11b6ddf715e5d6226372e8d03b42dec5fc7d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215983"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Kosten plannen en beheren voor Azure Machine Learning

In dit artikel wordt beschreven hoe u de kosten voor Azure Machine Learning plant en beheert. Eerst gebruikt u de prijs calculator van Azure om te helpen bij het plannen van kosten voordat u resources toevoegt. Wanneer u de Azure-resources toevoegt, controleert u vervolgens de geschatte kosten. Gebruik ten slotte kostenbesparende tips wanneer u uw model traint met beheerde Azure Machine Learning compute-clusters.

Nadat u Azure Machine Learning resources hebt gebruikt, gebruikt u de functies voor kosten beheer om budgetten in te stellen en kosten te bewaken. Bekijk ook de geraamde kosten en Identificeer uitgaven trends om gebieden te identificeren waar u mogelijk wilt handelen.

Houd er rekening mee dat de kosten voor Azure Machine Learning slechts een deel van de maandelijkse kosten in uw Azure-factuur zijn. Als u andere Azure-Services gebruikt, wordt u gefactureerd voor alle Azure-Services en-resources die worden gebruikt in uw Azure-abonnement, inclusief de services van derden. In dit artikel wordt uitgelegd hoe u de kosten voor Azure Machine Learning plant en beheert. Nadat u vertrouwd bent met het beheren van de kosten voor Azure Machine Learning, moet u vergelijk bare methoden Toep assen om de kosten te beheren voor alle Azure-Services die in uw abonnement worden gebruikt.

Wanneer u uw machine learning modellen traint, gebruikt u beheerde Azure Machine Learning compute-clusters om te profiteren van meer tips voor het besparen van kosten:

* Uw trainings clusters configureren voor automatisch schalen
* Quota's instellen voor uw abonnement en werk ruimten
* Beëindigings beleid instellen voor uw trainings uitvoering
* Virtuele machines met lage prioriteit (VM) gebruiken
* Een voor Azure gereserveerde VM-instantie gebruiken

## <a name="prerequisites"></a>Vereisten

Kostenanalyse biedt ondersteuning voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Als u kostengegevens wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig. 

Zie [Toegang tot gegevens toewijzen](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) voor meer informatie over het toewijzen van toegang tot de gegevens in Azure Cost Management.

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Kosten schatten vóór het gebruik van Azure Machine Learning

Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) om de kosten te schatten voordat u de resources in een Azure machine learning-account maakt. Selecteer aan de linkerkant **AI + machine learning** en selecteer vervolgens **Azure machine learning** om te beginnen.  

De volgende scherm afbeelding toont de kosten raming door gebruik te maken van de Calculator:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Kosten raming in azure Calculator":::

Wanneer u nieuwe resources aan uw werk ruimte toevoegt, keert u terug naar deze reken machine en voegt u hier dezelfde resource toe om uw kosten ramingen bij te werken.

Zie [Azure machine learning prijzen](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie.

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>Het volledige facturerings model voor Azure Machine Learning begrijpen

Azure Machine Learning wordt uitgevoerd op een Azure-infra structuur, waarmee de kosten samen met Azure Machine Learning worden samengevoegd wanneer u de nieuwe resource implementeert. Het is belang rijk om te begrijpen dat extra infra structuur kosten kan toenemen. U moet deze kosten beheren wanneer u wijzigingen aanbrengt in geïmplementeerde resources. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Kosten die normaal gesp roken toenemen met Azure Machine Learning

Wanneer u resources voor een Azure Machine Learning-werk ruimte maakt, worden er ook resources voor andere Azure-Services gemaakt. Dit zijn:

* [Azure container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Basis account
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (algemeen gebruik v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Kosten kunnen toenemen na het verwijderen van resources

Wanneer u een Azure Machine Learning-werk ruimte in de Azure Portal of met Azure CLI verwijdert, blijven de volgende resources bestaan. Ze blijven kosten voor samen voegen totdat u ze verwijdert.

* Azure Container Registry
* Blob Storage Azure-blok kering
* Key Vault
* Application Insights

Als u de werk ruimte samen met deze afhankelijke resources wilt verwijderen, gebruikt u de SDK:

```python
ws.delete(delete_dependent_resources=True)
```

Als u de Azure Kubernetes-service (AKS) in uw werk ruimte maakt, of als u reken resources aan uw werk ruimte koppelt, moet u deze afzonderlijk in [Azure Portal](https://portal.azure.com)verwijderen.

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure-vooruitbetalings tegoed gebruiken met Azure Machine Learning

U kunt betalen voor Azure Machine Learning kosten met uw Azure-voor uitbetaling (voorheen monetaire toezeg ging genoemd)-tegoed. U kunt Azure-voor uitbetaling echter niet gebruiken om te betalen voor de kosten van producten en services van derden, inclusief die van de Azure Marketplace.


## <a name="create-budgets"></a>Budgetten maken

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) maken om kosten te beheren en [waarschuwingen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven. Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten. Budgetten en waarschuwingen worden gemaakt voor Azure-abonnementen en-resource groepen, dus zijn ze nuttig als onderdeel van een strategie voor de kosten bewaking. 

Budgetten kunnen worden gemaakt met filters voor specifieke resources of services in azure als u meer granulariteit in uw bewaking wilt. Met filters kunt u ervoor zorgen dat u niet per ongeluk nieuwe resources maakt die u extra geld kosten. Zie [groeps-en filter opties](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)voor meer informatie over de filter opties wanneer u een budget maakt.

## <a name="export-cost-data"></a>Kostengegevens exporteren

U kunt ook [uw kosten gegevens exporteren](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) naar een opslag account. Dit is handig wanneer u of anderen extra gegevens analyse voor kosten moeten uitvoeren. Een financiële teams kunnen de gegevens bijvoorbeeld analyseren met Excel of Power BI. U kunt uw kosten per dag, wekelijks of maandelijks exporteren en een aangepast datum bereik instellen. Het exporteren van kosten gegevens is de aanbevolen manier om kosten sets op te halen.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Andere manieren om de kosten voor Azure Machine Learning te beheren en te verlagen

Gebruik deze tips voor het gebruiken van kosten op uw machine learning reken resources.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning Compute-Cluster (AmlCompute) gebruiken

Met gegevens die voortdurend worden gewijzigd, moet u snelle en gestroomlijnde model training en retraining volgen om nauw keurige modellen te onderhouden. Doorlopende training is echter een kost prijs, met name voor diepe leer modellen op Gpu's. 

Azure Machine Learning gebruikers kunnen gebruikmaken van het beheerde Azure Machine Learning Compute-Cluster, ook wel AmlCompute genoemd. AmlCompute ondersteunt diverse GPU-en CPU-opties. De AmlCompute wordt intern gehost namens uw abonnement door Azure Machine Learning. Het biedt dezelfde zakelijke beveiliging, naleving en governance op Azure IaaS-Cloud schaal.

Omdat deze reken groepen zich bevinden in de IaaS-infra structuur van Azure, kunt u uw training implementeren, schalen en beheren met dezelfde beveiligings-en nalevings vereisten als de rest van uw infra structuur.  Deze implementaties worden uitgevoerd in uw abonnement en voldoen aan de regels voor beheer. Meer informatie over [Azure machine learning Compute](how-to-create-attach-compute-cluster.md).

### <a name="configure-training-clusters-for-autoscaling"></a>Trainings clusters configureren voor automatisch schalen

Door clusters automatisch te schalen op basis van de vereisten van uw workload, kunt u uw kosten verlagen, zodat u alleen de gewenste functies gebruikt.

AmlCompute-clusters zijn zodanig ontworpen dat ze dynamisch kunnen worden geschaald op basis van uw werk belasting. Het cluster kan worden geschaald naar het maximum aantal knoop punten dat u configureert. Wanneer de uitvoering is voltooid, worden knoop punten door het cluster vrijgegeven en geschaald naar het geconfigureerde minimum aantal knoop punten.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

U kunt ook configureren hoe lang het knoop punt inactief moet zijn voordat omlaag wordt geschaald. Standaard wordt de niet-actieve tijd vóór de schaal ingesteld op 120 seconden.

+ Als u minder iteratieve experimenten uitvoert, moet u deze tijd beperken om kosten te besparen.
+ Als u een sterk iteratief dev/test-experiment wilt uitvoeren, moet u mogelijk de tijd verhogen zodat u niet betaalt voor constante schaling omhoog en omlaag na elke wijziging in uw trainings script of-omgeving.

AmlCompute-clusters kunnen worden geconfigureerd voor uw gewijzigde werkbelasting vereisten in Azure Portal, met behulp van de [AMLCOMPUTE SDK-klasse](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) [AmlCompute cli](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute), met de [rest api's](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable).

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Quota instellen voor resources

AmlCompute wordt geleverd met een [quotum configuratie (of limiet)](how-to-manage-quotas.md#azure-machine-learning-compute). Dit quotum is van de VM-serie (bijvoorbeeld dv2-serie, NCv3-serie) en varieert per regio voor elk abonnement. Abonnementen beginnen met kleine standaard waarden om aan de slag te gaan, maar u kunt deze instelling gebruiken om te bepalen hoeveel Amlcompute resources beschikbaar zijn in uw abonnement. 

Configureer ook het [quotum van het werkruimte niveau per VM-serie](how-to-manage-quotas.md#workspace-level-quotas)voor elke werk ruimte in een abonnement. Als u dit doet, kunt u meer nauw keurigere controle over de kosten die elke werk ruimte mogelijk maakt en bepaalde VM-families beperken. 

Als u quota's op het niveau van de werk ruimte wilt instellen, begint u in de [Azure Portal](https://portal.azure.com).  Selecteer een werk ruimte in uw abonnement en selecteer **gebruik en quota's** in het linkerdeel venster. Selecteer vervolgens het tabblad **Quota's configureren** om de quota's weer te geven. U hebt bevoegdheden nodig voor het abonnements bereik om het quotum in te stellen, omdat het een instelling is die van invloed is op meerdere werk ruimten.

### <a name="set-run-autotermination-policies"></a>Beleid voor het uitvoeren van autoterminaters instellen 

In sommige gevallen moet u uw trainings uitvoeringen configureren om de duur te beperken of ze vroegtijdig te beëindigen. Als u bijvoorbeeld gebruikmaakt van de ingebouwde afstemming-afstemming of geautomatiseerde machine learning van Azure Machine Learning.

Hier volgen enkele opties die u hebt:
* Definieer een para meter `max_run_duration_seconds` in uw RunConfiguration om de maximale duur te bepalen dat een uitvoering kan worden uitgebreid naar de compute die u kiest (lokale of externe Cloud Compute).
* Voor [afstemming tuning](how-to-tune-hyperparameters.md#early-termination)definieert u een beleid voor vroegtijdige beëindiging van een Bandit-beleid, een mediaan stop beleid of een selectie beleid voor afkap ping. Gebruik para meters zoals of als u afstemming-sweeps verder wilt beheren `max_total_runs` `max_duration_minutes` .
* Stel voor [automatische machine learning](how-to-configure-auto-train.md#exit)soort gelijke afsluitings beleid in met behulp van de  `enable_early_stopping` vlag. Gebruik ook eigenschappen zoals `iteration_timeout_minutes` en `experiment_timeout_minutes` om de maximale duur van een uitvoering of voor het hele experiment te bepalen.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Virtuele machines met lage prioriteit gebruiken

Met Azure kunt u gebruikmaken van overtollige ongebruikte capaciteit als Low-Priority Vm's voor schaal sets voor virtuele machines, batch en de Machine Learning-service. Deze toewijzingen zijn pre-emptible, maar worden geleverd tegen een gereduceerde prijs vergeleken met toegewezen Vm's. Over het algemeen kunt u het beste Low-Priority Vm's gebruiken voor batch-workloads. U moet ze ook gebruiken waar onderbrekingen kunnen worden hersteld via opnieuw verzenden (voor batch destorie) of via het opnieuw opstarten (voor diepe trainingen met controle punten).

Low-Priority-Vm's hebben één quotum gescheiden van de toegewezen quota waarde, die wordt door de VM-serie. Meer informatie [over AmlCompute-quota's](how-to-manage-quotas.md).

 Low-Priority-Vm's werken niet voor reken instanties, omdat ze interactieve notitieblok ervaringen moeten ondersteunen.

### <a name="use-reserved-instances"></a>Gereserveerde instanties gebruiken

Een andere manier om geld te besparen op reken resources is een door Azure gereserveerd VM-exemplaar. Met deze aanbieding gaat u naar een periode van één of drie jaar. Deze kortingen variëren van Maxi maal 72% van de betalen naar gebruik-prijzen en worden direct toegepast op uw maandelijkse Azure-factuur.

Azure Machine Learning Compute ondersteunt gereserveerde instanties inherent. Als u een gereserveerde instantie van één jaar of drie jaar aanschaft, wordt automatisch korting toegepast op uw Azure Machine Learning beheerde reken kracht.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over hoe u uw investering in de Cloud optimaliseert met Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over het beheren van kosten met [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Meer informatie over hoe u [onverwachte kosten kunt voor komen](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Neem de [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) begeleide training door.