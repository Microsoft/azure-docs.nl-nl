---
title: Consul installeren in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en gebruiken van consul voor het maken van een service-net in een Azure Kubernetes service-cluster (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683916"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Consul installeren en gebruiken in azure Kubernetes service (AKS)

[Consul][consul-github] is een open-source service-net dat een belang rijke set functionaliteit biedt voor de micro Services in een Kubernetes-cluster. Deze functies omvatten service detectie, status controle, service segmentatie en waarneembaarheid. Zie voor meer informatie over consul de officiële [What is consul?][consul-docs-concepts] -documentatie.

In dit artikel wordt beschreven hoe u consul installeert. De consul-onderdelen worden in een Kubernetes-cluster op AKS geïnstalleerd.

> [!NOTE]
> Deze instructies verwijzen naar consul `1.6.0` -versie en gebruiken ten minste helm-versie `2.14.2` .
>
> De consul- `1.6.x` releases kunnen worden uitgevoerd op Kubernetes-versies `1.13+` . U kunt aanvullende consul-versies vinden op [github-consul releases][consul-github-releases] en informatie over elk van de releases op [consul-release opmerkingen][consul-release-notes].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De consul-onderdelen installeren op AKS
> * De consul-installatie valideren
> * Consul verwijderen uit AKS

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.13` en hoger, met KUBERNETES RBAC is ingeschakeld) en een `kubectl` verbinding met het cluster tot stand hebt gebracht. Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart]start. Zorg ervoor dat uw cluster ten minste drie knoop punten in de Linux-knooppunt groep bevat.

U hebt [helm][helm] nodig om deze instructies te volgen en consul te installeren. Het is raadzaam dat u de nieuwste stabiele versie correct hebt geïnstalleerd en geconfigureerd in uw cluster. Als u hulp nodig hebt bij het installeren van helm, raadpleegt u de [AKS helm Installation guidance][helm-install](Engelstalig). Alle consul-peulen moeten ook worden gepland om te worden uitgevoerd op Linux-knoop punten.

In dit artikel worden de consul-installatie richtlijnen gescheiden in verschillende afzonderlijke stappen. Het eind resultaat is hetzelfde als de officiële consul-installatie [richtlijnen][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>De consul-onderdelen installeren op AKS

We beginnen met `v0.10.0` het downloaden van de versie van het consul helm-diagram. Deze versie van de grafiek bevat consul-versie `1.6.0` .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Gebruik helm en de gedownloade `consul-helm` grafiek om de consul-onderdelen `consul` in de naam ruimte in uw AKS-cluster te installeren. 

> [!NOTE]
> **Installatieopties**
> 
> We gebruiken de volgende opties als onderdeel van onze installatie:
> - `connectInject.enabled=true` -proxy's inschakelen voor injectie in peul
> - `client.enabled=true` -Consul-clients kunnen worden uitgevoerd op elk knoop punt
> - `client.grpc=true` -gRPC-listener inschakelen voor connectInject
> - `syncCatalog.enabled=true` -Kubernetes-en consul-Services synchroniseren
>
> **Knooppunt selecties**
>
> Consul moet momenteel worden ingepland om te worden uitgevoerd op Linux-knoop punten. Als u Windows Server-knoop punten in uw cluster hebt, moet u ervoor zorgen dat de consul van de peulen enkel worden uitgevoerd op Linux-knoop punten. We gebruiken [knooppunt selecties][kubernetes-node-selectors] om ervoor te zorgen dat de juiste knoop punten van peulen worden gepland.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Het `Consul` helm-diagram implementeert een aantal objecten. U kunt de lijst zien in de uitvoer van de `helm install` bovenstaande opdracht. Het volt ooien van de implementatie van de consul-onderdelen kan drie minuten duren, afhankelijk van uw cluster omgeving.

Op dit moment hebt u consul geïmplementeerd op uw AKS-cluster. Om ervoor te zorgen dat we een geslaagde implementatie van consul hebben, gaan we verder met de volgende sectie om de consul-installatie te valideren.

## <a name="validate-the-consul-installation"></a>De consul-installatie valideren

Controleer of de resources zijn gemaakt. Gebruik de opdrachten [kubectl Get SVC][kubectl-get] en [kubectl Get POD][kubectl-get] om de naam ruimte op te vragen `consul` , waarbij de consul-onderdelen zijn geïnstalleerd met behulp van de `helm install` opdracht:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

In de volgende voorbeeld uitvoer ziet u de services en het Peul (gepland op Linux-knoop punten) die nu moeten worden uitgevoerd:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Alle peulen moeten de status van hebben `Running` . Als uw peul niet over deze status beschikt, moet u een paar minuten wachten totdat dit het geval is. Als een van de peulen een probleem rapporteert, gebruikt u de [kubectl pod][kubectl-describe] opdracht om de uitvoer en status te controleren.

## <a name="accessing-the-consul-ui"></a>Toegang tot de consul-gebruikers interface

De consul-gebruikers interface is in onze installatie hierboven geïnstalleerd en biedt configuratie op basis van de gebruikers interface voor consul. De gebruikers interface voor consul wordt niet openbaar weer gegeven via een extern IP-adres. Als u toegang wilt krijgen tot de consul-gebruikers interface, gebruikt u de opdracht [kubectl-doorstuur poort][kubectl-port-forward] . Met deze opdracht maakt u een beveiligde verbinding tussen uw client computer en de relevante pod in uw AKS-cluster.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

U kunt nu een browser openen en deze aanwijzen om `http://localhost:8080/ui` de consul-gebruikers interface te openen. Wanneer u de gebruikers interface opent, ziet u het volgende:

![Consul-gebruikers interface](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Consul verwijderen uit AKS

> [!WARNING]
> Het verwijderen van consul van een actief systeem kan leiden tot problemen met verkeer tussen uw services. Zorg ervoor dat u voor het systeem nog steeds goed werkt zonder consul voordat u doorgaat.

### <a name="remove-consul-components-and-namespace"></a>Consul-onderdelen en naam ruimte verwijderen

Gebruik de volgende opdrachten om consul te verwijderen uit uw AKS-cluster. `helm delete`Met de opdrachten wordt de `consul` grafiek verwijderd en `kubectl delete namespace` wordt de naam ruimte verwijderd met de opdracht `consul` .

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over de installatie-en configuratie opties voor consul, raadpleegt u de volgende officiële consul-artikelen:

- [Installatie handleiding voor consul-helm][consul-install-k8]
- [Installatie opties voor consul-helm][consul-install-helm-options]

U kunt ook aanvullende scenario's volgen met behulp van:

- [Voorbeeld toepassing consul][consul-app-example]
- [Consul Kubernetes-referentie architectuur][consul-reference]
- [Consul mesh-gateways][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
