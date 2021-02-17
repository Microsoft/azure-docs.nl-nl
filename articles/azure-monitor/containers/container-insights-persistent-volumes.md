---
title: HW-bewaking configureren met Azure Monitor voor containers | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewakings Kubernetes-clusters kunt configureren met permanente volumes met Azure Monitor voor containers.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: d7da6bc88e7c8526e3940714502d3c92d2f37dd8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613014"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>HW-bewaking met Azure Monitor voor containers configureren

Met ingang van agent versie *ciprod10052020* ondersteunt Azure monitor voor containers Integrated agent nu het gebruik van HW (permanent volume) te controleren.

## <a name="pv-metrics"></a>Meet waarden voor hw

Azure Monitor voor containers wordt het controleren van de HW automatisch gestart door de volgende metrische gegevens te verzamelen met 60sec intervallen en op te slaan in de tabel **InsightMetrics** .

|Naam van metrische gegevens |Metrische dimensie (Tags) |Description |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Gebruikte ruimte in bytes voor een specifiek permanent volume met een claim die wordt gebruikt door een specifieke pod. `pvCapacityBytes` wordt gevouwen in als een dimensie in het veld Tags om de kosten voor de opname van gegevens te verlagen en query's te vereenvoudigen.|

## <a name="monitor-persistent-volumes"></a>Permanente volumes bewaken

Azure Monitor voor containers bevat vooraf geconfigureerde grafieken voor deze metrische gegevens in een werkmap voor elk cluster. U kunt de grafieken vinden op het tabblad permanent volume van de werkmap **Details werk belasting** rechtstreeks vanuit een AKS-cluster door werkmappen te selecteren in het linkerdeel venster en in de vervolg keuzelijst **werkmappen weer geven** in het inzicht. U kunt ook een aanbevolen waarschuwing voor het gebruik van HW inschakelen en deze metrische gegevens in Log Analytics opvragen.  

![Voor beeld van werkmap van Azure Monitor HW werk belasting](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over verzamelde meet waarden voor hw [vindt u hier](./container-insights-agent-config.md).
