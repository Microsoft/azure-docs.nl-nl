---
title: Linkerd installeren in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en gebruiken van Linkerd voor het maken van een service-net in een Azure Kubernetes service-cluster (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5b2ec220bbf14a79247ed2c78a0e84f4717fae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94684233"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Linkerd installeren in azure Kubernetes service (AKS)

[Linkerd][linkerd-github] is een open-source service-net-en [CNCF-incubatie-project][linkerd-cncf]. Linkerd is een ultralight service-net dat functies biedt die het beheer van verkeer, service-identiteit en beveiliging, betrouw baarheid en naleving kunnen bevatten. Voor meer informatie over Linkerd raadpleegt u de officiële [Veelgestelde vragen over Linkerd][linkerd-faq] en [Linkerd Architecture][linkerd-architecture] -documentatie.

In dit artikel wordt beschreven hoe u Linkerd installeert. Het `linkerd` binaire bestand van de Linkerd-client wordt geïnstalleerd op de client computer en de Linkerd-onderdelen worden geïnstalleerd in een Kubernetes-cluster op AKS.

> [!NOTE]
> Deze instructies verwijzen naar Linkerd-versie `stable-2.6.0` .
>
> De Linkerd `stable-2.6.x` kan worden uitgevoerd op Kubernetes-versies `1.13+` . U vindt extra stabiele en Linkerd versies van [github-Linkerd-releases][linkerd-github-releases].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Down load en installeer het binaire bestand voor de Linkerd Linkerd-client
> * Linkerd installeren op AKS
> * De Linkerd-installatie valideren
> * Het dash board openen
> * Linkerd verwijderen uit AKS

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.13` en hoger, met KUBERNETES RBAC is ingeschakeld) en een `kubectl` verbinding met het cluster tot stand hebt gebracht. Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart]start.

Alle Linkerd-peulen moeten worden gepland om te worden uitgevoerd op Linux-knoop punten. deze instelling is de standaard waarde voor de installatie methode die hieronder wordt beschreven en vereist geen aanvullende configuratie.

In dit artikel worden de Linkerd-installatie richtlijnen gescheiden in verschillende afzonderlijke stappen. Het resultaat is hetzelfde als de officiële Linkerd aan de [slag.][linkerd-getting-started]

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Linkerd installeren op AKS

Voordat we Linkerd installeren, voeren we controles voorafgaand aan de installatie uit om te bepalen of het besturings vlak op het AKS-cluster kan worden geïnstalleerd:

```console
linkerd check --pre
```

Als het goed is, ziet u een van de volgende opties om aan te geven dat uw AKS-cluster een geldig installatie doel is voor Linkerd:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Nu is het tijd om de Linkerd-onderdelen te installeren. Gebruik de `linkerd` en `kubectl` binaire bestanden voor het installeren van de Linkerd-onderdelen in uw AKS-cluster. Er `linkerd` wordt automatisch een naam ruimte gemaakt en de onderdelen worden in deze naam ruimte geïnstalleerd.

```console
linkerd install | kubectl apply -f -
```

Linkerd implementeert een aantal objecten. U ziet de lijst in de uitvoer van de `linkerd install` bovenstaande opdracht. De implementatie van de Linkerd-onderdelen moet ongeveer 1 minuut duren, afhankelijk van uw cluster omgeving.

Op dit moment hebt u Linkerd geïmplementeerd op uw AKS-cluster. Om ervoor te zorgen dat de implementatie van Linkerd succesvol is, gaan we verder met de volgende sectie om [de Linkerd-installatie te valideren](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>De Linkerd-installatie valideren

Controleer of de resources zijn gemaakt. Gebruik de opdrachten [kubectl Get SVC][kubectl-get] en [kubectl Get POD][kubectl-get] om de naam ruimte op te vragen `linkerd` , waarbij de Linkerd-onderdelen zijn geïnstalleerd met behulp van de `linkerd install` opdracht:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

In de volgende voorbeeld uitvoer ziet u de services en het Peul (gepland op Linux-knoop punten) die nu moeten worden uitgevoerd:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd biedt een opdracht via de `linkerd` binaire client om te valideren dat het Linkerd-besturings vlak is geïnstalleerd en geconfigureerd.

```console
linkerd check
```

Als het goed is, ziet u een van de volgende zaken:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Het dash board openen

Linkerd wordt geleverd met een dash board dat inzicht biedt in het service-net en werk belastingen. Gebruik de opdracht om toegang te krijgen tot het dash board `linkerd dashboard` . Met deze opdracht maakt u een beveiligde verbinding tussen uw client computer en de relevante peul in uw AKS-cluster met behulp van de [kubectl-poort][kubectl-port-forward] . Vervolgens wordt het dash board automatisch in de standaard browser geopend.

```console
linkerd dashboard
```

De opdracht maakt ook een port-forward en retourneert een koppeling voor de Grafana-Dash boards.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Linkerd verwijderen uit AKS

> [!WARNING]
> Het verwijderen van Linkerd van een actief systeem kan leiden tot problemen met verkeer tussen uw services. Zorg ervoor dat u voor het systeem nog steeds goed werkt zonder Linkerd voordat u doorgaat.

Eerst moet u de gegevensfeed-proxy's verwijderen. Verwijder alle [aantekeningen][linkerd-automatic-proxy-injection] voor automatische proxy-injecties uit de naam ruimten van de werk ruimte en implementeer uw implementaties van werk belastingen. Uw workloads moeten geen bijbehorende gegevenslaag onderdelen meer hebben.

Ten slotte verwijdert u het besturings vlak als volgt:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over de installatie-en configuratie opties voor Linkerd, raadpleegt u de volgende officiële Linkerd-richt lijnen:

- [Linkerd-helm-installatie][linkerd-install-with-helm]
- [Linkerd-installatie op meerdere stages voor rol privileges][linkerd-multi-stage-installation]

U kunt ook aanvullende scenario's volgen met behulp van:

- [Linkerd emojivoto-demo][linkerd-demo-emojivoto]
- [Demo van Linkerd-boeken][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
