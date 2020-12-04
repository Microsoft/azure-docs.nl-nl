---
title: Bewerkingen, gebeurtenissen en prestatie meter items bewaken voor open bare basis Load Balancer
titleSuffix: Azure Load Balancer
description: Meer informatie over het inschakelen van logboek registratie voor de Azure Load Balancer
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572776"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Azure Monitor logboeken voor de standaard Azure Load Balancer

U kunt verschillende typen logboeken in azure gebruiken om standaard load balancers te beheren en problemen op te lossen. Logboeken kunnen worden gestreamd naar een Event Hub-of Log Analytics-werk ruimte. Alle logboeken kunnen worden geëxtraheerd uit Azure Blob-opslag en worden weer gegeven in verschillende hulpprogram ma's, zoals Excel en Power BI.  In de onderstaande lijst vindt u meer informatie over de verschillende typen logboeken.

* **Activiteiten logboeken:** U kunt [activiteiten logboeken weer geven gebruiken om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md) om alle activiteiten weer te geven die worden verzonden naar uw Azure-abonnement (en) en hun status. Activiteiten logboeken zijn standaard ingeschakeld en kunnen worden weer gegeven in de Azure Portal. Deze logboeken zijn beschikbaar voor zowel de basis als de standaard load balancers.
* **Standard Load Balancer metrische gegevens:** U kunt dit logboek gebruiken om een query uit te voeren op de metrische gegevens die worden geëxporteerd als logboeken voor uw standaard Azure Load Balancer. Deze logboeken zijn alleen beschikbaar voor standaard load balancers.

