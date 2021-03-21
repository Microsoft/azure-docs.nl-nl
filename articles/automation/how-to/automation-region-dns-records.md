---
title: DNS-records voor Azure-data centers die worden gebruikt door Azure Automation | Microsoft Docs
description: In dit artikel vindt u de DNS-records die worden vereist door Azure Automation-functies bij het beperken van de communicatie met een specifieke Azure-regio die als host fungeert voor het Automation-account.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: 8630afa7410aad81a7a3c61540fc74702fc6481c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575993"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>DNS-records voor Azure-regio's die worden gebruikt door Azure Automation

De [Azure Automation](../automation-intro.md) -service gebruikt een aantal DNS-records voor functies om verbinding te maken met de service. Als u een Automation-account hebt dat is gedefinieerd voor een specifieke regio, kunt u de communicatie beperken tot dat regionale Data Center. Mogelijk moet u deze records weten zodat de volgende automatiserings functies kunnen worden gebruikt wanneer deze wordt gehost achter een firewall:

* Hybrid Runbook Worker
* State Configuration
* Webhooks

>[!NOTE]
>De registratie van Linux-Hybrid Runbook Worker mislukt met de nieuwe records, tenzij dit versie 1.6.10.2 of hoger is. U moet een upgrade uitvoeren naar een nieuwere versie van de [log Analytics-agent voor Linux](../../azure-monitor/agents/agent-linux.md) zodat de machine een bijgewerkte versie van de werknemersrol kan ontvangen en deze nieuwe records kan gebruiken. Bestaande machines blijven werken zonder dat er problemen zijn.  

## <a name="dns-records-per-region"></a>DNS-records per regio

De volgende tabel bevat de DNS-record voor elke regio.

>[!NOTE]
>De lijst met DNS-records voor automatisering die hier worden opgegeven, blijft actief, zodat u de tijd hebt om te migreren naar de nieuwe records die worden weer gegeven onder [ondersteuning voor privé koppeling](#support-for-private-link) en fouten met uw automatiserings processen te voor komen.

| **Regio** | **DNS-record** |
| --- | --- |
| Australië - centraal |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australië - oost |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australië - zuidoost |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canada - midden |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| India - centraal |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| VS - oost 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japan - oost |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa - noord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| VS - zuid-centraal |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Azië - zuidoost |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Verenigd Koninkrijk Zuid | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| VS (overheid) - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| VS - west-centraal | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa -west |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| VS - west 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Ondersteuning voor privé-koppeling

Ter ondersteuning van een [persoonlijke koppeling](../../private-link/private-link-overview.md) in azure Automation worden de DNS-records voor elk ondersteund Data Center bijgewerkt. In plaats van landspecifieke Url's zijn de Url's specifiek voor het Automation-account.

| **Regio** | **DNS-record** |
| --- | --- |
| VS - west-centraal |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| VS - west |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| VS - west 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| VS - centraal |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| VS - zuid-centraal |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| VS - noord-centraal |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| VS - oost |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| VS - oost 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Canada - midden |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| Europa -west |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Europa - noord |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Azië - zuidoost |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Azië - oost |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| India - centraal |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Japan - oost |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Korea - centraal |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Australië - zuidoost |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Australië - oost |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Australië - centraal |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Verenigd Koninkrijk Zuid |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Frankrijk - centraal |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Zuid-Afrika - noord |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Brazilië - zuid |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| China - noord |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| China - noord 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| China - oost 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| VS (overheid) - Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| VS (overheid) - Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| VS (overheid) - Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Vervang `<accountId>` in de DNS-record met de GUID die uw Automation-account-id van de waarde- **URL** vertegenwoordigt. U kunt de ID die is vereist voor **sleutels** ophalen onder **account instellingen** in de Azure Portal.

![Pagina primaire sleutel Automation-account](./media/automation-region-dns-records/automation-account-keys.png)

Kopieer de waarde na *accounts/* van het **URL** -veld- `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Alle DNS-records voor de webhook en agentservice zijn bijgewerkt naar de nieuwe DNS-records met stijl voor de ondersteuning van een persoonlijke koppeling. Voor JRDS DNS-records worden zowel oude als nieuwe stijl-DNS-records ondersteund. Als u geen persoonlijke koppeling gebruikt, ziet u de oude DNS-records voor de stijl, terwijl deze met behulp van een persoonlijke koppeling nieuwe stijl van DNS-records worden weer geven.

U wordt aangeraden de adressen te gebruiken die worden weer gegeven bij het definiëren van [uitzonde ringen](../automation-runbook-execution.md#exceptions). Voor een lijst met IP-adressen van regio's in plaats van regio namen kunt u het JSON-bestand downloaden van het micro soft Download centrum voor de volgende Cloud omgevingen:

* [IP-adresbereiken en service tags van Azure-open bare Azure](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP-bereiken en-service Tags-Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [IP-adresbereiken en service tags van Azure-Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP-bereiken en-service Tags – Azure China ViaNet 21](https://www.microsoft.com/download/details.aspx?id=57062)

Het IP-adres bestand bevat de IP-adresbereiken die worden gebruikt in de Microsoft Azure data centers. Het omvat compute-, SQL-en opslag bereiken en weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weer gegeven, worden gedurende ten minste één week niet gebruikt in de data centers.

Het is een goed idee om elke week het nieuwe IP-adres bestand te downloaden. Werk vervolgens uw site bij om de services die in Azure worden uitgevoerd, correct te identificeren.

> [!NOTE]
> Als u Azure ExpressRoute gebruikt, moet u er rekening mee houden dat het IP-adres bestand wordt gebruikt voor het bijwerken van de Border Gateway Protocol (BGP) advertisement van Azure Space in de eerste week van elke maand.

## <a name="next-steps"></a>Volgende stappen

* Zie [problemen met Hybrid Runbook worker oplossen](../troubleshoot/hybrid-runbook-worker.md#general)voor meer informatie over het oplossen van problemen met uw Hybrid Runbook Workers.

* Zie problemen met de [status configuratie oplossen](../troubleshoot/desired-state-configuration.md)voor meer informatie over het oplossen van problemen met de status configuratie.