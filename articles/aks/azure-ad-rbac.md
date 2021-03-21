---
title: Gebruik Azure AD en Kubernetes RBAC voor clusters
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van Azure Active Directory groepslid maatschap voor het beperken van de toegang tot cluster bronnen met behulp van Kubernetes op basis van rollen (Kubernetes RBAC) in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 72b2c456d62b899f2b04041929434da668cad82d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583527"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Toegang tot cluster bronnen beheren met op rollen gebaseerd toegangs beheer en Azure Active Directory-identiteiten in de Azure Kubernetes-service

Azure Kubernetes service (AKS) kan worden geconfigureerd om Azure Active Directory (AD) te gebruiken voor gebruikers verificatie. In deze configuratie meldt u zich aan bij een AKS-cluster met behulp van een Azure AD-verificatie token. U kunt ook Kubernetes met op rollen gebaseerd toegangs beheer (Kubernetes RBAC) configureren om de toegang tot cluster bronnen te beperken op basis van de identiteit of het lidmaatschap van een gebruiker.

In dit artikel wordt beschreven hoe u Azure AD-groepslid maatschap kunt gebruiken voor het beheren van de toegang tot naam ruimten en cluster bronnen met behulp van Kubernetes RBAC in een AKS-cluster. Voor beelden van groepen en gebruikers worden gemaakt in azure AD, vervolgens worden rollen en RoleBindings in het AKS-cluster gemaakt om de juiste machtigingen te verlenen voor het maken en weer geven van resources.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat er een bestaand AKS-cluster is ingeschakeld met Azure AD-integratie. Als u een AKS-cluster nodig hebt, raadpleegt u [Azure Active Directory integreren met AKS][azure-ad-aks-cli].

U moet de Azure CLI-versie 2.0.61 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-demo-groups-in-azure-ad"></a>Demo groepen maken in azure AD

In dit artikel gaan we twee gebruikers rollen maken die kunnen worden gebruikt om te laten zien hoe Kubernetes RBAC en Azure AD Control toegang hebben tot cluster bronnen. De volgende twee voorbeeld rollen worden gebruikt:

* **Toepassings ontwikkelaar**
    * Een gebruiker met de naam *aksdev* die deel uitmaakt van de groep *appdev* .
* **Site betrouwbaarheids technicus**
    * Een gebruiker met de naam *akssre* die deel uitmaakt van de groep *opssre* .

In productie omgevingen kunt u bestaande gebruikers en groepen gebruiken binnen een Azure AD-Tenant.

Haal eerst de resource-ID van uw AKS-cluster op met behulp van de opdracht [AZ AKS show][az-aks-show] . Wijs de resource-ID toe aan een variabele met de naam *AKS_ID* zodat er in extra opdrachten naar kan worden verwezen.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Maak de eerste voorbeeld groep in azure AD voor ontwikkel aars van toepassingen met behulp van de opdracht [AZ Ad Group Create][az-ad-group-create] . In het volgende voor beeld wordt een groep met de naam *appdev* gemaakt:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Maak nu een Azure-roltoewijzing voor de groep *appdev* met de opdracht [AZ Role Assignment Create][az-role-assignment-create] . Met deze toewijzing kan elk lid van de groep `kubectl` worden gebruikt voor interactie met een AKS-cluster door hen de gebruikersrol *Azure Kubernetes service cluster* te verlenen.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Als er een fout optreedt zoals `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , wacht u enkele seconden tot de object-id van de Azure AD-groep wordt door gegeven via de Directory en probeer de `az role assignment create` opdracht opnieuw uit te voeren.

Maak een tweede voor beeld van een groep met de naam *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Maak opnieuw een Azure-roltoewijzing om leden van de groep de *Azure Kubernetes service-cluster* gebruikersrol te verlenen:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Demo-gebruikers maken in azure AD

Met twee voor beelden van groepen die zijn gemaakt in azure AD voor onze toepassings ontwikkelaars en SREs, kunt u nu twee voor beeld-gebruikers maken. Als u de Kubernetes RBAC-integratie aan het einde van het artikel wilt testen, meldt u zich aan bij het AKS-cluster met deze accounts.

Stel de user principal name (UPN) en het wacht woord voor de ontwikkel aars van de toepassing in. De volgende opdracht vraagt u om de UPN en stelt deze in op *AAD_DEV_UPN* voor gebruik in een latere opdracht (Houd er rekening mee dat de opdrachten in dit artikel worden ingevoerd in een bash-shell). De UPN moet bijvoorbeeld de geverifieerde domein naam van uw Tenant bevatten `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

