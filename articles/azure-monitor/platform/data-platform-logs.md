---
title: Azure Monitor-logboeken
description: Hierin worden Azure Monitor Logboeken beschreven die worden gebruikt voor geavanceerde analyse van bewakings gegevens.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 69fda41249ced8518e430af8305c0cb10822b214
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559062"
---
# <a name="azure-monitor-logs-overview"></a>Overzicht van Azure Monitor-logboeken
Azure Monitor-Logboeken is een functie van Azure Monitor waarmee logboek-en prestatie gegevens van [bewaakte bronnen](../monitor-reference.md)worden verzameld en ingedeeld. Gegevens uit verschillende bronnen, zoals [platform logboeken](platform-logs-overview.md) van Azure-Services, logboek-en prestatie gegevens van [virtuele machines](agents-overview.md), en gebruiks-en prestatie gegevens van [toepassingen](../app/app-insights-overview.md) kunnen worden geconsolideerd in één werk ruimte, zodat ze kunnen worden geanalyseerd met behulp van een geavanceerde query taal die snel miljoenen records kan analyseren. U kunt een eenvoudige query uitvoeren waarbij alleen een specifieke set records wordt opgehaald of een geavanceerde gegevens analyse uit te voeren om essentiële patronen in uw bewakings gegevens te identificeren. Werk met logboek query's en hun resultaten interactief met Log Analytics, gebruik deze in een waarschuwings regel om proactief op de hoogte te worden gesteld van problemen of om hun resultaten te visualiseren in een werkmap of dash board.

> [!NOTE]
> Azure Monitor Logboeken is een helft van het gegevens platform dat Azure Monitor ondersteunt. De andere is [Azure monitor meet waarden](data-platform-metrics.md) die numerieke gegevens in een time series-data base opslaan. Dit maakt deze gegevens meer lichter dan gegevens in Azure Monitor logboeken en kunnen bijna realtime-scenario's ondersteunen, waardoor ze vooral nuttig zijn voor waarschuwingen en snelle detectie van problemen. Metrieken kunnen alleen numerieke gegevens in een bepaalde structuur opslaan, terwijl Logboeken verschillende verschillende gegevens typen met hun eigen structuur kunnen opslaan. U kunt ook complexe analyses uitvoeren op logboek gegevens met behulp van logboek query's die niet kunnen worden gebruikt voor de analyse van metrische gegevens.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Wat kunt u doen met Azure Monitor-logboeken?
In de volgende tabel worden enkele van de verschillende manieren beschreven waarop u Logboeken in Azure Monitor kunt gebruiken:

