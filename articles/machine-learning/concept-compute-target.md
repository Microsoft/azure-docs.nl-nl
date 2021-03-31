---
title: Wat zijn reken doelen?
titleSuffix: Azure Machine Learning
description: Meer informatie over het aanwijzen van een reken resource of-omgeving voor het trainen of implementeren van uw model met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: b4422c664071087ccae73a5b6f642b53dcca74bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574642"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Wat zijn compute-doelen in Azure Machine Learning?

Een *Compute-doel* is een aangewezen reken resource of omgeving waar u uw trainings script uitvoert of uw service-implementatie host. Deze locatie is mogelijk uw lokale machine of een cloud-gebaseerde Compute-resource. Met Compute-doelen kunt u op een later tijdstip uw berekenings omgeving wijzigen zonder dat u de code hoeft te wijzigen.

In een typische levens cyclus voor model ontwikkeling kunt u het volgende doen:

1. Begin met het ontwikkelen en experimenteren van een kleine hoeveelheid gegevens. In deze fase gebruikt u uw lokale omgeving, zoals een lokale computer of een virtuele machine op basis van de Cloud (VM), als uw reken doel.
1. Schaal omhoog naar grotere gegevens of voer gedistribueerde training uit met behulp van een van deze [trainings Compute-doelen](#train).
1. Nadat uw model klaar is, implementeert u het in een webhost of IoT-apparaat met een van deze [implementatie Compute-doelen](#deploy).

De reken resources die u voor uw reken doelen gebruikt, worden aan een [werk ruimte](concept-workspace.md)gekoppeld. Andere reken bronnen dan de lokale computer worden gedeeld door gebruikers van de werk ruimte.

## <a name="training-compute-targets"></a><a name="train"></a> Doelen van de trainings compute

Azure Machine Learning heeft verschillende ondersteuning voor verschillende Compute-doelen. Een typische levens cyclus voor model ontwikkeling begint met het ontwikkelen of experimenteren van een kleine hoeveelheid gegevens. Gebruik in deze fase een lokale omgeving zoals uw lokale computer of een virtuele machine in de Cloud. Gebruik Azure Machine Learning Compute om een cluster met één of meerdere knoop punten te maken dat automatisch wordt geschaald wanneer u een uitvoering verzendt als u uw training op grotere gegevens sets wilt schalen of gedistribueerde trainingen wilt uitvoeren. U kunt ook uw eigen reken resource toevoegen, maar ondersteuning voor verschillende scenario's kan variëren.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Meer informatie over het [verzenden van een trainings uitvoering naar een compute-doel](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Reken doelen voor demijnen

De volgende reken bronnen kunnen worden gebruikt voor het hosten van uw model implementatie.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Bij het uitvoeren van een dematiging maakt Azure Machine Learning een docker-container die het model host en de bijbehorende resources die nodig zijn om het te gebruiken. Deze container wordt vervolgens gebruikt in een van de volgende implementatie scenario's:

* Als een *webservice* die wordt gebruikt voor realtime-interferentie. Implementaties van webservices gebruiken een van de volgende Compute-doelen:

    * [Lokale computer](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning-rekeninstantie](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (preview-versie). Implementatie naar-functies is alleen afhankelijk van Azure Machine Learning voor het bouwen van de docker-container. Vanaf daar kunt u het implementeren met behulp van-functies. Zie [een machine learning model implementeren op Azure functions (preview)](how-to-deploy-functions.md)voor meer informatie.

* Als een _batch_ -defactorion-eind punt dat wordt gebruikt om periodiek batches gegevens te verwerken. Met batch-interferenties wordt gebruikgemaakt van [Azure machine learning reken clusters](how-to-create-attach-compute-cluster.md).

* Naar een _IOT-apparaat_ (preview-versie). Implementatie naar een IoT-apparaat is alleen afhankelijk van Azure Machine Learning voor het bouwen van de docker-container. Van daaruit wordt het geïmplementeerd met behulp van Azure IoT Edge. Zie [Deploy as a IOT Edge module (preview)](../iot-edge/tutorial-deploy-machine-learning.md)voor meer informatie.

Meer informatie over [waar en hoe u uw model implementeert in een compute-doel](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (beheerd)

Een beheerde Compute-resource wordt gemaakt en beheerd door Azure Machine Learning. Deze berekening is geoptimaliseerd voor machine learning workloads. Azure Machine Learning compute-clusters en [reken instanties](concept-compute-instance.md) zijn de enige beheerde reken processen.

U kunt Azure Machine Learning Compute-exemplaren of reken clusters maken van:

* [Azure machine learning Studio](how-to-create-attach-compute-studio.md).
* De python-SDK en CLI:
    * [Reken instantie](how-to-create-manage-compute-instance.md).
    * [Reken cluster](how-to-create-attach-compute-cluster.md).
* De [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (preview).
* Een Azure Resource Manager sjabloon. Zie [een Azure machine learning Compute-cluster maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)voor een voorbeeld sjabloon.
* Een machine learning- [extensie voor de Azure cli](reference-azure-machine-learning-cli.md#resource-management).

Bij het maken worden deze reken resources automatisch opgenomen in uw werk ruimte, in tegens telling tot andere soorten reken doelen.


|Mogelijkheid  |Rekencluster  |Rekenproces  |
|---------|---------|---------|
|Cluster met één of meerdere knoop punten     |    **&check;**       |         |
|Automatisch schalen elke keer dat u een uitvoering verzendt     |     **&check;**      |         |
|Automatische Cluster beheer en taak planning     |   **&check;**        |     **&check;**      |
|Ondersteuning voor zowel CPU-als GPU-resources     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Wanneer een berekenings *cluster* niet actief is, wordt het automatisch geschaald naar 0 knoop punten, zodat u niet betaalt wanneer het niet in gebruik is. Een reken *instantie* is altijd ingeschakeld en wordt niet automatisch geschaald. U moet [het reken exemplaar stoppen](how-to-create-manage-compute-instance.md#manage) wanneer u het niet gebruikt om extra kosten te voor komen.

### <a name="supported-vm-series-and-sizes"></a>Ondersteunde VM-reeksen en-groottes

Wanneer u de grootte van een knoop punt selecteert voor een beheerde Compute-resource in Azure Machine Learning, kunt u kiezen uit beschik bare VM-grootten in Azure. Azure biedt tal van grootten voor Linux en Windows voor verschillende werk belastingen. Zie [VM-typen en-grootten](../virtual-machines/sizes.md)voor meer informatie.

Er zijn enkele uitzonde ringen en beperkingen voor het kiezen van een VM-grootte:

* Sommige VM-reeksen worden niet ondersteund in Azure Machine Learning.
* Sommige VM-reeksen zijn beperkt. Als u een beperkte serie wilt gebruiken, neemt u contact op met de ondersteuning en vraagt u een quotum toename aan te vragen voor de serie. Zie [ondersteunings opties voor Azure](https://azure.microsoft.com/support/options/)voor meer informatie over hoe u contact kunt opnemen met de ondersteuning.

Raadpleeg de volgende tabel voor meer informatie over ondersteunde reeksen en beperkingen.

| **Ondersteunde VM-serie**  | **Beperkingen** | **Categorie** | **Ondersteund door** |
|------------|------------|------------|------------|
| D | Geen. | Algemeen doel | Reken clusters en-exemplaren |
| DDSv4 | Geen. | Algemeen doel | Reken clusters en-exemplaren |
| Dv2 | Geen. | Algemeen doel | Reken clusters en-exemplaren |
| Dv3 | Geen.| Algemeen doel | Reken clusters en-exemplaren |
| DSv2 | Geen. | Algemeen doel | Reken clusters en-exemplaren |
| DSv3 | Geen.| Algemeen doel | Reken clusters en-exemplaren |
| EAv4 | Geen. | Geoptimaliseerd geheugen | Reken clusters en-exemplaren |
| Ev3 | Geen. | Geoptimaliseerd geheugen | Reken clusters en-exemplaren |
| FSv2 | Geen. | Geoptimaliseerde rekenkracht | Reken clusters en-exemplaren |
| H | Geen. | Krachtig rekenvermogen | Reken clusters en-exemplaren |
| HB | Goed keuring vereist. | Krachtig rekenvermogen | Reken clusters en-exemplaren |
| HBv2 | Goed keuring vereist. |  Krachtig rekenvermogen | Reken clusters en-exemplaren |
| HCS | Goed keuring vereist. |  Krachtig rekenvermogen | Reken clusters en-exemplaren |
| M | Goed keuring vereist. | Geoptimaliseerd geheugen | Reken clusters en-exemplaren |
| NC | Geen. |  GPU | Reken clusters en-exemplaren |
| NC-aanbieding | Geen. | GPU | Reken clusters en-exemplaren |
| NCsv2 | Goed keuring vereist. | GPU | Reken clusters en-exemplaren |
| NCsv3 | Goed keuring vereist. | GPU | Reken clusters en-exemplaren |  
| NDs | Goed keuring vereist. | GPU | Reken clusters en-exemplaren | 
| NDv2 | Goed keuring vereist. | GPU | Reken clusters en-exemplaren | 
| NV | Geen. | GPU | Reken clusters en-exemplaren | 
| NVv3 | Goed keuring vereist. | GPU | Reken clusters en-exemplaren | 


Hoewel Azure Machine Learning deze VM-serie ondersteunt, zijn ze mogelijk niet beschikbaar in alle Azure-regio's. Zie [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)om te controleren of de VM-serie beschikbaar is.

> [!NOTE]
> Azure Machine Learning biedt geen ondersteuning voor alle VM-grootten die door Azure Compute worden ondersteund. Als u de beschik bare VM-grootten wilt weer geven, gebruikt u een van de volgende methoden:
> * [REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>Reken isolatie

Azure Machine Learning Compute biedt VM-grootten die zijn geïsoleerd voor een specifiek hardwaretype en die zijn toegewezen aan één klant. Geïsoleerde VM-grootten zijn het meest geschikt voor werk belastingen die een hoge mate van isolatie vereisen ten opzichte van de workloads van andere klanten om redenen die voldoen aan de nalevings vereisten en regelgeving. Het gebruik van een geïsoleerde grootte garandeert dat uw virtuele machine de enige is die op dat specifieke Server exemplaar wordt uitgevoerd.

De huidige aanbiedingen voor geïsoleerde VM'S zijn onder andere:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*RDMA-compatibel

Zie voor meer informatie over isolatie [isolatie in de open bare Azure-Cloud](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Onbeheerde compute

Een niet-beheerd reken doel wordt *niet* beheerd door Azure machine learning. U maakt dit type reken doel buiten Azure Machine Learning en koppelt het vervolgens aan uw werk ruimte. Onbeheerde reken resources kunnen extra stappen vereisen om de prestaties van machine learning werk belastingen te hand haven of te verbeteren.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:
* [Een reken doel gebruiken om uw model te trainen](how-to-set-up-training-targets.md)
* [Uw model implementeren naar een berekenings doel](how-to-deploy-and-where.md)
