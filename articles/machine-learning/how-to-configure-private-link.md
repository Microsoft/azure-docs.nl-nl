---
title: Een privé-eindpunt configureren
titleSuffix: Azure Machine Learning
description: Gebruik Azure Private Link veilige toegang tot uw Azure Machine Learning werkruimte vanuit een virtueel netwerk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 5e13c97427736d60f300d2d7b502c6f3e15fb481
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861441"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Een Azure Private Link configureren voor Azure Machine Learning werkruimte

In dit document leert u hoe u Azure Private Link gebruikt met uw Azure Machine Learning werkruimte. Zie Overzicht van isolatie en privacy van virtuele netwerken voor meer Azure Machine Learning over het maken van een [virtueel netwerk](how-to-network-security-overview.md) voor Azure Machine Learning

Azure Private Link kunt u verbinding maken met uw werkruimte met behulp van een privé-eindpunt. Het privé-eindpunt is een set privé-IP-adressen binnen uw virtuele netwerk. Vervolgens kunt u de toegang tot uw werkruimte beperken tot alleen de privé-IP-adressen. Private Link helpt het risico op gegevens exfiltratie te verminderen. Zie het artikel Azure Private Link meer informatie over [privé Azure Private Link](../private-link/private-link-overview.md) eindpunten.

> [!IMPORTANT]
> Azure Private Link heeft geen invloed op het Azure-besturingsvlak (beheerbewerkingen), zoals het verwijderen van de werkruimte of het beheren van rekenbronnen. Bijvoorbeeld het maken, bijwerken of verwijderen van een rekendoel. Deze bewerkingen worden normaal via het openbare internet uitgevoerd. Gegevensvlakbewerkingen, zoals het gebruik Azure Machine Learning-studio, API's (inclusief gepubliceerde pijplijnen) of de SDK gebruiken het privé-eindpunt.
>
> Als u Mozilla Firefox gebruikt, kunt u problemen ondervinden bij het openen van het privé-eindpunt voor uw werkruimte. Dit probleem kan te maken hebben met DNS via HTTPS in Mozilla. We raden u aan Microsoft Edge of Google Chrome als tijdelijke oplossing te gebruiken.

## <a name="prerequisites"></a>Vereisten