|  |  |
|:---|:---|
| **Analyseren** | [Log Analytics](../log-query/log-analytics-tutorial.md) in het Azure Portal gebruiken om [logboek query's](../log-query/log-query-overview.md) te schrijven en logboek gegevens interactief te analyseren met behulp van een krachtige analyse-engine |
| **Waarschuwing** | Een [waarschuwings regel](alerts-log.md) voor het logboek configureren die een melding verzendt of [geautomatiseerd actie](action-groups.md) onderneemt wanneer de resultaten van de query overeenkomen met een bepaald resultaat. |
| **Visualiseren** | Query resultaten weer geven als tabellen of grafieken aan een [Azure-dash board](../../azure-portal/azure-portal-dashboards.md).<br>Een [werkmap](./workbooks-overview.md) maken om te combi neren met meerdere gegevens sets in een interactief rapport. <br>De resultaten van een query exporteren naar [Power bi](powerbi.md) om verschillende visualisaties te gebruiken en te delen met gebruikers buiten Azure.<br>De resultaten van een query exporteren naar [Grafana](grafana-plugin.md) om gebruik te maken van Dash boards en combi neren met andere gegevens bronnen.|
| **Inzichten** | Ondersteuning voor [inzichten](../monitor-reference.md#insights-and-core-solutions) die een aangepaste bewakings ervaring bieden voor bepaalde toepassingen en services.  |
| **Ophalen** | Toegang tot logboek query resultaten van een opdracht regel met behulp van [Azure cli](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Access-logboek query resultaten van een opdracht regel met behulp van [Power shell-cmdlets](/powershell/module/az.operationalinsights).<br>Toegang tot logboek query resultaten van een aangepaste toepassing met behulp van [rest API](https://dev.loganalytics.io/). |
| **Exporteren** | [Automatisch exporteren van logboek gegevens](logs-data-export.md) naar Azure Storage-account of Azure Event hubs configureren.<br>Maak een werk stroom om logboek gegevens op te halen en kopieer deze naar een externe locatie met behulp van [Logic apps](logicapp-flow-connector.md). |

![Overzicht van Logboeken](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Gegevens verzamelen
Wanneer u een Log Analytics-werk ruimte hebt gemaakt, moet u verschillende bronnen configureren voor het verzenden van de gegevens. Er worden niet automatisch gegevens verzameld. Deze configuratie is afhankelijk van de gegevens bron. U kunt bijvoorbeeld [Diagnostische instellingen maken](diagnostic-settings.md) om resource logboeken te verzenden van Azure-resources naar de werk ruimte. [Schakel Azure monitor voor VM's](../insights/vminsights-enable-overview.md) in om gegevens van virtuele machines te verzamelen. [Gegevens bronnen in de werk ruimte](data-sources.md) configureren voor het verzamelen van aanvullende gebeurtenissen en prestatie gegevens.

- Zie [wat wordt bewaakt door Azure monitor?](../monitor-reference.md) voor een volledige lijst met gegevens bronnen die u kunt configureren om gegevens te verzenden naar Azure monitor-Logboeken.


## <a name="log-analytics-workspaces"></a>Log Analytics-werkruimten
Gegevens die worden verzameld door Azure Monitor logboeken, worden opgeslagen in een of meer [log Analytics-werk ruimten](./design-logs-deployment.md). De werk ruimte definieert de geografische locatie van de gegevens, toegangs rechten die bepalen welke gebruikers toegang hebben tot gegevens en configuratie-instellingen, zoals de prijs categorie en gegevens retentie.  

U moet ten minste één werk ruimte maken om Azure Monitor-logboeken te kunnen gebruiken. Eén werk ruimte kan voldoende zijn voor al uw bewakings gegevens of u kunt ervoor kiezen om meerdere werk ruimten te maken, afhankelijk van uw vereisten. U kunt bijvoorbeeld één werk ruimte hebben voor uw productie gegevens en een andere voor het testen. 

- Zie [een log Analytics-werk ruimte maken in de Azure Portal](../learn/quick-create-workspace.md) om een nieuwe werk ruimte te maken.
- Zie [de implementatie van uw Azure monitor-logboeken ontwerpen](design-logs-deployment.md) voor overwegingen bij het maken van meerdere werk ruimten.

## <a name="data-structure"></a>Gegevensstructuur
Hiermee worden de gegevens uit een Log Analytics-werk ruimte opgehaald in een logboek. Elke werk ruimte bevat meerdere tabellen die zijn ingedeeld in afzonderlijke kolommen met meerdere gegevens rijen. Elke tabel wordt gedefinieerd door een unieke set kolommen die worden gedeeld door de gegevens rijen die door de gegevens bron worden opgegeven. 

[![Structuur van Azure Monitor logboeken](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Logboek gegevens van Application Insights worden ook opgeslagen in Azure Monitor logboeken, maar worden opgeslagen, afhankelijk van hoe uw toepassing is geconfigureerd. Voor een op werk ruimte gebaseerde toepassing worden gegevens opgeslagen in een Log Analytics-werk ruimte in een standaardset met gegevens, zoals toepassings aanvragen, uitzonde ringen en pagina weergaven. Meerdere toepassingen kunnen dezelfde werk ruimte gebruiken. Voor een klassieke toepassing worden de gegevens niet opgeslagen in een Log Analytics-werk ruimte. De query taal wordt gebruikt en u kunt query's maken en uitvoeren met behulp van hetzelfde Log Analytics-hulp programma in de Azure Portal. Gegevens voor klassieke toepassingen worden onafhankelijk van elkaar opgeslagen. De algemene structuur is hetzelfde als op werk ruimte gebaseerde toepassingen, hoewel de tabel-en kolom namen verschillend zijn. Zie [resource wijzigingen op basis van een werk ruimte](../app/apm-tables.md) voor een gedetailleerde vergelijking van het schema voor op werk ruimte gebaseerde en klassieke toepassingen.


> [!NOTE]
> We bieden nog steeds volledige compatibiliteit voor uw Application Insights klassieke resource query's, werkmappen en waarschuwingen op basis van Logboeken in de Application Insights ervaring. Als u wilt zoeken/weer geven op [basis van de nieuwe, op werk ruimte gebaseerde tabel structuur/schema,](../app/apm-tables.md) moet u eerst naar uw log Analytics-werk ruimte navigeren. Tijdens de preview-periode selecteert u **Logboeken** in het deel venster Application Insights. u krijgt dan toegang tot de klassieke Application Insights query-ervaring. Zie [query bereik](../log-query/scope.md) voor meer informatie.


[![Structuur van Azure Monitor logboeken voor Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Logboekquery's
Gegevens worden opgehaald uit een Log Analytics-werk ruimte met behulp van een logboek query die een alleen-lezen aanvraag is voor het verwerken van gegevens en het retour neren van resultaten. Logboek query's worden geschreven in [Kusto query language (KQL)](/azure/data-explorer/kusto/query/). Dit is de query taal die wordt gebruikt door Azure Data Explorer. U kunt logboek query's in Log Analytics schrijven om hun resultaten interactief te analyseren, ze gebruiken in waarschuwings regels om proactief op de hoogte te worden gesteld van problemen of hun resultaten in werkmappen of Dash boards toevoegen. Inzichten bevatten vooraf ontwikkelde query's om hun weer gaven en werkmappen te ondersteunen.

- Zie [logboek query's in azure monitor](log-query/../../log-query/log-query-overview.md) voor een lijst met waar logboek query's worden gebruikt en verwijzingen naar zelf studies en andere documentatie om aan de slag te gaan.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Gebruik Log Analytics, een hulp programma in de Azure Portal, om logboek query's te bewerken en uit te voeren en de resultaten interactief te analyseren. U kunt vervolgens de query's gebruiken die u hebt gemaakt ter ondersteuning van andere functies in Azure Monitor, zoals waarschuwingen voor logboek query's en werkmappen. Toegang tot Log Analytics via de optie **Logboeken** in het menu Azure monitor of vanuit de meeste andere services in de Azure Portal.

- Zie [overzicht van log Analytics in azure monitor](../log-query/log-analytics-overview.md) voor een beschrijving van log Analytics. 
- Zie [log Analytics zelf studie](../log-query/log-analytics-tutorial.md) voor instructies over het gebruik van log Analytics-functies voor het maken van een eenvoudige logboek query en het analyseren van de resultaten.



## <a name="relationship-to-azure-data-explorer"></a>Relatie met Azure Data Explorer
Azure Monitor-Logboeken is gebaseerd op Azure Data Explorer. Een Log Analytics werk ruimte is ongeveer het equivalent van een data base in azure Data Explorer, tabellen worden gestructureerd en beide gebruiken dezelfde Kusto query language (KQL). De ervaring van het gebruik van Log Analytics om met Azure Monitor query's in de Azure Portal te werken, is vergelijkbaar met de ervaring met behulp van de Web-UI van Azure Data Explorer. U kunt zelfs [gegevens uit een log Analytics-werk ruimte in een Azure Data Explorer-query toevoegen](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het ophalen en analyseren van gegevens uit een log Analytics-werk ruimte.
- Meer informatie over [metrische gegevens in azure monitor](data-platform-metrics.md).
- Meer informatie over de [beschik bare bewakings gegevens](data-sources.md) voor verschillende bronnen in Azure.
