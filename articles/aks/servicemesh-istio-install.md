---
title: Istio installeren in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en gebruiken van Istio voor het maken van een service-net in een Azure Kubernetes service-cluster (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94683825"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Istio installeren en gebruiken in azure Kubernetes service (AKS)

[Istio][istio-github] is een open-source service-net dat een belang rijke set functionaliteit biedt voor de micro Services in een Kubernetes-cluster. Deze functies omvatten verkeer beheer, service-identiteit en-beveiliging, afdwinging van beleid en de bekrachtiging. Zie voor meer informatie over Istio de officiële [What is Istio?][istio-docs-concepts] -documentatie.

In dit artikel wordt beschreven hoe u Istio installeert. Het `istioctl` binaire bestand van de Istio-client wordt geïnstalleerd op de client computer en de Istio-onderdelen worden geïnstalleerd in een Kubernetes-cluster op AKS.

> [!NOTE]
> De volgende instructies verwijzen naar Istio-versie `1.7.3` .
>
> De Istio- `1.7.x` releases zijn getest door het Istio-team tegen Kubernetes-versie `1.16+` . U kunt aanvullende Istio-versies vinden op [github-Istio-releases][istio-github-releases], informatie over elk van de releases van [Istio News][istio-release-notes] en ondersteunde Kubernetes-versies op [Istio algemene veelgestelde vragen][istio-faq].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Down load en installeer het binaire bestand voor de Istio istioctl-client
> * Istio installeren op AKS
> * De Istio-installatie valideren
> * Toegang tot de invoeg toepassingen
> * Istio verwijderen uit AKS

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.16` en hoger, met KUBERNETES RBAC is ingeschakeld) en een `kubectl` verbinding met het cluster tot stand hebt gebracht. Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart]start.

Zorg ervoor dat u de Istio-documentatie over [prestaties en schaal baarheid](https://istio.io/docs/concepts/performance-and-scalability/) hebt gelezen om inzicht te krijgen in de aanvullende bron vereisten voor het uitvoeren van Istio in uw AKS-cluster. De kern-en geheugen vereisten variëren op basis van uw specifieke werk belasting. Kies het juiste aantal knoop punten en de VM-grootte voor uw installatie.

In dit artikel worden de Istio-installatie richtlijnen gescheiden in verschillende afzonderlijke stappen. Het eind resultaat is hetzelfde als de officiële Istio-installatie [richtlijnen][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>De Istio-operator op AKS installeren

Istio biedt een [operator][istio-install-operator] voor het beheren van de installatie en updates van de Istio-onderdelen in uw AKS-cluster. De Istio-operator wordt geïnstalleerd met behulp van de `istioctl` binaire client.

```bash
istioctl operator init
```

Als u wilt controleren of de operator Istio is geïnstalleerd, ziet u iets zoals in de volgende uitvoer.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

De Istio-operator wordt in de `istio-operator` naam ruimte geïnstalleerd. Query uitvoeren op de naam ruimte.

```bash
kubectl get all -n istio-operator
```

U ziet nu de volgende onderdelen zijn geïmplementeerd.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

U kunt meer te weten komen over het operator patroon en hoe u hiermee complexe taken kunt automatiseren via [kubernetes.io][kubernetes-operator].


### <a name="install-istio-components"></a>Istio-onderdelen installeren

Nu de Istio-operator in het AKS-cluster is geïnstalleerd, is het tijd om de Istio-onderdelen te installeren. 

Het `default` [Istio-configuratie profiel][istio-configuration-profiles] wordt gebruikt om de [Istio-operator spec][istio-control-plane]te maken.

U kunt de volgende `istioctl` opdracht uitvoeren om de configuratie voor het Istio-configuratie profiel weer te geven `default` .

```bash
istioctl profile dump default
```

> [!NOTE]
> Istio moet momenteel worden ingepland om te worden uitgevoerd op Linux-knoop punten. Als u Windows Server-knoop punten in uw cluster hebt, moet u ervoor zorgen dat de Istio van de peulen enkel worden uitgevoerd op Linux-knoop punten. We gebruiken [knooppunt selecties][kubernetes-node-selectors] om ervoor te zorgen dat de juiste knoop punten van peulen worden gepland.

> [!CAUTION]
> De [ISTIO cni][istio-feature-cni] Istio-functies bevinden zich op dit moment in [alpha][istio-feature-stages], dus u moet er rekening mee doen voordat u deze functie inschakelt. 

Maak een bestand `istio.aks.yaml` met de naam met de volgende inhoud. Dit bestand bevat de [Istio-operator specificatie][istio-control-plane] voor het configureren van Istio.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Maak de `istio-system` naam ruimte en implementeer de Istio-operator specificatie voor die naam ruimte. De operator Istio wordt gebruikt voor het installeren en configureren van Istio in uw AKS-cluster.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

Op dit moment hebt u Istio geïmplementeerd op uw AKS-cluster. Om ervoor te zorgen dat we een geslaagde implementatie van Istio hebben, gaan we verder met de volgende sectie om [de Istio-installatie te valideren](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>De Istio-installatie valideren

Voer een query uit op de `istio-system` naam ruimte, waarbij de Istio en invoeg toepassings onderdelen zijn geïnstalleerd door de Istio-operator:

```bash
kubectl get all -n istio-system
```

De volgende onderdelen moeten worden weer geven:

- `istio*` -de Istio-onderdelen
- `jaeger-*`, `tracing` en `zipkin` -invoeg toepassing traceren
- `prometheus` -invoeg toepassing voor metrische gegevens
- `grafana` -invoeg toepassing dash board voor analyse en bewaking
- `kiali` -invoeg toepassing dash board service-net

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

U kunt ook meer inzicht krijgen in de installatie door de logboeken voor de Istio-operator te bekijken.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Als `istio-ingressgateway` er een extern IP-adres van wordt weer gegeven `<pending>` , wacht u enkele minuten totdat er een IP-adressen zijn toegewezen door Azure-netwerken.

Alle peulen moeten de status van hebben `Running` . Als uw peul niet over deze status beschikt, moet u een paar minuten wachten totdat dit het geval is. Als een van de peulen een probleem rapporteert, gebruikt u de [kubectl pod][kubectl-describe] opdracht om de uitvoer en status te controleren.

## <a name="accessing-the-add-ons"></a>De invoeg toepassingen openen

Er zijn een aantal invoeg toepassingen geïnstalleerd door de Istio-operator die aanvullende functionaliteit biedt. De webtoepassingen voor de invoeg toepassingen worden **niet** openbaar weer gegeven via een extern IP-adres. 

Gebruik de opdracht om toegang te krijgen tot de gebruikers interfaces van de invoeg toepassing `istioctl dashboard` . Deze opdracht maakt gebruik van [kubectl-poort-forward][kubectl-port-forward] en een wille keurige poort om een beveiligde verbinding te maken tussen uw client computer en de relevante pod in uw AKS-cluster. De invoeg toepassing wordt vervolgens automatisch geopend in de standaard browser.

### <a name="grafana"></a>Grafana

De analyse-en bewakings dashboards voor Istio worden gegeven door [Grafana][grafana]. Vergeet niet om de referenties te gebruiken die u eerder hebt gemaakt via het Grafana-geheim wanneer u hierom wordt gevraagd. Open het Grafana-dash board veilig als volgt:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Metrische gegevens voor Istio worden gegeven door [Prometheus][prometheus]. Open het Prometheus-dash board veilig als volgt:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Tracering binnen Istio wordt verschaft door [Jaeger][jaeger]. Open het Jaeger-dash board veilig als volgt:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Een dash board met Service-net-behulp van [Kiali][kiali]. Vergeet niet om de referenties te gebruiken die u eerder hebt gemaakt via het Kiali-geheim wanneer u hierom wordt gevraagd. Open het Kiali-dash board veilig als volgt:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Er is een eenvoudige interface beschikbaar voor de [Envoy][envoy] -proxy's. Het bevat configuratie-informatie en metrische gegevens voor een Envoy-proxy die wordt uitgevoerd in een opgegeven pod. Open de Envoy-interface veilig als volgt:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Istio verwijderen uit AKS

> [!WARNING]
> Het verwijderen van Istio van een actief systeem kan leiden tot problemen met verkeer tussen uw services. Zorg ervoor dat u voor het systeem nog steeds goed werkt zonder Istio voordat u doorgaat.

### <a name="remove-istio"></a>Istio verwijderen

Als u Istio uit uw AKS-cluster wilt verwijderen, verwijdert u de `IstioOperator` resource met de naam `istio-control-plane` die u eerder hebt toegevoegd. De Istio-operator erkent dat de spec van de Istio-operator is verwijderd en verwijder vervolgens alle bijbehorende Istio-onderdelen.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

U kunt het volgende uitvoeren om te controleren wanneer alle Istio-onderdelen zijn verwijderd.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Istio-operator verwijderen

Zodra Istio is verwijderd, kunt u ook de operator Istio verwijderen.

```bash
istioctl operator remove
```

En verwijder ten slotte de `istio-` naam ruimten.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over de installatie-en configuratie opties voor Istio, raadpleegt u de volgende officiële Istio-richt lijnen:

- [Istio-installatie handleidingen][istio-installation-guides]

U kunt ook aanvullende scenario's volgen met behulp van:

- [Voor beeld van Istio Bookinfo-toepassing][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
