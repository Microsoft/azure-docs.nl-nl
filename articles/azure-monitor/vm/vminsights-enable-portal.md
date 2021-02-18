---
title: Azure Monitor inschakelen voor een enkele virtuele-machine of een virtuele-machineschaalset in Azure Portal
description: Meer informatie over het inschakelen van Azure Monitor voor VM's op één virtuele machine of virtuele-machine schaalset met behulp van de Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba075930aa3541d0453b678c7d654635ae20da58
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612186"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Azure Monitor inschakelen voor een enkele virtuele-machine of een virtuele-machineschaalset in Azure Portal
In dit artikel wordt beschreven hoe u Azure Monitor voor VM's inschakelt voor een virtuele machine of virtuele-machine schaalset met behulp van de Azure Portal. Deze procedure kan worden gebruikt voor het volgende:

- Azure virtuele machine
- Schaalset voor virtuele Azure-machines
- Hybride virtuele machine verbonden met Azure-boog

## <a name="prerequisites"></a>Vereisten

- [Een log Analytics-werk ruimte maken en configureren](../insights/vminsights-configure-workspace.md). U kunt ook een nieuwe werk ruimte maken tijdens dit proces.
- Zie [ondersteunde besturings systemen](../insights/vminsights-enable-overview.md#supported-operating-systems) om ervoor te zorgen dat het besturings systeem van de virtuele machine of virtuele-machine schaalset die u inschakelt, wordt ondersteund. 

## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor voor VM's inschakelen

Selecteer in de Azure Portal **virtuele machines**, **virtuele-machine schaal sets** of **servers-Azure-boog** en selecteer een resource in de lijst. Selecteer in de sectie **bewaking** van het menu **inzichten** en **Schakel** vervolgens in. In het volgende voor beeld ziet u een virtuele machine van Azure, maar het menu is vergelijkbaar voor virtuele-machine schaal sets van Azure of Azure Arc.

![Azure Monitor voor VM's voor een VM inschakelen](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Als de virtuele machine nog niet is verbonden met een Log Analytics-werk ruimte, wordt u gevraagd om er een te selecteren. Als u nog geen [werk ruimte hebt gemaakt](../../azure-monitor/learn/quick-create-workspace.md), kunt u een standaard waarde selecteren voor de locatie waar de virtuele machine of virtuele-machine schaalset wordt geïmplementeerd in het abonnement. Deze werk ruimte wordt gemaakt en geconfigureerd als deze nog niet bestaat. Als u een bestaande werk ruimte selecteert, wordt deze geconfigureerd voor Azure Monitor voor VM's als dit nog niet is gebeurd.

> [!NOTE]
> Als u een werk ruimte selecteert die niet eerder is geconfigureerd voor Azure Monitor voor VM's, wordt de *VMInsights* -Management Pack toegevoegd aan deze werk ruimte. Dit wordt toegepast op alle agents die al zijn verbonden met de werk ruimte, ongeacht of deze is ingeschakeld voor Azure Monitor voor VM's. Prestatie gegevens worden verzameld van deze virtuele machines en opgeslagen in de tabel *InsightsMetrics* .

![Werkruimte selecteren](media/vminsights-enable-portal/select-workspace.png)

U ontvangt status berichten wanneer de configuratie wordt uitgevoerd.

>[!NOTE]
>Als u een hand matig upgrade model voor de schaalset voor virtuele machines gebruikt, moet u de exemplaren bijwerken om de installatie te volt ooien. U kunt de upgrades starten vanaf de pagina **instanties** , in de sectie **instellingen** .

![Verwerking van de implementatie van de bewaking van Azure Monitor voor VM's inschakelen](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Volgende stappen

* Zie [Azure monitor voor VM's toewijzing gebruiken](vminsights-maps.md) om gedetecteerde toepassings afhankelijkheden weer te geven. 
* Zie [Azure-VM-prestaties weer geven](vminsights-performance.md) om knel punten, algemeen gebruik en de prestaties van uw virtuele machine te identificeren.
