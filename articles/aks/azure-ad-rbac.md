---
title: Azure AD en Kubernetes RBAC gebruiken voor clusters
titleSuffix: Azure Kubernetes Service
description: Leer hoe u Azure Active Directory groepslidmaatschap kunt gebruiken om de toegang tot clusterbronnen te beperken met behulp van kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC) in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 0d5171e9e9a5d7f033ff615a3f1205b8dc93966f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769549"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Toegang tot clusterbronnen beheren met behulp van kubernetes op rollen gebaseerd toegangsbeheer en Azure Active Directory identiteiten in Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan worden geconfigureerd voor het gebruik van Azure Active Directory (AD) voor gebruikersverificatie. In deze configuratie meldt u zich aan bij een AKS-cluster met behulp van een Azure AD-verificatie-token. U kunt kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC) ook configureren om de toegang tot clusterbronnen te beperken op basis van de identiteit of het groepslidmaatschap van een gebruiker.

In dit artikel wordt beschreven hoe u azure AD-groepslidmaatschap gebruikt om de toegang tot naamruimten en clusterbronnen te beheren met behulp van Kubernetes RBAC in een AKS-cluster. Voorbeeldgroepen en gebruikers worden gemaakt in Azure AD. Vervolgens worden rollen en RoleBindings gemaakt in het AKS-cluster om de juiste machtigingen te verlenen voor het maken en weergeven van resources.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt ingeschakeld met Azure AD-integratie. Als u een AKS-cluster nodig hebt, zie [Integratie van Azure Active Directory met AKS.][azure-ad-aks-cli]

Azure CLI versie 2.0.61 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-demo-groups-in-azure-ad"></a>Demogroepen maken in Azure AD

In dit artikel maken we twee gebruikersrollen die kunnen worden gebruikt om te laten zien hoe Kubernetes RBAC en Azure AD de toegang tot clusterbronnen beheren. De volgende twee voorbeeldrollen worden gebruikt:

* **Toepassingsontwikkelaar**
    * Een gebruiker met de *naam aksdev* die deel uitmaakt van de *appdev-groep.*
* **Site reliability engineer**
    * Een gebruiker met de *naam akssre* die deel uitmaakt van de *opssre-groep.*

In productieomgevingen kunt u bestaande gebruikers en groepen binnen een Azure AD-tenant gebruiken.

Haal eerst de resource-id van uw AKS-cluster op met behulp van [de opdracht az aks show.][az-aks-show] Wijs de resource-id toe aan een *variabele AKS_ID,* zodat er in aanvullende opdrachten naar kan worden verwezen.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Maak de eerste voorbeeldgroep in Azure AD voor de toepassingsontwikkelaars met behulp van [de opdracht az ad group create.][az-ad-group-create] In het volgende voorbeeld wordt een groep met de *naam appdev gemaakt:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Maak nu een Azure-roltoewijzing voor de *appdev-groep* met behulp [van de opdracht az role assignment create.][az-role-assignment-create] Met deze toewijzing kan elk lid van de groep gebruiken om te communiceren met een AKS-cluster door hem of haar de Azure Kubernetes Service `kubectl` *clustergebruikersrol te verlenen.*

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Als u een foutbericht zoals ontvangt, wacht u een paar seconden totdat de object-id van de Azure AD-groep is doorgegeven via de map en probeert u `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` de `az role assignment create` opdracht opnieuw.

Maak een tweede voorbeeldgroep, deze voor SRE's met de *naam opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Maak opnieuw een Azure-roltoewijzing om leden van de groep de rol Azure Kubernetes Service *clustergebruikersrol* te verlenen:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Demogebruikers maken in Azure AD

Met twee voorbeeldgroepen die in Azure AD zijn gemaakt voor onze toepassingsontwikkelaars en SRE's, kunnen we nu twee voorbeeldgebruikers maken. Als u de Kubernetes RBAC-integratie aan het einde van het artikel wilt testen, moet u zich met deze accounts aanmelden bij het AKS-cluster.

