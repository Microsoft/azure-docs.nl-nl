---
title: Bewaking configureren in Azure Monitor voor VM's-gast status (preview-versie)
description: Hierin wordt beschreven hoe u de standaard bewaking voor Azure Monitor voor VM's gast status (preview) wijzigt met behulp van de Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 427bdec2b5e5ab14d566375d5ad8f9da9dc3e81b
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505593"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Bewaking configureren in Azure Monitor voor VM's-gast status (preview-versie)
Met Azure Monitor voor VM's gast status kunt u de status van een virtuele machine weer geven zoals gedefinieerd door een set prestatie metingen die regel matig worden steek proeven. In dit artikel wordt beschreven hoe u de standaard bewaking kunt wijzigen met behulp van de Azure Portal. Hierin worden ook de basis concepten beschreven van monitors die zijn vereist voor [het configureren van bewaking met behulp van een gegevens verzamelings regel](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Monitor configuratie openen
Open de Azure Portal bin configuratie-lade bewaken door de monitor te selecteren en vervolgens op het tabblad **configuratie** .

[![Configuratie details controleren](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Monitors in-of uitschakelen
Zowel de unit-monitors als de geaggregeerde monitors hebben een status instelling van **Health Monitor** waarmee u de monitor kunt in-of uitschakelen. Wanneer een monitor is ingeschakeld, wordt de status weer gegeven en gebruikt om de status van de virtuele machine in te stellen. Wanneer een monitor is uitgeschakeld, wordt de status niet berekend en wordt deze niet gebruikt om de status van de virtuele machine in te stellen.

| Instelling | Beschrijving |
|:---|:---|
| Ingeschakeld | De monitor is ingeschakeld ongeacht de instelling van het bovenliggende item. |
| Uitgeschakeld | De monitor is uitgeschakeld ongeacht de instelling van het bovenliggende item. |
| Hetzelfde als bovenliggend | De monitor wordt ingeschakeld of uitgeschakeld, afhankelijk van de instelling van het bovenliggende item. |

Wanneer een monitor is uitgeschakeld, worden er criteria weer gegeven die niet beschikbaar zijn, zoals in het volgende voor beeld wordt weer gegeven.

![Uitgeschakelde monitor](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> Als een bovenliggende monitor is uitgeschakeld, worden onderliggende monitors ook uitgeschakeld. Als u de onderliggende monitor expliciet inschakelt, wordt het bovenliggende item ook ingeschakeld, maar blijft de configuratie status hetzelfde. In dit geval wordt het volgende bericht weer gegeven in de bovenliggende monitor.
>
> *Er is een verschil als de geconfigureerde status van de monitor is uitgeschakeld, maar de status komt niet overeen met die. Dit komt doordat de geconfigureerde wijzigingen worden door gegeven of een van de onderliggende monitors expliciet is ingeschakeld.*

## <a name="enable-or-disable-virtual-machine"></a>Virtuele machine in-of uitschakelen
U kunt de bewaking voor een virtuele machine uitschakelen om alle monitors tijdelijk te stoppen. U kunt de bewaking voor een virtuele machine uitschakelen bijvoorbeeld wanneer u onderhouds werkzaamheden uitvoert.

| Instelling | Beschrijving |
|:---|:---|
| Ingeschakeld  | De status van de computer wordt weer gegeven. |
| Uitgeschakeld | De status van de computer wordt weer gegeven als **uitgeschakeld**. Er worden geen waarschuwingen gemaakt. |

## <a name="health-state-logic"></a>Status logica
Met de status logica voor een unit-monitor worden de criteria voor het instellen van de status gedefinieerd. U kunt opgeven hoeveel statussen een monitor heeft en wat de drempel voor het berekenen van de status is.

![Voor beeld van status criteria](media/vminsights-health-configure/sample-health-criteria.png)

Aggregatie schermen geven geen status logica op. Hun status wordt ingesteld door de unit-monitors onder hen volgens hun status pakket.

Eenheids monitors hebben elk twee of drie statussen. Elk krijgt altijd een goede status en optioneel een waarschuwings status, een kritieke status of beide. Waarschuwings-en kritieke statussen vereisen elk een uniek criterium dat definieert wanneer de monitor is ingesteld op deze status. De status in orde heeft geen criteria omdat deze status is ingesteld wanneer niet aan de criteria voor de andere status wordt voldaan.

In het volgende voor beeld wordt het CPU-gebruik ingesteld op de volgende statussen:

- Kritiek als deze groter is dan 90%.
- Waarschuwing als deze groter is dan of gelijk is aan 80%.
- In orde als de minder dan 80%. Dit is geïmpliceerd omdat het de voor waarde is waarin geen van de andere voor waarden van toepassing is.

## <a name="next-steps"></a>Volgende stappen

- [Configureer monitors op schaal met behulp van regels voor gegevens verzameling.](vminsights-health-configure-dcr.md)