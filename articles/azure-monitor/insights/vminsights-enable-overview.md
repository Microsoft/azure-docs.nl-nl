---
title: Overzicht Azure Monitor voor VM's inschakelen
description: Meer informatie over het implementeren en configureren van Azure Monitor voor VM's. Ontdek de systeem vereisten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: f5e774e9b7327d4b403f6a09187e97082a77aa78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186798"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Overzicht Azure Monitor voor VM's inschakelen

In dit artikel vindt u een overzicht van de beschik bare opties voor het inschakelen van Azure Monitor voor VM's om de status en prestaties van het volgende te controleren:

- Azure-VM's 
- Virtuele-machineschaalsets van Azure
- Hybride virtuele machines die zijn verbonden met Azure Arc
- On-premises virtuele machines
- Virtuele machines die worden gehost in een andere cloud omgeving.  

Azure Monitor voor VM's instellen:

* Schakel één Azure VM, Azure VMSS of Azure Arc machine in door **inzichten** rechtstreeks te selecteren in het menu van het Azure Portal.
* Meerdere virtuele machines van Azure, Azure VMSS of Azure Arc inschakelen met behulp van Azure Policy. Deze methode zorgt ervoor dat de vereiste afhankelijkheden op bestaande en nieuwe virtuele machines en schaal sets worden geïnstalleerd en op de juiste wijze zijn geconfigureerd. Niet-compatibele Vm's en schaal sets worden gerapporteerd, zodat u kunt beslissen of u ze wilt inschakelen en deze wilt herstellen.
* Meerdere virtuele machines van Azure, Azure-Arc-Vm's, Azure VMSS of Azure Arc-machines in een opgegeven abonnement of resource groep inschakelen met behulp van Power shell.
* Schakel Azure Monitor voor VM's in om Vm's of fysieke computers te bewaken die worden gehost in uw bedrijfs netwerk of een andere cloud omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u de informatie in de volgende secties begrijpt. 

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicetoewijzing oplossing](service-map.md).  

### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte

Azure Monitor voor VM's ondersteunt een Log Analytics-werk ruimte in de volgende regio's:

- Afrika
  - Zuid-Afrika - noord
- Azië en Stille Oceaan
  - Azië - oost
  - Azië - zuidoost
- Australië
  - Australië - oost
  - Australië - zuidoost
- Azure Government
  - US Gov AZ
  - US Gov VA
- Canada
  - Canada - midden
- Europa
  - Europa - noord
  - Europa -west
- India
  - India - centraal
- Japan
  - Japan - oost
- Verenigd Koninkrijk
  - Verenigd Koninkrijk Zuid
- Verenigde Staten
  - Central US
  - VS - oost
  - VS - oost 2
  - VS - noord-centraal
  - VS - zuid-centraal
  - VS - west-centraal
  - VS - west
  - VS - west 2


>[!NOTE]
>U kunt virtuele Azure-machines bewaken in elke regio. De Vm's zijn niet beperkt tot de regio's die worden ondersteund door de Log Analytics-werk ruimte.
>