> [!IMPORTANT]
> **Gebeurtenis logboeken voor de status test en Load Balancer waarschuwingen worden momenteel niet weer gegeven en worden vermeld in de [bekende problemen voor de Azure Load Balancer](whats-new.md#known-issues).** 

> [!IMPORTANT]
> Logboeken zijn alleen beschikbaar voor resources die zijn geïmplementeerd in het Resource Manager-implementatie model. U kunt geen Logboeken gebruiken voor bronnen in het klassieke implementatie model. Zie [Resource Manager-implementatie en klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie over de implementatie modellen.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. Schakel logboek registratie voor gebeurtenis-en status controle in om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via deze logboeken. Gebruik de volgende stappen om logboek registratie in te scha kelen.

Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen load balancer hebt, [maakt u een Load Balancer](./quickstart-load-balancer-standard-public-portal.md) voordat u doorgaat.

1. Klik in de portal op **resource groepen**.
2. Selecteer **\<resource-group-name>** waar uw Load Balancer is.
3. Selecteer uw load balancer.
4. Selecteer **Activity log**  >  **Diagnostische instellingen** voor activiteiten logboek.
5. Selecteer in het deel venster **Diagnostische instellingen** onder **Diagnostische instellingen** de optie **+ Diagnostische instelling toevoegen**.
6. Voer in het deel venster **Diagnostische instellingen** maken **myLBDiagnostics** in het veld **naam** in.
7. Er zijn drie opties voor de **Diagnostische instellingen**.  U kunt een, twee of alle drie kiezen en elk configureren voor uw vereisten:
   * **Archiveren naar een opslag account**
   * **Streamen naar een Event Hub**
   * **Verzenden naar Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Archiveren naar een opslagaccount
    U hebt een opslag account nodig dat al is gemaakt voor dit proces.  Zie [een opslag account maken](../storage/common/storage-account-create.md?tabs=azure-portal) voor meer informatie over het maken van een opslag account.

    1. Schakel het selectie vakje in naast **archiveren naar een opslag account**.
    2. Selecteer **configureren** om het deel venster **een opslag account selecteren** te openen.
    3. Selecteer het **abonnement** waarin uw opslag account is gemaakt in de vervolg keuzelijst.
    4. Selecteer de naam van uw opslag account onder **opslag account** in de vervolg keuzelijst.
    5. Selecteer OK.

    ### <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub
    U hebt een Event Hub nodig dat voor dit proces al is gemaakt.  Als u een Event Hub wilt maken, raadpleegt u [Quick Start: een event hub maken met behulp van Azure Portal](../event-hubs/event-hubs-create.md)

    1. Schakel het selectie vakje **in naast streamen naar een event hub**
    2. Selecteer **configureren** om het deel venster **Event hub selecteren** te openen.
    3. Selecteer het **abonnement** waarin uw event hub is gemaakt in de vervolg keuzelijst.
    4. **Selecteer Event hub naam ruimte** in de vervolg keuzelijst.
    5. **Selecteer Event hub-beleids naam** in de vervolg keuzelijst.
    6. Selecteer OK.

    ### <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics
    U moet al een log Analytics-werk ruimte hebben gemaakt en geconfigureerd voor dit proces.  Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/learn/quick-create-workspace.md) om een log Analytics-werk ruimte te maken.

    1. Schakel het selectie vakje in naast **verzenden naar log Analytics**.
    2. Selecteer het **abonnement** waarin uw log Analytics werk ruimte zich in de vervolg keuzelijst bevindt.
    3. Selecteer de **log Analytics werk ruimte** in de vervolg keuzelijst.


8.  Schakel onder de sectie **metric** in het deel venster **Diagnostische instellingen** het selectie vakje in bij: **AllMetrics**

9. Controleer of alles goed lijkt en klik boven aan het deel venster **Diagnostische instellingen** maken op **Opslaan** .

## <a name="activity-log"></a>Activiteitenlogboek

Het activiteiten logboek wordt standaard gegenereerd. Het kan worden geconfigureerd om te worden geëxporteerd op een abonnements niveau volgens de [instructies in dit artikel](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log). Lees voor meer informatie over deze logboeken de [activiteiten logboeken weer geven om acties op het artikel resources te controleren](../azure-resource-manager/management/view-activity-logs.md) .

### <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U kunt activiteiten logboek gegevens weer geven en analyseren met een van de volgende methoden:

* **Azure-hulpprogram ma's:** Gegevens ophalen uit het activiteiten logboek via Azure PowerShell, de Azure-opdracht regel interface (CLI), de Azure-REST API of de Azure Portal. Stapsgewijze instructies voor elke methode worden beschreven in het artikel [controle bewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Als u nog geen [Power bi](https://powerbi.microsoft.com/pricing) account hebt, kunt u het gratis uitproberen. Met de [integratie van Azure audit logs voor Power bi](https://powerbi.microsoft.com/integrations/azure-audit-logs/)kunt u uw gegevens analyseren met vooraf geconfigureerde Dash boards, of u kunt weer gaven aanpassen aan uw vereisten.

## <a name="metrics-as-logs"></a>Metrische gegevens als Logboeken
Met metrische gegevens voor het vastleggen van de export functionaliteit van Azure Monitor, kunt u uw Load Balancer metrische gegevens exporteren. Deze metrische gegevens genereren een logboek vermelding voor elke enkele minuut steekproef interval.

De metrische gegevens voor het exporteren van het logboek zijn ingeschakeld op een niveau per resource. U kunt deze logboeken inschakelen door te gaan naar de Blade Diagnostische instellingen, te filteren op resource groep en de Load Balancer te selecteren waarvoor u metrische gegevens export wilt inschakelen. Wanneer de pagina Diagnostische instellingen Load Balancer is ingesteld, selecteert u AllMetrics om in aanmerking komende metrische gegevens te exporteren als Logboeken.

Zie de sectie [beperkingen](#limitations) in dit artikel voor metrische export beperkingen.

### <a name="view-and-analyze-metrics-as-logs"></a>Metrische gegevens weer geven en analyseren als Logboeken
Nadat u AllMetrics in de diagnostische instellingen van uw Standard Load Balancer hebt ingeschakeld, worden deze logboeken ingevuld in de tabel AzureMonitor als u een event hub-of Log Analytics-werk ruimte gebruikt. Als u naar opslag exporteert, maakt u verbinding met uw opslag account en haalt u de JSON-logboek vermeldingen op voor gebeurtenis-en Health probe Logboeken. Wanneer u de JSON-bestanden hebt gedownload, kunt u deze converteren naar CSV en weer geven in Excel, Power BI of een ander hulp programma voor gegevens visualisatie. 

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.

## <a name="stream-to-an-event-hub"></a>Streamen naar een Event Hub
Wanneer diagnostische gegevens worden gestreamd naar een Event Hub, kan deze worden gebruikt voor gecentraliseerde logboek analyse in een SIEM-hulp programma van derden met Azure Monitor-integratie. Zie [Azure-bewakings gegevens streamen naar een event hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) voor meer informatie

## <a name="send-to-log-analytics"></a>Verzenden naar Log Analytics
De diagnostische gegevens van resources in azure kunnen rechtstreeks naar een Log Analytics-werk ruimte worden verzonden, waar complexe query's kunnen worden uitgevoerd op basis van de informatie voor probleem oplossing en analyse.  Zie [Azure-resource logboeken verzamelen in log Analytics werk ruimte in azure monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) voor meer informatie.

## <a name="limitations"></a>Beperkingen
Er zijn momenteel de volgende beperkingen bij het gebruik van de metrische gegevens voor de export functie voor load balancers:
* Metrische gegevens worden momenteel weer gegeven met interne namen wanneer deze worden geëxporteerd als Logboeken. u kunt de toewijzing vinden in de onderstaande tabel
* De dimensionaliteit van metrische gegevens is niet behouden. Met metrische gegevens zoals een DipAvailability (Health probe status) kunt u bijvoorbeeld niet splitsen of weer geven op back-end-IP-adres
* Gebruikte SNAT-poorten en toegewezen statistieken voor de SNAT-poort zijn momenteel niet beschikbaar voor exporteren als Logboeken

## <a name="next-steps"></a>Volgende stappen
* [Bekijk de beschik bare metrische gegevens voor uw Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Query's maken en testen na Azure Monitor instructies](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Geef feedback over dit artikel of Load Balancer functionaliteit met behulp van de onderstaande koppelingen
