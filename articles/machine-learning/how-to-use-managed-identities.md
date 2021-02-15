---
title: Beheerde identiteiten gebruiken voor toegangs beheer (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van beheerde identiteiten voor het beheren van de toegang tot Azure-resources vanuit Azure Machine Learning werk ruimte.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 014c592713a8568b3bbc7e8e536f81b203271ccc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388070"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Beheerde identiteiten gebruiken met Azure Machine Learning (preview-versie)

Met [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) kunt u uw werk ruimte configureren met de *Mini maal vereiste machtigingen voor toegang tot bronnen*. 

Wanneer u Azure Machine Learning werk ruimte op een betrouw bare manier configureert, is het belang rijk om ervoor te zorgen dat verschillende services die zijn gekoppeld aan de werk ruimte, het juiste toegangs niveau hebben. Tijdens machine learning werk stroom moet de werk ruimte bijvoorbeeld toegang hebben tot Azure Container Registry (ACR) voor docker-installatie kopieën en opslag accounts voor trainings gegevens. 

Daarnaast kunnen beheerde identiteiten nauw keurige controle over machtigingen toestaan, bijvoorbeeld kunt u toegang verlenen of intrekken van specifieke Compute-resources naar een specifieke ACR.

In dit artikel leert u hoe u beheerde identiteiten kunt gebruiken voor het volgende:

 * Configureer en gebruik ACR voor uw Azure Machine Learning-werk ruimte zonder beheer gebruikers toegang tot ACR te hoeven inschakelen.
 * Krijg toegang tot een persoonlijke ACR voor uw werk ruimte, zodat u basis afbeeldingen kunt ophalen voor training of demijnen.
 * Maak een werk ruimte met door de gebruiker toegewezen beheerde identiteit om toegang te krijgen tot gekoppelde resources.

> [!IMPORTANT]
> Beheerde identiteiten gebruiken om de toegang tot resources te beheren met Azure Machine Learning is momenteel beschikbaar als preview-versie. De Preview-functionaliteit wordt verstrekt "as-is", zonder garanties van ondersteuning of service level agreement. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie.
 
## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.
- De [Azure cli-extensie voor de machine learning-service](reference-azure-machine-learning-cli.md)
- De [Azure machine learning python-SDK](/python/api/overview/azure/ml/intro?view=azure-ml-py).
- Als u rollen wilt toewijzen, moet de aanmelding voor uw Azure-abonnement de rol [Managed Identity-operator](../role-based-access-control/built-in-roles.md#managed-identity-operator) hebben, of een andere rol die de vereiste acties verleent (zoals __eigenaar__).
- U moet bekend zijn met het maken en werken met [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="configure-managed-identities"></a>Beheerde identiteiten configureren

In sommige gevallen is het nood zakelijk om de toegang van gebruikers met beheerders rechten tot Azure Container Registry te weigeren. De ACR kan bijvoorbeeld worden gedeeld en u moet de beheerders toegang door andere gebruikers niet toestaan. Of het maken van ACR met ingeschakelde beheerder is niet toegestaan door het beleid op abonnements niveau.

> [!IMPORTANT]
> Wanneer u Azure Machine Learning gebruikt voor het dezicht van een Azure container instance (ACI), is de gebruiker toegang tot de beheerder op ACR __vereist__. Schakel dit niet uit als u van plan bent om modellen te implementeren op ACI.

Wanneer u ACR maakt zonder de gebruikers toegang van de beheerder in te scha kelen, worden beheerde identiteiten gebruikt voor toegang tot de ACR om docker-installatie kopieën te bouwen en uit te breiden.

Wanneer u de werk ruimte maakt, kunt u uw eigen ACR met beheerders rechten uitschakelen. U kunt ook Azure Machine Learning werk ruimte ACR maken en de gebruiker met beheerders rechten later uitschakelen.

### <a name="bring-your-own-acr"></a>Uw eigen ACR meenemen

Als ACR admin-gebruiker niet wordt toegestaan door het abonnements beleid, moet u eerst ACR maken zonder beheerder en deze vervolgens koppelen aan de werk ruimte. Als u al een bestaande ACR hebt met een gebruiker die door de beheerder is uitgeschakeld, kunt u deze koppelen aan de werk ruimte.

[ACR maken op basis van Azure cli](../container-registry/container-registry-get-started-azure-cli.md) zonder een ```--admin-enabled``` argument in te stellen, of op basis van Azure Portal zonder beheer gebruiker in te scha kelen. Wanneer u vervolgens Azure Machine Learning werk ruimte maakt, geeft u de Azure-Resource-ID van de ACR op. In het volgende voor beeld ziet u hoe u een nieuwe Azure ML-werk ruimte maakt die gebruikmaakt van een bestaand ACR:

> [!TIP]
> Als u de waarde voor de `--container-registry` para meter wilt ophalen, gebruikt u de opdracht [AZ ACR show](/cli/azure/acr#az_acr_show) om informatie weer te geven voor uw ACR. Het `id` veld bevat de resource-id voor uw ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>ACR-werk ruimte maken met Azure Machine Learning service

Als u geen eigen ACR meebrengt, maakt Azure Machine Learning service er een voor u wanneer u een bewerking uitvoert die er een nodig heeft. U kunt bijvoorbeeld een trainings uitvoering verzenden naar Machine Learning Compute, een omgeving bouwen of een webservice-eind punt implementeren. De ACR die door de werk ruimte is gemaakt, heeft de gebruiker beheerder ingeschakeld en u moet de beheerder hand matig uitschakelen.

1. Een nieuwe werkruimte maken

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Voer een actie uit die ACR vereist. Bijvoorbeeld, de [zelf studie over het trainen van een model](tutorial-train-models-with-aml.md).

1. De ACR-naam ophalen die door het cluster is gemaakt:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Met deze opdracht wordt een waarde geretourneerd die overeenkomt met de volgende tekst. U wilt alleen het laatste gedeelte van de tekst, de naam van het ACR-exemplaar:

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Werk de ACR bij om de gebruiker met beheerders rechten uit te scha kelen:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Een compute with Managed Identity maken voor toegang tot docker-installatie kopieën voor training

Om toegang te krijgen tot de ACR van de werk ruimte, maakt u machine learning Compute-cluster waarvoor door het systeem toegewezen beheerde identiteit is ingeschakeld. U kunt de identiteit van Azure Portal of Studio inschakelen bij het maken van compute, of vanuit Azure CLI met behulp van de onderstaande. Zie [using Managed identity with compute clusters](how-to-create-attach-compute-cluster.md#managed-identity)voor meer informatie.

# <a name="python"></a>[Python](#tab/python)

Wanneer u een reken cluster maakt met de [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py), gebruikt `identity_type` u de para meter om het type beheerde identiteit in te stellen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zie [beheerde identiteit instellen](how-to-create-attach-compute-studio.md#managed-identity)voor meer informatie over het configureren van een beheerde identiteit bij het maken van een reken cluster in Studio.

---

Een beheerde identiteit krijgt automatisch een ACRPull-rol in de werk ruimte ACR om pull-upinstallatie kopieën voor training in te scha kelen.

> [!NOTE]
> Als u eerst Compute maakt voordat u de werk ruimte ACR hebt gemaakt, moet u de ACRPull-rol hand matig toewijzen.

## <a name="access-base-images-from-private-acr"></a>Toegang tot basis installatie kopieën vanuit een privé-ACR

Azure Machine Learning maakt standaard gebruik van docker-basis installatie kopieën die afkomstig zijn van een open bare opslag plaats die wordt beheerd door micro soft. Vervolgens wordt uw training gegenereerd of wordt de omgeving op deze installatie kopieën getraind. Zie [Wat zijn omgevingen van ml?](concept-environments.md)voor meer informatie.

Als u een aangepaste basis installatie kopie wilt gebruiken die intern is voor uw bedrijf, kunt u beheerde identiteiten gebruiken om toegang te krijgen tot uw persoonlijke ACR. Er zijn twee use-cases:

 * Gebruik basis installatie kopie voor training als dat zo is.
 * Bouw Azure Machine Learning beheerde installatie kopie met een aangepaste installatie kopie als basis.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Docker-basis installatie kopie voor het machine learning Compute-Cluster voor training

Maak machine learning Compute-cluster waarvoor door het systeem toegewezen beheerde identiteit is ingeschakeld, zoals eerder is beschreven. Bepaal vervolgens de principal-ID van de beheerde identiteit.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

U kunt het berekenings cluster eventueel bijwerken om een door de gebruiker toegewezen beheerde identiteit toe te wijzen:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Als u wilt toestaan dat het berekenings cluster de basis installatie kopieën kan ophalen, geeft u de ACRPull rol Managed Service Identity toe op de privé ACR

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Ten slotte geeft u bij het verzenden van een trainings uitvoering de locatie van de basis installatie kopie op in de [omgevings definitie](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Stel in `user_managed_dependencies = True` en geef geen Dockerfile op om ervoor te zorgen dat de basis installatie kopie direct wordt opgehaald voor de reken resource. Anders probeert Azure Machine Learning service een nieuwe docker-installatie kopie te maken, omdat alleen het Compute-Cluster toegang heeft tot het ophalen van de basis installatie kopie van ACR.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Azure Machine Learning beheerde omgeving bouwen in de basis installatie kopie van een privé-ACR voor training of afwijzen

In dit scenario bouwt Azure Machine Learning service de training of de detraining-omgeving boven op een basis installatie kopie die u van een persoonlijke ACR opgeeft. Omdat de taak installatie kopie maken plaatsvindt in de werk ruimte ACR met ACR-taken, moet u meer stappen uitvoeren om toegang toe te staan.

1. Maak een door de __gebruiker toegewezen beheerde identiteit__ en verleen de identiteit ACRPull toegang tot de __persoonlijke ACR__.  
1. Verleen de door het __systeem toegewezen beheerde identiteit__ een beheerde identiteits operator rol op de door de __gebruiker toegewezen beheerde identiteit__ uit de vorige stap. Met deze rol kan de werk ruimte de door de gebruiker toegewezen beheerde identiteit toewijzen aan de ACR-taak voor het bouwen van de beheerde omgeving. 

    1. De principal-ID van de door het systeem toegewezen beheerde identiteit verkrijgen:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. De rol beheerde identiteits operator verlenen:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        De resource-ID van de UAI is de Azure-Resource-ID van de door de gebruiker toegewezen identiteit in de indeling `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. Geef de externe ACR en de client-ID op van de door de __gebruiker toegewezen beheerde identiteit__ in werkruimte verbindingen met behulp van [Workspace.set_connection methode](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-):

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Zodra de configuratie is voltooid, kunt u de basis installatie kopieën van persoonlijke ACR gebruiken bij het bouwen van omgevingen voor training of demijnen. Het volgende code fragment laat zien hoe u de basis installatie kopie en de naam van de installatie kopie kunt opgeven in een omgevings definitie:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Desgewenst kunt u de URL van de beheerde identiteits bron en de client-ID opgeven in de omgevings definitie zelf met behulp van [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py). Als u de register identiteit expliciet gebruikt, worden alle eerder opgegeven werkruimte verbindingen overschreven:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Docker-installatie kopieën gebruiken voor demijnen

Zodra u ACR zonder beheer gebruiker hebt geconfigureerd zoals eerder beschreven, kunt u toegang krijgen tot docker-installatie kopieën zonder beheer sleutels vanuit uw Azure Kubernetes service (AKS). Wanneer u AKS maakt of koppelt aan de werk ruimte, wordt de service-principal van het cluster automatisch toegewezen ACRPull toegang tot de werk ruimte ACR.

> [!NOTE]
> Als u uw eigen AKS-cluster meebrengt, moet op het cluster service-principal zijn ingeschakeld in plaats van beheerde identiteit.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>Een werk ruimte maken met een door de gebruiker toegewezen beheerde identiteit

Bij het maken van de werk ruimte kunt u een door de gebruiker toegewezen beheerde identiteit opgeven die wordt gebruikt voor toegang tot de gekoppelde bronnen: ACR, sleutel kluis, opslag en app Insights.

Maak eerst een door de [gebruiker toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli])en noteer de arm-resource-id van de beheerde identiteit.

Vervolgens gebruikt u de Azure CLI-of python-SDK om de werk ruimte te maken. Wanneer u de CLI gebruikt, geeft u de ID op met behulp van de `--primary-user-assigned-identity` para meter. Gebruik voor het gebruik van de SDK `primary_user_assigned_identity` . Hier volgen enkele voor beelden van het gebruik van de Azure CLI en python om een nieuwe werk ruimte te maken met behulp van de volgende para meters:

__Azure-CLI__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

U kunt ook [een arm-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) gebruiken om een werk ruimte te maken met door de gebruiker toegewezen beheerde identiteit.

> [!IMPORTANT]
> Als u uw eigen gekoppelde resources meebrengt in plaats van Azure Machine Learning service te maken, moet u de beheerde identiteits rollen voor die resources verlenen. Gebruik de [arm-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) voor het toewijzen van rollen om de toewijzingen te maken.

Voor een werk ruimte met (door de klant beheerde sleutels voor versleuteling) [ https://docs.microsoft.com/azure/machine-learning/concept-data-encryption ] kunt u een door de gebruiker toegewezen beheerde identiteit door geven voor verificatie van de opslag tot Key Vault. Gebruik het argument dat door de __gebruiker is toegewezen, Identity-for-CMK-Encryption__ (CLI) of __user_assigned_identity_for_cmk_encryption__ (SDK) om de beheerde identiteit door te geven. Deze beheerde identiteit kan hetzelfde zijn als de door de primaire gebruiker toegewezen beheerde identiteit van de werk ruimte.

Als u een bestaande werk ruimte hebt, kunt u deze bijwerken van het systeem aan een door de gebruiker toegewezen beheerde identiteit met behulp van de ```az ml workspace update``` cli-opdracht of de ```Workspace.update``` python SDK-methode.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Enter prise Security in azure machine learning](concept-enterprise-security.md).
