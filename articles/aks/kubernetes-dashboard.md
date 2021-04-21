---
title: Een cluster Azure Kubernetes Service beheren met het webdashboard
description: Meer informatie over het gebruik van het ingebouwde Kubernetes-webinterfacedashboard om een AKS-cluster (Azure Kubernetes Service) te beheren
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 0d872a60c4aea89e621fe25ade45697244a74fa8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779719"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Toegang tot het Kubernetes-webdashboard in Azure Kubernetes Service (AKS)

Kubernetes bevat een webdashboard dat kan worden gebruikt voor basisbeheerbewerkingen. Met dit dashboard kunt u de basisstatus en metrische gegevens voor uw toepassingen weergeven, services maken en implementeren en bestaande toepassingen bewerken. In dit artikel wordt beschreven hoe u toegang krijgt tot het Kubernetes-dashboard met behulp van de Azure CLI en hoe u vervolgens enkele eenvoudige dashboardbewerkingen doormaakt.

Zie Kubernetes Web UI Dashboard voor meer informatie over het [Kubernetes-dashboard.][kubernetes-dashboard] AKS maakt gebruik van versie 2.0 en hoger van het opensource-dashboard.

> [!WARNING]
> **De invoegvoeging van het AKS-dashboard is ingesteld voor afschaffing. Gebruik in plaats daarvan de [Kubernetes-resourceweergave in Azure Portal (preview).][kubernetes-portal]** 
> * Het Kubernetes-dashboard is standaard ingeschakeld voor clusters met een Kubernetes-versie lager dan 1.18.
> * De invoegseling van het dashboard wordt standaard uitgeschakeld voor alle nieuwe clusters die zijn gemaakt op Kubernetes 1.18 of hoger. 
 > * Vanaf Kubernetes 1.19 in preview biedt AKS geen ondersteuning meer voor de installatie van de beheerde kube-dashboard-invoegsel. 
 > * Bestaande clusters met de invoeg-invoeging ingeschakeld, worden niet beïnvloed. Gebruikers kunnen het opensource-dashboard nog steeds handmatig installeren als door de gebruiker geïnstalleerde software.

## <a name="before-you-begin"></a>Voordat u begint

In de stappen die in dit document worden beschreven, wordt ervan uitgenomen dat u een AKS-cluster hebt gemaakt en verbinding `kubectl` hebt gemaakt met het cluster. Als u een AKS-cluster wilt maken, gaat u naar [Quickstart: Een][aks-quickstart]cluster Azure Kubernetes Service implementeren met behulp van de Azure CLI.

Ook moet Azure CLI versie 2.6.0 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="disable-the-kubernetes-dashboard"></a>Het Kubernetes-dashboard uitschakelen

De invoegwaarde kube-dashboard is standaard ingeschakeld op clusters die ouder **zijn dan K8s 1.18.** De invoegopdracht kan worden uitgeschakeld door de volgende opdracht uit te voeren.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Het Kubernetes-dashboard starten

> [!WARNING]
> De invoegversie van het AKS-dashboard is afgeschaft voor versie 1.19+. Gebruik in plaats [daarvan de Kubernetes-resourceweergave in de Azure Portal (preview).][kubernetes-portal] 
> * Met de volgende opdracht wordt nu de resourceweergave van Azure Portal geopend in plaats van het kubernetes-dashboard voor versie 1.19 en hoger.

Als u het Kubernetes-dashboard in een cluster wilt starten, gebruikt u [de opdracht az aks browse.][az-aks-browse] Voor deze opdracht is de installatie van de invoegsel kube-dashboard op het cluster vereist. Deze is standaard opgenomen in clusters met een oudere versie dan Kubernetes 1.18.

In het volgende voorbeeld wordt het dashboard geopend voor het cluster met de *naam myAKSCluster* in de resourcegroep met de *naam myResourceGroup:*

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Met deze opdracht maakt u een proxy tussen uw ontwikkelsysteem en de Kubernetes-API en opent u een webbrowser naar het Kubernetes-dashboard. Als het Kubernetes-dashboard niet in een webbrowser wordt geopend, kopieert en plakt u het URL-adres dat u in de Azure CLI hebt genoteerd, meestal `http://127.0.0.1:8001` .

> [!NOTE]
> Als u het dashboard op niet `http://127.0.0.1:8001` ziet, kunt u handmatig een route naar de volgende adressen maken. Clusters met 1.16 of hoger gebruiken https en vereisen een afzonderlijk eindpunt.
> * K8s 1.16 of hoger: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 en lager: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Meld u aan bij het dashboard (kubernetes 1.16+)

