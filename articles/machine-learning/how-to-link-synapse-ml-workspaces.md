---
title: Een gekoppelde service maken met Synapse-en Azure Machine Learning-werk ruimten (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het koppelen van Azure Synapse-en Azure Machine Learning-werk ruimten voor een Unified Data wrangling-ervaring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 8941a7f7a27f6ffe58cda3f0bf2c6833ec226783
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456237"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Azure Synapse Analytics en Azure Machine Learning-werk ruimten (preview) koppelen

In dit artikel leert u hoe u een gekoppelde service kunt maken die uw [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) -werk ruimte en [Azure machine learning-werk ruimte](concept-workspace.md)koppelt.

Als uw Azure Machine Learning-werk ruimte is gekoppeld aan uw Azure Synapse-werk ruimte, kunt u een Apache Spark pool koppelen als een toegewezen Compute voor data wrangling op schaal en model training uitvoeren vanuit hetzelfde notitie blok.

U kunt uw Synapse-werk ruimte en de werk ruimte van uw ML koppelen via de [python-SDK](#link-sdk) of de [Azure machine learning Studio](#link-studio).

U kunt ook werk ruimten koppelen en een Synapse Spark-groep koppelen aan een enkele [Azure Resource Manager (arm)-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> De Azure Machine Learning-en Azure Synapse-integratie bevindt zich in de open bare preview. De functionaliteit van het `azureml-synapse` pakket is [experimentele](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) preview-functies en kan op elk gewenst moment worden gewijzigd.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md?tabs=python)

* [Maak een Synapse-werk ruimte in azure Portal](/synapse-analytics/quickstart-create-workspace.md).

* [Apache Spark groep maken met behulp van Azure Portal, web tools of Synapse Studio](/synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* De [Azure machine learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) installeren

* Toegang tot de [Azure machine learning Studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Werk ruimten koppelen met de python-SDK

> [!IMPORTANT]
> Als u een koppeling wilt maken naar de Synapse-werk ruimte, moet u de rol **eigenaar** van de Synapse-werk ruimte krijgen. Controleer uw toegang in de [Azure Portal](https://ms.portal.azure.com/).
>
> Als u geen **eigenaar** bent en alleen een **bijdrager** aan de Synapse-werk ruimte bent, kunt u alleen bestaande gekoppelde services gebruiken. Zie [een bestaande gekoppelde service ophalen en gebruiken](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

De volgende code maakt gebruik van [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) de [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) klassen en en

* Koppel uw machine learning-werk ruimte aan `ws` uw Azure Synapse-werk ruimte.
* Registreer uw Synapse-werk ruimte met Azure Machine Learning als een gekoppelde service.

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
> Er wordt een beheerde identiteit `system_assigned_identity_principal_id` voor elke gekoppelde service gemaakt. Aan deze beheerde identiteit moet de **Synapse Apache Spark** beheerdersrol van de Synapse-werk ruimte worden verleend voordat u uw Synapse-sessie start. [Wijs de Synapse Apache Spark beheerdersrol toe aan de beheerde identiteit in de Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Als u wilt zoeken naar `system_assigned_identity_principal_id` een specifieke gekoppelde service, gebruikt u `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Gekoppelde services beheren

Bekijk alle gekoppelde services die zijn gekoppeld aan uw machine learning-werk ruimte.

```python
LinkedService.list(ws)
```

Als u uw werk ruimten wilt ontkoppelen, gebruikt u de `unregister()` methode

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Werk ruimten koppelen via Studio

Koppel uw machine learning-werk ruimte en de Synapse-werk ruimte via de Azure Machine Learning Studio met de volgende stappen: 

1. Meld u aan bij de [Azure machine learning Studio](https://ml.azure.com/).
1. Selecteer **gekoppelde services** in het gedeelte **beheren** van het linkerdeel venster.
1. Selecteer **integratie toevoegen**.
1. Vul de velden in op het formulier **werk ruimte koppelen** 
    Veld| Beschrijving    
    ---|---
    Name| Geef een naam op voor de gekoppelde service. Deze naam wordt gebruikt om te verwijzen naar deze specifieke gekoppelde service.
    Abonnementsnaam | Selecteer de naam van uw abonnement dat is gekoppeld aan uw machine learning-werk ruimte. 
    Synapse-werk ruimte | Selecteer de Synapse-werk ruimte waarmee u een koppeling wilt maken.
1. Selecteer **volgende** om het formulier **Spark-Pools selecteren (optioneel)** te openen. Op dit formulier selecteert u welke Synapse Spark-pool u aan uw werk ruimte wilt koppelen

1. Selecteer **volgende** om het **controle** formulier te openen en uw selecties te controleren.
1. Selecteer **maken** om het gekoppelde proces voor het maken van de service te volt ooien.

## <a name="next-steps"></a>Volgende stappen

* [Koppel Synapse Spark-Pools voor gegevens voorbereiding met Azure Synapse (preview)](how-to-data-prep-synapse-spark-pool.md).
* [Apache Spark in uw machine learning-pijp lijn gebruiken met Azure Synapse (preview)](how-to-use-synapsesparkstep.md)
* [Train een model](how-to-set-up-training-targets.md).
