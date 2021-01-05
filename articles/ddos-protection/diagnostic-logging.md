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
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: dd350cc5fa0c3b30b4f0d57938348a8328af311a
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827390"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Diagnostische logboek registratie voor DDoS weer geven en configureren

Azure DDoS Protection Standard biedt uitgebreide aanval en visualisatie met DDoS-aanvals analyses. Klanten die hun virtuele netwerken beschermen tegen DDoS-aanvallen, hebben een gedetailleerde zicht baarheid van het aanvals verkeer en acties die zijn ondernomen om de aanval via rapporten voor aanvallen te beperken & beperkende stroom Logboeken. Uitgebreide telemetrie wordt weer gegeven via Azure Monitor, met inbegrip van gedetailleerde metrische gegevens tijdens de duur van een DDoS-aanval. U kunt waarschuwingen configureren voor een van de Azure Monitor metrische gegevens die door DDoS Protection worden weer gegeven. Logboek registratie kan verder worden geïntegreerd met [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event hubs), OMS Log Analytics en Azure Storage voor geavanceerde analyse via de diagnostische-interface voor Azure monitor.

De volgende Diagnostische logboeken zijn beschikbaar voor Azure DDoS Protection Standard: 

- **DDoSProtectionNotifications**: meldingen ontvangen u telkens wanneer een open bare IP-resource wordt aangevallen en wanneer de aanval wordt verkleind.
- **DDoSMitigationFlowLogs**: met de stroom logboeken voor aanvallen kunt u het verloren verkeer, het doorgestuurde verkeer en andere interessante data Points tijdens een actieve DDoS-aanval in vrijwel real time bekijken. U kunt de constante stroom van deze gegevens opnemen in azure Sentinel of aan uw SIEM-systemen van derden via Event Hub voor een nabije real-time bewaking, mogelijke acties ondernemen en de nood zaak van uw verdedigings activiteiten aanpakken.
- **DDoSMitigationReports**: rapporten voor risico beperking van aanvallen gebruiken de netstroom protocol gegevens die worden geaggregeerd voor gedetailleerde informatie over de aanval van uw bron. Telkens wanneer een open bare IP-resource wordt aangevallen, wordt het rapport gegenereerd zodra de oplossing wordt gestart. Er wordt een incrementeel rapport gegenereerd om de 5 minuten en een rapport na de risico beperking voor de hele afkortings periode. Dit is om ervoor te zorgen dat de DDoS-aanval gedurende een langere periode langer duurt, dan kunt u de meest actuele moment opname van het risico rapport elke vijf minuten bekijken en een volledige samen vatting zodra de risico beperking is overschreden. 
- **AllMetrics**: biedt alle mogelijke metrische gegevens die beschikbaar zijn tijdens de duur van een DDoS-aanval. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DDoS Diagnostische logboeken configureren, inclusief meldingen, risico rapporten en beperkende stroom Logboeken. 
> * Diagnostische logboek registratie inschakelen voor alle open bare IP-adressen in een gedefinieerd bereik.
> * Logboek gegevens weer geven in werkmappen.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md) maken en moet DDoS Protection standaard zijn ingeschakeld op een virtueel netwerk.
- DDoS bewaakt open bare IP-adressen die zijn toegewezen aan bronnen in een virtueel netwerk. Als u geen resources met open bare IP-adressen in het virtuele netwerk hebt, moet u eerst een resource met een openbaar IP-adres maken. U kunt het open bare IP-adres van alle resources die zijn geïmplementeerd via Resource Manager (niet klassiek) bewaken in het [virtuele netwerk voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inclusief Azure load balancers waarbij de virtuele machines in het virtuele netwerk zich bevinden), met uitzonde ring van Azure app service omgevingen en Azure VPN gateway. Als u wilt door gaan met deze zelf studie, kunt u snel een virtuele [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine maken.    

## <a name="configure-ddos-diagnostic-logs"></a>Diagnostische logboeken voor DDoS configureren

Als u diagnostische logboek registratie automatisch wilt inschakelen voor alle open bare IP-adressen binnen een omgeving, gaat u verder met het [inschakelen van diagnostische logboek registratie voor alle open bare ip's](#enable-diagnostic-logging-on-all-public-ips).

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer bij **instellingen** de optie **Diagnostische instellingen**.
4. Selecteer het **abonnement** en de **resource groep** die het open bare IP-adres bevatten dat u wilt registreren.
5. Selecteer **openbaar IP-adres** voor het **bron type** en selecteer vervolgens het specifieke open bare IP-adres waarvoor u logboeken wilt inschakelen.
6. Selecteer **Diagnostische instellingen toevoegen**. Selecteer onder **categorie Details** zo veel van de volgende opties die u nodig hebt en selecteer vervolgens **Opslaan**.

    ![Diagnostische instellingen voor DDoS](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. Selecteer onder **doel Details** zo veel van de volgende opties als u nodig hebt:

    - **Archiveren naar een opslag account**: gegevens worden naar een Azure Storage-account geschreven. Zie [Archief bron logboeken](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)voor meer informatie over deze optie.
    - **Streamen naar een event hub**: Hiermee kan een logboek ontvanger logboeken ophalen met behulp van een Azure Event hub. Event hubs maken integratie mogelijk met Splunk of andere SIEM-systemen. Zie [bron logboeken streamen naar een event hub](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)voor meer informatie over deze optie.
    - **Verzenden naar log Analytics**: schrijft logboeken naar de Azure Monitor-service. Zie [Logboeken verzamelen voor gebruik in azure monitor-logboeken voor](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)meer informatie over deze optie.

### <a name="log-schemas"></a>Logboek schema's

De volgende tabel bevat de namen en beschrijvingen van velden:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Veldnaam | Beschrijving |
| --- | --- |
| **TimeGenerated** | De datum en tijd in UTC waarop de melding is gemaakt. |
| **ResourceId** | De resource-ID van uw open bare IP-adres. |
| **Categorie** | Voor meldingen is dit `DDoSProtectionNotifications` .|
| **ResourceGroup** | De resource groep met uw open bare IP-adres en virtuele netwerk. |
| **Abonnements** | De abonnements-ID van uw DDoS-beschermings plan. |
| **Resource** | De naam van uw open bare IP-adres. |
| **Resource** | Dit is altijd `PUBLICIPADDRESS` . |
| **OperationName** | Voor meldingen is dit `DDoSProtectionNotifications` .  |
| **Bericht** | Details van de aanval. |
| **Type** | Type melding. Mogelijke waarden zijn `MitigationStarted` . `MitigationStopped`. |
| **PublicIPAddress** | Uw open bare IP-adres. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Veldnaam | Beschrijving |
| --- | --- |
| **TimeGenerated** | De datum en tijd in UTC waarop het stroom logboek is gemaakt. |
| **ResourceId** | De resource-ID van uw open bare IP-adres. |
| **Categorie** | Voor stroom Logboeken is dit `DDoSMitigationFlowLogs` .|
| **ResourceGroup** | De resource groep met uw open bare IP-adres en virtuele netwerk. |
| **Abonnements** | De abonnements-ID van uw DDoS-beschermings plan. |
| **Resource** | De naam van uw open bare IP-adres. |
| **Resource** | Dit is altijd `PUBLICIPADDRESS` . |
| **OperationName** | Voor stroom Logboeken is dit `DDoSMitigationFlowLogs` . |
| **Bericht** | Details van de aanval. |
| **SourcePublicIpAddress** | Het open bare IP-adres van de client die verkeer naar uw open bare IP-adres genereert. |
| **SourcePort** | Poort nummer tussen 0 en 65535. |
| **DestPublicIpAddress** | Uw open bare IP-adres. |
| **DestPort** | Poort nummer tussen 0 en 65535. |
| **Protocol** | Type protocol. Mogelijke waarden zijn `tcp` , `udp` ,, `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Veldnaam | Beschrijving |
| --- | --- |
| **TimeGenerated** | De datum en tijd in UTC waarop het rapport is gemaakt. |
| **ResourceId** | De resource-ID van uw open bare IP-adres. |
| **Categorie** | Voor meldingen is dit `DDoSProtectionNotifications` .|
| **ResourceGroup** | De resource groep met uw open bare IP-adres en virtuele netwerk. |
| **Abonnements** | De abonnements-ID van uw DDoS-beschermings plan. |
| **Resource** | De naam van uw open bare IP-adres. |
| **Resource** | Dit is altijd `PUBLICIPADDRESS` . |
| **OperationName** | Voor risico beperkings rapporten is dit `DDoSMitigationReports` . |
| **Report type** | Mogelijke waarden zijn `Incremental` : `PostMitigation` .|
| **MitigationPeriodStart** | De datum en tijd in UTC waarop de beperking is gestart.  |
| **MitigationPeriodEnd** | De datum en tijd in UTC waarop de risico beperking is geëindigd. |
| **IPAddress** | Uw open bare IP-adres. |
| **AttackVectors** |  Uitsplitsing van de typen aanvallen. Sleutels zijn onder andere `TCP SYN flood` ,, `TCP flood` ,, `UDP flood` `UDP reflection` `Other packet flood` .|
| **TrafficOverview** |  Uitsplitsing van het aanvals verkeer. Sleutels zijn `Total packets` , `Total packets dropped` ,,,,, `Total TCP packets` `Total TCP packets dropped` `Total UDP packets` `Total UDP packets dropped` `Total Other packets` , `Total Other packets dropped` . |
| **Protocollen** | Uitsplitsing van de betrokken protocollen. Sleutels zijn `TCP` , `UDP` ,, `Other` . |
| **DropReasons** | Uitsplitsing van de redenen voor verwijderde pakketten. Sleutels zijn `Protocol violation invalid TCP syn` ,,,,,,, `Protocol violation invalid TCP` `Protocol violation invalid UDP` `UDP reflection` `TCP rate limit exceeded` `UDP rate limit exceeded` `Destination limit exceeded` `Other packet flood` , `Rate limit exceeded` , `Packet was forwarded to service` . |
| **TopSourceCountries** | Uitsplitsing van de Top 10 van bron landen van binnenkomend verkeer. |
| **TopSourceCountriesForDroppedPackets** | Uitsplitsing van de Top 10 van de bron landen van het aanvals verkeer dat is/werd verholpen. |
| **TopSourceASNs** | Uitsplitsing van de Top 10 van bron-autonome systeem nummers (ASN) van het binnenkomende verkeer.  |
| **SourceContinents** | Uitsplitsing van de bron-continenten van binnenkomend verkeer. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Diagnostische logboek registratie inschakelen voor alle open bare Ip's

Met deze [sjabloon](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) maakt u een definitie van een Azure Policy om automatisch diagnostische logboek registratie in te scha kelen voor alle open bare IP-Logboeken in een gedefinieerd bereik.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Logboek gegevens in werkmappen weer geven

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel Data Connector

U kunt Logboeken verbinden met Azure Sentinel, uw gegevens in werkmappen bekijken en analyseren, aangepaste waarschuwingen maken en deze opnemen in onderzoek processen. Zie [verbinding maken met Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)om verbinding te maken met Azure Sentinel. 

![Azure Sentinel DDoS-connector](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection werkmap

U kunt deze Azure Resource Manager (ARM)-sjabloon gebruiken voor het implementeren van een aanvals Analytics-werkmap. Met deze werkmap kunt u aanvals gegevens op verschillende deel Vensters visualiseren om eenvoudig inzicht te krijgen in wat het aandeel is. 

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS Protection werkmap](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Valideren en testen

Zie [DDoS Detection valideren](test-through-simulations.md)als u een DDoS-aanval wilt simuleren om uw logboeken te valideren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

- DDoS Diagnostische logboeken configureren, inclusief meldingen, risico rapporten en beperkende stroom Logboeken. 
- Diagnostische logboek registratie inschakelen voor alle open bare IP-adressen in een gedefinieerd bereik.
- Logboek gegevens weer geven in werkmappen.

Ga door naar de volgende zelf studie voor meer informatie over het configureren van aanvals waarschuwingen.

> [!div class="nextstepaction"]
> [Waarschuwingen voor DDoS-beveiliging weer geven en configureren](alerts.md)