Met de volgende opdracht wordt u gevraagd om het wacht woord en wordt deze ingesteld op *AAD_DEV_PW* voor gebruik in een latere opdracht.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Maak het eerste gebruikers account in azure AD met behulp van de opdracht [AZ AD user create][az-ad-user-create] .

In het volgende voor beeld wordt een gebruiker gemaakt met de weergave naam *AKS dev* en het UPN-en beveiligde wacht woord met behulp van de waarden in *AAD_DEV_UPN* en *AAD_DEV_PW*:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Voeg nu de gebruiker toe aan de *appdev* -groep die in de vorige sectie is gemaakt met behulp van de opdracht [AZ Ad Group member add][az-ad-group-member-add] :

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Stel de UPN en het wacht woord voor SREs in. De volgende opdracht vraagt u om de UPN en stelt deze in op *AAD_SRE_UPN* voor gebruik in een latere opdracht (Houd er rekening mee dat de opdrachten in dit artikel worden ingevoerd in een bash-shell). De UPN moet bijvoorbeeld de geverifieerde domein naam van uw Tenant bevatten `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

Met de volgende opdracht wordt u gevraagd om het wacht woord en wordt deze ingesteld op *AAD_SRE_PW* voor gebruik in een latere opdracht.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Maak een tweede gebruikers account. In het volgende voor beeld wordt een gebruiker gemaakt met de weergave naam *AKS SRE* en het UPN-en beveiligde wacht woord met behulp van de waarden in *AAD_SRE_UPN* en *AAD_SRE_PW*:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>De AKS-cluster resources maken voor de app ontwikkel aars

De Azure AD-groepen en-gebruikers worden nu gemaakt. Er zijn Azure-roltoewijzingen gemaakt voor de groeps leden om verbinding te maken met een AKS-cluster als gewone gebruiker. Nu gaan we het AKS-cluster configureren zodat deze verschillende groepen toegang tot specifieke bronnen kunnen krijgen.

Haal eerst de cluster beheerders referenties op met de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . In een van de volgende secties krijgt u de reguliere *gebruikers* cluster referenties om de Azure AD-verificatie stroom in actie te zien.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Maak een naam ruimte in het AKS-cluster met behulp van de [kubectl maken naam ruimte][kubectl-create] opdracht. In het volgende voor beeld wordt de naam van de naam ruimte name *dev*:

```console
kubectl create namespace dev
```

In Kubernetes definiëren *rollen* de machtigingen die moeten worden verleend en worden deze *RoleBindings* toegepast op de gewenste gebruikers of groepen. Deze toewijzingen kunnen worden toegepast op een bepaalde naam ruimte of in het hele cluster. Zie [using KUBERNETES RBAC Authorization][rbac-authorization](Engelstalig) voor meer informatie.

Maak eerst een rol voor de naam ruimte van de *ontwikkelaar* . Met deze rol worden volledige machtigingen verleend aan de naam ruimte. In productie omgevingen kunt u meer gedetailleerde machtigingen opgeven voor verschillende gebruikers of groepen.

Maak een bestand `role-dev-namespace.yaml` met de naam en plak het volgende YAML-manifest:

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

Maak de rol met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de bestands naam van het yaml-manifest op:

```console
kubectl apply -f role-dev-namespace.yaml
```

Haal vervolgens de resource-ID voor de *appdev* -groep op met behulp van de opdracht [AZ Ad Group show][az-ad-group-show] . Deze groep wordt in de volgende stap ingesteld als het onderwerp van een RoleBinding.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Maak nu een RoleBinding voor de *appdev* -groep om de eerder gemaakte rol voor naam ruimte toegang te gebruiken. Maak een bestand `rolebinding-dev-namespace.yaml` met de naam en plak het volgende YAML-manifest. Op de laatste regel vervangt u *groupObjectId*  door de groeps object-id-uitvoer van de vorige opdracht:

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

Maak de RoleBinding met de opdracht [kubectl apply][kubectl-apply] en geef de bestands naam van uw yaml-manifest op:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>De AKS-cluster resources maken voor SREs

Herhaal nu de vorige stappen om een naam ruimte, rol en RoleBinding voor de SREs te maken.

Maak eerst een naam ruimte voor *SRE* met behulp van de [kubectl maken naam ruimte][kubectl-create] opdracht:

```console
kubectl create namespace sre
```

Maak een bestand `role-sre-namespace.yaml` met de naam en plak het volgende YAML-manifest:

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

Maak de rol met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de bestands naam van het yaml-manifest op:

```console
kubectl apply -f role-sre-namespace.yaml
```

De resource-ID voor de *opssre* -groep ophalen met de opdracht [AZ Ad Group show][az-ad-group-show] :

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Maak een RoleBinding voor de *opssre* -groep om de eerder gemaakte rol voor naam ruimte toegang te gebruiken. Maak een bestand `rolebinding-sre-namespace.yaml` met de naam en plak het volgende YAML-manifest. Op de laatste regel vervangt u *groupObjectId*  door de groeps object-id-uitvoer van de vorige opdracht:

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

Maak de RoleBinding met de opdracht [kubectl apply][kubectl-apply] en geef de bestands naam van uw yaml-manifest op:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interactie met cluster bronnen met behulp van Azure AD-identiteiten

Nu gaan we de verwachte machtigingen testen wanneer u resources in een AKS-cluster maakt en beheert. In deze voor beelden plant en bekijkt u de peuling in de toegewezen naam ruimte van de gebruiker. Vervolgens probeert u het meren deel te plannen en weer te geven buiten de toegewezen naam ruimte.

Stel eerst de *kubeconfig* -context opnieuw in met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . In een vorige sectie stelt u de context in met de referenties van de Cluster beheerder. De gebruiker met beheerders rechten negeert aanmeldings prompts van Azure AD. Zonder de `--admin` para meter wordt de gebruikers context toegepast waarbij alle aanvragen moeten worden geverifieerd met behulp van Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Een eenvoudige NGINX-pod plannen met behulp van de opdracht [kubectl uitvoeren][kubectl-run] in de naam ruimte voor *ontwikkel aars* :

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Voer bij de aanmeldings prompt de referenties in voor uw eigen `appdev@contoso.com` account die aan het begin van het artikel is gemaakt. Zodra u bent aangemeld, wordt het account token in de cache opgeslagen voor toekomstige `kubectl` opdrachten. De NGINX is gepland, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Gebruik nu de opdracht [kubectl Get peul][kubectl-get] om het Peul weer te geven in de naam ruimte van de *ontwikkelaar* .

```console
kubectl get pods --namespace dev
```

Zoals in de volgende voorbeeld uitvoer wordt weer gegeven, wordt de NGINX pod *uitgevoerd*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Cluster resources maken en weer geven buiten de toegewezen naam ruimte

Probeer nu weer te geven van een andere wereld dan de naam ruimte van de *ontwikkelaar* . Gebruik de opdracht [kubectl Get peul][kubectl-get] opnieuw. deze keer ziet er `--all-namespaces` als volgt uit:

```console
kubectl get pods --all-namespaces
```

Het groepslid maatschap van de gebruiker heeft geen Kubernetes-rol waarmee deze actie kan worden uitgevoerd, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Op dezelfde manier kunt u een pod plannen in een andere naam ruimte, zoals de *SRE* -naam ruimte. Het groepslid maatschap van de gebruiker wordt niet uitgelijnd met een Kubernetes-rol en RoleBinding om deze machtigingen te verlenen, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>De SRE toegang tot de AKS-cluster resources testen

Ga als volgt te werk om te controleren of het lidmaatschap van Azure AD-groepslid maatschap en Kubernetes RBAC goed werken tussen verschillende gebruikers en groepen. Probeer de vorige opdrachten uit als u bent aangemeld als de *opssre* -gebruiker.

Stel de *kubeconfig* -context opnieuw in met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] waarmee het eerder in de cache opgeslagen verificatie token voor de *aksdev* -gebruiker wordt gewist:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Probeer in de toegewezen *SRE* -naam ruimte het peul te plannen en weer te geven. Wanneer u hierom wordt gevraagd, meldt u zich aan met uw eigen `opssre@contoso.com` referenties die aan het begin van het artikel zijn gemaakt:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Zoals in de volgende voorbeeld uitvoer wordt weer gegeven, kunt u het Peul maken en bekijken:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Probeer nu weer te geven of te plannen buiten de toegewezen SRE-naam ruimte:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Deze `kubectl` opdrachten mislukken, zoals wordt weer gegeven in de volgende voorbeeld uitvoer. Het groepslid maatschap van de gebruiker en de Kubernetes-rol en RoleBindings verlenen geen machtigingen voor het maken of beheren van resources in andere naam ruimten:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebt u bronnen gemaakt in het AKS-cluster en gebruikers en groepen in azure AD. Voer de volgende opdrachten uit om alle resources op te schonen:

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

Zie voor meer informatie over het beveiligen van Kubernetes-clusters [toegang en identiteits opties voor AKS)][rbac-authorization].

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][operator-best-practices-identity]voor aanbevolen procedures voor het beheren van identiteiten en resources.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
