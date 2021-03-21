---
title: Regionale toewijzingen voor container Insights
description: Hierin worden de regio toewijzingen beschreven die worden ondersteund tussen container Insights, Log Analytics-werk ruimte en aangepaste metrische gegevens.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728872"
---
# <a name="region-mappings-supported-by-container-insights"></a>Regionale toewijzingen die worden ondersteund door container Insights

 Bij het inschakelen van container Insights worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een AKS-cluster en het verzamelen van aangepaste metrische gegevens die worden verzonden naar Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Ondersteunde toewijzingen Log Analytics werk ruimte

Ondersteunde AKS regio's worden vermeld in [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). De Log Analytics-werk ruimte moet zich in dezelfde regio bevinden, met uitzonde ring van de regio's die in de volgende tabel worden weer gegeven. Bekijk [opmerkingen](https://github.com/Azure/AKS/releases) bij de release van AKS voor updates.


|**AKS-cluster regio** | **Log Analytics werkruimte regio** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |West-Europa |
|SouthAfricaWest |West-Europa |
|**Australië** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brazilië** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**India** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japan** | |
|JapanWest |JapanEast |
|**Korea** | |
|KoreaSouth |KoreaCentral |
|**VS** | |
|WestCentralUS<sup>1</sup>|Oost,<sup>1</sup>|


<sup>1</sup> vanwege capaciteits beperkingen is de regio niet beschikbaar bij het maken van nieuwe resources. Dit omvat een Log Analytics-werk ruimte. Bestaande gekoppelde resources in de regio moeten echter blijven werken.

## <a name="custom-metrics-supported-regions"></a>Ondersteunde regio's voor aangepaste metrische gegevens

Het verzamelen van metrische gegevens van knoop punten van Azure Kubernetes Services (AKS)-clusters en peulen wordt alleen ondersteund voor publicatie als aangepaste metrische gegevens in de volgende [Azure-regio's](../essentials/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Volgende stappen

Als u het AKS-cluster wilt bewaken, raadpleegt u [hoe u de container Insights inschakelt](container-insights-onboard.md) om de vereisten en beschik bare methoden te begrijpen voor het inschakelen van bewaking.  
