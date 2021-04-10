---
title: Azure Monitor-logboeken met een app met meerdere tenants
description: Azure Monitor-logboeken instellen en gebruiken met een SaaS-app met meerdere tenants die gebruikmaakt van Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 029e3ba799e5f239bde0ef049316dd268ebe4c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588782"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Azure Monitor-logboeken instellen en gebruiken met een SaaS-app met meerdere tenants die gebruikmaakt van Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie configureert en gebruikt u [Azure Monitor-logboeken](../../azure-monitor/logs/log-query-overview.md) om elastische pools en databases te controleren. In deze zelfstudie wordt voortgebouwd op de [zelfstudie Prestatiecontrole en -beheer](saas-dbpertenant-performance-monitoring.md). Hierin wordt beschreven hoe u Azure Monitor-logboeken gebruikt als uitbreiding op de controle- en waarschuwingsfuncties van Azure Portal. Azure Monitor-logboeken bieden ondersteuning voor het controleren van duizenden elastische pools en honderdduizenden databases. Met Azure Monitor-logboeken beschikt u over één controleoplossing die kan worden geïntegreerd met controlefuncties van andere apps en Azure-services in meerdere Azure-abonnementen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Monitor-logboeken installeren en configureren.
> * Azure Monitor-logboeken gebruiken om pools en databases te controleren.

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets-SaaS-database-per-tenant-app is geïmplementeerd. Zie [De SaaS-app Wingtip Tickets met een database per tenant implementeren en verkennen](./saas-dbpertenant-get-started-deploy.md) om de app in minder dan vijf minuten te implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.

Zie de [zelfstudie Prestatiecontrole en -beheer](saas-dbpertenant-performance-monitoring.md) voor een beschrijving van SaaS-scenario's en -patronen en de wijze waarop deze van invloed zijn op de vereisten voor een controleoplossing.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>De prestaties van databases en elastische pools controleren en beheren met Azure Monitor-logboeken

Voor Azure SQL Database zijn controle en waarschuwingen beschikbaar voor databases en pools in Azure Portal. Deze ingebouwde controles en waarschuwingen zijn handig, maar ze zijn ook afgestemd op specifieke resources. Dit betekent dat het minder geschikt is voor het controleren van grote installaties of voor het geven van een algemeen overzicht voor resources en abonnementen.

In scenario's met grote volumes kunt u Azure Monitor-logboeken gebruiken voor controles en waarschuwingen. Azure Monitor is een afzonderlijke Azure-service waarmee u analyses kunt uitvoeren van logboeken die zijn verzameld in een werkruimte van mogelijk veel services. Azure Monitor-logboeken beschikken over een ingebouwde querytaal en hulpprogramma's voor gegevensvisualisatie waarmee analyses van operationele gegevens kunnen worden uitgevoerd. De oplossing SQL Analytics biedt verschillende vooraf gedefinieerde query's en weergaven voor controle en waarschuwingen voor elastische pools en databases. Azure Monitor-logboeken bieden ook een functie voor het ontwerpen van aangepaste weergaven.

OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten. Log Analytics-werkruimten en analyseoplossingen worden geopend in Azure Portal. Azure Portal is het recentere toegangspunt, maar in sommige gebieden kan het zich mogelijk achter de Operations Management Suite-portal bevinden.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Diagnostische gegevens over prestaties maken door een workload in uw tenants te simuleren 

1. Open in PowerShell ISE *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Leermodules\\Prestatiecontrole en -beheer\\Demo-PerformanceMonitoringAndManagement.ps1*. Laat dit script geopend omdat u mogelijk verschillende keren een belasting wilt genereren tijdens deze zelfstudie.
1. Richt een batch met tenants in, als u dit nog niet hebt gedaan, om de controlecontext interessanter te maken. Dit duurt enkele minuten.

   a. Stel het volgende in: **$DemoScenario = 1**, _Een batch met tenants inrichten_.

   b. Druk op F5 om het script uit te voeren en 17 extra tenants te implementeren.

1. Start nu de belastinggenerator om een gesimuleerde belasting uit te voeren in alle tenants.

    a. Stel het volgende in: **$DemoScenario = 2**, _Belasting met normale intensiteit genereren (ongeveer 30 DTU)_ .

    b. Druk op F5 om het script uit te voeren.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip Tickets-SaaS-database-per-tenant-toepassingsscripts ophalen

