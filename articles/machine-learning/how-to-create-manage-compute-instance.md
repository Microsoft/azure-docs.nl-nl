---
title: Een reken-exemplaar maken en beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken en beheren van een Azure Machine Learning compute-instantie. Gebruik als uw ontwikkelomgeving of als rekendoel voor ontwikkel-/testdoeleinden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5ac525ae062efca25601c9e63a5c8f16f2be29be
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861215"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Een reken-Azure Machine Learning maken en beheren

Meer informatie over het maken en beheren van een [reken-exemplaar](concept-compute-instance.md) in Azure Machine Learning werkruimte.

Gebruik een rekenomgeving als uw volledig geconfigureerde en beheerde ontwikkelomgeving in de cloud. Voor ontwikkeling en testen kunt u het exemplaar ook gebruiken als een rekendoel voor training [of](concept-compute-target.md#train) voor [een deferentiedoel.](concept-compute-target.md#deploy)   Een reken-exemplaar kan meerdere taken parallel uitvoeren en heeft een taakwachtrij. Als ontwikkelomgeving kan een reken-exemplaar niet worden gedeeld met andere gebruikers in uw werkruimte.

In dit artikel leert u het volgende:

* Een rekenproces maken 
* Een reken-exemplaar beheren (starten, stoppen, opnieuw opstarten, verwijderen)
* Toegang tot het terminalvenster 
* R- of Python-pakketten installeren
* Nieuwe omgevingen of Jupyter-kernels maken

Reken-exemplaren kunnen taken veilig uitvoeren in een omgeving met een virtueel [netwerk,](how-to-secure-training-vnet.md)zonder dat ondernemingen SSH-poorten moeten openen. De taak wordt uitgevoerd in een containeromgeving en verpakt uw modelafhankelijkheden in een Docker-container. 

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)

