---
title: Een gekoppelde service maken met Synapse en Azure Machine Learning werkruimten (preview)
titleSuffix: Azure Machine Learning
description: Leer hoe u Azure Synapse en Azure Machine Learning koppelen voor een uniforme data-wrangling-ervaring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 23184eee67013e39400446db5f744dd0ddb7bc50
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575732"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Werkruimten Azure Synapse Analytics en Azure Machine Learning koppelen (preview)

In dit artikel leert u hoe u een gekoppelde service maakt die uw [Azure Synapse Analytics-werkruimte](/azure/synapse-analytics/overview-what-is) en [Azure Machine Learning koppelt.](concept-workspace.md)

Als uw Azure Machine Learning-werkruimte is gekoppeld aan uw Azure Synapse-werkruimte, kunt u een Apache Spark-pool koppelen als een toegewezen rekenkracht voor data-wrangling op schaal of modeltrainingen uitvoeren vanuit hetzelfde Python-notebook.

U kunt uw ML-werkruimte en Synapse-werkruimte koppelen via de [Python SDK](#link-sdk) of [de Azure Machine Learning-studio](#link-studio).

U kunt ook werkruimten koppelen en een Synapse Spark-pool koppelen met een [ARM-sjabloon (single Azure Resource Manager).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)

>[!IMPORTANT]
> De integratie Azure Machine Learning en Azure Synapse is in openbare preview. De functies van het pakket `azureml-synapse` zijn experimentele preview-functies [](/python/api/overview/azure/ml/#stable-vs-experimental) en kunnen op elk moment veranderen.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md?tabs=python)

* [Maak een Synapse-werkruimte in Azure Portal](/azure/synapse-analytics/quickstart-create-workspace).

* [Een Apache Spark maken met Azure Portal, webhulpprogramma's of Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* De [Python Azure Machine Learning-SDK installeren](/python/api/overview/azure/ml/intro)

* Toegang tot de [Azure Machine Learning-studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Werkruimten koppelen met de Python-SDK

> [!IMPORTANT]
> Als u een koppeling naar de Synapse-werkruimte wilt maken, moet u de rol Eigenaar **van** de Synapse-werkruimte krijgen. Controleer uw toegang in de [Azure Portal](https://ms.portal.azure.com/).
>
> Als u geen eigenaar bent **en** alleen bijdrager bent **aan** de Synapse-werkruimte, kunt u alleen bestaande gekoppelde services gebruiken. Bekijk hoe u een bestaande gekoppelde service opnieuw [kunt proberen en gebruiken.](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)

De volgende code maakt gebruik van de [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) klassen en voor,

* Koppel uw machine learning werkruimte aan `ws` uw Azure Synapse werkruimte.
* Registreer uw Synapse-werkruimte bij Azure Machine Learning als een gekoppelde service.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Er wordt een beheerde `system_assigned_identity_principal_id` identiteit, , gemaakt voor elke gekoppelde service. Aan deze beheerde identiteit moet de **rol Synapse Apache Spark-beheerder** van de Synapse-werkruimte worden verleend voordat u de Synapse-sessie start. [Wijs de rol synapse Apache Spark beheerder toe aan](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)de beheerde identiteit in de Synapse Studio .
>
> Gebruik om de `system_assigned_identity_principal_id` van een specifieke gekoppelde service te `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` vinden.

### <a name="manage-linked-services"></a>Gekoppelde services beheren

Alles weergeven gekoppelde services die zijn gekoppeld aan uw machine learning werkruimte.

```python
LinkedService.list(ws)
```

Als u uw werkruimten wilt ontkoppelen, gebruikt u de `unregister()` methode

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Werkruimten koppelen via Studio

Koppel uw machine learning en Synapse-werkruimte via de Azure Machine Learning-studio stappen: 

1. Meld u aan bij [Azure Machine Learning-studio](https://ml.azure.com/).
1. Selecteer **Gekoppelde services** in **de sectie** Beheren van het linkerdeelvenster.
1. Selecteer **Integratie toevoegen.**
1. Vul in **het formulier** Werkruimte koppelen de velden in

    |Veld| Beschrijving    
    |---|---
    |Name| Geef een naam op voor de gekoppelde service. Deze naam wordt gebruikt om naar deze specifieke gekoppelde service te verwijzen.
    |Abonnementsnaam | Selecteer de naam van uw abonnement dat is gekoppeld aan uw machine learning werkruimte. 
    |Synapse-werkruimte | Selecteer de Synapse-werkruimte die u wilt koppelen.
    
1. Selecteer **Volgende om** het formulier **Spark-pools selecteren (optioneel) te** openen. Op dit formulier selecteert u welke Synapse Spark-pool u wilt koppelen aan uw werkruimte

1. Selecteer **Volgende** om het formulier **Controleren te** openen en uw selecties te controleren.
1. Selecteer **Maken om** het proces voor het maken van de gekoppelde service te voltooien.

## <a name="next-steps"></a>Volgende stappen

* [Koppel Synapse Spark-pools voor gegevensvoorbereiding met Azure Synapse (preview)](how-to-data-prep-synapse-spark-pool.md).
* [Informatie over het gebruik Apache Spark in uw machine learning-pijplijn met Azure Synapse (preview)](how-to-use-synapsesparkstep.md)
* [Een model trainen.](how-to-set-up-training-targets.md)
