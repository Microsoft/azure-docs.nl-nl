---
title: Problemen met de VM-extensie van Azure Arc ingeschakelde servers oplossen
description: In dit artikel leest u hoe u problemen oplost en oplost met Azure VM-extensies die zich voordoen met Azure Arc-servers.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100584671"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Problemen met Arc ingeschakelde servers oplossen VM-extensie problemen

In dit artikel vindt u informatie over het oplossen van problemen die zich kunnen voordoen bij het implementeren of verwijderen van Azure VM-extensies op servers waarop Arc is ingeschakeld. Zie [Azure VM-extensies beheren en gebruiken](./manage-vm-extensions.md)voor algemene informatie.

## <a name="general-troubleshooting"></a>Algemene probleem oplossing

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal.

De volgende stappen voor probleem oplossing zijn van toepassing op alle VM-extensies.

1. Als u het logboek van de gast agent wilt controleren, bekijkt u de activiteit wanneer uw extensie is ingericht in `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` voor Windows en voor Linux onder `/var/lib/GuestConfig/ext_mgr_logs` .

2. Raadpleeg de extensie logboeken voor de specifieke extensie voor meer informatie over `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` Windows. Uitvoer van de extensie wordt vastgelegd in een bestand voor elke uitbrei ding die is geïnstalleerd in Linux onder `/var/lib/GuestConfig/extension_logs` .

3. Raadpleeg de sectie extensie-specifieke documentatie voor het oplossen van problemen met fout codes, bekende problemen etc. Meer informatie over het oplossen van problemen voor elke uitbrei ding vindt u in de sectie **problemen oplossen en ondersteuning** in het overzicht voor de uitbrei ding. Dit omvat de beschrijving van fout codes die naar het logboek zijn geschreven. De uitbrei ding artikelen worden gekoppeld in de [tabel extensies](manage-vm-extensions.md#extensions).

4. Bekijk de systeem Logboeken. Controleer op andere bewerkingen die mogelijk zijn verstoord met de extensie, zoals een langlopende installatie van een andere toepassing die exclusieve toegang tot Package Manager vereist.

## <a name="troubleshooting-specific-extension-scenarios"></a>Problemen met specifieke extensie scenario's oplossen

### <a name="vm-insights"></a>VM Insights

- Wanneer u VM Insights inschakelt voor een Azure Arc-server, wordt de afhankelijkheid en Log Analytics agent geïnstalleerd. Op een trage machine of een met een trage netwerk verbinding is het mogelijk dat tijdens het installatie proces time-outs worden weer geven. Micro soft neemt de stappen om dit in de verbonden machine-agent te aanpakken om dit probleem te helpen verbeteren. In de tijdelijke situatie kan een nieuwe poging van de installatie mislukken.

### <a name="log-analytics-agent-for-linux"></a>Log Analytics-agent voor Linux

- De 1.13.9-versie van de Log Analytics-agent (de overeenkomende extensie versie is 1.13.15) heeft geen juiste markering van geüploade gegevens met de resource-ID van de Azure Arc-server. Hoewel er logboeken naar de service worden verzonden, wanneer u probeert de gegevens van de geselecteerde server weer te geven nadat u **Logboeken** of **inzichten** hebt geselecteerd, worden er geen gegevens geretourneerd. U kunt de gegevens weer geven door query's uit te voeren vanuit Azure Monitor Logboeken of vanuit Azure Monitor voor VM's, die binnen het bereik van de werk ruimte vallen.

- Sommige distributies worden momenteel niet ondersteund door de Log Analytics-agent voor Linux. De agent vereist extra afhankelijkheden die moeten worden geïnstalleerd, inclusief python 2. Bekijk de ondersteunings matrix en vereisten [hier](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

- Fout code 52 in het status bericht wijst op ontbrekende afhankelijkheid. Controleer de uitvoer en logboeken voor meer informatie over welke afhankelijkheden ontbreken.

- Als een installatie mislukt, raadpleegt u de sectie **problemen oplossen en ondersteuning** in het overzicht voor de uitbrei ding. In de meeste gevallen is er een fout code opgenomen in het status bericht. Voor de Log Analytics-agent voor Linux worden status berichten [hier](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)beschreven, samen met algemene informatie over probleem oplossing voor deze VM-extensie.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

- Krijg antwoorden van Azure-experts via [micro soft Q&A](/answers/topics/azure-arc.html).

- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.

- Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.
