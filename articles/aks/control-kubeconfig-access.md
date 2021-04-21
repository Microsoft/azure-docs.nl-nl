---
title: Toegang tot kubeconfig beperken in Azure Kubernetes Service (AKS)
description: Meer informatie over het beheer van de toegang tot het Kubernetes-configuratiebestand (kubeconfig) voor clusterbeheerders en clustergebruikers
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 279ca9800d7d721cc2e77d269cb577d8bd166d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765553"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Op rollen gebaseerd toegangsbeheer van Azure gebruiken om toegang tot het Kubernetes-configuratiebestand in Azure Kubernetes Service (AKS) te definiëren

U kunt communiceren met Kubernetes-clusters met behulp van het `kubectl` hulpprogramma . De Azure CLI biedt een eenvoudige manier om de toegangsreferenties en configuratiegegevens op te halen om verbinding te maken met uw AKS-clusters met behulp van `kubectl` . Als u wilt beperken wie die Kubernetes-configuratiegegevens *(kubeconfig)* kan ontvangen en de machtigingen die ze vervolgens hebben, wilt beperken, kunt u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gebruiken.

In dit artikel wordt beschreven hoe u Azure-rollen toewijst die beperken wie de configuratiegegevens voor een AKS-cluster kan krijgen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

Voor dit artikel moet u ook Azure CLI versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="available-cluster-roles-permissions"></a>Beschikbare machtigingen voor clusterrollen

Wanneer u met een AKS-cluster communiceert met behulp van het hulpprogramma , wordt een configuratiebestand gebruikt waarmee informatie over de `kubectl` clusterverbinding wordt bepaald. Dit configuratiebestand wordt doorgaans opgeslagen in *~/.kube/config.* In dit *kubeconfig-bestand* kunnen meerdere clusters worden gedefinieerd. U schakelt tussen clusters met behulp van de [opdracht kubectl config use-context.][kubectl-config-use-context]

Met [de opdracht az aks get-credentials][az-aks-get-credentials] kunt u de toegangsreferenties voor een AKS-cluster op halen en deze samenvoegen in het *kubeconfig-bestand.* U kunt op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) gebruiken om de toegang tot deze referenties te controleren. Met deze Azure-rollen kunt u definiëren wie het *kubeconfig-bestand* kan ophalen en welke machtigingen ze vervolgens binnen het cluster hebben.

De twee ingebouwde rollen zijn:

* **Azure Kubernetes Service clusterbeheerdersrol**  
  * Hiermee krijgt u toegang tot de API-aanroep *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action.* Met deze [API-aanroep worden de beheerdersreferenties van het cluster vermeld.][api-cluster-admin]
  * Hiermee *downloadt u kubeconfig* voor de *rol clusterAdmin.*
* **Azure Kubernetes Service clustergebruikersrol**
  * Hiermee staat u toegang *tot de API-aanroep Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* toe. Met deze [API-aanroep worden de gebruikersreferenties van het cluster vermeld.][api-cluster-user]
  * Downloadt *kubeconfig* voor *clusterUser* role.

Deze Azure-rollen kunnen worden toegepast op een Azure Active Directory (AD)-gebruiker of -groep.

> [!NOTE]
> Op clusters die gebruikmaken van Azure AD hebben gebruikers met de *rol clusterUser* een leeg *kubeconfig-bestand* dat om een aanmeldprompt vraagt. Nadat gebruikers zijn aangemeld, hebben ze toegang op basis van hun Azure AD-gebruikers- of -groepsinstellingen. Gebruikers met de *rol clusterAdmin* hebben beheerderstoegang.
>
> Clusters die niet gebruikmaken van Azure AD, gebruiken alleen de *rol clusterAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Rolmachtigingen toewijzen aan een gebruiker of groep

Als u een van de beschikbare rollen wilt toewijzen, moet u de resource-id van het AKS-cluster en de id van het Azure AD-gebruikersaccount of -groep verkrijgen. De volgende voorbeeldopdrachten:

* Haal de clusterresource-id op met behulp van [de opdracht az aks show][az-aks-show] voor het cluster met de naam *myAKSCluster* in de resourcegroep *myResourceGroup.* Geef waar nodig de naam van uw eigen cluster en resourcegroep op.
* Gebruik de [opdrachten az account show en][az-account-show] az ad user show [om][az-ad-user-show] uw gebruikers-id op te halen.
* Wijs ten slotte een rol toe met behulp [van de opdracht az role assignment create.][az-role-assignment-create]

In het volgende voorbeeld wordt de *Azure Kubernetes Service clusterbeheerdersrol toegewezen* aan een afzonderlijk gebruikersaccount:

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
> In sommige gevallen is *de user.name* in het account anders dan de *userPrincipalName,* zoals bij Gastgebruikers van Azure AD:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> In dit geval stelt u de waarde van *ACCOUNT_UPN* in op *de userPrincipalName* van de Azure AD-gebruiker. Als uw *account* bijvoorbeeld user.name gebruiker *is, contoso.com \@*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Als u machtigingen wilt toewijzen aan een Azure AD-groep, moet u de parameter uit het vorige voorbeeld bijwerken met de object-id voor de groep in plaats van `--assignee` een *gebruiker*.  Gebruik de opdracht [az ad group show][az-ad-group-show] om de object-id voor een groep op te halen. In het volgende voorbeeld wordt de object-id voor de Azure AD-groep met de *naam appdev opgeslagen:*`az ad group show --group appdev --query objectId -o tsv`

U kunt de vorige  toewijzing naar behoefte wijzigen in de clustergebruikersrol.

In de volgende voorbeelduitvoer ziet u dat de roltoewijzing is gemaakt:

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

## <a name="get-and-verify-the-configuration-information"></a>De configuratiegegevens op halen en controleren

Als Azure-rollen zijn toegewezen, gebruikt u de opdracht [az aks get-credentials][az-aks-get-credentials] om de *kubeconfig-definitie* voor uw AKS-cluster op te halen. In het volgende voorbeeld worden *de referenties --admin* opgeslagen, die correct werken als aan de gebruiker de *rol Clusterbeheerder is verleend:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

U kunt vervolgens de [opdracht kubectl config view][kubectl-config-view] gebruiken om te controleren of de *context* voor het cluster laat zien dat de configuratiegegevens van de beheerder zijn toegepast:

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

Als u roltoewijzingen wilt verwijderen, gebruikt [u de opdracht az role assignment delete.][az-role-assignment-delete] Geef de account-id en clusterresource-id op, zoals verkregen in de vorige opdrachten. Als u de rol hebt toegewezen aan een groep in plaats van een gebruiker, geeft u de juiste groepsobject-id op in plaats van accountobject-id voor de `--assignee` parameter:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Volgende stappen

Voor verbeterde beveiliging bij toegang tot AKS-clusters integreert [u Azure Active Directory verificatie][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-role-assignment-delete]: /cli/azure/role/assignment#az_role_assignment_delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
