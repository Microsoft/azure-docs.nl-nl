---
title: Bewaken van de kosten voor container Insights | Microsoft Docs
description: In dit artikel worden de bewakings kosten beschreven voor metrische gegevens & door de container inzichten verzamelde inventarisatie-informatie om klanten te helpen hun gebruik en de bijbehorende kosten te beheren.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 78387e950d476126d7c2065a530844e44fd59b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728906"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>Bewaken van de berekenings kosten voor container Insights

In dit artikel vindt u richt lijnen voor de prijs informatie voor container Insights, zodat u inzicht krijgt in het volgende:

* Een schatting maken van de kosten vooraf voordat u dit inzicht inschakelt

* Kosten meten nadat container Insights is ingeschakeld voor een of meer containers

* Het verzamelen van gegevens en het maken van kosten reducties

Met Azure Monitor logboeken worden gegevens verzameld, geïndexeerd en opgeslagen die zijn gegenereerd door uw Kubernetes-cluster. 

Het prijs model voor Azure Monitor is voornamelijk gebaseerd op de hoeveelheid gegevens die is opgenomen in gigabytes per dag in uw Log Analytics-werk ruimte. De kosten van een Log Analytics werk ruimte zijn alleen gebaseerd op het volume van verzamelde gegevens. het is ook afhankelijk van het geselecteerde plan en hoe lang u ervoor hebt gekozen om gegevens op te slaan die zijn gegenereerd op basis van uw clusters.

