---
title: Prometheus-integratie met container Insights configureren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de container Insights-agent zo kunt configureren dat metrische gegevens worden afgeleid van Prometheus met uw Kubernetes-cluster.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 8affeb472b9452e4d234e99e5ea6bb4509770fac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731728"
---
# <a name="configure-scraping-of-prometheus-metrics-with-container-insights"></a>Het terugvallen van Prometheus-metrische gegevens met container Insights configureren

[Prometheus](https://prometheus.io/) is een populaire open source-bewakings oplossing en maakt deel uit van de [eigen Cloud Compute Foundation](https://www.cncf.io/). Container Insights biedt een naadloze onboarding-ervaring voor het verzamelen van Prometheus-metrische gegevens. Als u Prometheus wilt gebruiken, moet u normaal gesp roken een Prometheus-server met een archief instellen en beheren. Door te integreren met Azure Monitor is een Prometheus-server niet vereist. U hoeft het Prometheus-eind punt voor metrische gegevens alleen zichtbaar te maken via uw Exporters of peulen (toepassing), en de container-agent voor container Insights kan de metrische gegevens voor u oplopen. 

![Architectuur voor container bewaking voor Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>De minimale agent versie die wordt ondersteund voor het terugvallen van Prometheus-metrische gegevens is ciprod07092019 of hoger, en de agent versie die wordt ondersteund voor het schrijven van configuratie-en agent fouten in de `KubeMonAgentEvents` tabel is ciprod10112019. Voor Azure Red Hat open Shift en Red Hat open Shift v4, ciprod04162020 of hoger. 
>
>Zie [release opmerkingen](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)voor de agent voor meer informatie over de agent versies en wat is opgenomen in elke release. 
>Als u de versie van de agent wilt controleren, selecteert u een knoop punt op het tabblad **knoop** punt en noteert u in het deel venster Eigenschappen de waarde van de **Agent-installatie kopie label** eigenschap.

Het opwaarderen van metrische gegevens over Prometheus wordt ondersteund met Kubernetes-clusters die worden gehost op:

- Azure Kubernetes Service (AKS)
- Azure Stack of on-premises
- Azure Red Hat open Shift versie 3. x
- Azure Red Hat open Shift en Red Hat open Shift versie 4. x

### <a name="prometheus-scraping-settings"></a>Instellingen voor Prometheus-uitval

Actieve uitval van metrische gegevens van Prometheus wordt uitgevoerd vanuit een van de volgende twee perspectieven:

* Cluster-Wide-HTTP-URL en Detecteer doelen van de vermelde eind punten van een service. Bijvoorbeeld K8S services zoals uitvoeren-DNS en uitvoeren-State-metrics en pod annotaties die specifiek zijn voor een toepassing. De metrische gegevens die in deze context worden verzameld, worden gedefinieerd in de sectie ConfigMap *[Prometheus data_collection_settings. cluster]*.
* Node-Wide-HTTP URL en detectie doelen van de vermelde eind punten van een service. De metrische gegevens die in deze context worden verzameld, worden gedefinieerd in de sectie ConfigMap *[Prometheus_data_collection_settings. node]*.

| Eindpunt | Bereik | Voorbeeld |
|----------|-------|---------|
| Pod aantekening | Cluster-breed | aantekeningen <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes-service | Cluster-breed | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/eind punt | Per knoop punt en/of gehele cluster | `http://myurl:9101/metrics` |

Wanneer een URL is opgegeven, wordt het eind punt alleen door container Insights in het uitvallen. Wanneer de Kubernetes-service is opgegeven, wordt de service naam opgelost met de cluster-DNS-server om het IP-adres op te halen. vervolgens wordt de opgeloste service geuitval.

|Bereik | Sleutel | Gegevenstype | Waarde | Beschrijving |
|------|-----|-----------|-------|-------------|
| Cluster-breed | | | | Geef een van de volgende drie methoden op om eind punten voor metrische gegevens af te vallen. |
| | `urls` | Tekenreeks | Door komma's gescheiden matrix | HTTP-eind punt (ofwel een IP-adres of een geldig URL-pad opgegeven). Bijvoorbeeld: `urls=[$NODE_IP/metrics]`. ($NODE _IP is een specifieke container Insights-para meter en kan worden gebruikt in plaats van het IP-adres van het knoop punt. Mag alleen hoofd letters zijn.) |
| | `kubernetes_services` | Tekenreeks | Door komma's gescheiden matrix | Een matrix met Kubernetes-Services voor het opwaarderen van metrische gegevens uit uitvoeren. Bijvoorbeeld `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Booleaans | waar of onwaar | Als deze optie is ingesteld op `true` in de instellingen voor het hele cluster, wordt in de container Insights-agent Kubernetes peul voor het hele cluster geschroot voor de volgende Prometheus-aantekeningen:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Booleaans | waar of onwaar | Hiermee schakelt u de pod in. `monitor_kubernetes_pods` moet worden ingesteld op `true` . |
| | `prometheus.io/scheme` | Tekenreeks | http of https | De standaard instelling is het uitvallen van HTTP. Stel, indien nodig, in op `https` . | 
| | `prometheus.io/path` | Tekenreeks | Door komma's gescheiden matrix | Het HTTP-bronpad waarvan de metrische gegevens moeten worden opgehaald. Als het pad naar metrische gegevens niet is `/metrics` , definieert u dit met deze aantekening. |
| | `prometheus.io/port` | Tekenreeks | 9102 | Geef een poort op waarvan u wilt uitvallen. Als poort niet is ingesteld, wordt de standaard waarde van 9102. |
| | `monitor_kubernetes_pods_namespaces` | Tekenreeks | Door komma's gescheiden matrix | Een lijst met toegestane naam ruimten die de metrische gegevens van Kubernetes peul opwaarderen.<br> Bijvoorbeeld: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Knooppunt-breed | `urls` | Tekenreeks | Door komma's gescheiden matrix | HTTP-eind punt (ofwel een IP-adres of een geldig URL-pad opgegeven). Bijvoorbeeld: `urls=[$NODE_IP/metrics]`. ($NODE _IP is een specifieke container Insights-para meter en kan worden gebruikt in plaats van het IP-adres van het knoop punt. Mag alleen hoofd letters zijn.) |
| Het hele knoop punt of het hele cluster | `interval` | Tekenreeks | 60s | De standaard waarde voor de verzamelings interval is 1 minuut (60 seconden). U kunt de verzameling voor *[prometheus_data_collection_settings. node]* en/of *[prometheus_data_collection_settings. cluster]* wijzigen in tijds eenheden zoals s, m, h. |
| Het hele knoop punt of het hele cluster | `fieldpass`<br> `fielddrop`| Tekenreeks | Door komma's gescheiden matrix | U kunt bepaalde metrische gegevens opgeven die u wilt verzamelen of niet van het eind punt door de vermelding toestaan ( `fieldpass` ) en weigeren () in te stellen `fielddrop` . U moet eerst de acceptatie lijst instellen. |

ConfigMaps is een globale lijst en er kan slechts één ConfigMap op de agent worden toegepast. U kunt de verzamelingen niet overConfigMapsen.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps configureren en implementeren

Voer de volgende stappen uit om uw ConfigMap-configuratie bestand voor de volgende clusters te configureren:

* Azure Kubernetes Service (AKS)
* Azure Stack of on-premises
* Azure Red Hat open Shift versie 4. x en Red Hat open Shift versie 4. x

1. [Down load](https://aka.ms/container-azm-ms-agentconfig) het sjabloon bestand ConfigMap yaml en sla het op als container-AZM-MS-agentconfig. yaml.

   >[!NOTE]
   >Deze stap is niet vereist bij het werken met Azure Red Hat open Shift omdat de ConfigMap-sjabloon al in het cluster bestaat.

2. Bewerk het ConfigMap yaml-bestand met uw aanpassingen om Prometheus-metrische gegevens af te vallen.

    >[!NOTE]
    >Als u het ConfigMap yaml-bestand voor Azure Red Hat open Shift bewerkt, voert u eerst de opdracht uit `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` om het bestand in een tekst editor te openen.

    >[!NOTE]
    >De volgende aantekening `openshift.io/reconcile-protect: "true"` moet worden toegevoegd onder de meta gegevens van *container-AZM-MS-agentconfig* ConfigMap om afstemming te voor komen. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Configureer het ConfigMap-bestand met behulp van het volgende voor beeld om het Kubernetes Services-cluster niveau te verzamelen.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Configureer het ConfigMap-bestand met behulp van het volgende voor beeld om het uitvallen van Prometheus-metrische gegevens van een specifieke URL in het cluster te configureren.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Als u het terugvallen van Prometheus-metrische gegevens uit een daemon-set van een agent voor elk afzonderlijk knoop punt in het cluster wilt configureren, configureert u het volgende in de ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP is een specifieke container Insights-para meter en kan worden gebruikt in plaats van het IP-adres van het knoop punt. Deze moet allemaal hoofd letters zijn. 

    - Voer de volgende stappen uit om het uitvallen van metrische gegevens over Prometheus te configureren door een pod-aantekening op te geven:

       1. Geef in het ConfigMap het volgende op:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Geef de volgende configuratie op voor pod-aantekeningen:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Als u de bewaking wilt beperken tot specifieke naam ruimten voor peulen die aantekeningen bevatten, bijvoorbeeld alleen voor de werk belastingen die specifiek zijn toegewezen, stelt u de in op `monitor_kubernetes_pod` `true` in ConfigMap en voegt u het naam ruimte filter toe `monitor_kubernetes_pods_namespaces` om de naam ruimten op te geven waaruit u wilt terugvallen. Bijvoorbeeld: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Voer de volgende kubectl-opdracht uit: `kubectl apply -f <configmap_yaml_file.yaml>` .
    
    Bijvoorbeeld: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

Het kan een paar minuten duren voordat de configuratie wijziging is doorgevoerd en alle omsagent in het cluster opnieuw worden opgestart. Het opnieuw opstarten is een rolling start voor alle omsagent-peulen, niet allemaal tegelijk opnieuw opstarten. Wanneer het opnieuw opstarten is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat: `configmap "container-azm-ms-agentconfig" created` .

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>ConfigMaps configureren en implementeren: Azure Red Hat open Shift v3

In deze sectie vindt u de vereisten en stappen voor het configureren van uw ConfigMap-configuratie bestand voor het Azure Red Hat open Shift v3. x-cluster.

>[!NOTE]
>Voor Azure Red Hat open Shift v3. x wordt een sjabloon ConfigMap-bestand gemaakt in de naam ruimte *openshift-Azure-logging* . Het is niet geconfigureerd voor het actief opwaarderen van metrische gegevens of gegevensverzameling van de agent.

### <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u of u lid bent van de rol van Cluster beheerder voor klanten van uw Azure Red Hat open Shift-cluster om de instellingen voor de container agent en de Prometheus-schroot te configureren. Voer de volgende opdracht uit om te controleren of u lid bent van de groep *Osa-klant-Administrators* :

``` bash
  oc get groups
```

De uitvoer ziet er ongeveer als volgt uit:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Als u lid bent van de groep *Osa-klant-Administrators* , kunt u de ConfigMap weer geven `container-azm-ms-agentconfig` met behulp van de volgende opdracht:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

De uitvoer ziet er ongeveer als volgt uit:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>Bewaking inschakelen

Voer de volgende stappen uit om uw ConfigMap-configuratie bestand voor uw Azure Red Hat open Shift v3. x-cluster te configureren.

1. Bewerk het ConfigMap yaml-bestand met uw aanpassingen om Prometheus-metrische gegevens af te vallen. De ConfigMap-sjabloon bestaat al in het Red Hat open Shift v3-cluster. Voer de opdracht uit `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` om het bestand in een tekst editor te openen.

    >[!NOTE]
    >De volgende aantekening `openshift.io/reconcile-protect: "true"` moet worden toegevoegd onder de meta gegevens van *container-AZM-MS-agentconfig* ConfigMap om afstemming te voor komen. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Configureer het ConfigMap-bestand met behulp van het volgende voor beeld om het Kubernetes Services-cluster niveau te verzamelen.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Configureer het ConfigMap-bestand met behulp van het volgende voor beeld om het uitvallen van Prometheus-metrische gegevens van een specifieke URL in het cluster te configureren.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Als u het terugvallen van Prometheus-metrische gegevens uit een daemon-set van een agent voor elk afzonderlijk knoop punt in het cluster wilt configureren, configureert u het volgende in de ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP is een specifieke container Insights-para meter en kan worden gebruikt in plaats van het IP-adres van het knoop punt. Deze moet allemaal hoofd letters zijn. 

    - Voer de volgende stappen uit om het uitvallen van metrische gegevens over Prometheus te configureren door een pod-aantekening op te geven:

       1. Geef in het ConfigMap het volgende op:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Geef de volgende configuratie op voor pod-aantekeningen:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Als u de bewaking wilt beperken tot specifieke naam ruimten voor peulen die aantekeningen bevatten, bijvoorbeeld alleen voor de werk belastingen die specifiek zijn toegewezen, stelt u de in op `monitor_kubernetes_pod` `true` in ConfigMap en voegt u het naam ruimte filter toe `monitor_kubernetes_pods_namespaces` om de naam ruimten op te geven waaruit u wilt terugvallen. Bijvoorbeeld: `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. Sla uw wijzigingen op in de editor.

Het kan een paar minuten duren voordat de configuratie wijziging is doorgevoerd en alle omsagent in het cluster opnieuw worden opgestart. Het opnieuw opstarten is een rolling start voor alle omsagent-peulen, niet allemaal tegelijk opnieuw opstarten. Wanneer het opnieuw opstarten is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat: `configmap "container-azm-ms-agentconfig" created` .

U kunt de bijgewerkte ConfigMap weer geven door de opdracht uit te voeren `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` . 

## <a name="applying-updated-configmap"></a>Bijgewerkte ConfigMap Toep assen

Als u al een ConfigMap op uw cluster hebt geïmplementeerd en u deze wilt bijwerken met een nieuwere configuratie, kunt u het ConfigMap-bestand dat u eerder hebt gebruikt, bewerken en vervolgens Toep assen met dezelfde opdrachten als voorheen.

Voor de volgende Kubernetes omgevingen:

- Azure Kubernetes Service (AKS)
- Azure Stack of on-premises
- Azure Red Hat open Shift en Red Hat open Shift versie 4. x

Voer de opdracht uit `kubectl apply -f <configmap_yaml_file.yaml` . 

Voor een Azure Red Hat open Shift v3. x-cluster voert u de opdracht uit `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` om het bestand te openen in de standaard editor om het te wijzigen en vervolgens op te slaan.

Het kan een paar minuten duren voordat de configuratie wijziging is doorgevoerd en alle omsagent in het cluster opnieuw worden opgestart. Het opnieuw opstarten is een rolling start voor alle omsagent-peulen, niet allemaal tegelijk opnieuw opstarten. Wanneer het opnieuw opstarten is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verify-configuration"></a>Configuratie controleren

Als u wilt controleren of de configuratie is toegepast op een cluster, gebruikt u de volgende opdracht om de logboeken te controleren vanuit een agent Pod: `kubectl logs omsagent-fdf58 -n=kube-system` . 

>[!NOTE]
>Deze opdracht is niet van toepassing op Azure Red Hat open Shift v3. x-cluster.
> 

Als er configuratie fouten zijn van de omsagent Peul, worden de volgende fouten weer gegeven in de uitvoer:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fouten met betrekking tot het Toep assen van configuratie wijzigingen zijn ook beschikbaar voor controle. De volgende opties zijn beschikbaar voor het uitvoeren van extra problemen met configuratie wijzigingen en het opwaarderen van Prometheus-metrische gegevens:

- Vanuit een agent pod logboeken met dezelfde `kubectl logs` opdracht 
    >[!NOTE]
    >Deze opdracht is niet van toepassing op het Azure Red Hat open Shift-cluster.
    > 

- Van live data (preview). Live data (preview)-logboeken bevatten fouten die vergelijkbaar zijn met de volgende:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Vanuit de **KubeMonAgentEvents** -tabel in uw log Analytics-werk ruimte. Gegevens worden elk uur verzonden met een *waarschuwing* ernst voor uitval fouten en *fout* ernst voor configuratie fouten. Als er geen fouten zijn, heeft de vermelding in de tabel gegevens met *informatie* over de ernst, die geen fouten rapporteert. De eigenschap **Tags** bevat meer informatie over de Pod en de container-id waarop de fout is opgetreden en ook de eerste instantie, het laatste exemplaar en het aantal in het afgelopen uur.

- Raadpleeg de omsagent-Logboeken in de **ContainerLog** -tabel om te controleren of de logboek verzameling van open Shift-Azure-logging is ingeschakeld voor Azure Red Hat open Shift v3. x en v4. x.

Fouten zorgen ervoor dat omsagent het bestand niet kan parseren, waardoor het opnieuw wordt gestart en de standaard configuratie wordt gebruikt. Nadat u de fout (en) in ConfigMap op andere clusters dan Azure Red Hat open Shift v3. x hebt gecorrigeerd, slaat u het yaml-bestand op en past u de bijgewerkte ConfigMaps toe door de opdracht uit te voeren: `kubectl apply -f <configmap_yaml_file.yaml` . 

Voor Azure Red Hat open Shift v3. x kunt u de bijgewerkte ConfigMaps bewerken en opslaan door de opdracht uit te voeren: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` .

## <a name="query-prometheus-metrics-data"></a>Prometheus metrische gegevens opvragen

Als u de metrische gegevens van Prometheus wilt weer geven die door Azure Monitor worden geschroot en eventuele configuratie-en uitval fouten die door de agent worden gerapporteerd, raadpleegt u [query Prometheus Metrics data](container-insights-log-search.md#query-prometheus-metrics-data) en [query configuratie of uitval fouten](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Metrische Prometheus-gegevens weer geven in Grafana

Container Insights ondersteunt het weer geven van metrische gegevens die zijn opgeslagen in uw Log Analytics-werk ruimte in Grafana-Dash boards. We hebben een sjabloon die u kunt downloaden uit de Grafana van het [dash board](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) van micro soft om aan de slag te gaan en te verwijzen naar informatie over het zoeken naar extra gegevens van uw bewaakte clusters om te visualiseren in aangepaste Grafana-Dash boards. 

## <a name="review-prometheus-data-usage"></a>Prometheus-gegevens gebruik controleren

De volgende query wordt gegeven voor het identificeren van het opname volume van elke metrische grootte in GB per dag om te begrijpen of deze hoog is.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

De uitvoer ziet er ongeveer als volgt uit:

![Scherm afbeelding toont de logboek query resultaten van het volume gegevens opname](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Als u een schatting wilt maken van de grootte van elke metrische waarde in GB, kunt u de volgende query uitvoeren om te begrijpen of het volume van de gegevens die in de werk ruimte zijn opgenomen, hoog is.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

De uitvoer ziet er ongeveer als volgt uit:

![Query resultaten van het volume gegevens opname vastleggen](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Meer informatie over het bewaken van het gebruik van gegevens en het analyseren van de kosten is beschikbaar in het [beheer van gebruik en kosten met Azure monitor-logboeken](../logs/manage-cost-storage.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van de instellingen van de agent verzameling voor stdout-, stderr-en omgevings variabelen van container werkbelastingen [hier](container-insights-agent-config.md).