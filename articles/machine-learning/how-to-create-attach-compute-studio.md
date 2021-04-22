---
title: Training maken & computes implementeren (studio)
titleSuffix: Azure Machine Learning
description: Studio gebruiken om rekenbronnen voor training en implementatie (rekendoelen) te maken voor machine learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.openlocfilehash: 84db002e35f88bcee1e59a22b678f2a76047b5ef
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885826"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Rekendoelen maken voor modeltraining en -implementatie in Azure Machine Learning-studio

In dit artikel leert u hoe u rekendoelen maakt en beheert in Azure Machine Studio.  U kunt ook rekendoelen maken en beheren met:

* Azure Machine Learning Learning SDK of CLI-extensie voor Azure Machine Learning
  * [Reken-exemplaar](how-to-create-manage-compute-instance.md)
  * [Rekencluster](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes Service cluster](how-to-create-attach-kubernetes.md)
  * [Andere rekenbronnen](how-to-attach-compute-targets.md)
* De [VS Code-extensie](how-to-manage-resources-vscode.md#compute-clusters) voor Azure Machine Learning.


## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree) vandaag nog
* Een [Azure Machine Learning werkruimte](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Wat is een rekendoel? 

Met Azure Machine Learning kunt u uw model trainen op verschillende resources of omgevingen, gezamenlijk [__rekendoelen genoemd.__](concept-azure-machine-learning-architecture.md#compute-targets) Een rekendoel kan een lokale computer of een cloudresource zijn, zoals een Azure Machine Learning Compute, Azure HDInsight of een externe virtuele machine.  U kunt ook rekendoelen maken voor modelimplementatie, zoals beschreven in [Waar en hoe u uw modellen implementeert.](how-to-deploy-and-where.md)

## <a name="view-compute-targets"></a><a id="portal-view"></a>Rekendoelen weergeven

Als u alle rekendoelen voor uw werkruimte wilt zien, gebruikt u de volgende stappen:

1. Navigeer [naar Azure Machine Learning-studio](https://ml.azure.com).
 
1. Selecteer __compute onder__ __Beheren.__

1. Selecteer tabbladen bovenaan om elk type rekendoel weer te geven.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Lijst met rekendoelen weergeven":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Rekendoel maken

Volg de vorige stappen om de lijst met rekendoelen te bekijken. Gebruik vervolgens deze stappen om een rekendoel te maken:

1. Selecteer het tabblad bovenaan dat overeenkomt met het type rekenkracht dat u maakt.

1. Als u geen rekendoelen hebt, selecteert  **u** Maken in het midden van de pagina.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Rekendoel maken":::

1. Als u een lijst met rekenbronnen ziet, selecteert u **+Nieuw** boven de lijst.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Nieuwe selecteren":::


1. Vul het formulier in voor uw rekentype:

  * [Reken-exemplaar](#compute-instance)
  * [Rekenclusters](#amlcompute)
  * [De deferentieclusters](#inference-clusters)
  * [Gekoppelde rekenkracht](#attached-compute)

1. Selecteer __Maken__.

1. Bekijk de status van de maakbewerking door het rekendoel in de lijst te selecteren:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Rekenstatus van een lijst weergeven":::


### <a name="compute-instance"></a>Rekenproces

Gebruik de [bovenstaande stappen om](#portal-create) het reken-exemplaar te maken.  Vul het formulier vervolgens als volgt in:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Een nieuw reken-exemplaar maken":::


|Veld  |Description  |
|---------|---------|
|Naam berekening     |  <li>De naam is vereist en moet tussen de 3 en 24 tekens lang zijn.</li><li>Geldige tekens zijn hoofdletters en kleine letters, cijfers en het  **-** teken.</li><li>Naam moet beginnen met een letter</li><li>De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest niet uniek is</li><li>Als het teken wordt gebruikt, moet dit worden gevolgd door ten **-**  minste één letter later in de naam</li>     |
|Type virtuele machine |  Kies CPU of GPU. Dit type kan niet worden gewijzigd na het maken     |
|Grootte van de virtuele machine     |  Ondersteunde grootten van virtuele machines zijn mogelijk beperkt in uw regio. De [beschikbaarheidslijst controleren](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|SSH-toegang in-/uitschakelen     |   SSH-toegang is standaard uitgeschakeld.  SSH-toegang is niet mogelijk. gewijzigd na het maken. Zorg ervoor dat u toegang inschakelen als u van plan bent interactief fouten op te sporen met [VS Code Remote](how-to-set-up-vs-code-remote.md)   |
|Geavanceerde instellingen     |  Optioneel. Configureer een virtueel netwerk. Geef de **resourcegroep,** **het virtuele netwerk** en het **subnet op** om het reken-exemplaar binnen een Azure Virtual Network (vnet) te maken. Zie deze netwerkvereisten voor [vnet](./how-to-secure-training-vnet.md) voor meer informatie.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Rekenclusters

Maak een rekencluster met één of meerdere knooppunt voor uw trainings-, batchdeferencing- of bekrachtigde leerworkloads. Gebruik de [bovenstaande stappen om](#portal-create) het rekencluster te maken.  Vul het formulier als volgt in:


|Veld  |Description  |
|---------|---------|
|Naam berekening     |  <li>De naam is vereist en moet tussen de 3 en 24 tekens lang zijn.</li><li>Geldige tekens zijn hoofdletters en kleine letters, cijfers en het  **-** teken.</li><li>De naam moet beginnen met een letter</li><li>De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest niet uniek is</li><li>Als het teken wordt gebruikt, moet dit worden gevolgd door ten **-**  minste één letter later in de naam</li>     |
|Type virtuele machine |  Kies CPU of GPU. Dit type kan niet worden gewijzigd na het maken     |
|Prioriteit van virtuele machine | Kies **Toegewezen** of **Lage prioriteit.**  Virtuele machines met lage prioriteit zijn goedkoper, maar bieden geen garantie voor de rekenknooppunten. Uw taak is mogelijk niet meer nodig.
|Grootte van de virtuele machine     |  Ondersteunde grootten van virtuele machines zijn mogelijk beperkt in uw regio. De [beschikbaarheidslijst controleren](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Minimum aantal knooppunten | Minimum aantal knooppunten dat u wilt inrichten. Als u een toegewezen aantal knooppunten wilt, stelt u dit aantal hier in. Bespaar geld door het minimum in te stellen op 0, zodat u niet betaalt voor knooppunten wanneer het cluster inactief is. |
|Maximum aantal knooppunten | Maximum aantal knooppunten dat u wilt inrichten. De rekenkracht wordt automatisch geschaald naar een maximum van dit aantal knooppunt wanneer een taak wordt verzonden. |
|Geavanceerde instellingen     |  Optioneel. Configureer een virtueel netwerk. Geef de **resourcegroep,** **het virtuele netwerk** en het **subnet op** om het reken-exemplaar te maken binnen een Azure Virtual Network (vnet). Zie deze netwerkvereisten voor [vnet](./how-to-secure-training-vnet.md) voor meer informatie.   Voeg ook [beheerde identiteiten toe om](#managed-identity) toegang te verlenen tot resources     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Beheerde identiteit instellen

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Schakel tijdens het maken van het cluster of bij  het bewerken van de details van het rekencluster in de geavanceerde instellingen de schakelknop Een beheerde identiteit toewijzen in en geef een door het systeem toegewezen identiteit of door de gebruiker toegewezen identiteit op.

#### <a name="managed-identity-usage"></a>Gebruik van beheerde identiteit

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>De deferentieclusters

> [!IMPORTANT]
> Het Azure Kubernetes Service met Azure Machine Learning heeft meerdere configuratieopties. Voor sommige scenario's, zoals netwerken, zijn aanvullende instellingen en configuratie vereist. Zie Een cluster maken en koppelen voor meer informatie over het gebruik van AKS [Azure Kubernetes Service Azure ML.](how-to-create-attach-kubernetes.md)

Maak of koppel een AKS Azure Kubernetes Service cluster (AKS) voor grootschalige deferencing. Gebruik de [bovenstaande stappen om](#portal-create) het AKS-cluster te maken.  Vul het formulier vervolgens als volgt in:


|Veld  |Description  |
|---------|---------|
|Naam berekening     |  <li>Naam is vereist. De naam moet tussen 2 en 16 tekens lang zijn. </li><li>Geldige tekens zijn hoofdletters en kleine letters, cijfers en het  **-** teken.</li><li>Naam moet beginnen met een letter</li><li>De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest niet uniek is</li><li>Als het teken wordt gebruikt, moet dit worden gevolgd door ten **-**  minste één letter later in de naam</li>     |
|Kubernetes-service | Selecteer **Nieuwe maken** en vul de rest van het formulier in.  Of selecteer **Bestaande gebruiken** en selecteer vervolgens een bestaand AKS-cluster in uw abonnement.
|Region |  Selecteer de regio waar het cluster wordt gemaakt |
|Grootte van de virtuele machine     |  Ondersteunde grootten van virtuele machines zijn mogelijk beperkt in uw regio. De [beschikbaarheidslijst controleren](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Clusterdoel  | Selecteer **Productie** of **Dev-test** |
|Aantal knooppunten | Het aantal knooppunten vermenigvuldigd met het aantal kernen (vCCPUs) van de virtuele machine moet groter zijn dan of gelijk zijn aan 12. |
| Netwerkconfiguratie | Selecteer **Geavanceerd** om de rekenkracht binnen een bestaand virtueel netwerk te maken. Zie Netwerkisolatie tijdens het trainen en afleiden met privé-eindpunten en virtuele netwerken voor meer informatie over AKS in [een virtueel netwerk.](./how-to-secure-inferencing-vnet.md) |
| SSL-configuratie inschakelen | Gebruik dit om een SSL-certificaat te configureren op de rekenkracht |

### <a name="attached-compute"></a>Gekoppelde rekenkracht

Als u rekendoelen wilt gebruiken die buiten Azure Machine Learning werkruimte zijn gemaakt, moet u deze koppelen. Als u een rekendoel koppelt, is dit beschikbaar voor uw werkruimte.  Gebruik **Gekoppelde rekenkracht om** een rekendoel te koppelen voor **het trainen van**.  Gebruik **de deferentieclusters** om een AKS-cluster te koppelen voor **de deferentie.**

Gebruik de [bovenstaande stappen om](#portal-create) een rekenkracht te koppelen.  Vul het formulier vervolgens als volgt in:

1. Voer een naam in voor het rekendoel. 
1. Selecteer het type rekenkracht dat u wilt koppelen. Niet alle rekentypen kunnen worden gekoppeld vanuit Azure Machine Learning-studio. De rekentypen die momenteel kunnen worden gekoppeld voor training zijn onder andere:
    * Een virtuele Azure-machine (om een Data Science Virtual Machine)
    * Azure Databricks (voor gebruik in machine learning pijplijnen)
    * Azure Data Lake Analytics (voor gebruik in machine learning pijplijnen)
    * Azure HDInsight

1. Vul het formulier in en geef waarden op voor de vereiste eigenschappen.

    > [!NOTE]
    > Microsoft raadt u aan SSH-sleutels te gebruiken, die veiliger zijn dan wachtwoorden. Wachtwoorden zijn kwetsbaar voor brute force-aanvallen. SSH-sleutels zijn afhankelijk van cryptografische handtekeningen. Zie de volgende documenten voor meer informatie over het maken van SSH-sleutels Virtual Machines Azure-sleutels:
    >
    > * [SSH-sleutels maken en gebruiken in Linux of macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [SSH-sleutels maken en gebruiken in Windows](../virtual-machines/linux/ssh-from-windows.md)

1. Selecteer __Koppelen.__ 


## <a name="next-steps"></a>Volgende stappen

Nadat een doel is gemaakt en aan uw werkruimte is gekoppeld, gebruikt u het in uw [uitvoeringsconfiguratie](how-to-set-up-training-targets.md) met een `ComputeTarget` -object:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Gebruik de rekenresource om [een trainingsrun in te dienen.](how-to-set-up-training-targets.md)
* [Zelfstudie: Een model trainen maakt](tutorial-train-models-with-aml.md) gebruik van een beheerd rekendoel om een model te trainen.
* Meer informatie over het [efficiënt afstemmen van hyperparameters om](how-to-tune-hyperparameters.md) betere modellen te bouwen.
* Zodra u een getraind model hebt, leert [u hoe en waar u modellen implementeert.](how-to-deploy-and-where.md)
* [Een Azure Machine Learning azure virtual networks gebruiken](./how-to-network-security-overview.md)