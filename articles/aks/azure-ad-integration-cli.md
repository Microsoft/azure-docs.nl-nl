---
title: Integratie Azure Active Directory met Azure Kubernetes Service (verouderd)
description: Meer informatie over het gebruik van de Azure CLI om een Azure Active Directory AKS-cluster (verouderd) Azure Kubernetes Service maken en gebruiken
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: cb92f84560a88d406f0d519459c27b5d916ec5ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769567"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integratie Azure Active Directory met Azure Kubernetes Service met behulp van de Azure CLI (verouderd)

Azure Kubernetes Service (AKS) kan worden geconfigureerd voor het gebruik van Azure Active Directory (AD) voor gebruikersverificatie. In deze configuratie kunt u zich aanmelden bij een AKS-cluster met behulp van een Azure AD-verificatie-token. Clusteroperators kunnen kubernetes-toegangsbeheer op basis van rollen (Kubernetes RBAC) ook configureren op basis van de identiteit of het lidmaatschap van een directorygroep van een gebruiker.

In dit artikel wordt beschreven hoe u de vereiste Azure AD-onderdelen maakt, vervolgens een cluster met Azure AD-ondersteuning implementeert en een eenvoudige Kubernetes-rol maakt in het AKS-cluster.

Zie [Azure CLI-voorbeelden - AKS-integratie][complete-script]met Azure AD voor het volledige voorbeeldscript dat in dit artikel wordt gebruikt.

> [!Important]
> AKS heeft een nieuwe verbeterde [door AKS beheerde Azure AD-ervaring][managed-aad] waarvoor u geen server of clienttoepassing hoeft te beheren. Volg de instructies hier als u wilt [migreren.][managed-aad-migrate]

## <a name="the-following-limitations-apply"></a>De volgende beperkingen zijn van toepassing:

- Azure AD kan alleen worden ingeschakeld op een Kubernetes RBAC-cluster.
- Verouderde Azure AD-integratie kan alleen worden ingeschakeld tijdens het maken van het cluster.

## <a name="before-you-begin"></a>Voordat u begint