De databasescripts en app-broncode van de SaaS-app met meerdere tenants Wingtip Tickets vindt u in de GitHub-opslagplaats [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Raadpleeg de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen voor het downloaden en deblokkeren van de PowerShell-scripts voor Wingtip Tickets.

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>De Log Analytics-werkruimte en de oplossing Azure SQL-analyse installeren en configureren

Azure Monitor is een afzonderlijke service die moet worden geconfigureerd. Met Azure Monitor-logboeken worden logboekgegevens, telemetriegegevens en metrische gegevens verzameld in een Log Analytics-werkruimte. Net als andere resources in Azure moet een Log Analytics-werkruimte worden gemaakt. De werkruimte hoeft niet in dezelfde resourcegroep te worden gemaakt als de app die wordt gecontroleerd. Dit is vaak wel het handigst. Gebruik voor de Wingtip Tickets-app één resourcegroep om ervoor te zorgen dat de werkruimte met de app wordt verwijderd.

1. Open in PowerShell ISE *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Leermodules\\Prestatiecontrole en -beheer\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Druk op F5 om het script uit te voeren.

U kunt nu Azure Monitor-logboeken openen in Azure Portal. Het duurt enkele minuten om telemetriegegevens te verzamelen in de Log Analytics-werkruimte en om deze weer te geven. Hoe langer u het systeem diagnostische gegevens laat verzamelen, hoe interessanter het wordt. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Een Log Analytics-werkruimte en de oplossing SQL Analytics gebruiken om pools en databases te controleren


In deze oefening opent u de Log Analytics-werkruimte in Azure Portal om de telemetriegegevens te bekijken die worden verzameld voor de databases en pools.

1. Blader naar [Azure Portal](https://portal.azure.com). Selecteer **Alle services** om de Log Analytics-werkruimte te openen. Ga vervolgens naar Log Analytics.

   ![De Log Analytics-werkruimte openen](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecteer de werkruimte met de naam _wtploganalytics-&lt;user&gt;_ .

1. Selecteer **Overzicht** om de Log Analytics-oplossing te openen in Azure Portal.

   ![Overzicht](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Het kan enkele minuten duren voordat de oplossing actief is. 

1. Selecteer de tegel **Azure SQL-analyse** om de oplossing te openen.

    ![De tegel Overzicht](./media/saas-dbpertenant-log-analytics/overview.png)

1. U scrolt zijwaarts door de weergaven in de oplossing en de weergaven hebben onderaan een eigen interne schuifbalk. Vernieuw zo nodig de pagina.

1. Als u de overzichtspagina wilt verkennen, selecteert u de tegels of afzonderlijke databases om een verkenner te openen waarin u kunt inzoomen.

    ![Log Analytics-dashboard](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Wijzig de filterinstelling om het tijdsbereik aan te passen. Voor deze zelfstudie selecteert u **Afgelopen uur**.

    ![Tijdfilter](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecteer een afzonderlijke database om het querygebruik en de metrische gegevens voor die database te bekijken.

    ![Databaseanalyse](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Als u de metrische gegevens over het gebruik wilt bekijken, schuift u door de analysepagina aan de rechterkant.
 
     ![Metrische gegevens voor de database](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Schuif door de analysepagina aan de linkerkant en selecteer de servertegel in de lijst **Resourcegegevens**.  

    ![Lijst met resourcegegevens](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Er wordt een pagina geopend met de pools en databases op de server.

    ![Server met pools en databases](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecteer een pool. Schuif op de poolpagina die wordt geopend naar rechts om de metrische gegevens van de pool te bekijken. 

    ![Metrische gegevens van de pool](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Ga terug naar de Log Analytics-werkruimte en selecteer **OMS-portal** om de werkruimte daar te openen.

    ![Log Analytics-werkruimte](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

In de Log Analytics-werkruimte kunt u het logboek en de metrische gegevens nader verkennen. 

De controle en waarschuwingen in Azure Monitor-logboeken zijn gebaseerd op query's die worden uitgevoerd op de gegevens in de werkruimte, in tegenstelling tot de waarschuwingen die zijn gedefinieerd voor elke resource in Azure Portal. Door waarschuwingen te baseren op query's kunt u één waarschuwing definiëren die betrekking heeft op alle databases en hoeft u er niet een voor elke databases te definiëren. De uitvoering van query's wordt alleen beperkt door de gegevens die beschikbaar zijn in de werkruimte.

Zie [Werken met waarschuwingsregels in Azure Monitor-logboeken](../../azure-monitor/alerts/alerts-metric.md) voor meer informatie over het gebruik van Azure Monitor-logboeken bij het uitvoeren van query's en instellen van waarschuwingen.

De kosten die in rekening worden gebracht voor Azure Monitor-logboeken voor Azure SQL Database zijn gebaseerd op het gegevensvolume in de werkruimte. In deze zelfstudie hebt u een gratis werkruimte gemaakt, die beperkt is tot 500 MB per dag. Wanneer deze limiet is bereikt, worden er geen gegevens meer toegevoegd aan de werkruimte.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Azure Monitor-logboeken installeren en configureren.
> * Azure Monitor-logboeken gebruiken om pools en databases te controleren.

Lees de [zelfstudie Tenant-analyse](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelfstudies als vervolg op de eerste implementatie van de SaaS-app met een database per tenant Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-logboeken](../../azure-monitor/insights/azure-sql.md)