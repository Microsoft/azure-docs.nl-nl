---
title: Azure Monitor inschakelen voor een enkele virtuele-machine of een virtuele-machineschaalset in Azure Portal
description: Meer informatie over het inschakelen van VM Insights op één virtuele machine of virtuele-machine schaalset met behulp van de Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 47dde48e916361620a832d26e6249c4147d0f8b5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733734"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Azure Monitor inschakelen voor een enkele virtuele-machine of een virtuele-machineschaalset in Azure Portal
In dit artikel wordt beschreven hoe u VM Insights inschakelt voor een virtuele machine of een schaalset voor virtuele machines met behulp van de Azure Portal. Deze procedure kan worden gebruikt voor het volgende:

- Azure virtuele machine
- Schaalset voor virtuele Azure-machines
- Hybride virtuele machine verbonden met Azure-boog

## <a name="prerequisites"></a>Vereisten

- [Een log Analytics-werk ruimte maken en configureren](./vminsights-configure-workspace.md). U kunt ook een nieuwe werk ruimte maken tijdens dit proces.
- Zie [ondersteunde besturings systemen](./vminsights-enable-overview.md#supported-operating-systems) om ervoor te zorgen dat het besturings systeem van de virtuele machine of virtuele-machine schaalset die u inschakelt, wordt ondersteund. 

## <a name="enable-vm-insights"></a>VM Insights inschakelen

Selecteer in de Azure Portal **virtuele machines**, **virtuele-machine schaal sets** of **servers-Azure-boog** en selecteer een resource in de lijst. Selecteer in de sectie **bewaking** van het menu **inzichten** en **Schakel** vervolgens in. In het volgende voor beeld ziet u een virtuele machine van Azure, maar het menu is vergelijkbaar voor virtuele-machine schaal sets van Azure of Azure Arc.

![VM Insights inschakelen voor een VM](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Als de virtuele machine nog niet is verbonden met een Log Analytics-werk ruimte, wordt u gevraagd om er een te selecteren. Als u nog geen [werk ruimte hebt gemaakt](../logs/quick-create-workspace.md), kunt u een standaard waarde selecteren voor de locatie waar de virtuele machine of virtuele-machine schaalset wordt geïmplementeerd in het abonnement. Deze werk ruimte wordt gemaakt en geconfigureerd als deze nog niet bestaat. Als u een bestaande werk ruimte selecteert, wordt deze geconfigureerd voor VM Insights als dit nog niet is gebeurd.

> [!NOTE]
> Als u een werk ruimte selecteert die niet eerder is geconfigureerd voor VM Insights, wordt de *VMInsights* -Management Pack toegevoegd aan deze werk ruimte. Dit wordt toegepast op alle agents die al zijn verbonden met de werk ruimte, ongeacht of deze is ingeschakeld voor VM Insights. Prestatie gegevens worden verzameld van deze virtuele machines en opgeslagen in de tabel *InsightsMetrics* .

![Werkruimte selecteren](media/vminsights-enable-portal/select-workspace.png)

U ontvangt status berichten wanneer de configuratie wordt uitgevoerd.

>[!NOTE]
>Als u een hand matig upgrade model voor de schaalset voor virtuele machines gebruikt, moet u de exemplaren bijwerken om de installatie te volt ooien. U kunt de upgrades starten vanaf de pagina **instanties** , in de sectie **instellingen** .

![Verwerking van de implementatie van VM Insights-bewaking inschakelen](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Volgende stappen

* Zie [VM Insights-toewijzing gebruiken](vminsights-maps.md) om gedetecteerde toepassings afhankelijkheden weer te geven. 
* Zie [Azure-VM-prestaties weer geven](vminsights-performance.md) om knel punten, algemeen gebruik en de prestaties van uw virtuele machine te identificeren.