>[!NOTE]
>Alle grootten en prijzen zijn alleen voor de voorbeeld schatting. Raadpleeg de pagina met [prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor Azure monitor voor de meest recente prijzen op basis van uw Azure monitor log Analytics prijs model en Azure-regio.

Hier volgt een samen vatting van de typen gegevens die worden verzameld uit een Kubernetes-cluster met container inzichten die invloed hebben op de kosten en kunnen worden aangepast op basis van uw gebruik:

- Stdout, stderr-container logboeken van elke bewaakte container in elke Kubernetes-naam ruimte in het cluster

- Container omgevings variabelen van elke bewaakte container in het cluster

- Voltooide Kubernetes-taken/peuling in het cluster waarvoor geen bewaking is vereist

- Actieve uitval van Prometheus-metrische gegevens

- [Diagnostische logboek verzameling](../../aks/view-control-plane-logs.md) van Kubernetes-hoofd knooppunt Logboeken in uw AKS-cluster voor het analyseren van logboek gegevens die zijn gegenereerd door hoofd onderdelen, zoals de *uitvoeren-apiserver* en *uitvoeren-Controller-Manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>Wat wordt verzameld uit Kubernetes-clusters

Container Insights bevat een vooraf gedefinieerde set metrische gegevens en inventarisatie-items die zijn opgeslagen in de logboeken van uw Log Analytics-werk ruimte. Alle hieronder weer gegeven metrische gegevens worden standaard elke minuut verzameld.

### <a name="node-metrics-collected"></a>Metrische knooppunt gegevens verzameld

De volgende lijst bevat de 24 metrische gegevens per knoop punt dat wordt verzameld:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- gebruikt (schijf)
- gratis (schijf)
- used_percent (schijf)
- io_time (diskio)
- Schrijf bewerkingen (diskio)
- Lees bewerkingen (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>Metrische container gegevens

De volgende lijst bevat de acht metrische gegevens per container die worden verzameld:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Cluster inventaris

De volgende lijst bevat de inventarisatie gegevens voor de cluster die standaard worden verzameld:

- KubePodInventory – 1 per minuut per container
- KubeNodeInventory – 1 per knoop punt per minuut
- KubeServices – 1 per service per minuut
- ContainerInventory – 1 per container per minuut

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>De kosten voor het bewaken van uw AKS-cluster schatten

De onderstaande schatting is gebaseerd op een Azure Kubernetes service (AKS)-cluster met het volgende formaat voor beeld. De schatting is ook alleen van toepassing op metrische gegevens en inventaris verzamelde informatie. Voor container Logboeken (stdout, stderr en omgevings variabelen) is het afhankelijk van de grootte van het logboek dat wordt gegenereerd door de werk belasting en worden ze uitgesloten van onze schatting.

Als u de bewaking van een AKS-cluster hebt ingeschakeld, hebt u het volgende geconfigureerd:

- Drie knoop punten
- Twee schijven per knoop punt
- Eén netwerk interface per knoop punt
- 20 Peul (één container in elke pod = 20 containers in totaal)
- Twee Kubernetes-naam ruimten
- Vijf Kubernetes Services (inclusief uitvoeren-systeem van Peul, services en naam ruimte)
- Verzamelings frequentie = 60 seconden (standaard)

U kunt de tabellen en de hoeveelheid gegevens die per uur worden gegenereerd, bekijken in de werk ruimte toegewezen Log Analytics. Zie [container records](container-insights-log-search.md#container-records)voor meer informatie over elk van deze tabellen.

|Tabel | Geschatte grootte (MB/uur) |
|------|---------------|
|Prestaties | 12,9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContainerInventory | 3,6 |
|KubeHealth | 0,1 |
|KubeMonAgentEvents |0,005 |

Totaal = 31 MB/uur = 23,1 GB/maand (één maand = 31 dagen)

Met de standaard [prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor log Analytics, een model voor betalen per gebruik, kunt u de Azure monitor kosten per maand schatten. Na het opnemen van een capaciteits reservering, is de prijs hoger per maand, afhankelijk van de geselecteerde reserve ring.

## <a name="controlling-ingestion-to-reduce-cost"></a>Opname controleren om de kosten te verlagen

Bekijk een scenario waarin de verschillende Business Unit van uw organisatie Kubernetes-infra structuur en een Log Analytics-werk ruimte delen. Elke bedrijfs eenheid gescheiden door een Kubernetes-naam ruimte. U kunt visualiseren hoeveel gegevens worden opgenomen in elke werk ruimte met behulp van het runbook voor **gegevens gebruik** dat beschikbaar is via de vervolg keuzelijst **werkmappen weer geven** .

[![Vervolg keuzelijst werkmappen weer geven](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Deze werkmap helpt u bij het visualiseren van de bron van uw gegevens zonder dat u uw eigen bibliotheek met query's hoeft te bouwen op basis van wat we in onze documentatie delen. In deze werkmap zijn er grafieken waarmee u factureer bare gegevens uit deze perspectieven kunt weer geven als:

- Totale factureer bare gegevens die zijn opgenomen in GB per oplossing
- Factureer bare gegevens die zijn opgenomen door container Logboeken (toepassings Logboeken)
- Ontvangen container logboek gegevens die zijn opgenomen per Kubernetes-naam ruimte
- Ontvangen container logboek gegevens die zijn opgenomen in gescheiden door de cluster naam
- Ontvangen container logboek gegevens die zijn opgenomen door de logsource-vermelding
- Factureer bare diagnostische gegevens die zijn opgenomen door de logboeken van de diagnostische hoofd knooppunten

[![Data Usage-werkmap](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Raadpleeg [Access Control](../visualize/workbooks-access-control.md)voor meer informatie over het beheren van rechten en machtigingen voor de werkmap.

Na het volt ooien van de analyse om te bepalen welke bron of bronnen de meeste gegevens genereren of meer gegevens die uw vereisten overschrijden, kunt u het verzamelen van gegevens opnieuw configureren. Meer informatie over het configureren van een verzameling van stdout-, stderr-en omgevings variabelen wordt beschreven in het artikel instellingen voor het [verzamelen van agent gegevens configureren](container-insights-agent-config.md) .

Hier volgen enkele voor beelden van de wijzigingen die u kunt Toep assen op uw cluster door het ConfigMap-bestand te wijzigen om de kosten te bepalen.

1. Schakel het volgende in het ConfigMap-bestand in om de stdout-logboeken voor alle naam ruimten in het cluster te wijzigen:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Schakel het verzamelen van stderr-Logboeken uit in uw ontwikkelings naam ruimte (bijvoorbeeld **dev-test**) en ga door met het verzamelen van stderr-logboeken van andere naam ruimten (bijvoorbeeld: **Prod** en **default**) door het volgende te wijzigen in het ConfigMap-bestand:

    >[!NOTE]
    >De uitvoeren-systeem logboek verzameling is standaard uitgeschakeld. De standaard instelling blijft behouden, het toevoegen van de naam ruimte **dev-test** aan de lijst met uitgesloten naam ruimten wordt toegepast op de logboek verzameling van stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Schakel de verzameling van omgevings variabelen over het cluster uit door het volgende in het ConfigMap-bestand te wijzigen. Dit is van toepassing op alle containers in elke Kubernetes-naam ruimte. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Als u voltooide taken wilt opschonen, geeft u het opschoon beleid op in de taak definitie door de volgende wijzigingen aan te brengen in het ConfigMap-bestand:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Na het Toep assen van een of meer van deze wijzigingen in uw ConfigMaps, raadpleegt u [Updated ConfigMap Toep assen](container-insights-prometheus-integration.md#applying-updated-configmap) om het toe te passen op uw cluster.

### <a name="prometheus-metrics-scraping"></a>Prometheus metrische gegevens

Als u [Prometheus metrische uitval](container-insights-prometheus-integration.md)wilt gebruiken, moet u rekening houden met het volgende om het aantal metrische gegevens te beperken dat u van het cluster verzamelt:

- Controleer of de uitval frequentie optimaal is ingesteld (de standaard waarde is 60 seconden). U kunt de frequentie verhogen tot 15 seconden, zodat u zeker weet dat de metrische gegevens die u wilt opvallen, op die frequentie worden gepubliceerd. Als dat niet het geval is, worden er veel dubbele metrische gegevens bespaard en naar uw Log Analytics-werk ruimte verzonden met intervallen die worden toegevoegd aan de gegevens opname en de Bewaar kosten, maar die minder waarde hebben. 

- Container Insights ondersteunt uitsluitingen & lijsten met metrische naam. Als u bijvoorbeeld **kubedns** metrische gegevens in uw cluster wilt opwaarderen, kunnen er honderden worden opgehaald die standaard worden geknipseld, maar u bent waarschijnlijk alleen geïnteresseerd in een subset. Controleer of u een lijst met metrische gegevens hebt opgegeven die u wilt opvallen, of sluit anderen uit, behalve enkele om op te slaan op het volume met gegevens opname. Het is eenvoudig om uitval in te scha kelen en niet veel van deze metrische gegevens te gebruiken, zodat er geen extra kosten aan uw Log Analytics factuur worden toegevoegd.

- Zorg ervoor dat u op naam ruimte filtert bij het uitvallen van pod-aantekeningen, zodat u de uitvallen van pod-metrische gegevens uit naam ruimten die u niet gebruikt (bijvoorbeeld de naam ruimte **dev-test** ) uitsluit.

## <a name="next-steps"></a>Volgende stappen

Zie [uw gebruik en geschatte kosten beheren](../logs/manage-cost-storage.md)voor meer informatie over het begrijpen van de kosten die waarschijnlijk worden gebaseerd op recente gebruiks patronen van gegevens die zijn verzameld met container Insights.