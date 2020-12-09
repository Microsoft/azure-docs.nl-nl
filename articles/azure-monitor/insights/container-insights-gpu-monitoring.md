---
title: GPU-bewaking configureren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewakings Kubernetes-clusters kunt configureren met NVIDIA-en AMD GPU-knoop punten met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: e391117ab57211aa5d178d11c27b934b4ccd37f8
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905584"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>GPU-bewaking configureren met Azure Monitor voor containers

Met ingang van agent versie *ciprod03022019* biedt Azure monitor voor containers Integrated agent nu ondersteuning voor het gebruik van GPU (grafische verwerkings eenheden) op GPU-bewuste Kubernetes-cluster knooppunten en bewaakt u voor het bewaken van en het bevragen en gebruiken van de GPU-resources.

## <a name="supported-gpu-vendors"></a>Ondersteunde GPU-leveranciers

Azure Monitor voor containers ondersteunt bewaking van GPU-clusters van de volgende GPU-leveranciers:

- [GRAFISCHE](https://developer.nvidia.com/kubernetes-gpu)

- [POWERNOW](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor voor containers worden het GPU-gebruik op knoop punten automatisch bewaakt en GPU die een Peul en werk belasting aanvragen door de volgende metrische gegevens bij 60sec-intervallen te verzamelen en op te slaan in de tabel **InsightMetrics** .

>[!NOTE]
>Nadat u het cluster hebt ingericht met GPU-knoop punten, moet u ervoor zorgen dat het [GPU-stuur programma](../../aks/gpu-cluster.md) is geïnstalleerd zoals vereist door aks om GPU-workloads uit te voeren. Azure Monitor voor containers worden metrische gegevens over GPU verzameld via het GPU-stuur programma dat in het knoop punt wordt uitgevoerd. 

|Naam van metrische gegevens |Metrische dimensie (Tags) |Description |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Het percentage tijd in de afgelopen sample periode (60 seconden) gedurende welke GPU bezig was/actief werd verwerkt voor een container. Taak cyclus is een getal tussen 1 en 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Elke container kan limieten als een of meer Gpu's opgeven. Het is niet mogelijk een fractie van een GPU aan te vragen of te beperken. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Elke container kan een of meer Gpu's aanvragen. Het is niet mogelijk een fractie van een GPU aan te vragen of te beperken.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in beschik bare bytes voor een specifieke container. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in bytes die door een bepaalde container worden gebruikt. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Aantal Gpu's in een knoop punt dat kan worden gebruikt door Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Het totale aantal Gpu's in een knoop punt. |

## <a name="gpu-performance-charts"></a>Grafieken GPU-prestaties 

Azure Monitor voor containers bevat vooraf geconfigureerde grafieken voor de metrische gegevens die eerder in de tabel zijn opgenomen als een GPU-werkmap voor elk cluster. Zie [werkmappen in azure monitor voor containers](container-insights-reports.md) voor een beschrijving van de werkmappen die beschikbaar zijn voor Azure monitor voor containers.

## <a name="next-steps"></a>Volgende stappen

- Zie [Gpu's gebruiken voor computerintensieve werk belastingen op Azure Kubernetes service](../../aks/gpu-cluster.md) (AKS) voor meer informatie over het implementeren van een AKS-cluster met GPU-ingeschakelde knoop punten.

- Meer informatie over door [GPU geoptimaliseerde VM-sku's in Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Bekijk [GPU-ondersteuning in Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) voor meer informatie over de Kubernetes-experimentele ondersteuning voor het beheren van gpu's over een of meer knoop punten in een cluster.
