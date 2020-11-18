---
title: Azure Active Directory integreren met Azure Kubernetes service (verouderd)
description: Meer informatie over het gebruik van de Azure CLI voor het maken en Azure Active Directory van Azure Kubernetes service (AKS)-cluster (verouderd)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 4aa63493bb14db69821ac04db1d2c5a846de7dbe
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682465"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Azure Active Directory integreren met de Azure Kubernetes-service met behulp van de Azure CLI (verouderd)

Azure Kubernetes service (AKS) kan worden geconfigureerd om Azure Active Directory (AD) te gebruiken voor gebruikers verificatie. In deze configuratie kunt u zich aanmelden bij een AKS-cluster met behulp van een Azure AD-verificatie token. Cluster operators kunnen ook Kubernetes met op rollen gebaseerd toegangs beheer (Kubernetes RBAC) configureren op basis van de identiteit van een gebruiker of het lidmaatschap van de Directory groep.

In dit artikel wordt beschreven hoe u de vereiste onderdelen van Azure AD maakt en hoe u een Azure AD-cluster implementeert en hoe u een eenvoudige Kubernetes-rol maakt in het AKS-cluster.

Zie [Azure CLI-voor beelden-AKS-integratie met Azure AD][complete-script]voor het volledige voorbeeld script dat in dit artikel wordt gebruikt.

> [!Important]
> AKS heeft een nieuwe, verbeterde door [AKS beheerde Azure AD-][managed-aad] ervaring waarvoor u de server-of client toepassing niet hoeft te beheren. Als u wilt migreren, volgt u de instructies [hier][managed-aad-migrate].

## <a name="the-following-limitations-apply"></a>De volgende beperkingen zijn van toepassing:

- Azure AD kan alleen worden ingeschakeld op Kubernetes RBAC-cluster.
- Azure AD legacy-integratie kan alleen worden ingeschakeld tijdens het maken van het cluster.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.61 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Ga naar [https://shell.azure.com](https://shell.azure.com) om Cloud Shell in uw browser te openen.

Voor consistentie en het uitvoeren van de opdrachten in dit artikel maakt u een variabele voor de gewenste AKS-cluster naam. In het volgende voor beeld wordt de naam *myakscluster* gebruikt:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Overzicht van Azure AD-verificatie

Azure AD-verificatie wordt geleverd voor AKS-clusters met OpenID Connect Connect. OpenID Connect Connect is een id-laag die boven op het OAuth 2,0-protocol is gebouwd. Voor meer informatie over OpenID Connect Connect raadpleegt u de [Open-ID Connect-documentatie][open-id-connect].

Vanuit het Kubernetes-cluster wordt webhook-token verificatie gebruikt om verificatie tokens te verifiëren. Webhook-token verificatie wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster. Zie de [documentatie voor webhook-verificatie][kubernetes-webhook]voor meer informatie over de verificatie van webhook-tokens.

> [!NOTE]
> Bij het configureren van Azure AD voor AKS-verificatie, worden twee Azure AD-toepassingen geconfigureerd. Deze bewerking moet worden uitgevoerd door een beheerder van een Azure-Tenant.

## <a name="create-azure-ad-server-component"></a>Onderdeel van Azure AD-server maken

Voor de integratie met AKS maakt en gebruikt u een Azure AD-toepassing die fungeert als een eind punt voor de identiteits aanvragen. De eerste Azure AD-toepassing die u nodig hebt, haalt een Azure AD-groepslid maatschap voor een gebruiker op.

Maak het server toepassings onderdeel met behulp van de opdracht [AZ AD App Create][az-ad-app-create] en werk vervolgens de claim groepslid maatschappen bij met de opdracht [AZ AD App Update][az-ad-app-update] . In het volgende voor beeld wordt de variabele *aksname* gebruikt die is gedefinieerd in de sectie [voordat u begint](#before-you-begin) , en maakt een variabele

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Maak nu een service-principal voor de server-app met de opdracht [AZ AD SP Create][az-ad-sp-create] . Deze service-principal wordt gebruikt om zichzelf te verifiëren binnen het Azure-platform. Vervolgens haalt u het geheim van de Service-Principal op met de opdracht [AZ AD SP Credential reset][az-ad-sp-credential-reset] en wijst u de variabele met de naam *serverApplicationSecret* toe voor gebruik in een van de volgende stappen:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

De Azure AD-Service-Principal heeft machtigingen nodig om de volgende acties uit te voeren:

* Mapgegevens lezen
* Aanmelden en gebruikersprofiel lezen

Wijs deze machtigingen toe met behulp van de opdracht [AZ AD App permission add][az-ad-app-permission-add] :

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Ken ten slotte de machtigingen toe die zijn toegewezen in de vorige stap voor de server toepassing met behulp van de opdracht [AZ AD App permission Grant][az-ad-app-permission-grant] . Deze stap mislukt als het huidige account geen Tenant beheerder is. U moet ook machtigingen toevoegen voor de Azure AD-toepassing om informatie aan te vragen waarvoor anderszins een beheerders toestemming nodig is met de [AZ AD App permission Administrator-toestemming][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Een Azure AD-client onderdeel maken

De tweede Azure AD-toepassing wordt gebruikt wanneer een gebruiker zich aanmeldt bij het AKS-cluster met de Kubernetes CLI ( `kubectl` ). Deze client toepassing neemt de verificatie aanvraag van de gebruiker en verifieert hun referenties en machtigingen. Maak de Azure AD-App voor het client onderdeel met behulp van de opdracht [AZ AD App Create][az-ad-app-create] :

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Maak een service-principal voor de client toepassing met behulp van de opdracht [AZ AD SP Create][az-ad-sp-create] :

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Haal de oAuth2-ID op voor de server-app om de verificatie stroom tussen de twee app-onderdelen toe te staan met behulp van de opdracht [AZ AD App Show][az-ad-app-show] . Deze oAuth2-ID wordt in de volgende stap gebruikt.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Voeg de machtigingen voor de client toepassing en server toepassings onderdelen toe om de oAuth2-communicatie stroom te gebruiken met de opdracht [AZ AD App permission add][az-ad-app-permission-add] . Ken vervolgens machtigingen voor de client toepassing toe om te communiceren met de server toepassing met behulp van de opdracht [AZ AD App permission Grant][az-ad-app-permission-grant] :

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Het cluster implementeren

Maak nu het AKS-cluster op basis van de twee Azure AD-toepassingen die zijn gemaakt. Maak eerst een resource groep met de opdracht [AZ Group Create][az-group-create] . In het volgende voor beeld wordt de resource groep gemaakt in de regio *eastus* :

Maak een resource groep voor het cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Haal de Tenant-ID van uw Azure-abonnement op met behulp van de opdracht [AZ account show][az-account-show] . Maak vervolgens het AKS-cluster met behulp van de opdracht [AZ AKS Create][az-aks-create] . De opdracht voor het maken van het AKS-cluster bevat de server-en client toepassings-Id's, het Server Application Service-hoofd geheim en uw Tenant-ID:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Haal ten slotte de cluster beheerders referenties op met de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . In een van de volgende stappen krijgt u de reguliere *gebruikers* cluster referenties om de Azure AD-verificatie stroom in actie te zien.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Een Kubernetes RBAC-binding maken

Voordat een Azure Active Directory-account kan worden gebruikt met het AKS-cluster, moet een functie binding of cluster functie binding worden gemaakt. *Rollen* definiëren de machtigingen die moeten worden verleend en *bindingen* worden toegepast op de gewenste gebruikers. Deze toewijzingen kunnen worden toegepast op een bepaalde naam ruimte of in het hele cluster. Zie [using KUBERNETES RBAC Authorization][rbac-authorization](Engelstalig) voor meer informatie.

De user principal name (UPN) ophalen voor de gebruiker die momenteel is aangemeld met behulp van de opdracht [AZ AD afgemelde gebruiker weer geven][az-ad-signed-in-user-show] . Dit gebruikers account is in de volgende stap ingeschakeld voor Azure AD-integratie.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Als de gebruiker aan wie u de Kubernetes RBAC-binding hebt verleend, zich in dezelfde Azure AD-Tenant bevindt, wijst u machtigingen toe op basis van de *userPrincipalName*. Als de gebruiker zich in een andere Azure AD-Tenant bevindt, kunt u in plaats daarvan de eigenschap *objectId* opvragen en gebruiken.

Maak een YAML-manifest `basic-azure-ad-binding.yaml` met de naam en plak de volgende inhoud. Vervang op de laatste regel *userPrincipalName_or_objectId*  door de UPN-of object-id-uitvoer van de vorige opdracht:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Maak de ClusterRoleBinding met de opdracht [kubectl apply][kubectl-apply] en geef de bestands naam van uw yaml-manifest op:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Toegang tot cluster met Azure AD

We gaan nu de integratie van Azure AD-verificatie voor het AKS-cluster testen. Stel de `kubectl` configuratie context zo in dat reguliere gebruikers referenties worden gebruikt. Deze context geeft alle verificatie aanvragen door aan Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Gebruik nu de [kubectl Get peul][kubectl-get] -opdracht om de hele naam ruimte weer te geven:

```console
kubectl get pods --all-namespaces
```

U ontvangt een aanmeldings prompt om te verifiëren met behulp van de Azure AD-referenties via een webbrowser. Nadat u bent geverifieerd, wordt de `kubectl` opdracht weer gegeven in het AKS-cluster, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Het ontvangen verificatie token `kubectl` is in de cache opgeslagen. U wordt alleen opnieuw gevraagd om u aan te melden wanneer het token is verlopen of het Kubernetes-configuratie bestand opnieuw wordt gemaakt.

Als er een autorisatie fout bericht wordt weer gegeven nadat u zich hebt aangemeld met een webbrowser, zoals in de volgende voorbeeld uitvoer, controleert u de volgende mogelijke problemen:

```output
error: You must be logged in to the server (Unauthorized)
```

* U hebt de juiste object-ID of UPN-naam gedefinieerd, afhankelijk van of het gebruikers account zich in dezelfde Azure AD-Tenant bevindt of niet.
* De gebruiker is geen lid van meer dan 200 groepen.
* Het geheim dat is gedefinieerd in de registratie van de toepassing voor de server komt overeen met de waarde die is geconfigureerd met `--aad-server-app-secret`

## <a name="next-steps"></a>Volgende stappen

Voor het volledige script met de opdrachten die in dit artikel worden weer gegeven, raadpleegt u het [Azure AD-integratie script in de AKS-voor beelden opslag plaats][complete-script].

Zie [toegang tot cluster bronnen beheren met Kubernetes op rollen gebaseerd toegangs beheer en Azure AD-identiteiten in AKS][azure-ad-rbac]voor meer informatie over het gebruik van Azure AD-gebruikers en-groepen om de toegang tot cluster bronnen te beheren.

Zie voor meer informatie over het beveiligen van Kubernetes-clusters [toegang en identiteits opties voor AKS)][rbac-authorization].

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][operator-best-practices-identity]voor aanbevolen procedures voor het beheren van identiteiten en resources.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
