---
title: Een Azure Kubernetes service-cluster beheren met het webdashboard
description: Meer informatie over het gebruik van het ingebouwde Kubernetes Web UI-dash board voor het beheren van een Azure Kubernetes service-cluster (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: a80082ac524a4777b3b5ee32d946e9db8ec6e7f5
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681615"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Toegang tot het Kubernetes Web dash board in azure Kubernetes service (AKS)

Kubernetes bevat een webdashboard dat kan worden gebruikt voor elementaire beheer bewerkingen. Met dit dash board kunt u de basis status en-metrische gegevens voor uw toepassingen bekijken, services maken en implementeren, en bestaande toepassingen bewerken. In dit artikel wordt beschreven hoe u met de Azure CLI toegang krijgt tot het Kubernetes-dash board en vervolgens een aantal eenvoudige dashboard bewerkingen doorloopt.

Voor meer informatie over het Kubernetes-dash board raadpleegt u [Kubernetes Web UI-dash board][kubernetes-dashboard]. AKS maakt gebruik van versie 2,0 en groter van het open-source dash board.

> [!WARNING]
> **De AKS-dashboard invoeg toepassing is ingesteld voor afschaffing. Gebruik in plaats daarvan de [resource weergave Kubernetes in de Azure Portal (preview)][kubernetes-portal] .** 
> * Het Kubernetes-dash board is standaard ingeschakeld voor clusters met een Kubernetes-versie van minder dan 1,18.
> * De dash board-invoeg toepassing wordt standaard uitgeschakeld voor alle nieuwe clusters die zijn gemaakt op Kubernetes 1,18 of hoger. 
 > * Vanaf Kubernetes 1,19 in de preview-versie van AKS wordt de installatie van de invoeg toepassing Managed uitvoeren-dash board niet meer ondersteund. 
 > * Bestaande clusters waarvoor de invoeg toepassing is ingeschakeld, worden niet beïnvloed. Gebruikers kunnen het open-source dash board hand matig installeren als door de gebruiker geïnstalleerde software.

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit document worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt en een `kubectl` verbinding met het cluster tot stand hebt gebracht. Als u een AKS-cluster moet maken, raadpleegt u [Quick Start: een Azure Kubernetes service-cluster implementeren met behulp van de Azure cli][aks-quickstart].

Ook moet de Azure CLI-versie 2.6.0 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="disable-the-kubernetes-dashboard"></a>Het Kubernetes-dash board uitschakelen

De uitvoeren-dash board-invoeg toepassing is **standaard ingeschakeld voor clusters die ouder zijn dan K8s 1,18**. De invoeg toepassing kan worden uitgeschakeld door de volgende opdracht uit te voeren.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Het Kubernetes-dash board starten

Als u het Kubernetes-dash board op een cluster wilt starten, gebruikt u de opdracht [AZ AKS Browse][az-aks-browse] . Deze opdracht vereist de installatie van de uitvoeren-dashboard invoeg toepassing op het cluster, dat standaard is opgenomen in clusters met een oudere versie dan Kubernetes 1,18.

In het volgende voor beeld wordt het dash board geopend voor het cluster met de naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Met deze opdracht maakt u een proxy tussen uw ontwikkel systeem en de Kubernetes-API en opent u een webbrowser op het Kubernetes-dash board. Als een webbrowser niet wordt geopend in het Kubernetes-dash board, kopieert en plakt u het URL-adres dat u in de Azure CLI hebt genoteerd, meestal `http://127.0.0.1:8001` .

> [!NOTE]
> Als u het dash board niet ziet, `http://127.0.0.1:8001` kunt u hand matig naar de volgende adressen omleiden. Clusters op 1,16 of meer gebruikmaken van HTTPS en vereisen een afzonderlijk eind punt.
> * K8s 1,16 of hoger: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1,15 en lager: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Aanmelden bij het dash board (kubernetes 1.16 +)

> [!IMPORTANT]
> Vanaf [de v-1.10.1 van het Kubernetes-dash board](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) of Kubernetes v 1.16 + het service account "Kubernetes-dash board" kan niet meer worden gebruikt voor het ophalen van resources vanwege een [beveiligings oplossing in die release](https://github.com/kubernetes/dashboard/pull/3400). Daarom retour neren aanvragen zonder auth-gegevens een niet- [geautoriseerde 401-fout](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). Een Bearer-token dat is opgehaald uit een service account kan nog steeds worden gebruikt in dit [Kubernetes-dash board-voor beeld](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), maar dit heeft gevolgen voor de aanmeldings stroom van de dashboard-invoeg toepassing, vergeleken met oudere versies.
>
>Als u nog steeds een versie van vóór 1,16 uitvoert, kunt u nog steeds machtigingen geven voor het service account ' kubernetes-dash board ', maar dit wordt **niet aanbevolen**:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Het eerste scherm dat wordt weer gegeven, vereist een kubeconfig of token. Voor beide opties zijn resource machtigingen vereist om deze resources in het dash board weer te geven.

![aanmeldings scherm](./media/kubernetes-dashboard/login.png)

**Een kubeconfig gebruiken**

Voor zowel Azure AD ingeschakelde als niet-Azure AD-clusters kan een kubeconfig worden door gegeven in. Controleer of de toegangs tokens geldig zijn. als uw tokens verlopen zijn, kunt u tokens vernieuwen via kubectl.

1. De beheerder kubeconfig instellen met `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Selecteer `Kubeconfig` en klik `Choose kubeconfig file` om bestands kiezer te openen
1. Uw kubeconfig-bestand selecteren (de standaard instelling is $HOME/.Kube/config)
1. Klik op `Sign In`

**Een token gebruiken**

1. Voor **niet-Azure AD-cluster** voert u uit `kubectl config view` en kopieert u het token dat is gekoppeld aan het gebruikers account van uw cluster.
1. Plak de token optie bij het aanmelden.    
1. Klik op `Sign In`

Voor Azure AD-clusters haalt u uw AAD-token op met de volgende opdracht. Controleer of u de resource groep en de cluster naam in de opdracht hebt vervangen.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Als dit lukt, wordt een pagina weer gegeven die er ongeveer als volgt uitziet.

![De overzichts pagina van het Kubernetes Web dash board](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Een app maken

Voor de volgende stappen moet de gebruiker machtigingen hebben voor de respectieve resources. 

Als u wilt zien hoe het Kubernetes-dash board de complexiteit van beheer taken kan verminderen, gaan we een toepassing maken. U kunt een toepassing vanuit het Kubernetes-dash board maken door tekst invoer, een YAML-bestand of een grafische wizard te leveren.

Voer de volgende stappen uit om een toepassing te maken:

1. Selecteer de knop **maken** in het rechter venster.
1. Als u de grafische wizard wilt gebruiken, kiest u voor het **maken van een app**.
1. Geef een naam op voor de implementatie, bijvoorbeeld *nginx*
1. Voer de naam in voor de container installatie kopie die moet worden gebruikt, zoals *nginx: 1.15.5*
1. Als u poort 80 voor webverkeer beschikbaar wilt maken, maakt u een Kubernetes-service. Onder **service** selecteert u **extern** en voert u **80** in voor de poort en de doel poort.
1. Wanneer u klaar bent, selecteert u **implementeren** om de app te maken.

![Een app implementeren in het Kubernetes Web dash board](./media/kubernetes-dashboard/create-app.png)

Het duurt enkele minuten voordat een openbaar extern IP-adres wordt toegewezen aan de Kubernetes-service. Klik aan de linkerkant onder **detectie en taak verdeling** **Services** selecteren. De service van uw toepassing wordt vermeld, inclusief de *externe eind punten*, zoals wordt weer gegeven in het volgende voor beeld:

![Lijst met Services en eind punten weer geven](./media/kubernetes-dashboard/view-services.png)

Selecteer het eindpunt adres om een webbrowser venster te openen op de standaard NGINX-pagina:

![De standaard NGINX-pagina van de geïmplementeerde toepassing weer geven](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod-gegevens weer geven

Het Kubernetes-dash board kan basis gegevens over bewaking en informatie over het oplossen van problemen bieden, zoals Logboeken.

Als u meer informatie over uw toepassing wilt zien, selecteert u in het menu aan de linkerkant de optie **peul** . De lijst met beschik bare peulen wordt weer gegeven. Kies uw *nginx* pod om informatie weer te geven, zoals Resource verbruik:

![Pod-gegevens weer geven](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>De toepassing bewerken

Naast het maken en weer geven van toepassingen, kunt u het Kubernetes-dash board gebruiken om toepassings implementaties te bewerken en bij te werken. Als u aanvullende redundantie voor de toepassing wilt bieden, kunt u het aantal NGINX-replica's verhogen.

Een implementatie bewerken:

1. Selecteer **implementaties** in het menu links en kies vervolgens uw *nginx* -implementatie.
1. Selecteer **bewerken** in de rechter bovenhoek navigatie balk.
1. Zoek de `spec.replica` waarde, rond regel 20. Als u het aantal replica's voor de toepassing wilt verhogen, wijzigt u deze waarde van *1* in *3*.
1. Selecteer **bijwerken** wanneer gereed.

![De implementatie bewerken om het aantal replica's bij te werken](./media/kubernetes-dashboard/edit-deployment.png)

Het kan even duren voordat het nieuwe Peul is gemaakt binnen een replicaset. Kies in het menu links de optie **replica sets** en kies vervolgens uw *nginx* -replicaset. De lijst met peulen weerspiegelt nu het bijgewerkte aantal replica's, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

![Informatie over de replicaset weer geven](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het Kubernetes-dash board vindt u in het [dash board Kubernetes Web UI][kubernetes-dashboard].

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
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
