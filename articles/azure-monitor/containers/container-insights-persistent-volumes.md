---
title: HW-bewaking configureren met container Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u bewakings Kubernetes-clusters met permanente volumes met container Insights kunt configureren.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713725"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>HW-bewaking met container Insights configureren

Met ingang van agent versie *ciprod10052020* ondersteunt de geïntegreerde agent voor container Insights nu het gebruik van PV (persistent volume).

## <a name="pv-metrics"></a>Meet waarden voor hw

Met container Insights wordt de HW automatisch gecontroleerd door de volgende metrische gegevens te verzamelen met 60sec intervallen en op te slaan in de tabel **InsightMetrics** .

|Naam van metrische gegevens |Metrische dimensie (Tags) |Beschrijving |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Gebruikte ruimte in bytes voor een specifiek permanent volume met een claim die wordt gebruikt door een specifieke pod. `pvCapacityBytes` wordt gevouwen in als een dimensie in het veld Tags om de kosten voor de opname van gegevens te verlagen en query's te vereenvoudigen.|

## <a name="monitor-persistent-volumes"></a>Permanente volumes bewaken

Container Insights bevat vooraf geconfigureerde grafieken voor deze metrische gegevens in een werkmap voor elk cluster. U kunt de grafieken vinden op het tabblad permanent volume van de werkmap **Details werk belasting** rechtstreeks vanuit een AKS-cluster door werkmappen te selecteren in het linkerdeel venster en in de vervolg keuzelijst **werkmappen weer geven** in het inzicht. U kunt ook een aanbevolen waarschuwing voor het gebruik van HW inschakelen en deze metrische gegevens in Log Analytics opvragen.  

![Voor beeld van werkmap van Azure Monitor HW werk belasting](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over verzamelde meet waarden voor hw [vindt u hier](./container-insights-agent-config.md).
