---
title: Lokale metrische gegevens en logboeken configureren voor Azure API Management zelf-hostende gateway | Microsoft Docs
description: Meer informatie over het configureren van lokale metrische gegevens en logboeken voor Azure API Management zelf-hostende gateway in een Kubernetes-Custer
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594168"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Lokale metrische gegevens en logboeken voor Azure API Management zelf-hostende gateway configureren

In dit artikel vindt u informatie over het configureren van lokale metrische gegevens en logboeken voor de [zelf-hostende gateway](./self-hosted-gateway-overview.md) die is geïmplementeerd op een Kubernetes-cluster. Raadpleeg [dit artikel](how-to-configure-cloud-metrics-logs.md)voor informatie over het configureren van metrische gegevens en logboeken voor de Cloud. 

## <a name="metrics"></a>Metrische gegevens
De zelf-hostende gateway ondersteunt [Statistieken](https://github.com/statsd/statsd), die een afsluitend protocol vormen voor verzameling van metrische gegevens en aggregatie. In deze sectie worden de stappen beschreven voor het implementeren van statistieken voor Kubernetes, het configureren van de gateway voor het verzenden van metrische gegevens via statistieken en het gebruik van [Prometheus](https://prometheus.io/) om de metrische gegevens te bewaken. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Statistieken en Prometheus implementeren in het cluster

Hieronder vindt u een voor beeld van een YAML-configuratie voor het implementeren van statistieken en Prometheus naar het Kubernetes-cluster waarop een zelf-hostende gateway wordt geïmplementeerd. Er wordt ook een [service](https://kubernetes.io/docs/concepts/services-networking/service/) voor elke gemaakt. De zelf-hostende gateway publiceert metrische gegevens naar de service met statistieken. We hebben toegang tot het Prometheus-dash board via de service.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Sla de configuraties op in een bestand met `metrics.yaml` de naam en gebruik de onderstaande opdracht om alles op het cluster te implementeren:

```console
kubectl apply -f metrics.yaml
```

Zodra de implementatie is voltooid, voert u de onderstaande opdracht uit om te controleren of de peulen worden uitgevoerd. Houd er rekening mee dat de naam van uw Pod afwijkt. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Voer de onderstaande opdracht uit om te controleren of de services worden uitgevoerd. Noteer de `CLUSTER-IP` en `PORT` de service met statistieken. Dit zou later nodig zijn. U kunt het Prometheus-dash board bezoeken met de `EXTERNAL-IP` en `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>De zelf-hostende gateway configureren voor het verzenden van metrische gegevens

Nu zowel de statistieken als de Prometheus zijn geïmplementeerd, kunnen we de configuraties van de zelf-hostende gateway bijwerken om het verzenden van metrische gegevens via een statief te starten. De functie kan worden ingeschakeld of uitgeschakeld met behulp `telemetry.metrics.local` van de sleutel in de ConfigMap van de zelf-hostende gateway-implementatie met extra opties. Hieronder vindt u een uitsplitsing van de beschik bare opties:

| Veld  | Standaard | Beschrijving |
| ------------- | ------------- | ------------- |
| telemetrie. Metrics. local  | `none` | Hiermee schakelt u de logboek registratie in via statistieken. Waarde kan zijn `none` , `statsd` . |
| telemetrie. Metrics. local. stats. restated. Endpoint  | n.v.t. | Hiermee geeft u het getunnelde eind punt op. |
| telemetrie. Metrics. local. statd. samples. sampling  | n.v.t. | Geeft de sampling frequentie van metrische gegevens aan. De waarde kan tussen 0 en 1 liggen. bijvoorbeeld, `0.5`|
| telemetrie. Metrics. local. stats. tag-Format  | n.v.t. | Indeling van de [code ring](https://github.com/prometheus/statsd_exporter#tagging-extensions)van het export model met statistieken. Waarde kan zijn `none` , `librato` , `dogStatsD` , `influxDB` . |

Hier volgt een voor beeld van een configuratie:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Werk het YAML-bestand van de zelf-hostende gateway-implementatie bij met de bovenstaande configuraties en pas de wijzigingen toe met behulp van de onderstaande opdracht: 

```console
kubectl apply -f <file-name>.yaml
 ```

Start de gateway implementatie opnieuw met behulp van de onderstaande opdracht om de nieuwste configuratie wijzigingen op te halen:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>De metrische gegevens weer geven

Nu alles is geïmplementeerd en geconfigureerd, moet de zelf-hostende gateway metrische gegevens rapporteren via statistieken. Met Prometheus worden de metrische gegevens opgehaald uit de statistieken. Ga naar het Prometheus-dash board met behulp `EXTERNAL-IP` van de en `PORT` van de Prometheus-service. 

Maak een aantal API-aanroepen via de zelf-hostende gateway als alles correct is geconfigureerd, kunt u de onderstaande metrische gegevens weer geven:

| Metrisch  | Beschrijving |
| ------------- | ------------- |
| Aanvragen  | Aantal API-aanvragen in de periode |
| DurationInMS | Aantal milliseconden vanaf het moment dat de gateway de aanvraag ontving tot het moment dat het antwoord volledig werd verzonden |
| BackendDurationInMS | Aantal milliseconden dat in totaal is besteed aan IO van de back-end (verbinding maken, bytes verzenden en ontvangen)  |
| ClientDurationInMS | Aantal milliseconden dat in totaal is besteed aan IO van de client (verbinding maken, bytes verzenden en ontvangen)  |

## <a name="logs"></a>Logboeken

De zelf-hostende Gateway levert standaard logboeken naar `stdout` en `stderr` . U kunt de Logboeken eenvoudig weer geven met de volgende opdracht:

```console
kubectl logs <pod-name>
```

Als uw zelf-hostende gateway is geïmplementeerd in de Azure Kubernetes-service, kunt u [Azure monitor voor containers](../azure-monitor/containers/container-insights-overview.md) inschakelen `stdout` en `stderr` van uw workloads verzamelen en de logboeken in log Analytics weer geven. 

De zelf-hostende gateway biedt ook ondersteuning voor een aantal protocollen `localsyslog` , waaronder, `rfc5424` en `journal` . De onderstaande tabel bevat een overzicht van alle ondersteunde opties. 

| Veld  | Standaard | Beschrijving |
| ------------- | ------------- | ------------- |
| telemetrie. logs. std  | `text` | Hiermee schakelt u de logboek registratie in voor standaard stromen. Waarde kan zijn `none` , `text` , `json` |
| telemetrie. logs. lokaal  | `none` | Lokale logboek registratie inschakelen. Waarde kan `none` ,, `auto` , `localsyslog` `rfc5424` , `journal`  |
| telemetrie. logs. local. localsyslog. eind punt  | n.v.t. | Hiermee wordt het localsyslog-eind punt opgegeven.  |
| telemetrie. logs. local. localsyslog. Facility  | n.v.t. | Hiermee wordt localsyslog [faciliteit code](https://en.wikipedia.org/wiki/Syslog#Facility)opgegeven. bijvoorbeeld, `7` 
| telemetrie. logs. local. rfc5424. eind punt  | n.v.t. | Hiermee wordt het rfc5424-eind punt opgegeven.  |
| telemetrie. logs. local. rfc5424. Facility  | n.v.t. | Geeft faciliteit code per [rfc5424](https://tools.ietf.org/html/rfc5424)aan. bijvoorbeeld, `7`  |
| telemetrie. logs. lokaal. dagboek. eind punt  | n.v.t. | Hiermee wordt het eind punt van het logboek opgegeven.  |

Hier volgt een voor beeld van de configuratie van lokale logboek registratie:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de zelf-hostende Gateway vindt u in [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)
* Meer informatie over [het configureren en persistent maken van Logboeken in de Cloud](how-to-configure-local-metrics-logs.md)
