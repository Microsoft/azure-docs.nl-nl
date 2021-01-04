---
title: Beperk de toegang tot kubeconfig in azure Kubernetes service (AKS)
description: Meer informatie over het beheren van de toegang tot het Kubernetes-configuratie bestand (kubeconfig) voor cluster beheerders en cluster gebruikers
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 77b9988557106ef460d3b222ef85eb29e08f31c8
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693989"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Gebruik Azure op rollen gebaseerd toegangs beheer voor het definiëren van toegang tot het Kubernetes-configuratie bestand in azure Kubernetes service (AKS)

U kunt met het hulp programma communiceren met Kubernetes-clusters `kubectl` . De Azure CLI biedt een eenvoudige manier om de toegangs referenties en configuratie gegevens op te halen om verbinding te maken met uw AKS-clusters met behulp van `kubectl` . Als u wilt beperken wie de gegevens van de Kubernetes-configuratie (*kubeconfig*) kan ophalen en de machtigingen die ze hebben, wilt beperken, kunt u gebruikmaken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC).

In dit artikel wordt beschreven hoe u Azure-rollen toewijst die de configuratie gegevens voor een AKS-cluster kunnen beperken.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Voor dit artikel moet u ook de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="available-cluster-roles-permissions"></a>Beschik bare machtigingen voor cluster rollen

Wanneer u met het hulp programma met een AKS-cluster communiceert `kubectl` , wordt er een configuratie bestand gebruikt waarmee de verbindings gegevens van de cluster worden gedefinieerd. Dit configuratie bestand wordt doorgaans opgeslagen in *~/.Kube/config*. Er kunnen meerdere clusters worden gedefinieerd in dit *kubeconfig* -bestand. U schakelt tussen clusters met behulp van de opdracht [kubectl config use-context][kubectl-config-use-context] .

Met de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] kunt u de toegangs referenties voor een AKS-cluster ophalen en deze samen voegen in het *kubeconfig* -bestand. U kunt Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om de toegang tot deze referenties te beheren. Met deze Azure-rollen kunt u bepalen wie het *kubeconfig* -bestand kan ophalen en welke machtigingen ze vervolgens hebben in het cluster.

De twee ingebouwde rollen zijn:

* **Rol van Cluster beheerder voor Azure Kubernetes-service**  
  * Hiermee hebt u toegang tot *micro soft. container service/managedClusters/listClusterAdminCredential/Action* API call. Deze API-aanroep [vermeldt de cluster beheerders referenties][api-cluster-admin].
  * Down load *kubeconfig* voor de *clusterAdmin* -rol.
* **Gebruikersrol Azure Kubernetes service-cluster**
  * Hiermee hebt u toegang tot *micro soft. container service/managedClusters/listClusterUserCredential/Action* API call. Deze API-aanroep [vermeldt de gebruikers referenties van het cluster][api-cluster-user].
  * Downloadt *kubeconfig* voor de *clusterUser* -rol.

Deze Azure-rollen kunnen worden toegepast op een Azure Active Directory (AD) gebruiker of groep.

> [!NOTE]
> Voor clusters die gebruikmaken van Azure AD, hebben gebruikers met de rol *clusterUser* een leeg *kubeconfig* -bestand waarin wordt gevraagd om zich aan te melden. Wanneer gebruikers eenmaal zijn aangemeld, hebben ze toegang tot de gebruikers-of groeps instellingen van Azure AD. Gebruikers met de rol *clusterAdmin* hebben beheerders toegang.
>
> Clusters die niet gebruikmaken van Azure AD, maken alleen gebruik van de functie *clusterAdmin* .

## <a name="assign-role-permissions-to-a-user-or-group"></a>Rollen machtigingen toewijzen aan een gebruiker of groep

Als u een van de beschik bare rollen wilt toewijzen, moet u de resource-ID van het AKS-cluster en de ID van de Azure AD-gebruikers account of-groep ophalen. De volgende voorbeeld opdrachten:

* Haal de cluster bron-ID op met behulp van de opdracht [AZ AKS show][az-aks-show] voor het cluster met de naam *myAKSCluster* in de resource groep *myResourceGroup* . Geef uw eigen cluster en de naam van de resource groep op, indien nodig.
* Gebruik de opdracht [AZ account show][az-account-show] en [AZ AD User show][az-ad-user-show] om uw gebruikers-id op te halen.
* Wijs tot slot een rol toe met behulp van de opdracht [AZ Role Assignment Create][az-role-assignment-create] .

In het volgende voor beeld wordt de *rol Azure Kubernetes service cluster admin* toegewezen aan een afzonderlijke gebruikers account:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!IMPORTANT]
> In sommige gevallen wijkt de *User.name* in het account af van de *userPrincipalName*, zoals met Azure AD-gast gebruikers:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> In dit geval stelt u de waarde van *ACCOUNT_UPN* in op de *userPrincipalName* van de Azure AD-gebruiker. Als uw account bijvoorbeeld *User.name* is, is *gebruikers \@ contoso.com*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Als u machtigingen wilt toewijzen aan een Azure AD-groep, werkt u de `--assignee` para meter die wordt weer gegeven in het vorige voor beeld bij met de object-id voor de *groep* in plaats van een *gebruiker*. Als u de object-ID voor een groep wilt ophalen, gebruikt u de opdracht [AZ Ad Group show][az-ad-group-show] . In het volgende voor beeld wordt de object-ID van de Azure AD-groep met de naam *appdev* opgehaald: `az ad group show --group appdev --query objectId -o tsv`

U kunt de voor gaande toewijzing wijzigen naar de *gebruikersrol cluster* als dat nodig is.

In de volgende voorbeeld uitvoer ziet u dat de roltoewijzing is gemaakt:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>De configuratie gegevens ophalen en verifiëren

Als u Azure-rollen hebt toegewezen, gebruikt u de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] om de *kubeconfig* -definitie voor uw AKS-cluster op te halen. In het volgende voor beeld worden de referenties van de *beheerder* opgehaald, die goed werken als de gebruiker de *rol cluster beheerder* heeft gekregen:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

U kunt vervolgens de [kubectl-configuratie weergave][kubectl-config-view] opdracht gebruiken om te controleren of de *context* van het cluster laat zien dat de informatie over de beheer configuratie is toegepast:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Rolmachtigingen verwijderen

Als u roltoewijzingen wilt verwijderen, gebruikt u de opdracht [AZ Role Assignment delete][az-role-assignment-delete] . Geef de account-ID en de cluster bron-ID op, zoals deze in de vorige opdrachten zijn verkregen. Als u de rol hebt toegewezen aan een groep in plaats van een gebruiker, geeft u de juiste groeps object-ID op in plaats van account object-ID voor de `--assignee` para meter:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Volgende stappen

Voor een betere beveiliging van de toegang tot AKS-clusters, [integreert u Azure Active Directory-verificatie][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