> [!IMPORTANT]
> Vanaf [v1.10.1 van het Kubernetes-dashboard of kubernetes](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) v1.16+ kan het serviceaccount 'kubernetes-dashboard' niet meer worden gebruikt om resources op te halen vanwege een beveiligingsfix [in die release](https://github.com/kubernetes/dashboard/pull/3400). Als gevolg hiervan retourneren aanvragen zonder auth-gegevens de fout [401 unauthorized](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). Een Bearer-token dat is opgehaald uit een serviceaccount kan nog steeds worden gebruikt zoals in dit [Kubernetes-dashboardvoorbeeld,](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)maar dit is van invloed op de aanmeldingsstroom van de dashboardinvoegsel vergeleken met oudere versies.
>
>Als u nog steeds een eerdere versie dan 1.16 hebt, kunt u nog steeds machtigingen verlenen voor het serviceaccount 'kubernetes-dashboard', maar dit wordt **niet aanbevolen:**
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Voor het eerste scherm dat wordt weergegeven, is een kubeconfig of token vereist. Voor beide opties zijn resourcemachtigingen vereist om deze resources weer te geven in het dashboard.

![aanmeldingsscherm](./media/kubernetes-dashboard/login.png)

**Een kubeconfig gebruiken**

Voor clusters die zowel voor Azure AD als voor niet-Azure AD zijn ingeschakeld, kan een kubeconfig worden doorgegeven. Zorg ervoor dat toegangstokens geldig zijn. Als uw tokens zijn verlopen, kunt u tokens vernieuwen via kubectl.

1. De beheerder kubeconfig instellen met `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Selecteer `Kubeconfig` en klik om de `Choose kubeconfig file` bestands selector te openen
1. Selecteer uw kubeconfig-bestand (standaard ingesteld op $HOME/.kube/config)
1. Klik op `Sign In`

**Een token gebruiken**

1. Voor **een cluster dat niet is ingeschakeld voor Azure AD,** moet u het token uitvoeren en kopiëren dat is gekoppeld aan het `kubectl config view` gebruikersaccount van uw cluster.
1. Plak bij het aanmelden in de tokenoptie.    
1. Klik op `Sign In`

Haal voor clusters met Azure AD uw AAD-token op met de volgende opdracht. Controleer of u de resourcegroep en clusternaam in de opdracht hebt vervangen.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Zodra dit is gelukt, wordt een pagina weergegeven die vergelijkbaar is met de onderstaande pagina.

![De overzichtspagina van het Kubernetes-webdashboard](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Een app maken

Voor de volgende stappen moet de gebruiker machtigingen hebben voor de respectieve resources. 

Om te zien hoe het Kubernetes-dashboard de complexiteit van beheertaken kan verminderen, gaan we een toepassing maken. U kunt een toepassing maken vanuit het Kubernetes-dashboard door tekstinvoer, een YAML-bestand of via een grafische wizard op te geven.

Voltooi de volgende stappen om een toepassing te maken:

1. Selecteer de **knop** Maken in het rechterbovenvenster.
1. Als u de grafische wizard wilt gebruiken, kiest u **Een app maken.**
1. Geef een naam op voor de implementatie, zoals *nginx*
1. Voer de naam in voor de containerafbeelding die moet worden gebruikt, zoals *nginx:1.15.5*
1. Als u poort 80 voor webverkeer wilt gebruiken, maakt u een Kubernetes-service. Selecteer **onder Service** de optie **Extern** en voer **vervolgens 80 in** voor zowel de poort als de doelpoort.
1. Wanneer u klaar bent, **selecteert u Implementeren om** de app te maken.

![Een app implementeren in het Kubernetes-webdashboard](./media/kubernetes-dashboard/create-app.png)

Het duurt een paar minuten voor een openbaar extern IP-adres is toegewezen aan de Kubernetes-service. Selecteer aan de linkerkant onder **Detectie en taakverdeling** de optie **Services.** De service van uw toepassing wordt weergegeven, met inbegrip van de *externe eindpunten*, zoals wordt weergegeven in het volgende voorbeeld:

![Lijst met services en eindpunten weergeven](./media/kubernetes-dashboard/view-services.png)

Selecteer het eindpuntadres om een webbrowservenster te openen op de standaard NGINX-pagina:

![De standaard NGINX-pagina van de geïmplementeerde toepassing weergeven](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Podgegevens weergeven

Het Kubernetes-dashboard kan eenvoudige metrische bewakingsgegevens en informatie over probleemoplossing bieden, zoals logboeken.

Selecteer **Pods** in het linkermenu voor meer informatie over uw toepassingspods. De lijst met beschikbare pods wordt weergegeven. Kies uw *nginx-pod* om informatie weer te geven, zoals resourceverbruik:

![Podgegevens weergeven](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>De toepassing bewerken

Naast het maken en weergeven van toepassingen kan het Kubernetes-dashboard worden gebruikt om toepassingsimplementaties te bewerken en bij te werken. Om extra redundantie voor de toepassing te bieden, gaan we het aantal NGINX-replica's verhogen.

Een implementatie bewerken:

1. Selecteer **Implementaties** in het linkermenu en kies vervolgens uw *nginx-implementatie.*
1. Selecteer **Bewerken** in de rechterbovenhoek van de navigatiebalk.
1. Zoek de `spec.replica` waarde rond regel 20. Als u het aantal replica's voor de toepassing wilt verhogen, wijzigt u deze waarde *van 1* in *3*.
1. Selecteer **Bijwerken wanneer** u klaar bent.

![De implementatie bewerken om het aantal replica's bij te werken](./media/kubernetes-dashboard/edit-deployment.png)

Het duurt even voordat de nieuwe pods in een replicaset zijn gemaakt. Kies in het menu aan de linkerkant **Replicasets** en kies vervolgens uw *nginx-replicaset.* De lijst met pods geeft nu het bijgewerkte aantal replica's weer, zoals wordt weergegeven in de volgende voorbeelduitvoer:

![Informatie over de replicaset weergeven](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Volgende stappen

Zie het Kubernetes-webinterfacedashboard voor meer informatie over [het Kubernetes-dashboard.][kubernetes-dashboard]

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account#az_account_get-access-token
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