Stel de user principal name (UPN) en het wachtwoord in voor de toepassingsontwikkelaars. De volgende opdracht vraagt u om de UPN en stelt deze in op *AAD_DEV_UPN* voor gebruik in een latere opdracht (houd er rekening mee dat de opdrachten in dit artikel worden ingevoerd in een BASH-shell). De UPN moet de geverifieerde domeinnaam van uw tenant bevatten, bijvoorbeeld `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

De volgende opdracht vraagt u om het wachtwoord en stelt deze in op *AAD_DEV_PW* voor gebruik in een latere opdracht.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Maak het eerste gebruikersaccount in Azure AD met behulp van [de opdracht az ad user create.][az-ad-user-create]

In het volgende voorbeeld wordt een gebruiker gemaakt met de weergavenaam *AKS Dev* en de UPN en het beveiligde wachtwoord met behulp van de waarden in *AAD_DEV_UPN* en *AAD_DEV_PW:*

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Voeg nu de gebruiker toe aan de *appdev-groep* die u in de vorige sectie hebt gemaakt met behulp van [de opdracht az ad group member add:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Stel de UPN en het wachtwoord voor SRE's in. De volgende opdracht vraagt u om de UPN en stelt deze in op *AAD_SRE_UPN* voor gebruik in een latere opdracht (onthoud dat de opdrachten in dit artikel worden ingevoerd in een BASH-shell). De UPN moet de geverifieerde domeinnaam van uw tenant bevatten, bijvoorbeeld `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

De volgende opdracht vraagt u om het wachtwoord en stelt deze in *op AAD_SRE_PW* voor gebruik in een latere opdracht.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Maak een tweede gebruikersaccount. In het volgende voorbeeld wordt een gebruiker gemaakt met de weergavenaam *AKS SRE* en de UPN en het wachtwoord beveiligd met behulp van de waarden in *AAD_SRE_UPN* en *AAD_SRE_PW:*

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>De AKS-clusterbronnen voor app-ontwikkelaars maken

De Azure AD-groepen en -gebruikers zijn nu gemaakt. Azure-roltoewijzingen zijn gemaakt om de groepsleden als gewone gebruiker verbinding te laten maken met een AKS-cluster. Nu gaan we het AKS-cluster configureren zodat deze verschillende groepen toegang hebben tot specifieke resources.

Haal eerst de beheerdersreferenties van het cluster op met de [opdracht az aks get-credentials.][az-aks-get-credentials] In een van de volgende secties  krijgt u de reguliere gebruikersclusterreferenties om de Azure AD-verificatiestroom in actie te zien.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Maak een naamruimte in het AKS-cluster met behulp van de [opdracht kubectl create namespace.][kubectl-create] In het volgende voorbeeld wordt een naam voor de naamruimte *gemaakt:*

```console
kubectl create namespace dev
```

In Kubernetes definiëren *rollen* de machtigingen die moeten worden verleend en *RoleBindings* passen deze toe op gewenste gebruikers of groepen. Deze toewijzingen kunnen worden toegepast op een bepaalde naamruimte of op het hele cluster. Zie Using [Kubernetes RBAC authorization (Kubernetes RBAC-autorisatie gebruiken) voor meer informatie.][rbac-authorization]

Maak eerst een rol voor de *naamruimte dev.* Deze rol verleent volledige machtigingen voor de naamruimte. In productieomgevingen kunt u gedetailleerdere machtigingen opgeven voor verschillende gebruikers of groepen.

Maak een bestand met de `role-dev-namespace.yaml` naam en plak het volgende YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Maak de rol met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f role-dev-namespace.yaml
```

Haal vervolgens de resource-id voor de *appdev-groep* op met de [opdracht az ad group show.][az-ad-group-show] Deze groep wordt in de volgende stap ingesteld als het onderwerp van een RoleBinding.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Maak nu een RoleBinding voor de *appdev-groep* om de eerder gemaakte Rol te gebruiken voor toegang tot de naamruimte. Maak een bestand met de `rolebinding-dev-namespace.yaml` naam en plak het volgende YAML-manifest. Vervang *groupObjectId*  op de laatste regel door de uitvoer van de groepsobject-id van de vorige opdracht:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Maak de RoleBinding met behulp van de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>De AKS-clusterresources voor SRE's maken

Herhaal nu de vorige stappen om een naamruimte, rol en RoleBinding voor de SRE's te maken.

Maak eerst een naamruimte voor *sre met behulp* van de [opdracht kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Maak een bestand met de `role-sre-namespace.yaml` naam en plak het volgende YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Maak de rol met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f role-sre-namespace.yaml
```

