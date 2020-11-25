---
title: Computer Vision-container gebruiken met Kubernetes en helm
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van de Computer Vision-container met behulp van Kubernetes en helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: dce8893cac156ce2941652e32409357cb8ec3b1a
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96015312"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Computer Vision-container gebruiken met Kubernetes en helm

Een optie voor het on-premises beheren van uw Computer Vision-containers is het gebruik van Kubernetes en helm. Door Kubernetes en helm te gebruiken om een Computer Vision container installatie kopie te definiëren, maken we een Kubernetes-pakket. Dit pakket wordt on-premises geïmplementeerd op een Kubernetes-cluster. Ten slotte verkennen we hoe u de geïmplementeerde Services kunt testen. Zie [Computer Vision containers installeren en uitvoeren](computer-vision-how-to-install-containers.md)voor meer informatie over het uitvoeren van docker-containers zonder Kubernetes-indeling.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten voordat u Computer Vision containers on-premises gebruikt:

| Vereist | Doel |
|----------|---------|
| Azure-account | Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][free-azure-account] aan voordat u begint. |
| Kubernetes CLI | De [KUBERNETES cli][kubernetes-cli] is vereist voor het beheren van de gedeelde referenties in het container register. Kubernetes is ook vereist voordat helm, de Kubernetes Package Manager. |
| Helm CLI | Installeer de [helm cli][helm-install], die wordt gebruikt om een helm-grafiek (container Package Definition) te installeren. |
| Computer Vision resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure **Computer Vision** -resource en de bijbehorende API-sleutel de EINDPUNT-URI. Beide waarden zijn beschikbaar op de pagina overzicht en sleutels voor de resource en zijn vereist om de container te starten.<br><br>**{API_KEY}**: een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}**: het eind punt op de pagina **overzicht**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Container vereisten en aanbevelingen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Verbinding maken met het Kubernetes-cluster

