---
title: Problemen met VM Insights oplossen
description: Problemen met de installatie van VM Insights oplossen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555413"
---
# <a name="troubleshoot-vm-insights"></a>Problemen met VM Insights oplossen
In dit artikel vindt u informatie over het oplossen van problemen met het inschakelen of gebruiken van VM Insights.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Kan geen VM Insights op een computer inschakelen
Wanneer u een virtuele machine van Azure uitschakelt vanuit het Azure Portal, gebeurt het volgende:

- Er wordt een standaard Log Analytics werkruimte gemaakt als die optie is geselecteerd.
- De Log Analytics-agent wordt op virtuele machines van Azure geïnstalleerd met behulp van een VM-extensie als de agent al is geïnstalleerd.
- De afhankelijkheids agent wordt op virtuele machines van Azure geïnstalleerd met behulp van een uitbrei ding, indien deze vereist is.
  
Tijdens het voorbereidings proces wordt elk van deze stappen gecontroleerd en een meldings status weer gegeven in de portal. De configuratie van de werk ruimte en de installatie van de agent duurt doorgaans 5 tot 10 minuten. Het duurt nog vijf tot tien minuten voordat de gegevens beschikbaar zijn om te worden weer gegeven in de portal.

Als u een bericht ontvangt dat de virtuele machine moet worden ingepakt nadat u het voorbereidings proces hebt uitgevoerd, moet u Maxi maal 30 minuten wachten totdat het proces is voltooid. Als het probleem zich blijft voordoen, raadpleegt u de volgende secties voor mogelijke oorzaken.

### <a name="is-the-virtual-machine-running"></a>Wordt de virtuele machine uitgevoerd?
 Als de virtuele machine al een tijdje is uitgeschakeld, is uitgeschakeld of alleen onlangs is ingeschakeld, zijn er geen gegevens om weer te geven voor een beetje totdat de gegevens binnenkomen.

### <a name="is-the-operating-system-supported"></a>Wordt het besturings systeem ondersteund?
Als het besturings systeem niet voor komt in de lijst met [ondersteunde besturings systemen](vminsights-enable-overview.md#supported-operating-systems) , zal de uitbrei ding niet worden geïnstalleerd en ziet u dit bericht dat er wordt gewacht tot er gegevens worden ontvangen.

### <a name="did-the-extension-install-properly"></a>Is de uitbrei ding op de juiste wijze geïnstalleerd?
Als u nog steeds een bericht ziet dat de virtuele machine onboarding moet worden uitgevoerd, kan dit betekenen dat een of beide uitbrei dingen niet goed konden worden geïnstalleerd. Controleer op de pagina **extensies** voor uw virtuele machine in de Azure Portal om te controleren of de volgende extensies worden weer gegeven.

| Besturingssysteem | Agents | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Micro soft. Azure. monitoring. DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Als de beide uitbrei dingen voor uw besturings systeem niet worden weer geven in de lijst met geïnstalleerde uitbrei dingen, moeten ze worden geïnstalleerd. Als de uitbrei dingen worden weer gegeven, maar de status niet wordt weer gegeven wanneer de *inrichting is geslaagd*, moet de extensie worden verwijderd en opnieuw worden geïnstalleerd.

### <a name="do-you-have-connectivity-issues"></a>Hebt u verbindings problemen?
Voor Windows-computers kunt u het  *TestCloudConnectivity* -hulp programma gebruiken om het connectiviteits probleem te identificeren. Dit hulp programma wordt standaard geïnstalleerd met de agent in de map *%systemroot%\Program Files\Microsoft monitoring Agent\Agent*. Voer het hulp programma uit vanaf een opdracht prompt met verhoogde bevoegdheden. Er worden resultaten geretourneerd en er wordt gemarkeerd waar de test mislukt. 

![Hulp programma TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Meer problemen oplossen met agents

Raadpleeg de volgende artikelen voor het oplossen van problemen met de Log Analytics-agent:

- [Problemen met de Log Analytics-agent voor Windows oplossen](../agents/agent-windows-troubleshoot.md)
- [Problemen met de Log Analytics-agent voor Linux oplossen](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>Prestatie weergave bevat geen gegevens
Als de agents correct worden geïnstalleerd, maar u geen gegevens ziet in de weer gave prestaties, raadpleegt u de volgende secties voor mogelijke oorzaken.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Is de gegevens limiet bereikt voor uw Log Analytics-werk ruimte?
Controleer de [capaciteits reserveringen en de prijzen voor gegevens opname](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Verzendt de virtuele machine logboek-en prestatie gegevens naar Azure Monitor logboeken?

Open Log Analytics van **Logboeken** in het Azure monitor menu van de Azure Portal. Voer de volgende query uit voor uw computer:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Als u geen gegevens ziet, hebt u mogelijk problemen met uw agent. Zie de sectie hierboven voor informatie over het oplossen van problemen met agents.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>De virtuele machine wordt niet weer gegeven in de kaart weergave

### <a name="is-the-dependency-agent-installed"></a>Is de afhankelijkheids agent geïnstalleerd?
 Gebruik de informatie in de bovenstaande secties om te bepalen of de afhankelijkheids agent is geïnstalleerd en goed werkt.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Bevindt u zich op de laag Log Analytics gratis?
De [log Analytics gratis laag](https://azure.microsoft.com/pricing/details/monitor/) dit is een verouderd prijs plan dat Maxi maal vijf unieke servicetoewijzing machines toestaat. Alle volgende computers worden niet weergegeven in het serviceoverzicht, zelfs niet als de voorgaande vijf geen gegevens meer verzenden.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Verzendt de virtuele machine logboek-en prestatie gegevens naar Azure Monitor logboeken?
Gebruik de sectie logboek query in de [prestatie weergave bevat geen gegevens](#performance-view-has-no-data) om te bepalen of gegevens worden verzameld voor de virtuele machine. Als er geen gegevens worden verzameld, gebruikt u het TestCloudConnectivity-hulp programma dat hierboven wordt beschreven om te bepalen of u verbindings problemen hebt.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>De virtuele machine wordt weer gegeven in de kaart weergave, maar er ontbreken gegevens
Als de virtuele machine zich in de kaart weergave bevindt, wordt de afhankelijkheids agent geïnstalleerd en uitgevoerd, maar het kernel-stuur programma is niet geladen. Controleer het logboek bestand op de volgende locaties:

| Besturingssysteem | Logboek | 
|:---|:---|
| Windows | C:\Program Files\Microsoft dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

De laatste regels van het bestand zouden moeten aangeven waarom de kernel niet is geladen. Het is bijvoorbeeld mogelijk dat de kernel niet wordt ondersteund op Linux als u uw kernel hebt bijgewerkt.
## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van VM Insights inschakelen](vminsights-enable-overview.md)voor meer informatie over het onboarden van VM Insights-agents.