* De [Azure CLI-extensie voor Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)of de [Azure Machine Learning Visual Studio Code-extensie](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Maken

**Geschatte tijd:** Ongeveer 5 minuten.

Het maken van een rekenproces is een een enkel proces voor uw werkruimte. U kunt de rekenkracht hergebruiken als ontwikkelwerkstation of als rekendoel voor training. U kunt meerdere reken-exemplaren koppelen aan uw werkruimte.

De toegewezen kernen per regio per VM-familiequotum en het totale regionale quotum, dat van toepassing is op het maken van een rekenproces, worden samengevoegd en gedeeld met Azure Machine Learning training van rekenclusterquota. Als u de reken-instantie stopt, wordt er geen quotum uitgebracht om ervoor te zorgen dat u de reken-instantie opnieuw kunt starten. Houd er rekening mee dat het niet mogelijk is om de grootte van de virtuele machine van het reken exemplaar te wijzigen zodra deze is gemaakt.

In het volgende voorbeeld wordt gedemonstreerd hoe u een reken-exemplaar maakt:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [ComputeInstance-klasse](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Zie de naslaginformatie [over az ml computetarget create computeinstance voor meer](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance) informatie.

# <a name="studio"></a>[Studio](#tab/azure-studio)

Maak in uw werkruimte in Azure Machine Learning-studio een nieuw reken-exemplaar vanuit de sectie **Compute** of in de sectie Notebooks wanneer u klaar bent om een van uw **notebooks** uit te voeren.

Zie Rekendoelen maken in Azure Machine Learning-studio voor meer informatie over het maken van [een reken-Azure Machine Learning-studio.](how-to-create-attach-compute-studio.md#compute-instance)

---

U kunt ook een reken-exemplaar maken met een [Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Maken namens (preview)

Als beheerder kunt u namens een data scientist een reken-exemplaar maken en het exemplaar aan hen toewijzen met:
* [Azure Resource Manager sjabloon .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)  Zie Id's van [identiteitsobjecten](../healthcare-apis/fhir/find-identity-object-ids.md)zoeken voor verificatieconfiguratie voor meer informatie over het vinden van de TenantID en ObjectID die nodig zijn in deze sjabloon.  U kunt deze waarden ook vinden in Azure Active Directory portal.
* REST-API

De data scientist voor wie u de reken-instantie maakt, heeft de volgende machtigingen voor op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../role-based-access-control/overview.md) nodig: 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

De data scientist kan de reken-instantie starten, stoppen en opnieuw starten. Ze kunnen de reken-instantie gebruiken voor:
* Jupyter
* JupyterLab
* RStudio
* Geïntegreerde notebooks

## <a name="manage"></a>Beheren

Een reken-exemplaar starten, stoppen, opnieuw starten en verwijderen. Een reken-exemplaar wordt niet automatisch omlaag geschaald, dus zorg ervoor dat u de resource stopt om doorlopende kosten te voorkomen.

> [!TIP]
> Het reken exemplaar heeft een besturingssysteemschijf van 120 GB. Als u geen schijfruimte meer hebt, gebruikt u de [terminal](how-to-access-terminal.md) om ten minste 1-2 GB te leeg te maken voordat u de reken-instantie stopt of opnieuw start.

# <a name="python"></a>[Python](#tab/python)

In de onderstaande voorbeelden is de naam van het reken exemplaar **instance**

* Status op halen

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Stoppen

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Starten

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Opnieuw starten

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Verwijderen

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

In de onderstaande voorbeelden is de naam van het reken exemplaar **instance**

* Stoppen

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Zie az [ml computetarget stop computeinstance voor meer informatie.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop)

* Starten 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Zie az [ml computetarget start computeinstance voor meer informatie.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start)

* Opnieuw starten 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Zie az [ml computetarget restart computeinstance voor meer informatie.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart)

* Verwijderen

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Zie az [ml computetarget delete computeinstance voor meer informatie.](/cli/azure/ml/computetarget#az_ml_computetarget_delete)

# <a name="studio"></a>[Studio](#tab/azure-studio)

Selecteer in uw werkruimte Azure Machine Learning-studio **compute** en selecteer vervolgens **Rekenin exemplaar** bovenaan.

![Een reken-exemplaar beheren](./media/concept-compute-instance/manage-compute-instance.png)

U kunt de volgende acties uitvoeren:

* Een nieuwe reken-instantie maken 
* Vernieuw het tabblad Reken-exemplaren.
* Een reken-exemplaar starten, stoppen en opnieuw starten.  U betaalt voor de instantie wanneer deze wordt uitgevoerd. Stop de reken-instantie wanneer u deze niet gebruikt om de kosten te verlagen. Als u een reken-exemplaar stopt, wordt de toewijzing ervan weer in de weg gerekend. Start deze opnieuw wanneer u deze nodig hebt.
* Een reken-exemplaar verwijderen.
* Filter de lijst met reken-exemplaren om alleen de exemplaren weer te geven die u hebt gemaakt.

Voor elke reken-instantie in uw werkruimte die u hebt gemaakt (of die voor u is gemaakt), kunt u het volgende doen:

* Toegang tot Jupyter, JupyterLab, RStudio op de reken-instantie
* SSH in reken-exemplaar. SSH-toegang is standaard uitgeschakeld, maar kan worden ingeschakeld tijdens het maken van het rekenproces. SSH-toegang is via een mechanisme voor openbare/persoonlijke sleutels. Op het tabblad vindt u details voor de SSH-verbinding, zoals IP-adres, gebruikersnaam en poortnummer.
* Meer informatie over een specifiek reken-exemplaar, zoals IP-adres en regio.

---


[Met Azure RBAC](../role-based-access-control/overview.md) kunt u bepalen welke gebruikers in de werkruimte een reken-exemplaar kunnen maken, verwijderen, starten, stoppen en opnieuw starten. Alle gebruikers in de rol Inzender en Eigenaar van de werkruimte kunnen reken-instanties in de werkruimte maken, verwijderen, starten, stoppen en opnieuw starten. Alleen de maker van een specifiek reken-exemplaar of de gebruiker die is toegewezen als deze namens hem of haar is gemaakt, heeft echter toegang tot Jupyter, JupyterLab en RStudio op dat reken-exemplaar. Een reken-exemplaar is toegewezen aan één gebruiker met hoofdtoegang en kan worden geterminal via Jupyter/JupyterLab/RStudio. Het rekenproces heeft een aanmelding voor één gebruiker en alle acties gebruiken de identiteit van die gebruiker voor Azure RBAC en de toewijzing van experiment-runs. SSH-toegang wordt beheerd via een mechanisme voor openbare/persoonlijke sleutels.

Deze acties kunnen worden beheerd door Azure RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>Volgende stappen

* [Toegang tot de terminal van het reken exemplaar](how-to-access-terminal.md)
* [Bestanden maken en beheren](how-to-manage-files.md)
* [Een trainingsrun verzenden](how-to-set-up-training-targets.md)
