---
title: Azure AD-identiteit gebruiken met uw webservice
titleSuffix: Azure Machine Learning
description: Gebruik Azure AD Identity met uw webservice in azure Kubernetes service om toegang te krijgen tot cloud resources tijdens het scoren.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b6e6288f125da2a29a8eff56b64f327914f90cb4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520469"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure AD-identiteit gebruiken met uw Machine Learning-webservice in Azure Kubernetes Service

In deze procedure leert u hoe u een Azure Active Directory-identiteit (Azure AD) toewijst aan uw geïmplementeerde machine learning model in de Azure Kubernetes-service. Met het [Azure AD pod Identity](https://github.com/Azure/aad-pod-identity) -project kunnen toepassingen veilig toegang krijgen tot Cloud bronnen met Azure AD met behulp van een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) en Kubernetes primitieven. Zo kunt u uw webservices veilig toegang geven tot uw Azure-resources zonder dat u referenties hoeft in te sluiten of tokens rechtstreeks in uw script hoeft te beheren `score.py` . In dit artikel worden de stappen beschreven voor het maken en installeren van een Azure-identiteit in uw Azure Kubernetes service-cluster en het toewijzen van de identiteit aan uw geïmplementeerde webservice.

## <a name="prerequisites"></a>Vereisten

- De [Azure cli-extensie voor de machine learning-service](reference-azure-machine-learning-cli.md), de [Azure machine learning SDK voor Python](/python/api/overview/azure/ml/intro)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

- Toegang tot uw AKS-cluster met behulp van de `kubectl` opdracht. Zie [verbinding maken met het cluster](../aks/kubernetes-walkthrough.md#connect-to-the-cluster) voor meer informatie.

- Een Azure Machine Learning-webservice die is geïmplementeerd in uw AKS-cluster.

## <a name="create-and-install-an-azure-identity"></a>Een Azure-identiteit maken en installeren

1. Gebruik de volgende opdracht om te bepalen of het AKS-cluster Kubernetes RBAC is ingeschakeld:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Met deze opdracht wordt de waarde retourneert `true` als KUBERNETES RBAC is ingeschakeld. Deze waarde bepaalt de opdracht die in de volgende stap moet worden gebruikt.

1. Installeer de [Azure AD pod-identiteit](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) in uw AKS-cluster.

1. [Een identiteit maken in azure](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) Volg de stappen die worden weer gegeven in de Azure AD pod-identiteits project pagina.

1. [Implementeer identiteit](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) volgens de stappen die worden weer gegeven in de Azure AD pod Identity project-pagina.

1. [Implementeer AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) volgens de stappen die worden weer gegeven in de Azure AD pod Identity project-pagina.

1. Als de Azure-identiteit die u in de vorige stap hebt gemaakt, zich niet in dezelfde knooppunt resource groep bevindt voor uw AKS-cluster, volgt u de stappen voor [roltoewijzing](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) die worden weer gegeven in de Azure AD pod Identity project-pagina.

## <a name="assign-azure-identity-to-web-service"></a>Azure-identiteit aan de webservice toewijzen

De volgende stappen maken gebruik van de Azure-identiteit die in de vorige sectie is gemaakt en wijst deze toe aan uw AKS-webservice via een **selector-label**.

Bepaal eerst de naam en naam ruimte van uw implementatie in uw AKS-cluster waaraan u de Azure-identiteit wilt toewijzen. U kunt deze informatie verkrijgen door de volgende opdracht uit te voeren. De naam ruimten moet uw Azure Machine Learning werkruimte zijn en uw implementatie naam moet de naam van het eind punt zijn zoals weer gegeven in de portal.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Voeg het label selector van Azure-identiteit toe aan uw implementatie door de implementatie specificatie te bewerken. De selectorwaarde moet de waarde zijn die u hebt gedefinieerd in stap 5 van [Deploy AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Bewerk de implementatie om het label van de Azure Identity Selector toe te voegen. Ga naar de volgende sectie onder `/spec/template/metadata/labels` . U ziet waarden zoals `isazuremlapp: “true”` . Voeg het label Aad-pod-Identity toe, zoals hieronder wordt weer gegeven.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Voer de volgende opdracht uit om te controleren of het label correct is toegevoegd. U moet ook de statussen van de nieuw gemaakte peulen zien.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Zodra het meren onderdeel actief is, kunnen de webservices voor deze implementatie nu toegang krijgen tot Azure-resources via uw Azure-identiteit zonder dat u de referenties in uw code hoeft in te sluiten.

## <a name="assign-roles-to-your-azure-identity"></a>Rollen toewijzen aan uw Azure-identiteit

[Wijs uw door Azure beheerde identiteit toe met de juiste rollen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) om toegang te krijgen tot andere Azure-resources. Zorg ervoor dat de rollen die u toewijst de juiste **gegevens acties** hebben. De rol van de [gegevens lezer](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) van de opslag-BLOB heeft bijvoorbeeld lees machtigingen voor uw opslag-BLOB terwijl de rol van algemene [lezer](../role-based-access-control/built-in-roles.md#reader) niet mogelijk is.

## <a name="use-azure-identity-with-your-web-service"></a>Azure-identiteit gebruiken met uw web-service

Implementeer een model op uw AKS-cluster. Het `score.py` script kan bewerkingen bevatten die verwijzen naar de Azure-resources waartoe uw Azure-identiteit toegang heeft. Zorg ervoor dat u de vereiste client bibliotheek afhankelijkheden hebt geïnstalleerd voor de bron waartoe u toegang probeert te krijgen. Hieronder vindt u enkele voor beelden van hoe u uw Azure-identiteit kunt gebruiken voor toegang tot verschillende Azure-resources vanuit uw service.

### <a name="access-key-vault-from-your-web-service"></a>Toegang tot Key Vault via uw webservice

Als u uw Azure Identity Lees toegang hebt gegeven tot een geheim in een **Key Vault**, `score.py` kunt u er toegang toe krijgen met de volgende code.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> In dit voor beeld wordt het DefaultAzureCredential gebruikt. Zie [een Key Vault toegangs beleid toewijzen met behulp van de Azure-cli](../key-vault/general/assign-access-policy-cli.md)als u uw identiteit toegang wilt verlenen met behulp van een specifiek toegangs beleid.

### <a name="access-blob-from-your-web-service"></a>Toegang tot BLOB vanuit uw webservice

Als u uw Azure Identity Lees toegang hebt tot gegevens in een **opslag-BLOB**, `score.py` kunt u deze openen met de volgende code.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Volgende stappen

* Zie de [opslag plaats](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) op github voor meer informatie over het gebruik van de python Azure Identity client-bibliotheek.
* Zie voor een gedetailleerde hand leiding voor het implementeren van modellen voor Azure Kubernetes-Service clusters de [instructies](how-to-deploy-azure-kubernetes-service.md).