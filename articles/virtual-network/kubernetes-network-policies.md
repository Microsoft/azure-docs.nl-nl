---
title: Azure Kubernetes-netwerk beleid | Microsoft Docs
description: Meer informatie over Kubernetes-netwerk beleid voor het beveiligen van uw Kubernetes-cluster.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b7c683edd15ab05e9efc239ffe07759078754607
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222646"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Overzicht van Azure Kubernetes-netwerk beleid

Netwerk beleid biedt micro segmentatie voor heel wat is net als netwerk beveiligings groepen (Nsg's) micro segmentatie voor Vm's. De Azure Network Policy Manager (ook wel bekend als Azure NPM)-implementatie ondersteunt de standaard Kubernetes-netwerk beleids specificatie. U kunt labels gebruiken om een groep van peulen te selecteren en een lijst met regels voor binnenkomend en uitgaand verkeer te definiëren voor het filteren van gegevens van en naar deze peulen. Meer informatie over het Kubernetes-netwerk beleid in de [Kubernetes-documentatie](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Overzicht van Kubernetes-netwerk beleid](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM-implementatie werkt in combi natie met de Azure-CNI die VNet-integratie voor containers biedt. NPM wordt momenteel alleen ondersteund in Linux. De implementatie dwingt het filteren van verkeer af door IP-regels voor toestaan en weigeren in Linux IPTables te configureren op basis van het gedefinieerde beleid. Deze regels worden samen met Linux IPSets gegroepeerd.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Beveiliging plannen voor uw Kubernetes-cluster
Gebruik netwerk beveiligings groepen (Nsg's) bij het implementeren van de beveiliging voor uw cluster om verkeer dat uw cluster subnet invoert en verlaat (Noord-Zuid verkeer), te filteren. Gebruik Azure NPM voor verkeer tussen peulen in uw cluster (Oost-West-verkeer).

## <a name="using-azure-npm"></a>Azure NPM gebruiken
Azure NPM kan worden gebruikt in de volgende manieren om micro segmentering te bieden voor peulen.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM is standaard beschikbaar in AKS en kan worden ingeschakeld op het moment dat het cluster wordt gemaakt. Meer informatie hierover vindt u in [beveiligd verkeer tussen het gebruik van netwerk beleid in azure Kubernetes service (AKS)](../aks/use-network-policies.md).

### <a name="aks-engine"></a>AKS-engine
AKS-Engine is een hulp programma waarmee een Azure Resource Manager sjabloon wordt gegenereerd voor de implementatie van een Kubernetes-cluster in Azure. De configuratie van het cluster is opgenomen in een JSON-bestand dat aan het hulpprogramma wordt doorgegeven bij het genereren van de sjabloon. Zie Microsoft Azure Container Service Engine - Cluster Definition (Microsoft Azure Container Service Engine - Clusterdefinitie) voor de volledige lijst met ondersteunde clusterinstellingen en de bijbehorende beschrijvingen.

Als u beleids regels wilt inschakelen voor clusters die zijn geïmplementeerd met ACS-engine, geeft u de waarde van de networkPolicy-instelling in het cluster definitie bestand op "Azure".

#### <a name="example-configuration"></a>Voorbeeldconfiguratie

De onderstaande JSON-voorbeeld configuratie maakt een nieuw virtueel netwerk en subnet en implementeert een Kubernetes-cluster met Azure CNI. U kunt het beste Klad blok gebruiken om het JSON-bestand te bewerken. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Doe het zelf (zelf) Kubernetes-clusters in azure
 Voor zelf-clusters installeert u eerst de CNI-invoeg toepassing en schakelt u deze in op elke virtuele machine in een cluster. Zie [Deploy plug-in for a Kubernetes cluster](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster) (Invoegtoepassing implementeren voor een Kubernetes-cluster) voor gedetailleerde instructies.

Zodra het cluster is geïmplementeerd, voert u de volgende `kubectl` opdracht uit om de Azure NPM- *daemon-set* te downloaden en toe te passen op het cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
De oplossing is ook open source en de code is beschikbaar op de [Azure container-netwerk opslagplaats](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Netwerk configuraties bewaken en visualiseren met Azure NPM
Azure NPM bevat informatieve Prometheus-gegevens die u in staat stellen om uw configuraties te bewaken en beter te begrijpen. Het biedt ingebouwde visualisaties in de Azure Portal of Grafana Labs. U kunt beginnen met het verzamelen van deze metrische gegevens met behulp van Azure Monitor of een Prometheus-server.

### <a name="benefits-of-azure-npm-metrics"></a>Voor delen van Azure NPM-metrische gegevens
Gebruikers hadden eerder alleen meer informatie over hun netwerk configuratie met de opdracht `iptables -L` uitgevoerd in een cluster knooppunt, wat een uitgebreide en moeilijk te begrijpen uitvoer oplevert. NPM-metrische gegevens bieden de volgende voor delen met betrekking tot netwerk beleid, IPTables-regels en IPSets.
- Biedt inzicht in de relatie tussen de drie en een tijd dimensie om fouten in een configuratie op te sporen.
- Aantal vermeldingen in alle IPSets en elke IPSet.
- De tijd die nodig is voor het Toep assen van een beleid met IPTable/IPSet niveau granulariteit.
 
### <a name="supported-metrics"></a>Ondersteunde metrische gegevens
Hier volgt een lijst met ondersteunde metrische gegevens:

|Naam meetwaarde |Beschrijving  |Type metrische waarde voor Prometheus  |Labels  |
|---------|---------|---------|---------|
|`npm_num_policies`     |aantal netwerk beleidsregels          |Meter         |-         |
|`npm_num_iptables_rules`     | aantal IPTables-regels     | Meter        |-         |         
|`npm_num_ipsets`     |aantal IPSets         |Meter            |-         |
|`npm_num_ipset_entries`     |aantal IP-adres vermeldingen in alle IPSets         |Meter         |-         |
|`npm_add_policy_exec_time`     |runtime voor het toevoegen van een netwerk beleid         |Samenvatting         |quantile (0,5, 0,9 of 0,99)         |
|`npm_add_iptables_rule_exec_time`     |runtime voor het toevoegen van een IPTables-regel         |Samenvatting         |quantile (0,5, 0,9 of 0,99)         |
|`npm_add_ipset_exec_time`     |runtime voor het toevoegen van een IPSet         |Samenvatting         |quantile (0,5, 0,9 of 0,99)         |
|`npm_ipset_counts` gevanceerde     |aantal vermeldingen binnen elke afzonderlijke IPSet         |GaugeVec         |naam van &-hash instellen         |

Met de verschillende quantile-niveaus in de metrische gegevens voor ' exec_time ' kunt u onderscheid maken tussen de algemene en ergste scenario's.

Er is ook een metrische waarde voor ' exec_time_count ' en ' exec_time_sum ' voor elke ' exec_time-overzichts metriek.

De metrische gegevens kunnen worden geschroot door Azure Monitor voor containers of via Prometheus.

### <a name="setup-for-azure-monitor"></a>Instellen voor Azure Monitor
De eerste stap is het inschakelen van Azure Monitor voor containers voor uw Kubernetes-cluster. U kunt de stappen vinden in [Azure monitor voor containers Overview](../azure-monitor/insights/container-insights-overview.md). Als u Azure Monitor voor containers hebt ingeschakeld, configureert u de [Azure monitor voor containers ConfigMap](https://aka.ms/container-azm-ms-agentconfig) om NPM-integratie en verzameling van Prometheus NPM-gegevens in te scha kelen. Azure monitor voor containers ConfigMap bevat een ```integrations``` sectie met instellingen voor het verzamelen van NPM-metrische gegevens. Deze instellingen zijn standaard uitgeschakeld in de ConfigMap. Als u de basis instelling inschakelt ```collect_basic_metrics = true``` , worden algemene NPM-metrische gegevens verzameld. Als u de geavanceerde instelling inschakelt ```collect_advanced_metrics = true``` , worden er naast basis gegevens ook geavanceerde metrische gegevens verzameld. 

Nadat u de ConfigMap hebt bewerkt, slaat u deze lokaal op en past u de ConfigMap als volgt toe op uw cluster.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Hieronder vindt u een fragment van de [Azure monitor voor containers ConfigMap](https://aka.ms/container-azm-ms-agentconfig), waarin de NPM-integratie wordt weer gegeven die is ingeschakeld met de verzameling geavanceerde metrische gegevens.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Geavanceerde meet waarden zijn optioneel. Als u deze inschakelt, wordt automatisch de verzameling basis metrieken ingeschakeld. Geavanceerde metrische gegevens bevatten momenteel alleen `npm_ipset_counts`

Meer informatie over [Azure monitor voor containers verzamelings instellingen in configuratie toewijzing](../azure-monitor/insights/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Visualisatie opties voor Azure Monitor
Zodra de verzameling met NPM-metrische gegevens is ingeschakeld, kunt u de metrische gegevens weer geven in de Azure Portal met behulp van container Insights of in Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Weer geven in Azure Portal onder inzichten voor het cluster
Open Azure Portal. Ga in de inzichten van uw cluster naar werkmappen en open de configuratie Network Policy Manager (NPM).

Naast het weer geven van de werkmap (afbeeldingen hieronder) kunt u de metrische gegevens voor Prometheus ook rechtstreeks opvragen in ' logboeken ' in de sectie inzichten. Met deze query worden bijvoorbeeld alle metrische gegevens weer gegeven die worden verzameld.
| waar TimeGenerated > geleden (5H) | Where-naam bevat "npm_"

U kunt ook rechtstreeks een query uitvoeren op Log Analytics voor de metrische gegevens. Meer informatie hierover vindt u aan de slag [met log Analytics query's](../azure-monitor/insights/container-insights-log-search.md) 

#### <a name="viewing-in-grafana-dashboard"></a>Weer geven in Grafana-dash board
Stel uw Grafana-server in en configureer een Log Analytics gegevens bron zoals [hier](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource)wordt beschreven. Importeer vervolgens [Grafana-dash board met een log Analytics back-end](https://grafana.com/grafana/dashboards/10956) in uw Grafana Labs.

Het dash board heeft visuele elementen die vergelijkbaar zijn met de Azure-werkmap. U kunt deel Vensters toevoegen aan grafiek & metrische gegevens NPM uit InsightsMetrics-tabel visualiseren.

### <a name="setup-for-prometheus-server"></a>Setup voor Prometheus-server
Sommige gebruikers kunnen ervoor kiezen om metrische gegevens te verzamelen met een Prometheus-server in plaats van Azure Monitor voor containers. U hoeft alleen twee taken aan uw uitval configuratie toe te voegen om NPM-metrische gegevens te verzamelen.

Als u een eenvoudige Prometheus-server wilt installeren, voegt u dit helm opslag plaats toe aan uw cluster
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
vervolgens een server toevoegen
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
waar `prometheus-server-scrape-config.yaml` uit
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


U kunt de taak ook vervangen `azure-npm-node-metrics` door de inhoud hieronder of deze opnemen in een vooraf bestaande taak voor Kubernetes peul:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Visualisatie opties voor Prometheus
Wanneer u een Prometheus-server gebruikt, wordt alleen Grafana-dash board ondersteund. 

Als u dat nog niet hebt gedaan, stelt u uw Grafana-server in en configureert u een Prometheus-gegevens bron. Importeer vervolgens het [Grafana-dash board met een Prometheus-back-end](https://grafana.com/grafana/dashboards/13000) in uw Grafana Labs.

De visuele elementen voor dit dash board zijn identiek aan het dash board met een container Insights/Log Analytics back-end.

### <a name="sample-dashboards"></a>Voorbeelddashboards
Hieronder vindt u een voor beeld van een dash board voor NPM metrische gegevens in container Insights (CI) en Grafana

#### <a name="ci-summary-counts"></a>Aantal CI-samen vattingen
![Aantal samen vattingen van Azure-werkmap](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>CI-tellingen in de loop van de tijd
[![Azure-werkmap telt in de loop van de tijd](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI-IPSet vermeldingen
[![IPSet-vermeldingen voor Azure-werkmap](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI-runtime-Quantiles
![Runtime-quantiles van Azure-werkmap](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Aantal Grafana-dashboard samenvattingen
![Aantal Grafana-dashboard samenvattingen](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Grafana-dash board telt over tijd
[![Grafana-dash board telt over tijd](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana dash board IPSet-vermeldingen
[![Grafana dash board IPSet-vermeldingen](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana dash board runtime Quantiles
[![Grafana dash board runtime quantiles](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [Azure Kubernetes-service](../aks/intro-kubernetes.md).
-  Meer informatie over [container netwerken](container-networking-overview.md).
- [Implementeer de invoeg toepassing](deploy-container-networking.md) voor Kubernetes-clusters of docker-containers.

