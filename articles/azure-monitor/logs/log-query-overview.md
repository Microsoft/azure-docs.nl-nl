---
title: Logboekquery's in Azure Monitor
description: Naslag informatie voor de Kusto-query taal die wordt gebruikt door Azure Monitor. Bevat extra elementen die specifiek zijn voor Azure Monitor en elementen die niet worden ondersteund in Azure Monitor-logboek query's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 623230b59d4f479b20b9b8532135bb2b70885ecb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610124"
---
# <a name="log-queries-in-azure-monitor"></a>Logboekquery's in Azure Monitor
Azure Monitor Logboeken is gebaseerd op Azure Data Explorer en logboek query's worden geschreven met dezelfde Kusto query language (KQL). Dit is een uitgebreide taal, ontworpen om gemakkelijk te lezen en te schrijven, zodat u met een aantal basis richtlijnen query's kunt starten.

In de gebieden in Azure Monitor u de volgende query's gaat gebruiken:

- [Log Analytics](../logs/log-analytics-overview.md). Primair hulp programma in de Azure Portal voor het bewerken van logboek query's en het interactief analyseren van de resultaten. Zelfs als u van plan bent om een logboek query ergens anders in Azure Monitor te gebruiken, moet u deze doorgaans in Log Analytics schrijven en testen voordat u deze naar de uiteindelijke locatie kopieert.
- [Waarschuwings regels in logboek registreren](../platform/alerts-overview.md). Proactief problemen identificeren van gegevens in uw werk ruimte.  Elke waarschuwings regel is gebaseerd op een logboek query die automatisch regel matig wordt uitgevoerd.  De resultaten worden gecontroleerd om te bepalen of er een waarschuwing moet worden gemaakt.
- [Werkmappen](../visualize/workbooks-overview.md). Neem de resultaten van logboek query's op met behulp van verschillende visualisaties in interactieve visuele rapporten in de Azure Portal.
- [Azure-Dash boards](../learn/tutorial-logs-dashboards.md). De resultaten van een query in een Azure-dash board vastmaken, waarmee u logboek-en metrische gegevens gezamenlijk kunt visualiseren en optioneel kunt delen met andere Azure-gebruikers.
- [Logic apps](../logs/logicapp-flow-connector.md).  Gebruik de resultaten van een logboek query in een geautomatiseerde werk stroom met behulp van Logic Apps.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). Gebruik de resultaten van een logboek query in een Power shell-script vanaf een opdracht regel of een Azure Automation runbook dat gebruikmaakt van Get-AzOperationalInsightsSearchResults.
- [API voor Azure monitor-logboeken](https://dev.loganalytics.io). Logboek gegevens uit de werk ruimte ophalen van een REST API-client.  De API-aanvraag bevat een query die wordt uitgevoerd op Azure Monitor om te bepalen welke gegevens moeten worden opgehaald.

## <a name="getting-started"></a>Aan de slag
De beste manier om aan de slag te gaan met het schrijven van logboek query's met behulp van KQL is de beschik bare zelf studies en voor beelden.

- [Log Analytics zelf studie](../log-query/log-analytics-tutorial.md) : zelf studie over het gebruik van de functies van log Analytics. Dit is het hulp programma dat u in de Azure Portal gebruikt om query's te bewerken en uit te voeren. U kunt hiermee ook eenvoudige query's schrijven zonder rechtstreeks met de query taal te werken. Als u nog geen Log Analytics eerder hebt gebruikt, kunt u hier beginnen, zodat u begrijpt welk hulp programma u met de andere zelf studies en voor beelden kunt gebruiken.
- [KQL-zelf studie](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) : begeleide Walk via Basic KQL-concepten en algemene Opera tors. Dit is de beste plek om snel aan de slag te gaan met de taal zelf en de structuur van logboek query's. 
- [Voorbeeld query's](../logs/example-queries.md) : beschrijving van de beschik bare voorbeeld query's in log Analytics. U kunt de query's zonder aanpassing gebruiken of ze als voor beelden gebruiken om KQL te leren.
- [Query voorbeelden](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) : voorbeeld query's die verschillende concepten illustreren.



## <a name="reference-documentation"></a>Referentiedocumentatie
[Documentatie voor KQL](/azure/data-explorer/kusto/query/) , met inbegrip van de naslag informatie voor alle opdrachten en Opera Tors, is beschikbaar in de documentatie van Azure Data Explorer. Zelfs wanneer u gewend bent met KQL, gebruikt u nog steeds de referentie om nieuwe opdrachten en scenario's te onderzoeken die u nog niet eerder hebt gebruikt.


## <a name="language-differences"></a>Verschillen in taal
Hoewel Azure Monitor dezelfde KQL gebruikt als Azure Data Explorer, zijn er enkele verschillen. In de KQL-documentatie worden de Opera tors opgegeven die niet worden ondersteund door Azure Monitor of die andere functionaliteit hebben. Opera tors die specifiek zijn voor Azure Monitor, worden beschreven in de Azure Monitor-inhoud. In de volgende secties vindt u een lijst met de verschillen tussen versies van de taal voor snelle naslag doeleinden.

### <a name="statements-not-supported-in-azure-monitor"></a>Instructies worden niet ondersteund in Azure Monitor

* [Toe](/azure/kusto/query/aliasstatement)
* [Queryparameters](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Functies die niet worden ondersteund in Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Opera tors worden niet ondersteund in Azure Monitor

* [Cross-cluster-koppeling](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Invoeg toepassingen die niet worden ondersteund in Azure Monitor

* [Python-invoeg toepassing](/azure/kusto/query/pythonplugin)
* [Invoegtoepassing voor sql_request](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Extra Opera tors in Azure Monitor
De volgende Opera tors ondersteunen specifieke Azure Monitor functies en zijn niet beschikbaar buiten Azure Monitor.

* [app ()](../logs/app-expression.md)
* [resource ()](../log-query/resource-expression.md)
* [werk ruimte ()](../logs/workspace-expression.md)

## <a name="next-steps"></a>Volgende stappen
- Door loop een [zelf studie over het schrijven van query's](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- Open de volledige [referentie documentatie voor de Kusto-query taal](/azure/kusto/query/).