* Als u van plan bent om een werkruimte met private link te gebruiken met een door de klant beheerde sleutel, moet u deze functie aanvragen met behulp van een ondersteuningsticket. Zie Quota beheren en verhogen [voor meer informatie.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

* U moet een bestaand virtueel netwerk hebben om het privé-eindpunt in te maken. U moet ook [netwerkbeleid voor privé-eindpunten uitschakelen voordat](../private-link/disable-private-endpoint-network-policy.md) u het privé-eindpunt toevoegt.
## <a name="limitations"></a>Beperkingen

* Het gebruik van Azure Machine Learning werkruimte met private link is niet beschikbaar in de Azure Government regio's.
* Als u openbare toegang inschakelen voor een werkruimte beveiligd met private link en Azure Machine Learning-studio via het openbare internet, sommige functies zoals de ontwerpfunctie mogelijk geen toegang tot uw gegevens. Dit probleem doet zich voor wanneer de gegevens worden opgeslagen in een service die is beveiligd achter het VNet. Bijvoorbeeld een Azure Storage account.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Een werkruimte maken die gebruikmaakt van een privé-eindpunt

Gebruik een van de volgende methoden om een werkruimte met een privé-eindpunt te maken. Voor elk van deze __methoden is een bestaand virtueel netwerk vereist:__

> [!TIP]
> Zie Een Azure Resource Manager-sjabloon gebruiken om een werkruimte te maken voor een Azure Machine Learning als u tegelijkertijd een werkruimte, privé-eindpunt en virtueel netwerk [wilt Azure Machine Learning.](how-to-create-workspace-template.md)

# <a name="python"></a>[Python](#tab/python)

De Azure Machine Learning Python SDK biedt de [klasse PrivateEndpointConfig,](/python/api/azureml-core/azureml.core.privateendpointconfig) die kan worden gebruikt met [Workspace.create() om](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) een werkruimte met een privé-eindpunt te maken. Voor deze klasse is een bestaand virtueel netwerk vereist.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure CLI-extensie voor machine learning](reference-azure-machine-learning-cli.md) biedt de opdracht az ml workspace [create.](/cli/azure/ml/workspace#az_ml_workspace_create) De volgende parameters voor deze opdracht kunnen worden gebruikt om een werkruimte met een particulier netwerk te maken, maar hiervoor is een bestaand virtueel netwerk vereist:

* `--pe-name`: de naam van het privé-eindpunt dat wordt gemaakt.
* `--pe-auto-approval`: Of privé-eindpuntverbindingen met de werkruimte automatisch moeten worden goedgekeurd.
* `--pe-resource-group`: de resourcegroep waarin u het privé-eindpunt wilt maken. Moet dezelfde groep zijn die het virtuele netwerk bevat.
* `--pe-vnet-name`: Het bestaande virtuele netwerk waarin het privé-eindpunt wordt aan te maken.
* `--pe-subnet-name`: De naam van het subnet waarin het privé-eindpunt moet worden maken. De standaardwaarde is `default`.

Deze parameters zijn een aanvulling op andere vereiste parameters voor de opdracht create. Met de volgende opdracht maakt u bijvoorbeeld een nieuwe werkruimte in de regio VS - west met behulp van een bestaande resourcegroep en een VNet:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Op __het tabblad__ Azure Machine Learning-studio kunt u een privé-eindpunt configureren. Hiervoor is echter een bestaand virtueel netwerk vereist. Zie Werkruimten maken [in de portal voor meer informatie.](how-to-manage-workspace.md)

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Een privé-eindpunt toevoegen aan een werkruimte

Gebruik een van de volgende methoden om een privé-eindpunt toe te voegen aan een bestaande werkruimte:

> [!WARNING]
>
> Als er bestaande rekendoelen zijn gekoppeld aan deze werkruimte en ze zich niet achter hetzelfde virtuele netwerk of het privé-eindpunt hebben gemaakt, werken ze niet.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Zie [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) en Workspace.add_private_endpoint voor meer informatie over de klassen en methoden die in dit [voorbeeld worden Workspace.add_private_endpoint.](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure CLI-extensie voor machine learning](reference-azure-machine-learning-cli.md) biedt de opdracht az ml workspace [private-endpoint add.](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_add)

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in Azure Machine Learning werkruimte in de portal __privé-eindpuntverbindingen__ en selecteer __vervolgens + Privé-eindpunt.__ Gebruik de velden om een nieuw privé-eindpunt te maken.

* Wanneer u regio __selecteert,__ selecteert u dezelfde regio als uw virtuele netwerk. 
* Wanneer u __Resourcetype selecteert,__ gebruikt __u Microsoft.MachineLearningServices/workspaces.__ 
* Stel resource __in__ op de naam van uw werkruimte.

Selecteer ten slotte __Maken om__ het privé-eindpunt te maken.

---

## <a name="remove-a-private-endpoint"></a>Een privé-eindpunt verwijderen

Gebruik een van de volgende methoden om een privé-eindpunt uit een werkruimte te verwijderen:

# <a name="python"></a>[Python](#tab/python)

Gebruik [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) om een privé-eindpunt te verwijderen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure CLI-extensie voor machine learning](reference-azure-machine-learning-cli.md) biedt de opdracht az ml workspace [private-endpoint delete.](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in Azure Machine Learning werkruimte in de portal Privé-eindpuntverbindingen en selecteer vervolgens het eindpunt dat u wilt verwijderen. Selecteer ten slotte __Verwijderen.__

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Een werkruimte gebruiken via een privé-eindpunt

Omdat communicatie met de werkruimte alleen is toegestaan vanuit het virtuele netwerk, moeten ontwikkelomgevingen die gebruikmaken van de werkruimte lid zijn van het virtuele netwerk. Bijvoorbeeld een virtuele machine in het virtuele netwerk.

> [!IMPORTANT]
> Om tijdelijke onderbreking van de connectiviteit te voorkomen, raadt Microsoft aan de DNS-cache leeg te maken op computers die verbinding maken met de werkruimte nadat de Private Link. 

Zie de documentatie over Virtual Machines [azure-Virtual Machines voor meer informatie.](../virtual-machines/index.yml)

## <a name="enable-public-access"></a>Openbare toegang inschakelen

In sommige gevallen wilt u misschien toestaan dat iemand verbinding maakt met uw beveiligde werkruimte via een openbaar eindpunt, in plaats van via het VNet. Nadat u een werkruimte met een privé-eindpunt hebt geconfigureerd, kunt u desgewenst openbare toegang tot de werkruimte inschakelen. Hierdoor wordt het privé-eindpunt niet verwijderd. Alle communicatie tussen onderdelen achter het VNet is nog steeds beveiligd. Hiermee is alleen openbare toegang tot de werkruimte mogelijk, naast de privétoegang via het VNet.

> [!WARNING]
> Wanneer u verbinding maakt via het openbare eindpunt, hebben sommige functies van Studio geen toegang tot uw gegevens. Dit probleem doet zich voor wanneer de gegevens worden opgeslagen in een service die is beveiligd achter het VNet. Bijvoorbeeld een Azure Storage account. Houd er ook rekening mee dat de jupyter-/JupyterLab-/RStudio-functionaliteit van het rekencluster en het uitvoeren van notebooks niet werken.

Als u openbare toegang tot een werkruimte met Private Link wilt inschakelen, gebruikt u de volgende stappen:

# <a name="python"></a>[Python](#tab/python)

Gebruik [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) om een privé-eindpunt te verwijderen.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure CLI-extensie voor machine learning](reference-azure-machine-learning-cli.md) biedt de opdracht az ml workspace [update.](/cli/azure/ml/workspace#az_ml_workspace_update) Als u openbare toegang tot de werkruimte wilt inschakelen, voegt u de parameter `--allow-public-access true` toe.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Er is momenteel geen manier om deze functionaliteit in te schakelen met behulp van de portal.

---


## <a name="next-steps"></a>Volgende stappen

* Zie het artikel Virtueel netwerkisolatie en privacyoverzicht voor meer Azure Machine Learning over het [beveiligen van uw werkruimte.](how-to-network-security-overview.md)

* Zie Een werkruimte gebruiken met een aangepaste [DNS-server](how-to-custom-dns.md)als u van plan bent een aangepaste DNS-oplossing in uw virtuele netwerk te gebruiken.