Er wordt naar verwachting een beschik bare Kubernetes-cluster op de hostcomputer. Raadpleeg deze zelf studie over het [implementeren van een Kubernetes-cluster](../../aks/tutorial-kubernetes-deploy-cluster.md) voor een conceptuele uitleg over het implementeren van een Kubernetes-cluster op een hostcomputer. Meer informatie over implementaties vindt u in de [Kubernetes-documentatie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="configure-helm-chart-values-for-deployment"></a>Helm-grafiek waarden voor implementatie configureren

Maak eerst een map met de naam *Read*. Plak vervolgens de volgende YAML-inhoud in een nieuw bestand met de naam `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Als u de standaard waarden van de helm-grafiek wilt configureren, kopieert en plakt u de volgende YAML in een bestand met de naam `values.yaml` . Vervang de `# {ENDPOINT_URI}` opmerkingen en door `# {API_KEY}` uw eigen waarden. Configureer resultExpirationPeriod, redis en RabbitMQ zo nodig.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Als de `billing` `apikey` waarden en niet zijn opgenomen, verlopen de Services na 15 minuten. De verificatie mislukt ook omdat de services niet beschikbaar zijn.
> 
> - Als u meerdere Lees containers achter een load balancer implementeert, bijvoorbeeld onder docker opstellen of Kubernetes, moet u een externe cache hebben. Omdat de verwerkings container en de container voor GET-aanvragen mogelijk niet hetzelfde zijn, worden de resultaten door een externe cache opgeslagen en gedeeld in containers. Zie [Computer Vision docker-containers configureren](./computer-vision-resource-container-config.md)voor meer informatie over de cache-instellingen.
>

Maak een map *sjablonen* onder de map *lezen* . Kopieer en plak de volgende YAML in een bestand met de naam `deployment.yaml` . Het `deployment.yaml` bestand moet worden gebruikt als een helm-sjabloon.

> Sjablonen genereren manifest bestanden. Dit zijn YAML bron beschrijvingen die Kubernetes kunnen begrijpen. [-Hand leiding voor helm-grafiek sjablonen][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Kopieer en plak de volgende Help-functies in de map zelfde *sjablonen* in `helpers.tpl` . `helpers.tpl` Hiermee definieert u nuttige functies voor het genereren van helm-sjablonen.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
De sjabloon specificeert een load balancer service en de implementatie van de container/installatie kopie voor lezen.

### <a name="the-kubernetes-package-helm-chart"></a>Het Kubernetes-pakket (helm-grafiek)

Het *helm-diagram* bevat de configuratie van de docker-installatie kopie (n) die moet worden opgehaald uit het `mcr.microsoft.com` container register.

> Een [helm-grafiek][helm-charts] is een verzameling bestanden waarin een gerelateerde set Kubernetes-resources wordt beschreven. Eén grafiek kan worden gebruikt om een eenvoudig te implementeren, zoals een memcached Pod, of iets complex, zoals een volledige web-app-stack met HTTP-servers, data bases, caches, enzovoort.

De meegeleverde *helm-grafieken* halen de docker-installatie kopieën van de computer vision-service en de bijbehorende service uit het `mcr.microsoft.com` container register.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>De helm-grafiek op het Kubernetes-cluster installeren

Als u de *helm-grafiek* wilt installeren, moet u de [`helm install`][helm-install-cmd] opdracht uitvoeren. Zorg ervoor dat u de installatie opdracht uitvoert vanuit de map die zich boven de `read` map bevindt.

```console
helm install read ./read
```

Hier volgt een voorbeeld uitvoer die u kunt verwachten van een geslaagde installatie-uitvoering:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Het volt ooien van de implementatie van Kubernetes kan enkele minuten duren. Voer de volgende opdracht uit om te controleren of zowel de peulen als de services goed zijn geïmplementeerd en beschikbaar zijn:

```console
kubectl get all
```

U ziet dat er iets lijkt op de volgende uitvoer:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Meerdere v3-containers implementeren op het Kubernetes-cluster

Vanaf v3 van de container kunt u de containers parallel op zowel een taak-als pagina niveau gebruiken.

Elke V3-container heeft standaard een verzender en een erkennings medewerker. De verzender is verantwoordelijk voor het splitsen van een taak met meerdere pagina's in meerdere subtaken van één pagina. De herkennings medewerker is geoptimaliseerd voor het herkennen van één pagina document. Implementeer meerdere v3-containers achter een load balancer en laat de containers een universele opslag en wachtrij delen om de parallelle beveiliging op pagina niveau te garanderen. 

> [!NOTE] 
> Momenteel worden alleen Azure Storage en Azure-wachtrij ondersteund. 

De container die de aanvraag ontvangt, kan de taak in subtaken van één pagina splitsen en toevoegen aan de universele wachtrij. Elke herkennings medewerker van een minder drukke container kan gebruikmaken van enkele pagina subtaken uit de wachtrij, het uitvoeren van de herkenning en het uploaden van het resultaat naar de opslag. De door Voer kan worden verbeterd `n` , afhankelijk van het aantal geïmplementeerde containers.

Kopieer en plak de volgende YAML in een bestand met de naam `deployment.yaml` . Vervang de `# {ENDPOINT_URI}` opmerkingen en door `# {API_KEY}` uw eigen waarden. Vervang de `# {AZURE_STORAGE_CONNECTION_STRING}` Opmerking door uw Azure Storage verbindings reeks. Configureer `replicas` de gewenste waarde `3` in het volgende voor beeld.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

Voer de volgende opdracht uit. 

```console
kubectl apply -f deployment.yaml
```

Hieronder ziet u een voor beeld van een uitvoer van een geslaagde implementatie:

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

Het volt ooien van de implementatie van Kubernetes kan enkele minuten duren. Voer de volgende opdracht uit om te controleren of zowel de peulen als de services goed zijn geïmplementeerd en beschikbaar zijn:

```console
kubectl get all
```

De console-uitvoer ziet er ongeveer als volgt uit:

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het installeren van toepassingen met helm in azure Kubernetes service (AKS) [vindt u hier][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services-containers][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md