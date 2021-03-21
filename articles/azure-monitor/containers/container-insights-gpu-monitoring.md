---
title: GPU-bewaking configureren met container Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewakings Kubernetes-clusters kunt configureren met NVIDIA-en AMD GPU-knoop punten met container Insights.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731864"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>GPU-bewaking met container Insights configureren

Met ingang van agent versie *ciprod03022019* ondersteunt de container Insights Integrated agent nu het gebruik van GPU (grafische processor eenheden) op GPU-bewuste Kubernetes-cluster knooppunten en bewaakt u voor het bewaken en gebruiken van het aantal GPU-resources.

## <a name="supported-gpu-vendors"></a>Ondersteunde GPU-leveranciers

Container Insights ondersteunt bewaking van GPU-clusters van de volgende GPU-leveranciers:

- [GRAFISCHE](https://developer.nvidia.com/kubernetes-gpu)

- [POWERNOW](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Met container Insights wordt het GPU-gebruik in knoop punten automatisch gecontroleerd en wordt er een GPU en werk belastingen aangevraagd door de volgende metrische gegevens bij 60sec-intervallen te verzamelen en op te slaan in de tabel **InsightMetrics** .

>[!NOTE]
>Nadat u het cluster hebt ingericht met GPU-knoop punten, moet u ervoor zorgen dat het [GPU-stuur programma](../../aks/gpu-cluster.md) is geïnstalleerd zoals vereist door aks om GPU-workloads uit te voeren. Container Insights verzamelen GPU-metrische gegevens via het GPU-stuur programma dat in het knoop punt wordt uitgevoerd. 

|Naam van metrische gegevens |Metrische dimensie (Tags) |Beschrijving |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Het percentage tijd in de afgelopen sample periode (60 seconden) gedurende welke GPU bezig was/actief werd verwerkt voor een container. Taak cyclus is een getal tussen 1 en 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Elke container kan limieten als een of meer Gpu's opgeven. Het is niet mogelijk een fractie van een GPU aan te vragen of te beperken. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Elke container kan een of meer Gpu's aanvragen. Het is niet mogelijk een fractie van een GPU aan te vragen of te beperken.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in beschik bare bytes voor een specifieke container. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Hoeveelheid GPU-geheugen in bytes die door een bepaalde container worden gebruikt. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Aantal Gpu's in een knoop punt dat kan worden gebruikt door Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Het totale aantal Gpu's in een knoop punt. |

## <a name="gpu-performance-charts"></a>Grafieken GPU-prestaties 

Container Insights bevat vooraf geconfigureerde grafieken voor de metrische gegevens die eerder in de tabel zijn vermeld als een GPU-werkmap voor elk cluster. Zie [werkmappen in container Insights](../insights/container-insights-reports.md) voor een beschrijving van de werkmappen die beschikbaar zijn voor container Insights.

## <a name="next-steps"></a>Volgende stappen

- Zie [Gpu's gebruiken voor computerintensieve werk belastingen op Azure Kubernetes service](../../aks/gpu-cluster.md) (AKS) voor meer informatie over het implementeren van een AKS-cluster met GPU-ingeschakelde knoop punten.

- Meer informatie over door [GPU geoptimaliseerde VM-sku's in Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Bekijk [GPU-ondersteuning in Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) voor meer informatie over de Kubernetes-experimentele ondersteuning voor het beheren van gpu's over een of meer knoop punten in een cluster.