Haal de resource-id voor de *opssre-groep* op met de [opdracht az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Maak een RoleBinding voor de *opssre-groep* om de eerder gemaakte Rol te gebruiken voor toegang tot de naamruimte. Maak een bestand met de `rolebinding-sre-namespace.yaml` naam en plak het volgende YAML-manifest. Vervang *groupObjectId*  op de laatste regel door de uitvoer van de groepsobject-id van de vorige opdracht:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Maak de RoleBinding met behulp van de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interactie met clusterbronnen met behulp van Azure AD-identiteiten

Nu gaan we de verwachte machtigingen testen wanneer u resources in een AKS-cluster maakt en beheert. In deze voorbeelden gaat u pods in de door de gebruiker toegewezen naamruimte plannen en weergeven. Vervolgens probeert u pods buiten de toegewezen naamruimte te plannen en weer te geven.

Stel eerst de *kubeconfig-context* opnieuw in met [de opdracht az aks get-credentials.][az-aks-get-credentials] In een vorige sectie hebt u de context ingesteld met behulp van de beheerdersreferenties van het cluster. De gebruiker met beheerdersrechten omzeilt aanmeldingsprompts van Azure AD. Zonder de parameter wordt de gebruikerscontext toegepast die vereist dat alle aanvragen `--admin` worden geverifieerd met behulp van Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Plan een eenvoudige NGINX-pod met behulp van [de opdracht kubectl run][kubectl-run] in de *naamruimte dev:*

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Voer bij de aanmeldingsprompt de referenties in voor uw eigen `appdev@contoso.com` account dat u aan het begin van het artikel hebt gemaakt. Zodra u bent aangemeld, wordt het account-token in de cache opgeslagen voor toekomstige `kubectl` opdrachten. De NGINX is gepland, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Gebruik nu de [opdracht kubectl get pods][kubectl-get] om pods in de *naamruimte dev* weer te geven.

```console
kubectl get pods --namespace dev
```

Zoals u in de volgende voorbeelduitvoer kunt zien, wordt de NGINX-pod *uitgevoerd:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Clusterbronnen buiten de toegewezen naamruimte maken en weergeven

Probeer nu pods buiten de naamruimte *dev* weer te geven. Gebruik de [opdracht kubectl get pods][kubectl-get] opnieuw om dit keer als `--all-namespaces` volgt te zien:

```console
kubectl get pods --all-namespaces
```

Het groepslidmaatschap van de gebruiker heeft geen Kubernetes-rol die deze actie toestaat, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Probeer op dezelfde manier een pod in een andere naamruimte te plannen, zoals de *sre-naamruimte.* Het groepslidmaatschap van de gebruiker komt niet overeen met een Kubernetes-rol en RoleBinding om deze machtigingen te verlenen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>De SRE-toegang tot de AKS-clusterresources testen

Als u wilt controleren of ons Azure AD-groepslidmaatschap en Kubernetes RBAC correct werken tussen verschillende gebruikers en groepen, voert u de vorige opdrachten uit wanneer u bent aangemeld als *opssre-gebruiker.*

Stel de *kubeconfig-context* opnieuw in met de [opdracht az aks get-credentials,][az-aks-get-credentials] die het eerder in de cache opgeslagen verificatie-token voor de *aksdev-gebruiker leegt:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Probeer pods in de toegewezen *sre-naamruimte* te plannen en weer te geven. Meld u aan met uw eigen referenties die u aan het begin van het artikel hebt gemaakt wanneer u hier om `opssre@contoso.com` wordt gevraagd:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Zoals u in de volgende voorbeelduitvoer ziet, kunt u de pods maken en weergeven:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Probeer nu pods buiten de toegewezen SRE-naamruimte weer te geven of te plannen:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Deze `kubectl` opdrachten mislukken, zoals wordt weergegeven in de volgende voorbeelduitvoer. Het groepslidmaatschap van de gebruiker en de Kubernetes-rol en RoleBindings verlenen geen machtigingen voor het maken of beheren van resources in andere naamruimten:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebt u resources gemaakt in het AKS-cluster en gebruikers en groepen in Azure AD. Voer de volgende opdrachten uit om al deze resources op te schonen:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Volgende stappen

Zie Toegangs- en identiteitsopties voor [AKS)][rbac-authorization]voor meer informatie over het beveiligen van Kubernetes-clusters.

Zie Best practices voor verificatie en autorisatie in AKS voor best practices voor identiteits- en [resourcebeheer.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
