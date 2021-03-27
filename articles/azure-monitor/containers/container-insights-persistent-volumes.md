---
title: HW-bewaking configureren met container Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewakings Kubernetes-clusters met permanente volumes met container Insights kunt configureren.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 7c6ddd62bf06d313987289e444962378cea43fc8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627894"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>HW-bewaking met container Insights configureren

Met ingang van agent versie *ciprod10052020* ondersteunt Azure monitor voor containers Integrated agent nu het gebruik van HW (permanent volume) te controleren. Met Agent versie *ciprod01112021* ondersteunt de agent het bewaken van HW-inventaris, met inbegrip van informatie over de status, opslag klasse, type, toegangs modi en andere details.
## <a name="pv-metrics"></a>Meet waarden voor hw

Met container Insights wordt het gebruik van de HW automatisch gecontroleerd door de volgende metrische gegevens te verzamelen om 60 sec-intervallen en op te slaan in de tabel **InsightMetrics** .

| Naam van metrische gegevens | Metrische dimensie (Tags) | Beschrijving van metrische gegevens |
|-----|-----------|----------|
| `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, clustername| Gebruikte ruimte in bytes voor een specifiek permanent volume met een claim die wordt gebruikt door een specifieke pod. `capacityBytes` wordt gevouwen in als een dimensie in het veld Tags om de kosten voor de opname van gegevens te verlagen en query's te vereenvoudigen.|

Meer informatie over het configureren van verzamelde meet waarden voor [hw.](./container-insights-agent-config.md)

## <a name="pv-inventory"></a>HW-inventaris

Azure Monitor voor containers wordt het bewaken van PVs automatisch gestart door de volgende informatie te verzamelen met 60 sec-intervallen en op te slaan in de tabel **KubePVInventory** .

|Gegevens |Gegevensbron| Gegevenstype| Velden|
|-----|-----------|----------|-------|
|Inventarisatie van permanente volumes in een Kubernetes-cluster |Uitvoeren-API |`KubePVInventory` |    PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, clustername, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Permanente volumes bewaken

Azure Monitor voor containers bevat vooraf geconfigureerde grafieken voor deze metrische gegevens over gebruik en inventaris in werkmap sjablonen voor elk cluster. U kunt ook een aanbevolen waarschuwing inschakelen voor het gebruik van HW en query's uitvoeren op deze metrische gegevens in Log Analytics.  

### <a name="workload-details-workbook"></a>Werkmap met werk belasting Details

U kunt gebruiks grafieken voor specifieke werk belastingen vinden op het tabblad permanent volume van de werkmap **Details werk belasting** rechtstreeks vanuit een AKS-cluster door werkmappen te selecteren in het linkerdeel venster, vanuit de vervolg keuzelijst **werkmappen weer geven** in het deel venster inzichten of op het **tabblad rapporten (preview)** in het deel venster inzichten.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Voor beeld van werkmap van Azure Monitor HW werk belasting":::

### <a name="persistent-volume-details-workbook"></a>Werkmap met details van permanent volume

U kunt een overzicht van de permanente volume inventaris in de **permanente volume Details** werkmap rechtstreeks vanuit een AKS-cluster vinden door werkmappen te selecteren in het linkerdeel venster, vanuit de vervolg keuzelijst **werkmappen weer geven** in het deel venster inzichten of op het tabblad **rapporten (preview)** in het deel venster inzichten.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Voor beeld van een werkmap met Azure Monitor HW-Details":::

### <a name="persistent-volume-usage-recommended-alert"></a>Waarschuwing voor gebruik van permanente volume aanbevolen
U kunt een aanbevolen waarschuwing inschakelen om u te waarschuwen wanneer het gemiddelde HW-gebruik voor een pod hoger is dan 80%. Meer informatie over waarschuwingen [vindt u hier](./container-insights-metric-alerts.md) en hoe u de [standaard drempelwaarde](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps)kunt negeren.
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over verzamelde meet waarden voor hw [vindt u hier](./container-insights-agent-config.md).