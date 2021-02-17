---
title: Bewerkingen, gebeurtenissen en tellers voor een open bare load balancer bewaken
titleSuffix: Azure Load Balancer
description: Meer informatie over het inschakelen van logboek registratie voor Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 7a456057bc088264cefb91be9f3e5069b29474a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596803"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure Monitor logboeken voor Azure Standard Load Balancer

U kunt verschillende soorten Azure Monitor Logboeken gebruiken voor het beheren en oplossen van problemen met Azure Standard Load Balancer. Logboeken kunnen worden gestreamd naar een Event Hub-of Log Analytics-werk ruimte. U kunt alle logboeken uit Azure Blob Storage extra heren en weer geven in hulpprogram ma's zoals Excel en Power BI. 

De typen logboeken zijn:

* **Activiteiten logboeken:** U kunt alle activiteiten weer geven die worden verzonden naar uw Azure-abonnementen, samen met hun status. Zie [activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie. Activiteiten logboeken zijn standaard ingeschakeld en kunnen worden weer gegeven in de Azure Portal. Deze logboeken zijn beschikbaar voor zowel Azure Basic-Load Balancer als voor Standard Load Balancer.
* **Standard Load Balancer metrische gegevens:** U kunt dit logboek gebruiken om een query uit te voeren op de metrische gegevens die worden geëxporteerd als logboeken voor Standard Load Balancer. Deze logboeken zijn alleen beschikbaar voor Standard Load Balancer.

> [!IMPORTANT]
> Gebeurtenis logboeken voor de status test en Load Balancer waarschuwingen worden momenteel niet weer gegeven en worden vermeld in de [bekende problemen voor Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> Logboeken zijn alleen beschikbaar voor resources die zijn geïmplementeerd in het Azure Resource Manager-implementatie model. U kunt geen Logboeken gebruiken voor bronnen in het klassieke implementatie model. Zie [Resource Manager-implementatie en klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie over de implementatie modellen.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Schakel logboek registratie voor gebeurtenis-en status controle in om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via deze logboeken. Voer de volgende stappen uit:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen load balancer hebt, [maakt u een Load Balancer](./quickstart-load-balancer-standard-public-portal.md) voordat u doorgaat.
1. Selecteer in de portal **resource groepen**.
2. Selecteer **\<resource-group-name>** waar uw Load Balancer is.
3. Selecteer uw load balancer.
4. Selecteer   >  **Diagnostische instellingen** voor activiteiten logboek.
5. Selecteer in het deel venster **Diagnostische instellingen** onder **Diagnostische instellingen** de optie **+ Diagnostische instelling toevoegen**.
6. Voer in het deel venster **Diagnostische instellingen** maken **myLBDiagnostics** in het vak **naam** in.
7. Er zijn drie opties voor de **Diagnostische instellingen**. U kunt een, twee of alle drie kiezen en elk configureren voor uw vereisten:

   * **Archiveren naar een opslag account**. U hebt een opslag account nodig dat al is gemaakt voor dit proces. Raadpleeg [Een opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal) als u een opslagaccount wilt maken.
     1. Schakel het selectie vakje **archiveren naar een opslag account** in.
     2. Selecteer **configureren** om het deel venster **een opslag account selecteren** te openen.
     3. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waar uw opslag account is gemaakt.
     4. Selecteer in de vervolg keuzelijst **opslag account** de naam van uw opslag account.
     5. Selecteer **OK**.

   * **Streamen naar een event hub**. U hebt een Event Hub nodig dat voor dit proces al is gemaakt. Als u een Event Hub wilt maken, raadpleegt u [Quick Start: een event hub maken met behulp van de Azure Portal](../event-hubs/event-hubs-create.md).
     1. Schakel het selectievakje **Streamen naar een event hub** in.
     2. Selecteer **configureren** om het deel venster **Event hub selecteren** te openen.
     3. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waarin uw event hub is gemaakt.
     4. Selecteer in de vervolg keuzelijst **Event hub naam ruimte selecteren** de naam ruimte.
     5. Selecteer in de vervolg keuzelijst **Event hub beleids naam selecteren** de naam.
     6. Selecteer **OK**.

   * **Verzenden naar log Analytics**. U moet al een log Analytics-werk ruimte hebben gemaakt en geconfigureerd voor dit proces. Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md)om een log Analytics-werk ruimte te maken.
     1. Schakel het selectievakje **Verzenden naar Log Analytics** in.
     2. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waarin uw log Analytics-werk ruimte zich bevindt.
     3. Selecteer de werk ruimte in de vervolg keuzelijst **log Analytics werk ruimte** .

8. Schakel in de sectie **metric** van het deel venster **instellingen voor diagnostische gegevens** het selectie vakje **AllMetrics** in.

9. Controleer of alles er goed uitziet en selecteer vervolgens **Opslaan** boven aan het deel venster **Diagnostische instellingen** maken.

## <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

Het activiteiten logboek wordt standaard gegenereerd. U kunt de sjabloon zo configureren dat deze wordt geëxporteerd op abonnements niveau door [de instructies in dit artikel te volgen](../azure-monitor/platform/activity-log.md). Lees voor meer informatie over deze logboeken de [activiteiten logboeken weer geven om acties op het artikel resources te controleren](../azure-resource-manager/management/view-activity-logs.md) .

U kunt activiteiten logboek gegevens weer geven en analyseren met behulp van een van de volgende methoden:

* **Azure-hulpprogram ma's:** Gegevens ophalen uit het activiteiten logboek via Azure PowerShell, de Azure CLI, de Azure-REST API of de Azure Portal. Het artikel [controle bewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md) bevat stapsgewijze instructies voor elke methode.
* **Power BI:** Als u nog geen [Power bi](https://powerbi.microsoft.com/pricing) account hebt, kunt u het gratis uitproberen. Met de [integratie van Azure audit logs voor Power bi](https://powerbi.microsoft.com/integrations/azure-audit-logs/)kunt u uw gegevens analyseren met vooraf geconfigureerde Dash boards. U kunt ook weer gaven aanpassen aan uw vereisten.

## <a name="view-and-analyze-metrics-as-logs"></a>Metrische gegevens weer geven en analyseren als Logboeken
Door de export functionaliteit in Azure Monitor te gebruiken, kunt u uw Load Balancer metrische gegevens exporteren. Met deze metrische gegevens wordt een logboek vermelding gegenereerd voor elk interval van een steek proef van één minuut.

Het exporteren van metrische gegevens naar Logboeken is ingeschakeld op een niveau per bron. Deze logboeken inschakelen:

1. Ga naar het deel venster **Diagnostische instellingen** .
1. Filter op resource groep en selecteer vervolgens het Load Balancer exemplaar waarvoor u metrische gegevens export wilt inschakelen. 
1. Wanneer de pagina Diagnostische instellingen voor Load Balancer is ingesteld op, selecteert u **AllMetrics** om in aanmerking komende metrische gegevens te exporteren als Logboeken.

Zie de sectie [beperkingen](#limitations) van dit artikel voor de beperkingen voor metrische export.

Nadat u **AllMetrics** in de diagnostische instellingen van Standard Load Balancer hebt ingeschakeld, worden deze logboeken ingevuld in de tabel **AzureMonitor** als u een event hub-of log Analytics-werk ruimte gebruikt. 

Als u naar opslag exporteert, maakt u verbinding met uw opslag account en haalt u de JSON-logboek vermeldingen op voor gebeurtenis-en Health probe Logboeken. Nadat u de JSON-bestanden hebt gedownload, kunt u deze converteren naar CSV en weer geven in Excel, Power BI of een ander hulp programma voor gegevens visualisatie. 

> [!TIP]
> Als u bekend bent met Visual Studio en basis concepten voor het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [hulpprogram ma's voor logboek conversie](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn via github.

## <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub
Wanneer diagnostische gegevens worden gestreamd naar een Event Hub, kunt u deze gebruiken voor gecentraliseerde logboek analyse in een partner SIEM-hulp programma met Azure Monitor-integratie. Zie [Azure-bewakings gegevens streamen naar een event hub](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)voor meer informatie.

## <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics
U kunt Diagnostische gegevens voor resources rechtstreeks naar een Log Analytics-werk ruimte verzenden. In die werk ruimte kunt u complexe query's uitvoeren op basis van de informatie voor probleem oplossing en analyse. Zie [Azure-resource logboeken verzamelen in een log Analytics-werk ruimte in azure monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)voor meer informatie.

## <a name="limitations"></a>Beperkingen
De functie voor het exporteren van metrische gegevens naar Logboeken voor Azure Load Balancer heeft de volgende beperkingen:
* Metrische gegevens worden momenteel weer gegeven via interne namen wanneer ze worden geëxporteerd als Logboeken. U vindt de toewijzing in de onderstaande tabel.
* De dimensionaliteit van metrische gegevens is niet behouden. Met metrische gegevens zoals **DipAvailability** (Health probe status) kunt u bijvoorbeeld niet splitsen of weer geven met een back-end-IP-adres.
* Metrische gegevens voor gebruikte SNAT-poorten en toegewezen SNAT-poorten zijn momenteel niet beschikbaar voor exporteren als Logboeken.

## <a name="next-steps"></a>Volgende stappen
* [Bekijk de beschik bare metrische gegevens voor uw load balancer](./load-balancer-standard-diagnostics.md)
* [Query's maken en testen door Azure Monitor instructies te volgen](../azure-monitor/log-query/log-query-overview.md)