Azure CLI versie 2.0.61 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Ga naar [https://shell.azure.com](https://shell.azure.com) om Cloud Shell in uw browser te openen.

Voor consistentie en om de opdrachten in dit artikel uit te voeren, maakt u een variabele voor de naam van het gewenste AKS-cluster. In het volgende voorbeeld wordt de naam *myakscluster gebruikt:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Overzicht van Azure AD-verificatie

Azure AD-verificatie wordt geleverd aan AKS-clusters met OpenID Connect. OpenID Connect is een identiteitslaag die is gebouwd op het OAuth 2.0-protocol. Zie de Open ID Connect-OpenID Connect meer informatie over [het maken van een verbinding.][open-id-connect]

Vanuit het Kubernetes-cluster wordt webhooktokenverificatie gebruikt om verificatietokens te verifiëren. Webhook-tokenverificatie wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster. Zie de documentatie voor webhook-verificatie voor meer informatie over [webhook-tokenverificatie.][kubernetes-webhook]

> [!NOTE]
> Bij het configureren van Azure AD voor AKS-verificatie worden twee Azure AD-toepassingen geconfigureerd. Deze bewerking moet worden voltooid door een Azure-tenantbeheerder.

## <a name="create-azure-ad-server-component"></a>Een Azure AD-serveronderdeel maken

Als u wilt integreren met AKS, maakt en gebruikt u een Azure AD-toepassing die fungeert als eindpunt voor de identiteitsaanvragen. De eerste Azure AD-toepassing die u nodig hebt, krijgt een Azure AD-groepslidmaatschap voor een gebruiker.

Maak het servertoepassingsonderdeel met de [opdracht az ad app create][az-ad-app-create] en werk vervolgens de claims voor groepslidmaatschap bij met de opdracht az ad app [update.][az-ad-app-update] In het volgende voorbeeld wordt de *variabele aksname* gebruikt die is gedefinieerd in de sectie [Voordat u begint,](#before-you-begin) en wordt een variabele gemaakt

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Maak nu een service-principal voor de server-app met de [opdracht az ad sp create.][az-ad-sp-create] Deze service-principal wordt gebruikt om zichzelf te verifiëren binnen het Azure-platform. Haal vervolgens het geheim van de service-principal op met behulp van de [opdracht az ad sp credential reset][az-ad-sp-credential-reset] en wijs deze toe aan de variabele *serverApplicationSecret* voor gebruik in een van de volgende stappen:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

De Azure AD-service-principal heeft machtigingen nodig om de volgende acties uit te voeren:

* Mapgegevens lezen
* Aanmelden en gebruikersprofiel lezen

Wijs deze machtigingen toe met behulp van [de opdracht az ad app permission add:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Verleen ten slotte de machtigingen die in de vorige stap zijn toegewezen voor de servertoepassing met behulp van de [opdracht az ad app permission grant.][az-ad-app-permission-grant] Deze stap mislukt als het huidige account geen tenantbeheerder is. U moet ook machtigingen voor de Azure AD-toepassing toevoegen om informatie aan te vragen waarvoor anders beheerders toestemming is vereist met behulp van [az ad app permission admin-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Een Azure AD-clientonderdeel maken

De tweede Azure AD-toepassing wordt gebruikt wanneer een gebruiker zich bij het AKS-cluster aanmeldt met de Kubernetes CLI ( `kubectl` ). Deze clienttoepassing neemt de verificatieaanvraag van de gebruiker en verifieert zijn referenties en machtigingen. Maak de Azure AD-app voor het clientonderdeel met behulp van [de opdracht az ad app create:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Maak een service-principal voor de clienttoepassing met behulp van [de opdracht az ad sp create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Haal de oAuth2-id voor de server-app op om de verificatiestroom tussen de twee app-onderdelen toe te staan met behulp van [de opdracht az ad app show.][az-ad-app-show] Deze oAuth2-id wordt gebruikt in de volgende stap.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Voeg de machtigingen voor de clienttoepassing en servertoepassingsonderdelen toe om de oAuth2-communicatiestroom te gebruiken met behulp van de [opdracht az ad app permission add.][az-ad-app-permission-add] Verleen vervolgens machtigingen voor de clienttoepassing om te communiceren met de servertoepassing met behulp van [de opdracht az ad app permission grant:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Het cluster implementeren

Nu de twee Azure AD-toepassingen zijn gemaakt, maakt u het AKS-cluster zelf. Maak eerst een resourcegroep met de [opdracht az group create.][az-group-create] In het volgende voorbeeld wordt de resourcegroep gemaakt in de *regio EASTUS:*

Maak een resourcegroep voor het cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Haal de tenant-id van uw Azure-abonnement op met behulp [van de opdracht az account show.][az-account-show] Maak vervolgens het AKS-cluster met behulp van [de opdracht az aks create.][az-aks-create] De opdracht voor het maken van het AKS-cluster bevat de server- en clienttoepassings-id's, het principalgeheim van de servertoepassingsservice en uw tenant-id:

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

Haal ten slotte de beheerdersreferenties van het cluster op met [de opdracht az aks get-credentials.][az-aks-get-credentials] In een van de volgende stappen  krijgt u de reguliere gebruikersclusterreferenties om de Azure AD-verificatiestroom in actie te zien.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Kubernetes RBAC-binding maken

Voordat een Azure Active Directory kan worden gebruikt met het AKS-cluster, moet er een rolbinding of clusterrolbinding worden gemaakt. *Rollen* definiëren de machtigingen die moeten worden verleend en *bindingen* passen deze toe op de gewenste gebruikers. Deze toewijzingen kunnen worden toegepast op een bepaalde naamruimte of op het hele cluster. Zie Using [Kubernetes RBAC authorization (Kubernetes RBAC-autorisatie gebruiken) voor meer informatie.][rbac-authorization]

Haal de user principal name (UPN) op voor de gebruiker die momenteel is aangemeld met de opdracht [az ad signed-in-user show.][az-ad-signed-in-user-show] Dit gebruikersaccount is ingeschakeld voor Azure AD-integratie in de volgende stap.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Als de gebruiker die u de Kubernetes RBAC-binding verleent, zich in dezelfde Azure AD-tenant beidt, wijst u machtigingen toe op basis van *de userPrincipalName.* Als de gebruiker zich in een andere Azure AD-tenant heeft, kunt u in plaats daarvan de *eigenschap objectId opvragen en* gebruiken.

Maak een YAML-manifest met `basic-azure-ad-binding.yaml` de naam en plak de volgende inhoud. Vervang op de laatste regel *userPrincipalName_or_objectId*  door de UPN- of object-id-uitvoer van de vorige opdracht:

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

Maak de ClusterRoleBinding met behulp van de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Toegang tot cluster met Azure AD

Nu gaan we de integratie van Azure AD-verificatie voor het AKS-cluster testen. Stel de `kubectl` configuratiecontext in op het gebruik van reguliere gebruikersreferenties. Deze context geeft alle verificatieaanvragen door via Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Gebruik nu de [opdracht kubectl get pods][kubectl-get] om pods in alle naamruimten weer te geven:

```console
kubectl get pods --all-namespaces
```

U ontvangt een aanmeldingsprompt om te verifiëren met behulp van Azure AD-referenties met behulp van een webbrowser. Nadat u de verificatie hebt voltooid, worden met de opdracht de pods in het `kubectl` AKS-cluster weergegeven, zoals wordt weergegeven in de volgende voorbeelduitvoer:

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

Het ontvangen verificatie-token voor `kubectl` wordt in de cache opgeslagen. U wordt alleen opnieuw om u aan te melden wanneer het token is verlopen of het Kubernetes-configuratiebestand opnieuw is gemaakt.

Als er een autorisatiefoutbericht wordt weergegeven nadat u zich hebt aangemeld met een webbrowser, zoals in de volgende voorbeelduitvoer, controleert u de volgende mogelijke problemen:

```output
error: You must be logged in to the server (Unauthorized)
```

* U hebt de juiste object-id of UPN gedefinieerd, afhankelijk van of het gebruikersaccount zich al dan niet in dezelfde Azure AD-tenant heeft.
* De gebruiker is geen lid van meer dan 200 groepen.
* Geheim dat is gedefinieerd in de toepassingsregistratie voor server komt overeen met de waarde die is geconfigureerd met `--aad-server-app-secret`

## <a name="next-steps"></a>Volgende stappen

Zie het Azure AD-integratiescript in de [AKS-voorbeelden-repo][complete-script]voor het volledige script dat de opdrachten bevat die in dit artikel worden weergegeven.

Zie Toegang tot clusterbronnen beheren met op [kubernetes-rollen gebaseerd toegangsbeheer en Azure AD-identiteiten in AKS][azure-ad-rbac]als u Azure AD-gebruikers en -groepen wilt gebruiken om de toegang tot clusterbronnen te beheren.

Zie Access [and identity options for AKS][rbac-authorization](Toegangs- en identiteitsopties voor AKS) voor meer informatie over het beveiligen van Kubernetes-clusters.

Zie Best practices for [authentication and authorization in AKS (Best practices voor][operator-best-practices-identity]verificatie en autorisatie in AKS) voor best practices voor identiteits- en resourcebeheer.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-ad-app-create]: /cli/azure/ad/app#az_ad_app_create
[az-ad-app-update]: /cli/azure/ad/app#az_ad_app_update
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az_ad_app_permission_add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az_ad_app_permission_grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az_ad_app_permission_admin_consent
[az-ad-app-show]: /cli/azure/ad/app#az_ad_app_show
[az-group-create]: /cli/azure/group#az_group_create
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az_ad_signed_in_user_show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
