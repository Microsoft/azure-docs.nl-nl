---
title: Training maken & reken processen implementeren (Studio)
titleSuffix: Azure Machine Learning
description: Studio gebruiken voor het maken van trainings-en implementatie-reken resources (reken doelen) voor machine learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: 4194c71823e1affde1dcae47fd7e64668b57c0cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149355"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Reken doelen voor model training en implementatie in Azure Machine Learning Studio maken

In dit artikel leert u hoe u Compute-doelen kunt maken en beheren in azure machine Studio.  U kunt ook Compute-doelen maken en beheren met:

* Azure Machine Learning Learning SDK of CLI-extensie voor Azure Machine Learning
  * [Reken instantie](how-to-create-manage-compute-instance.md)
  * [Reken cluster](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes service-cluster](how-to-create-attach-kubernetes.md)
  * [Andere reken bronnen](how-to-attach-compute-targets.md)
* De [VS code-extensie](how-to-manage-resources-vscode.md#compute-clusters) voor Azure machine learning.


## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree)
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Wat is een reken doel? 

Met Azure Machine Learning kunt u uw model trainen op diverse resources of omgevingen, die gezamenlijk worden aangeduid als [__Compute-doelen__](concept-azure-machine-learning-architecture.md#compute-targets). Een compute-doel kan een lokale machine of een Cloud resource zijn, zoals een Azure Machine Learning compute, Azure HDInsight of een externe virtuele machine.  U kunt ook reken doelen voor model implementatie maken, zoals wordt beschreven in [' waar en hoe u uw modellen implementeert '](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Reken doelen weer geven

Als u alle reken doelen voor uw werk ruimte wilt zien, gebruikt u de volgende stappen:

1. Navigeer naar [Azure machine learning Studio](https://ml.azure.com).
 
1. Selecteer onder __beheren__ de optie __reken kracht__.

1. Selecteer de tabbladen bovenaan om elk type Compute-doel weer te geven.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Lijst met Compute-doelen weer geven":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Reken doel maken

Volg de vorige stappen om de lijst met Compute-doelen weer te geven. Gebruik vervolgens de volgende stappen om een compute-doel te maken:

1. Selecteer het tabblad bovenaan dat overeenkomt met het type Compute dat u gaat maken.

1. Als u geen Compute-doelen hebt, selecteert u in het midden van de pagina  **maken** .
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Reken doel maken":::

1. Als u een lijst met reken resources ziet, selecteert u **+ Nieuw** boven de lijst.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Selecteer Nieuw":::


1. Vul het formulier in voor het reken type:

  * [Reken instantie](#compute-instance)
  * [Reken clusters](#amlcompute)
  * [Clusters afleiding](#inference-clusters)
  * [Gekoppelde compute](#attached-compute)

1. Selecteer __Maken__.

1. Bekijk de status van de maak bewerking door het doel van de berekening te selecteren in de lijst:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Reken status weer geven uit een lijst":::


### <a name="compute-instance"></a>Rekenproces

Gebruik de [bovenstaande stappen](#portal-create) om het reken exemplaar te maken.  Vul het formulier vervolgens als volgt in:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Een nieuw reken exemplaar maken":::


|Veld  |Beschrijving  |
|---------|---------|
|Naam berekening     |  <li>De naam is vereist en moet tussen de 3 en 24 tekens lang zijn.</li><li>Geldige tekens zijn onder andere hoofd letters, cijfers en het  **-** teken.</li><li>De naam moet beginnen met een letter</li><li>De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest, niet uniek is</li><li>Als het **-**  teken wordt gebruikt, moet dit worden gevolgd door ten minste één letter later in de naam</li>     |
|Type virtuele machine |  Kies CPU of GPU. Dit type kan niet worden gewijzigd nadat het is gemaakt     |
|Grootte van de virtuele machine     |  Ondersteunde grootten voor virtuele machines kunnen worden beperkt in uw regio. De [beschikbaarheids lijst](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) controleren     |
|SSH-toegang inschakelen/uitschakelen     |   SSH-toegang is standaard uitgeschakeld.  SSH-toegang kan niet. gewijzigd na het maken. Zorg ervoor dat u toegang inschakelt als u van plan bent om interactief fouten op te lossen met [versus externe code](how-to-set-up-vs-code-remote.md)   |
|Geavanceerde instellingen     |  Optioneel. Configureer een virtueel netwerk. Geef de **resource groep**, het **virtuele netwerk** en het **subnet** op om het reken exemplaar te maken binnen een Azure-Virtual Network (vnet). Zie deze [netwerk vereisten](./how-to-secure-training-vnet.md) voor vnet voor meer informatie.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Reken clusters

Maak een berekenings cluster met één of meerdere knoop punten voor uw training, het afleiden van batch demijnen of de werk belasting van het onderwijs. Gebruik de [bovenstaande stappen](#portal-create) om het berekenings cluster te maken.  Vul het formulier vervolgens als volgt in:


|Veld  |Beschrijving  |
|---------|---------|
|Naam berekening     |  <li>De naam is vereist en moet tussen de 3 en 24 tekens lang zijn.</li><li>Geldige tekens zijn onder andere hoofd letters, cijfers en het  **-** teken.</li><li>De naam moet beginnen met een letter</li><li>De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest, niet uniek is</li><li>Als het **-**  teken wordt gebruikt, moet dit worden gevolgd door ten minste één letter later in de naam</li>     |
|Type virtuele machine |  Kies CPU of GPU. Dit type kan niet worden gewijzigd nadat het is gemaakt     |
|Prioriteit van virtuele machine | Kies een **toegewezen** of **lage prioriteit**.  Virtuele machines met lage prioriteit zijn goed koper, maar garanderen niet de reken knooppunten. De taak kan worden afgebroken.
|Grootte van de virtuele machine     |  Ondersteunde grootten voor virtuele machines kunnen worden beperkt in uw regio. De [beschikbaarheids lijst](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) controleren     |
|Minimum aantal knooppunten | Het minimum aantal knoop punten dat u wilt inrichten. Als u een toegewezen aantal knoop punten wilt, stelt u dit aantal hier in. Bespaar geld door het minimum in te stellen op 0, zodat u niet betaalt voor knoop punten wanneer het cluster niet actief is. |
|Maximum aantal knoop punten | Het maximum aantal knoop punten dat u wilt inrichten. De compute wordt automatisch geschaald naar een maximum van dit aantal knoop punten wanneer een taak wordt verzonden. |
|Geavanceerde instellingen     |  Optioneel. Configureer een virtueel netwerk. Geef de **resource groep**, het **virtuele netwerk** en het **subnet** op om het reken exemplaar te maken binnen een Azure-Virtual Network (vnet). Zie deze [netwerk vereisten](./how-to-secure-training-vnet.md) voor vnet voor meer informatie.   Ook [beheerde identiteiten](#managed-identity) koppelen om toegang tot resources te verlenen     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Beheerde identiteit instellen

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Tijdens het maken van het cluster of bij het bewerken van de details van het reken cluster, in de **Geavanceerde instellingen**, kunt u **een beheerde identiteit toewijzen** en een door het systeem toegewezen identiteit opgeven.

#### <a name="managed-identity-usage"></a>Gebruik van beheerde identiteit

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Clusters afleiding

> [!IMPORTANT]
> Het gebruik van de Azure Kubernetes-service met Azure Machine Learning heeft meerdere configuratie opties. Voor sommige scenario's, zoals netwerken, zijn aanvullende instellingen en configuratie vereist. Zie [een Azure Kubernetes-service cluster maken en koppelen](how-to-create-attach-kubernetes.md)voor meer informatie over het gebruik van AKS met Azure ml.

Maak of koppel een Azure Kubernetes service-cluster (AKS) voor grootschalige deinterferentie. Gebruik de [bovenstaande stappen](#portal-create) om het AKS-cluster te maken.  Vul het formulier vervolgens als volgt in:


|Veld  |Beschrijving  |
|---------|---------|
|Naam berekening     |  <li>De naam is vereist. De naam moet tussen 2 en 16 tekens lang zijn. </li><li>Geldige tekens zijn onder andere hoofd letters, cijfers en het  **-** teken.</li><li>De naam moet beginnen met een letter</li><li>De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest, niet uniek is</li><li>Als het **-**  teken wordt gebruikt, moet dit worden gevolgd door ten minste één letter later in de naam</li>     |
|Kubernetes-service | Selecteer **nieuwe maken** en vul de rest van het formulier in.  Of selecteer **bestaande gebruiken** en selecteer vervolgens een bestaand AKS-cluster in uw abonnement.
|Region |  Selecteer de regio waar het cluster wordt gemaakt |
|Grootte van de virtuele machine     |  Ondersteunde grootten voor virtuele machines kunnen worden beperkt in uw regio. De [beschikbaarheids lijst](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) controleren     |
|Cluster doel  | Selecteer **productie** of **dev-test** |
|Aantal knooppunten | Het aantal knoop punten vermenigvuldigd met het aantal kernen van de virtuele machine (Vcpu's) moet groter zijn dan of gelijk zijn aan 12. |
| Netwerkconfiguratie | Selecteer **Geavanceerd** om de compute in een bestaand virtueel netwerk te maken. Voor meer informatie over AKS in een virtueel netwerk, Zie [netwerk isolatie tijdens de training en demijnen met persoonlijke eind punten en virtuele netwerken](./how-to-secure-inferencing-vnet.md). |
| SSL-configuratie inschakelen | Gebruik deze om het SSL-certificaat op de compute te configureren |

### <a name="attached-compute"></a>Gekoppelde compute

Als u Compute-doelen wilt gebruiken die buiten de Azure Machine Learning-werk ruimte zijn gemaakt, moet u ze koppelen. Als u een reken doel koppelt, wordt het beschikbaar voor uw werk ruimte.  Gebruik **attached Compute** om een reken doel voor **training** te koppelen.  Gebruik **clusters** gebruiken om een AKS-cluster te **koppelen voor** demijnen.

Gebruik de [bovenstaande stappen](#portal-create) om een reken proces te koppelen.  Vul het formulier vervolgens als volgt in:

1. Voer een naam in voor het berekenings doel. 
1. Selecteer het type berekening dat u wilt koppelen. Niet alle reken typen kunnen worden bijgevoegd vanuit Azure Machine Learning Studio. De berekenings typen die momenteel aan de training kunnen worden gekoppeld, zijn:
    * Een virtuele machine van Azure (om een Data Science Virtual Machine toe te voegen)
    * Azure Databricks (voor gebruik in machine learning pijp lijnen)
    * Azure Data Lake Analytics (voor gebruik in machine learning pijp lijnen)
    * Azure HDInsight

1. Vul het formulier in en geef waarden op voor de vereiste eigenschappen.

    > [!NOTE]
    > Micro soft raadt u aan om SSH-sleutels te gebruiken, die veiliger zijn dan wacht woorden. Wacht woorden zijn gevoelig voor beveiligings aanvallen. SSH-sleutels zijn afhankelijk van cryptografische hand tekeningen. Raadpleeg de volgende documenten voor informatie over het maken van SSH-sleutels voor gebruik met Azure Virtual Machines:
    >
    > * [SSH-sleutels maken en gebruiken in Linux of macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [SSH-sleutels maken en gebruiken in Windows](../virtual-machines/linux/ssh-from-windows.md)

1. Selecteer __koppelen__. 


## <a name="next-steps"></a>Volgende stappen

Nadat een doel is gemaakt en aan uw werk ruimte is gekoppeld, gebruikt u dit in uw [uitvoerings configuratie](how-to-set-up-training-targets.md) met een- `ComputeTarget` object:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Gebruik de compute-resource om [een trainings uitvoering te verzenden](how-to-set-up-training-targets.md).
* [Zelf studie: een model trainen](tutorial-train-models-with-aml.md) maakt gebruik van een beheerd Compute-doel om een model te trainen.
* Meer informatie over hoe u [Hyper parameters efficiënt kunt afstemmen](how-to-tune-hyperparameters.md) om betere modellen te bouwen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* [Azure Machine Learning gebruiken met virtuele netwerken van Azure](./how-to-network-security-overview.md)