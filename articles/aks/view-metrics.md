---
title: Cluster metrieken weer geven voor Azure Kubernetes service (AKS)
description: Cluster metrieken weer geven voor Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969328"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Cluster metrieken weer geven voor Azure Kubernetes service (AKS)

AKS biedt een reeks metrische gegevens voor het besturings element, met inbegrip van de API-server en cluster-automatische schaal functie en cluster knooppunten. Met deze metrische gegevens kunt u de status van uw cluster bewaken en problemen oplossen. U kunt de metrische gegevens voor uw cluster weer geven met behulp van de Azure Portal.

> [!NOTE]
> Deze metrische gegevens van het AKS-cluster overlappen een subset van de [metrische gegevens van Kubernetes][kubernetes-metrics].

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Metrische gegevens weer geven voor uw AKS-cluster met behulp van de Azure Portal

De metrische gegevens voor uw AKS-cluster weer geven:

1. Meld u aan bij de [Azure Portal][azure-portal] en navigeer naar uw AKS-cluster.
1. Klik aan de linkerkant onder *bewaking* op *metrische gegevens*.
1. Maak een grafiek voor de metrische gegevens die u wilt weer geven. Maak bijvoorbeeld een grafiek:
    1. Kies uw cluster voor *bereik*.
    1. Kies voor *metrische naam ruimte* *Container Service (beheerde) standaard metrische gegevens*.
    1. Voor *metrische gegevens* selecteert u onder *peul* *aantal per fase*.
    1. Kies *Gem* voor *aggregatie* .

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

In het bovenstaande voor beeld ziet u de metrische gegevens voor het gemiddelde aantal peulen voor de *myAKSCluster*.

## <a name="available-metrics"></a>Beschikbare metrische gegevens

De volgende cluster metrieken zijn beschikbaar:

| Name | Groep | Id | Description |
| --- | --- | --- | ---- |
| Invlucht aanvragen | API-server (preview-versie) |apiserver_current_inflight_requests | Maximum aantal actieve invlucht aanvragen op de API-server per aanvraag type. |
| Clusterstatus | Cluster automatisch schalen (preview-versie) | cluster_autoscaler_cluster_safe_to_autoscale | Hiermee wordt bepaald of de cluster-automatische schaal actie wordt uitgevoerd op het cluster. |
| Omlaag schalen cooldown | Cluster automatisch schalen (preview-versie) | cluster_autoscaler_scale_down_in_cooldown | Bepaalt of de schaal omlaag zich in cooldown bevindt. tijdens deze periode worden er geen knoop punten verwijderd. |
| Overbodige knoop punten | Cluster automatisch schalen (preview-versie) | cluster_autoscaler_unneeded_nodes_count | Cluster auotscaler markeert deze knoop punten als kandidaten voor verwijdering en wordt uiteindelijk verwijderd. |
| Unschedulablee peul | Cluster automatisch schalen (preview-versie) | cluster_autoscaler_unschedulable_pods_count | Het aantal peulen dat momenteel wordt unschedulable in het cluster. |
| Totaal aantal beschik bare CPU-kernen in een beheerd cluster | Knooppunten | kube_node_status_allocatable_cpu_cores | Totaal aantal beschik bare CPU-kernen in een beheerd cluster. |
| Totale hoeveelheid beschikbaar geheugen in een beheerd cluster | Knooppunten | kube_node_status_allocatable_memory_bytes | Totale hoeveelheid beschikbaar geheugen in een beheerd cluster. |
| Statussen voor de verschillende knooppunt voorwaarden | Knooppunten | kube_node_status_condition | Statussen voor de verschillende knooppunt voorwaarden |
| Millicores CPU-gebruik | Knoop punten (preview-versie) | node_cpu_usage_millicores | Cumulatieve meting van het CPU-gebruik in millicores in het cluster. |
| Percentage CPU-gebruik | Knoop punten (preview-versie) | node_cpu_usage_percentage | Samengevoegd gemiddeld CPU-gebruik gemeten als percentage in het cluster. |
| Geheugen RSS-bytes | Knoop punten (preview-versie) | node_memory_rss_bytes | RSS-geheugen van container gebruikt in bytes. |
| Percentage van geheugen RSS | Knoop punten (preview-versie) | node_memory_rss_percentage | Het RSS-geheugen van de container wordt gebruikt als percentage. |
| Bytes van geheugen werkset | Knoop punten (preview-versie) | node_memory_working_set_bytes | Werkset geheugen voor de container wordt gebruikt in bytes. |
| Percentage werkset geheugen | Knoop punten (preview-versie) | node_memory_working_set_percentage | Werkset geheugen voor de container wordt gebruikt als percentage. |
| Gebruikte schijf bytes | Knoop punten (preview-versie) | node_disk_usage_bytes | Schijf ruimte die wordt gebruikt in bytes door apparaat. |
| Percentage gebruikte schijf | Knoop punten (preview-versie) | node_disk_usage_percentage | Schijf ruimte die wordt gebruikt voor een percentage van het apparaat. |
| Netwerk in bytes | Knoop punten (preview-versie) | node_network_in_bytes | Ontvangen bytes van netwerk. |
| Uitgaande netwerk bytes | Knoop punten (preview-versie) | node_network_out_bytes | Verzonden bytes door netwerk. |
| Aantal in de status gereed | Gehele | kube_pod_status_ready | Aantal peulen in de status *gereed* . |
| Aantal per fase | Gehele | kube_pod_status_phase | Aantal per fase. |

> [!IMPORTANT]
> Metrische gegevens in de preview-versie kunnen worden bijgewerkt of gewijzigd, inclusief hun namen en beschrijvingen, terwijl ze in de preview-versie zijn opgenomen.

## <a name="next-steps"></a>Volgende stappen

Naast de cluster metrieken voor AKS kunt u ook Azure Monitor gebruiken met uw AKS-cluster. Zie [Azure monitor voor containers][aks-azure-monitory]voor meer informatie over het gebruik van Azure monitor met AKS.

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/