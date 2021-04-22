---
title: Azure Monitor-logboeken
description: Beschrijft Azure Monitor logboeken die worden gebruikt voor geavanceerde analyse van bewakingsgegevens.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 9794c5f048b8795652e4b31e0134b36a77715abe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873375"
---
# <a name="azure-monitor-logs-overview"></a>Overzicht van Azure Monitor-logboeken
Azure Monitor logboeken is een functie van Azure Monitor waarmee logboek- en prestatiegegevens van bewaakte resources worden verzameld [en georganiseerd.](../monitor-reference.md) Gegevens uit verschillende bronnen, zoals [platformlogboeken](../essentials/platform-logs-overview.md) van Azure-services, logboek- en [](../app/app-insights-overview.md) prestatiegegevens van agents voor virtuele [machines,](../agents/agents-overview.md)en gebruiks- en prestatiegegevens van toepassingen kunnen worden geconsolideerd in één werkruimte, zodat ze samen kunnen worden geanalyseerd met behulp van een geavanceerde querytaal waarmee u snel miljoenen records kunt analyseren. U kunt een eenvoudige query uitvoeren die alleen een specifieke set records opvraagt of geavanceerde gegevensanalyse uitvoert om kritieke patronen in uw bewakingsgegevens te identificeren. Werk interactief met logboekquery's en de resultaten ervan met behulp van Log Analytics, gebruik ze in een waarschuwingsregels om proactief op de hoogte te worden gesteld van problemen of visualiseer de resultaten ervan in een werkmap of dashboard.

> [!NOTE]
> Azure Monitor logboeken is de helft van het gegevensplatform dat ondersteuning biedt Azure Monitor. De andere is [Azure Monitor Metrische gegevens](../essentials/data-platform-metrics.md) waarmee numerieke gegevens worden opgeslagen in een tijdreeksdatabase. Dit maakt deze gegevens lichter dan gegevens in Azure Monitor-logboeken en is geschikt voor het ondersteunen van bijna realtime scenario's, waardoor ze bijzonder nuttig zijn voor waarschuwingen en snelle detectie van problemen. Metrische gegevens kunnen echter alleen numerieke gegevens opslaan in een bepaalde structuur, terwijl logboeken verschillende gegevenstypen met elk een eigen structuur kunnen opslaan. U kunt ook complexe analyses uitvoeren op logboekgegevens met behulp van logboekquery's die niet kunnen worden gebruikt voor analyse van metrische gegevens.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Wat kunt u doen met Azure Monitor logboeken?
In de volgende tabel worden enkele van de verschillende manieren beschreven waarop u logboeken kunt gebruiken in Azure Monitor:

|  | Description |
|:---|:---|
| **Analyseren** | Log [Analytics](./log-analytics-tutorial.md) in de Azure Portal om logboekquery's te schrijven en logboekgegevens interactief te analyseren met behulp van een krachtige analyse-engine [](./log-query-overview.md) |
| **Waarschuwing** | Configureer een [logboekwaarschuwingsregel](../alerts/alerts-log.md) die een melding verzendt of geautomatiseerde actie [onderneemt](../alerts/action-groups.md) wanneer de resultaten van de query overeenkomen met een bepaald resultaat. |
| **Visualiseren** | Maak queryresultaten die als tabellen of grafieken worden weergegeven vast aan een [Azure-dashboard.](../../azure-portal/azure-portal-dashboards.md)<br>Maak een [werkmap om](../visualize/workbooks-overview.md) te combineren met meerdere gegevenssets in een interactief rapport. <br>Exporteert de resultaten van een query [om Power BI](../visualize/powerbi.md) verschillende visualisaties te gebruiken en te delen met gebruikers buiten Azure.<br>Exporteert de resultaten van een query naar [Grafana](../visualize/grafana-plugin.md) om gebruik te maken van de dashboards en deze te combineren met andere gegevensbronnen.|
| **Inzichten** | Ondersteuning [bieden voor inzichten](../monitor-reference.md#insights-and-core-solutions) die een aangepaste bewakingservaring bieden voor bepaalde toepassingen en services.  |
| **Ophalen** | Toegang tot logboekqueryresultaten vanaf een opdrachtregel met [behulp van Azure CLI.](/cli/azure/monitor/log-analytics)<br>Toegang tot logboekqueryresultaten vanaf een opdrachtregel met [behulp van PowerShell-cmdlets.](/powershell/module/az.operationalinsights)<br>Toegang tot logboekqueryresultaten van een aangepaste toepassing met [behulp REST API](https://dev.loganalytics.io/). |
| **Exporteren** | Configureer [geautomatiseerde export van logboekgegevens](./logs-data-export.md) naar Een Azure-opslagaccount of Azure Event Hubs.<br>Bouw een werkstroom om logboekgegevens op te halen en kopieer deze naar een externe locatie met [behulp van Logic Apps](./logicapp-flow-connector.md). |

![Overzicht van logboeken](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Gegevens verzamelen
Zodra u een Log Analytics-werkruimte maakt, moet u verschillende bronnen configureren om de gegevens te verzenden. Er worden geen gegevens automatisch verzameld. Deze configuratie is afhankelijk van de gegevensbron. Maak bijvoorbeeld [diagnostische instellingen voor het](../essentials/diagnostic-settings.md) verzenden van resourcelogboeken van Azure-resources naar de werkruimte. [VM-inzichten inschakelen voor](../vm/vminsights-enable-overview.md) het verzamelen van gegevens van virtuele machines. Configureer [gegevensbronnen in de werkruimte om](../agents/data-sources.md) aanvullende gebeurtenissen en prestatiegegevens te verzamelen.

- Zie [Wat wordt bewaakt door Azure Monitor?](../monitor-reference.md) voor een volledige lijst met gegevensbronnen die u kunt configureren voor het verzenden van gegevens naar Azure Monitor logboeken.


## <a name="log-analytics-workspaces"></a>Log Analytics-werkruimten
Gegevens die door Azure Monitor logboeken worden verzameld, worden opgeslagen in een of meer [Log Analytics-werkruimten.](./design-logs-deployment.md) De werkruimte definieert de geografische locatie van de gegevens, de toegangsrechten die definiëren welke gebruikers toegang hebben tot gegevens en configuratie-instellingen zoals de prijscategorie en gegevensretentie.  

U moet ten minste één werkruimte maken om de logboeken Azure Monitor gebruiken. Eén werkruimte is mogelijk voldoende voor al uw bewakingsgegevens of kan ervoor kiezen om meerdere werkruimten te maken, afhankelijk van uw vereisten. U hebt bijvoorbeeld één werkruimte voor uw productiegegevens en een andere om te testen. 

- Zie [Een Log Analytics-werkruimte maken in de Azure Portal](./quick-create-workspace.md) om een nieuwe werkruimte te maken.
- Zie [Implementatie van uw Azure Monitor logboeken](design-logs-deployment.md) ontwerpen voor overwegingen voor het maken van meerdere werkruimten.

## <a name="data-structure"></a>Gegevensstructuur
Logboekquery's halen hun gegevens op uit een Log Analytics-werkruimte. Elke werkruimte bevat meerdere tabellen die zijn ingedeeld in afzonderlijke kolommen met meerdere rijen gegevens. Elke tabel wordt gedefinieerd door een unieke set kolommen die worden gedeeld door de rijen met gegevens die door de gegevensbron worden verstrekt. 

[![Azure Monitor logboeken](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Logboekgegevens van Application Insights worden ook opgeslagen in Azure Monitor-logboeken, maar deze worden opgeslagen op verschillende manieren, afhankelijk van hoe uw toepassing is geconfigureerd. Voor een toepassing op basis van een werkruimte worden gegevens opgeslagen in een Log Analytics-werkruimte in een standaardset tabellen voor het opslaan van gegevens, zoals toepassingsaanvragen, uitzonderingen en paginaweergaven. Meerdere toepassingen kunnen dezelfde werkruimte gebruiken. Voor een klassieke toepassing worden de gegevens niet opgeslagen in een Log Analytics-werkruimte. Er wordt gebruikgemaakt van dezelfde querytaal en u kunt query's maken en uitvoeren met hetzelfde Log Analytics-hulpprogramma in de Azure Portal. Gegevens voor klassieke toepassingen worden echter afzonderlijk van elkaar opgeslagen. De algemene structuur is hetzelfde als toepassingen op basis van werkruimten, hoewel de tabel- en kolomnamen verschillend zijn. Zie [Op werkruimte gebaseerde resourcewijzigingen](../app/apm-tables.md) voor een gedetailleerde vergelijking van het schema voor op werkruimten gebaseerde en klassieke toepassingen.


> [!NOTE]
> We bieden nog steeds volledige compatibiliteit met eerdere Application Insights klassieke resourcequery's, werkmappen en waarschuwingen op basis van logboeken binnen de Application Insights ervaring. Als u een query/weergave wilt uitvoeren op basis van de nieuwe tabelstructuur/het nieuwe schema op basis van een werkruimte, moet u eerst naar uw Log [Analytics-werkruimte](../app/apm-tables.md) navigeren. Als u logboeken selecteert in **de** Application Insights deelvensters, hebt u toegang tot de klassieke Application Insights query-ervaring. Zie [Querybereik](./scope.md) voor meer informatie.


[![Azure Monitor logboekenstructuur voor Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Logboekquery's
Gegevens worden opgehaald uit een Log Analytics-werkruimte met behulp van een logboekquery. Dit is een alleen-lezenaanvraag voor het verwerken van gegevens en het retourneren van resultaten. Logboekquery's worden geschreven in [Kusto Query Language (KQL),](/azure/data-explorer/kusto/query/)dezelfde querytaal die wordt gebruikt door Azure Data Explorer. U kunt logboekquery's schrijven in Log Analytics om de resultaten interactief te analyseren, deze te gebruiken in waarschuwingsregels om proactief op de hoogte te worden gesteld van problemen of om de resultaten ervan op te nemen in werkmappen of dashboards. Inzichten omvatten vooraf gebouwde query's ter ondersteuning van hun weergaven en werkmappen.

- Zie [Logboekquery's in Azure Monitor](./log-query-overview.md) voor een lijst met logboekquery's en verwijzingen naar zelfstudies en andere documentatie om aan de slag te gaan.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Gebruik Log Analytics, een hulpprogramma in de Azure Portal, om logboekquery's te bewerken en uit te voeren en de resultaten interactief te analyseren. Vervolgens kunt u de query's die u maakt gebruiken ter ondersteuning van andere functies in Azure Monitor zoals logboekquerywaarschuwingen en werkmappen. Open Log Analytics via **de optie Logboeken** in Azure Monitor menu of vanuit de meeste andere services in de Azure Portal.

- Zie [Overzicht van Log Analytics in Azure Monitor](./log-analytics-overview.md) voor een beschrijving van Log Analytics. 
- Zie [de Zelfstudie over Log Analytics](./log-analytics-tutorial.md) voor informatie over het gebruik van Log Analytics-functies om een eenvoudige logboekquery te maken en de resultaten te analyseren.



## <a name="relationship-to-azure-data-explorer"></a>Relatie met Azure Data Explorer
Azure Monitor logboeken is gebaseerd op Azure Data Explorer. Een Log Analytics-werkruimte is ongeveer het equivalent van een database in Azure Data Explorer, tabellen zijn hetzelfde gestructureerd en gebruiken beide dezelfde Kusto Query Language (KQL). De ervaring met het gebruik van Log Analytics voor het Azure Monitor query's in de Azure Portal is vergelijkbaar met de ervaring met de Azure Data Explorer webinterface. U kunt zelfs [gegevens uit een Log Analytics-werkruimte opnemen in een Azure Data Explorer query](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboekquery's](./log-query-overview.md) voor het ophalen en analyseren van gegevens uit een Log Analytics-werkruimte.
- Meer informatie over [metrische gegevens in Azure Monitor](../essentials/data-platform-metrics.md).
- Meer informatie over de [bewakingsgegevens die beschikbaar](../agents/data-sources.md) zijn voor verschillende resources in Azure.