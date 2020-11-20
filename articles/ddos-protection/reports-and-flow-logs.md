---
title: Azure DDoS Protection standaard rapporten en stroom logboeken
description: Meer informatie over het configureren van rapporten en stroom Logboeken.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 96e22beb9f9b088e698f0db12104f526af2b936b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989450"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Rapporten en stroomlogboeken voor DDoS-aanvalsbeperking configureren 

Azure DDoS Protection Standard biedt uitgebreide aanval en visualisatie met DDoS-aanvals analyses. Klanten die hun virtuele netwerken beschermen tegen DDoS-aanvallen, hebben een gedetailleerde zicht baarheid van het aanvals verkeer en acties die zijn ondernomen om de aanval via rapporten voor aanvallen te beperken & beperkende stroom Logboeken. Uitgebreide telemetrie wordt weer gegeven via Azure Monitor, met inbegrip van gedetailleerde metrische gegevens tijdens de duur van een DDoS-aanval. U kunt waarschuwingen configureren voor een van de Azure Monitor metrische gegevens die door DDoS Protection worden weer gegeven. Logboek registratie kan verder worden geïntegreerd met [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event hubs), OMS Log Analytics en Azure Storage voor geavanceerde analyse via de diagnostische-interface voor Azure monitor.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Rapporten van DDoS-aanvallen weer geven en configureren
> * DDoS-aanvals stroom logboeken weer geven en configureren

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md)maken.

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Rapporten van DDoS-aanvallen weer geven en configureren

Bij het beperken van aanvallen wordt gebruikgemaakt van de netstroom protocol gegevens die worden geaggregeerd voor gedetailleerde informatie over de aanval van uw bron. Telkens wanneer een open bare IP-resource wordt aangevallen, wordt het rapport gegenereerd zodra de oplossing wordt gestart. Er wordt een incrementeel rapport gegenereerd om de 5 minuten en een rapport na de risico beperking voor de hele afkortings periode. Dit is om ervoor te zorgen dat de DDoS-aanval gedurende een langere periode langer duurt, dan kunt u de meest actuele moment opname van het risico rapport elke vijf minuten bekijken en een volledige samen vatting zodra de risico beperking is overschreden. 

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer bij **instellingen** de optie **Diagnostische instellingen**.
4. Selecteer het **abonnement** en de **resource groep** die het open bare IP-adres bevatten dat u wilt registreren.
5. Selecteer **openbaar IP-adres** voor het **bron type** en selecteer vervolgens het specifieke open bare IP-adres waarvoor u metrische gegevens wilt vastleggen.
6. Selecteer **Diagnostische gegevens inschakelen om het DDoSMitigationReports-logboek te verzamelen** en selecteer vervolgens zoveel van de volgende opties als u nodig hebt:

    - **Archiveren naar een opslag account**: gegevens worden naar een Azure Storage-account geschreven. Zie [Archief bron logboeken](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)voor meer informatie over deze optie.
    - **Streamen naar een event hub**: Hiermee kan een logboek ontvanger logboeken ophalen met behulp van een Azure Event hub. Event hubs maken integratie mogelijk met Splunk of andere SIEM-systemen. Zie [bron logboeken streamen naar een event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)voor meer informatie over deze optie.
    - **Verzenden naar log Analytics**: schrijft logboeken naar de Azure Monitor-service. Zie [Logboeken verzamelen voor gebruik in azure monitor-logboeken voor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)meer informatie over deze optie.

De volgende velden zijn zowel het incrementele & na het oplossen van problemen met beperkende rapporten
- Aanvals vectoren
- Verkeers statistieken
- Reden voor verwijderde pakketten
- Betrokken protocollen
- Top 10 van de bron landen of regio's
- Top 10 van de bron Asn's

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>DDoS-aanvals stroom logboeken weer geven en configureren
Met de stroom logboeken voor risico beperking kunt u het verloren verkeer, het doorgestuurde verkeer en andere interessante data Points tijdens een actieve DDoS-aanval in vrijwel real time bekijken. U kunt de constante stroom van deze gegevens opnemen in azure Sentinel of aan uw SIEM-systemen van derden via Event Hub voor een nabije real-time bewaking, mogelijke acties ondernemen en de nood zaak van uw verdedigings activiteiten aanpakken.

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer bij **instellingen** de optie **Diagnostische instellingen**.
4. Selecteer het **abonnement** en de **resource groep** die het open bare IP-adres bevatten dat u wilt registreren.
5. Selecteer **openbaar IP-adres** voor het **bron type** en selecteer vervolgens het specifieke open bare IP-adres waarvoor u metrische gegevens wilt vastleggen.
6. Selecteer **Diagnostische gegevens inschakelen om het DDoSMitigationFlowLogs-logboek te verzamelen** en selecteer vervolgens zoveel van de volgende opties als u nodig hebt:

    - **Archiveren naar een opslag account**: gegevens worden naar een Azure Storage-account geschreven. Zie [Archief bron logboeken](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)voor meer informatie over deze optie.
    - **Streamen naar een event hub**: Hiermee kan een logboek ontvanger logboeken ophalen met behulp van een Azure Event hub. Event hubs maken integratie mogelijk met Splunk of andere SIEM-systemen. Zie [bron logboeken streamen naar een event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)voor meer informatie over deze optie.
    - **Verzenden naar log Analytics**: schrijft logboeken naar de Azure Monitor-service. Zie [Logboeken verzamelen voor gebruik in azure monitor-logboeken voor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)meer informatie over deze optie.

Met deze [sjabloon](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) maakt u een Azure Policy definitie om diagnostische logboek registratie in te scha kelen.

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel Data Connector

U kunt verbinding maken met Azure Sentinel, uw gegevens in werkmappen bekijken en analyseren, aangepaste waarschuwingen maken en deze opnemen in onderzoek processen. Zie [verbinding maken met Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)om verbinding te maken met Azure Sentinel. 

![Azure Sentinel DDoS-connector](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection werkmap

Als u de gegevens van stroom logboeken wilt weer geven in het dash board van Azure Analytics, kunt u het voorbeeld dashboard importeren uit https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Stroom logboeken bevatten de volgende velden: 
- Bron-IP
- Doel-IP
- Bronpoort 
- Doelpoort 
- Protocol type 
- Actie ondernomen tijdens risico beperking

![DDoS Protection werkmap](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

Aanvals analyses werken alleen als DDoS Protection standaard is ingeschakeld op het virtuele netwerk van het open bare IP-adres. 

## <a name="sample-log-outputs"></a>Voorbeeld logboek uitvoer

De volgende scherm afbeeldingen zijn voor beelden van logboek uitvoer:

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS Protection DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS Protection DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS Protection DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

- Rapporten van DDoS-aanvallen weer geven en configureren
- DDoS-aanvals stroom logboeken weer geven en configureren

Voor informatie over het testen en simuleren van een DDoS-aanval raadpleegt u de hand leiding voor simulatie tests:

> [!div class="nextstepaction"]
> [Testen via simulaties](test-through-simulations.md)