Als u geen Log Analytics-werk ruimte hebt, kunt u er een maken met behulp van een van de volgende resources:
* [Azure-CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure-portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

- Azure virtuele machine
- Schaalset voor virtuele Azure-machines
- Hybride virtuele machine verbonden met Azure-boog

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Azure Monitor voor VM's ondersteunt elk besturings systeem dat ondersteuning biedt voor de Log Analytics agent en de afhankelijkheids agent. Zie [overzicht van Azure monitor agents ](../platform/agents-overview.md#supported-operating-systems) voor een volledige lijst.

Raadpleeg de volgende lijst met overwegingen voor Linux-ondersteuning van de afhankelijkheids agent die Azure Monitor voor VM's ondersteunt:

- Alleen standaard- en SMP Linux kernelversies worden ondersteund.
- Niet-standaard kernel-releases, zoals Physical Address Extension (PAE) en xen, worden niet ondersteund voor Linux-distributie. Bijvoorbeeld, een systeem met de release reeks *2.6.16.21-0,8-xen* wordt niet ondersteund.
- Aangepaste kernels, met inbegrip van hercompilaties van standaard-kernels, worden niet ondersteund.
- Voor Debian andere distributies dan versie 9,4 is de functie map niet ondersteund en is de functie prestaties alleen beschikbaar vanuit het Azure Monitor menu. Het is niet rechtstreeks beschikbaar vanuit het linkerdeel venster van de Azure-VM.
- De CentOSPlus-kernel wordt ondersteund.
- Er moet een patch worden uitgevoerd voor de Linux-kernel voor het Spectre-beveiligings probleem. Neem contact op met de leverancier van de Linux-distributie voor meer informatie.



## <a name="supported-azure-arc-machines"></a>Ondersteunde Azure-Arc-machines
Azure Monitor voor VM's is beschikbaar voor servers met Azure Arc ingeschakeld in regio's waar de Arc extension-service beschikbaar is. U moet versie 0,9 of hoger van de Arc-agent uitvoeren.

| Verbonden bron | Ondersteund | Description |
|:--|:--|:--|
| Windows-agents | Yes | Naast de [log Analytics-agent voor Windows](../platform/log-analytics-agent.md), hebben Windows-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](../platform/agents-overview.md#supported-operating-systems)voor meer informatie. |
| Linux-agents | Yes | Naast de [log Analytics-agent voor Linux](../platform/log-analytics-agent.md)hebben Linux-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](#supported-operating-systems)voor meer informatie. |
| Beheergroep System Center Operations Manager | No | |

## <a name="agents"></a>Agents
Azure Monitor voor VM's moeten de volgende twee agents zijn geïnstalleerd op elke virtuele machine of schaalset voor virtuele machines die moet worden bewaakt. Het installeren van deze agents en het verbinden ervan met de werk ruimte is de enige vereiste voor het onboarden van de resource.

- [Log Analytics-agent](../platform/log-analytics-agent.md). Verzamelt gebeurtenissen en prestatie gegevens van de virtuele machine of virtuele-machine schaal sets en levert deze aan de Log Analytics-werk ruimte. Voor de implementatie methoden voor de Log Analytics agent op Azure-resources wordt de VM-extensie voor [Windows](../../virtual-machines/extensions/oms-windows.md) en [Linux](../../virtual-machines/extensions/oms-linux.md)gebruikt.
- Afhankelijkheids agent. Verzamelt gedetecteerde gegevens over processen die worden uitgevoerd op de virtuele machine en de afhankelijkheden van het externe proces, die worden gebruikt door de [toewijzings functie in azure monitor voor VM's](vminsights-maps.md). De afhankelijkheids agent is afhankelijk van de Log Analytics-agent om de bijbehorende gegevens te leveren aan Azure Monitor. Voor de implementatie methoden voor de afhankelijkheids agent op Azure-resources wordt de VM-extensie voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) en [Linux](../../virtual-machines/extensions/agent-dependency-linux.md)gebruikt.

> [!NOTE]
> De Log Analytics-agent is dezelfde agent die wordt gebruikt door System Center Operations Manager. Azure Monitor voor VM's kunt agents bewaken die ook worden bewaakt door Operations Manager als deze rechtstreeks zijn verbonden en u de afhankelijkheids agent erop installeert. Agents die zijn verbonden met Azure Monitor via een [beheer groep-verbinding](../tform/../platform/om-agents.md) , kunnen niet worden bewaakt door Azure monitor voor VM's.

Hier volgen meerdere methoden voor het implementeren van deze agents. 

| Methode | Beschrijving |
|:---|:---|
| [Azure-portal](./vminsights-enable-portal.md) | Installeer beide agents op één virtuele machine, virtuele-machine schaalset of hybride virtuele machines die zijn verbonden met Azure Arc. |
| [Resource Manager-sjablonen](vminsights-enable-resource-manager.md) | Installeer beide agents met behulp van een van de ondersteunde methoden om een resource manager-sjabloon te implementeren, waaronder CLI en Power shell. |
| [Azure Policy](./vminsights-enable-policy.md) | Wijs Azure Policy initiatief toe om automatisch de agents te installeren wanneer een virtuele machine of virtuele-machine schaalset wordt gemaakt. |
| [Hand matige installatie](./vminsights-enable-hybrid.md) | Installeer de agents in het gast besturingssysteem op computers die buiten Azure worden gehost, inclusief in uw Data Center of andere Cloud omgevingen. |




## <a name="management-packs"></a>Management packs
Wanneer een Log Analytics-werk ruimte is geconfigureerd voor Azure Monitor voor VM's, worden twee Management Packs doorgestuurd naar alle Windows-computers die zijn verbonden met die werk ruimte. De Management Packs heten *micro soft. intelligence packs. ApplicationDependencyMonitor* en *micro soft. intelligence packs. VMInsights* en zijn geschreven naar *%ProgramFiles%\Microsoft monitoring Agent\Agent\Health service State\Management packs \* . 

De gegevens bron die wordt gebruikt door de *ApplicationDependencyMonitor* -Management Pack is **% Program Files%\Microsoft monitoring Agent\Agent\Health service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. De gegevens bron die wordt gebruikt door de *VMInsights* -Management Pack is *% Program Files%\Microsoft monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiks gegevens

Micro soft verzamelt automatisch gebruiks-en prestatie gegevens via uw gebruik van de Azure Monitor service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren. 

De kaart functie bevat gegevens over de configuratie van uw software om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden. De gegevens bevatten informatie zoals het besturings systeem en de versie, het IP-adres, de DNS-naam en de naam van het werk station. Micro soft verzamelt geen namen, adressen of andere contact gegevens.

Zie de [privacyverklaring voor micro soft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132)voor meer informatie over het verzamelen en gebruiken van gegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Azure monitor voor VM's prestaties weer geven](vminsights-performance.md)voor meer informatie over het gebruik van de functie voor prestatie bewaking. Zie [Azure monitor voor VM's kaart weer geven](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven.
