---
title: Prestaties van de data base bewaken met Intelligent Insights
description: Intelligent Insights in Azure SQL Database en Azure SQL Managed instance gebruiken ingebouwde intelligentie om continu database gebruik te bewaken door middel van kunst matige intelligentie en detectie van storende gebeurtenissen die de prestaties nadelig beïnvloeden.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 61033e3eb8264c1e462faac3e4553a855a1d06c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592081"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights met AI gebruiken om database prestaties te controleren en op te lossen (preview-versie)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Intelligent Insights in Azure SQL Database en Azure SQL Managed Instance kunt u zien wat er gebeurt met de prestaties van uw data base.

Intelligent Insights maakt gebruik van ingebouwde intelligentie om continu database gebruik te bewaken door middel van kunst matige intelligentie en detectie van storende gebeurtenissen die de prestaties nadelig beïnvloeden. Eenmaal gedetecteerd, wordt er een gedetailleerde analyse uitgevoerd waarmee een Intelligent Insights resource logboek wordt gegenereerd (SQLInsights genoemd) met een intelligente evaluatie van het probleem. Deze evaluatie bestaat uit een analyse van de hoofd oorzaak van het prestatie probleem van de data base en, waar mogelijk, aanbevelingen voor betere prestaties.

## <a name="what-can-intelligent-insights-do-for-you"></a>Wat kan er Intelligent Insights voor u doen?

Intelligent Insights is een unieke mogelijkheid van ingebouwde intelligentie van Azure die de volgende waarde biedt:

- Proactieve controle
- Aangepaste prestatie inzichten
- Vroegtijdige detectie van de prestaties van de data base verlagen
- Hoofd oorzaak analyse van gedetecteerde problemen
- Aanbevelingen voor prestatie verbetering
- Mogelijkheden voor uitschalen op honderd duizenden data bases
- Positieve impact op DevOps resources en de total cost of ownership

## <a name="how-does-intelligent-insights-work"></a>Hoe werkt Intelligent Insights

Intelligent Insights de prestaties van de data base geanalyseerd door de data base-workload van het afgelopen uur te vergelijken met de afgelopen zeven dagen werk belasting voor de basis lijn. De werk belasting van de data base bestaat uit query's die het belangrijkst zijn voor de prestaties van de data base, zoals de meest herhaalde en grootste query's. Omdat elke Data Base uniek is op basis van de structuur, gegevens, het gebruik en de toepassing, is elke werk belasting basis die wordt gegenereerd specifiek en uniek voor die werk belasting. Intelligent Insights, onafhankelijk van de basis lijn van de werk belasting, bewaakt ook absolute operationele drempels en detecteert problemen met buitensporige wacht tijden, kritieke uitzonde ringen en problemen met query parameterizations die van invloed kunnen zijn op de prestaties.

Wanneer er een probleem is opgetreden bij de uitvoering van een prestatie vermindering van meerdere waargenomen metrische gegevens met behulp van kunst matige intelligentie, wordt de analyse uitgevoerd. Er wordt een diagnostisch logboek gegenereerd met een intelligent inzicht in wat er gebeurt met uw data base. Met Intelligent Insights kunt u eenvoudig het prestatie probleem van de data base volgen tot oplossing. Elk gedetecteerde probleem wordt bijgehouden door de levens cyclus van de eerste detectie van het probleem en de controle van de prestatie verbetering tot de voltooiing ervan.

![Werk stroom voor analyse van database prestaties](./media/intelligent-insights-overview/intelligent-insights-concept.png)

De metrische gegevens die worden gebruikt om prestatie problemen met de data base te meten en te detecteren, zijn gebaseerd op de query duur, time-outaanvragen, buitensporige wacht tijden en mislukte aanvragen. Zie [detectie gegevens](#detection-metrics)voor meer informatie over metrische gegevens.

De geïdentificeerde prestatie vermindering van de Data Base worden vastgelegd in het SQLInsights-logboek met intelligente vermeldingen die bestaan uit de volgende eigenschappen:

| Eigenschap | Details |
| :------------------- | ------------------- |
| Database gegevens | Meta gegevens over een Data Base waarop een inzicht is gedetecteerd, zoals een resource-URI. |
| Waargenomen tijds bereik | Begin-en eind tijd voor de periode van het gedetecteerde inzicht. |
| Beïnvloede metrische gegevens | Metrische gegevens waardoor een inzicht werd gegenereerd: <ul><li>De duur van de query wordt verhoogd [seconden].</li><li>Overmatig wacht tijd [seconden].</li><li>Time-outaanvragen [percentage].</li><li>Gefoutde aanvragen [percentage].</li></ul>|
| Impact waarde | Waarde van een gemeten meting. |
| Beïnvloede query's en fout codes | Hash-of fout code opvragen. Deze kunnen worden gebruikt om de betrokken query's eenvoudig te correleren. Metrische gegevens die bestaan uit een toename van de query duur, de wacht tijd, het aantal time-outs of de fout codes worden weer gegeven. |
| Detecties | De detectie die tijdens de uitvoering van een gebeurtenis is geïdentificeerd bij de data base. Er zijn 15 detectie patronen. Zie [problemen met database prestaties oplossen met intelligent Insights](intelligent-insights-troubleshoot-performance.md)voor meer informatie. |
| Hoofdoorzaakanalyse | Analyse van de hoofd oorzaak van het probleem dat in een lees bare indeling is geïdentificeerd. Sommige inzichten bevatten waar mogelijk een aanbeveling voor de verbetering van de prestaties. |
|||

Voor een praktische beschrijving over het gebruik van Intelligent Insights met Azure SQL-analyse en voor typische gebruiks scenario's raadpleegt u deze video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights schijnen in het detecteren en oplossen van problemen met database prestaties. Zie [prestatie problemen oplossen met intelligent Insights](intelligent-insights-troubleshoot-performance.md)als u intelligent Insights wilt gebruiken om problemen met de database prestaties op te lossen.

## <a name="intelligent-insights-options"></a>Intelligent Insights opties

Intelligent Insights beschik bare opties zijn:

| Intelligent Insights optie | Ondersteuning voor Azure SQL Database | Ondersteuning van Azure SQL Managed Instance |
| :----------------------------- | ----- | ----- |
| **Intelligent Insights configureren** : Configureer intelligent Insights analyse voor uw data bases. | Ja | Ja |
| **Stream inzichten naar Azure SQL-analyse** --stream Insights Azure SQL-analyse. | Ja | Ja |
| **Stream inzichten naar Azure Event hubs** -stream insights naar Event hubs voor verdere aangepaste integraties. | Ja | Ja |
| **Stream inzichten om** inzicht te krijgen in azure Storage streamen om te Azure Storage voor verdere analyse en langdurige archivering. | Ja | Ja |

> [!NOTE]
> Intelligent Insights is een preview-functie die niet beschikbaar is in de volgende regio's: Europa-west, Europa-noord, VS-West 1 en VS-Oost 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>De export van het Intelligent Insights-logboek configureren

De uitvoer van de Intelligent Insights kan worden gestreamd naar een van de volgende bestemmingen voor analyse:

- Uitvoer die naar een Log Analytics-werk ruimte is gestreamd, kan worden gebruikt met [Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md) om inzichten weer te geven via de gebruikers interface van de Azure Portal. Dit is de geïntegreerde Azure-oplossing en de meest gang bare manier om inzichten weer te geven.
- Uitvoer die naar Azure Event Hubs is gestreamd, kan worden gebruikt voor de ontwikkeling van aangepaste bewakings-en waarschuwings scenario's
- Uitvoer die naar Azure Storage is gestreamd, kan worden gebruikt voor aangepaste toepassings ontwikkeling, zoals aangepaste rapportage, gegevens archivering op lange termijn, enzovoort.

Integratie van Azure SQL-analyse, Azure Event Hubs, Azure Storage of producten van derden voor gebruik wordt uitgevoerd via eerst Intelligent Insights logboek registratie (het "SQLInsights"-logboek) inschakelen op de Blade Diagnostische instellingen van een Data Base en vervolgens de configuratie van Intelligent Insights logboek gegevens naar een van deze bestemmingen.

Zie [metrische gegevens en Diagnostische logboeken](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)voor meer informatie over het inschakelen van intelligent Insights logboek registratie en het configureren van metrieke en bron logboeken die moeten worden gestreamd naar een verbruikte product.

### <a name="set-up-with-azure-sql-analytics"></a>Instellen met Azure SQL-analyse

Azure SQL-analyse oplossing biedt Graphical User Interface-, rapportage-en waarschuwings functies voor database prestaties met behulp van de Intelligent Insights resource logboek gegevens.

Azure SQL-analyse toevoegen aan uw Azure Portal dash board vanuit Marketplace en een werk ruimte maken, Zie [Azure SQL-analyse configureren](../../azure-monitor/insights/azure-sql.md#configuration)

Als u Intelligent Insights met Azure SQL-analyse wilt gebruiken, configureert u Intelligent Insights logboek gegevens die moeten worden gestreamd naar Azure SQL-analyse werk ruimte die u in de vorige stap hebt gemaakt, raadpleegt u de [logboek registratie voor metingen en diagnostiek](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

In het volgende voor beeld ziet u een Intelligent Insights die wordt weer gegeven via Azure SQL-analyse:

![Intelligent Insights rapport](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Instellen met Event Hubs

Als u Intelligent Insights met Event Hubs wilt gebruiken, configureert u Intelligent Insights logboek gegevens die moeten worden gestreamd naar Event Hubs, raadpleegt u de [logboek registratie voor metrieke en diagnostische](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) informatie en [registreert u logboeken van Azure diagnostics naar Event hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Zie [wat u kunt doen met metrische gegevens en Diagnostische logboeken in Event hubs](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs)voor informatie over het gebruik van Event hubs om aangepaste bewaking en waarschuwingen in te stellen.

### <a name="set-up-with-azure-storage"></a>Instellen met Azure Storage

Als u Intelligent Insights met opslag wilt gebruiken, moet u Intelligent Insights logboek gegevens configureren om te worden gestreamd naar opslag [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage). [](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)

### <a name="custom-integrations-of-intelligent-insights-log"></a>Aangepaste integraties van Intelligent Insights logboek

Zie [het logboek voor diagnostische gegevens over het intelligent Insights-Data Base gebruiken](intelligent-insights-use-diagnostics-log.md)om intelligent Insights te gebruiken met hulp middelen van derden of voor aangepaste waarschuwingen en bewakings ontwikkeling.

## <a name="detection-metrics"></a>Detectie gegevens

De metrische gegevens die worden gebruikt voor detectie modellen die Intelligent Insights genereren, zijn gebaseerd op bewaking:

- Query duur
- Time-outaanvragen
- Buitensporige wacht tijd
- Aanvragen met fouten

Query duur en time-outaanvragen worden gebruikt als primaire modellen bij het detecteren van problemen met de prestaties van de data base-workload. Ze worden gebruikt, omdat ze direct meten wat er gebeurt met de workload. Om alle mogelijke oorzaken van de prestatie vermindering van de werk belasting te detecteren, worden er buitensporige wacht tijden en aanvragen met een fout gebruikt als extra modellen om problemen aan te geven die van invloed zijn op de prestaties van de werk belasting.

Het systeem beschouwt automatisch wijzigingen aan de werk belasting en wijzigingen in het aantal query aanvragen dat is gedaan aan de data base om dynamische en out-of-the-out-performance drempel waarden voor data bases dynamisch te bepalen.

Alle metrische gegevens worden beschouwd als samen in verschillende relaties via een weten schappelijk afgeleid data model dat elk prestatie probleem gecategoriseerd heeft. Informatie die via een intelligent inzicht wordt verstrekt, omvat:

- Details van het prestatie probleem dat is gedetecteerd.
- Er is een analyse van de hoofd oorzaak van het probleem gedetecteerd.
- Aanbevelingen voor het verbeteren van de prestaties van de bewaakte data base, waar mogelijk.

## <a name="query-duration"></a>Query duur

Het model voor het degraderen van de duur van de query analyseert afzonderlijke query's en detecteert de toename in de tijd die nodig is voor het compileren en uitvoeren van een query vergeleken met de basis lijn van de prestaties.

Als ingebouwde intelligentie een aanzienlijke toename detecteert bij het compileren van query's of uitvoerings tijd van query's die van invloed zijn op de prestaties van de werk belasting, worden deze query's gemarkeerd als prestatie problemen met de prestaties van de query duur.

Het Intelligent Insights Diagnostische logboeken voert de query-hash uit van de query die is gedegradeerd voor prestaties. De query-hash geeft aan of de prestaties verslechteren bij het verg Roten van de query compilatie of uitvoerings tijd, waardoor de duur van de query wordt verhoogd.

## <a name="timeout-requests"></a>Time-outaanvragen

Het model voor de degradatie van time-outaanvragen analyseert afzonderlijke query's en detecteert toename in time-outs op het uitvoerings niveau van de query en de totale time-outs van aanvragen op database niveau vergeleken met de periode van de prestatie basislijn.

Sommige van de query's kunnen een time-out ondervinden, zelfs voordat ze de uitvoerings fase bereiken. Door middel van afgebroken werk nemers versus aanvragen die zijn gedaan, ingebouwde Intelligence-metingen en analyseert alle query's die de data base hebben bereikt, ongeacht of deze zijn ontvangen in de uitvoerings fase of niet.

Na het aantal time-outs voor het uitvoeren van query's of het aantal afgebroken aanvraag werkers overschrijdt de drempel waarde die door het systeem wordt beheerd, wordt een diagnose logboek gevuld met intelligente inzichten.

De gegenereerde inzichten bevatten het aantal time-outaanvragen en het aantal time-out query's. De indicatie van de prestatie vermindering is gerelateerd aan een time-out voor de uitvoering van de uitvoerings fase, of het algehele database niveau. Wanneer de toename van time-outs significant wordt geacht voor de prestaties van de data base, worden deze query's gemarkeerd als prestatie problemen tijdens de time-out van de prestaties.

## <a name="excessive-wait-times"></a>Buitensporige wacht tijden

Het model voor buitensporige wacht tijden bewaakt afzonderlijke database query's. Er worden ongebruikelijk hoge query-wacht statistieken gedetecteerd die de door het systeem beheerde absolute drempel waarden overschrijden. De volgende query met buitensporige wacht tijden meet waarden worden gemeten met behulp van, [query Store wait-statistieken (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Bron limieten bereiken
- Resource limieten voor elastische Pools bereiken
- Buitensporig aantal werk-of sessie-threads
- Buitensporige database vergrendeling
- Geheugendruk
- Andere wacht statistieken

Het bereiken van resource limieten of bronnen limieten voor elastische Pools duidt op het verbruik van beschik bare resources voor een abonnement of in de elastische groep met absolute drempel waarden. Deze statistieken wijzen op de prestaties van de werk belasting. Een buitensporig aantal werk-of sessie-threads geeft een toestand aan waarin het aantal werkthreads of sessies gepaard absolute drempel waarden heeft gepasseerd. Deze statistieken wijzen op de prestaties van de werk belasting.

Buitensporige database vergrendeling geeft aan dat het aantal vergren delingen op een Data Base gepaard absolute drempel waarden heeft. Dit stat geeft aan dat de prestaties van de werk belasting worden verminderd. Geheugen druk is een voor waarde waarin het aantal threads dat geheugen vraagt, een absolute drempel overschrijdt. Dit stat geeft aan dat de prestaties van de werk belasting worden verminderd.

Met de detectie van andere wacht tijden wordt aangegeven in welke omstandigheden diverse metrische gegevens die via de query Store worden gemeten, een absolute drempel overschrijden. Deze statistieken wijzen op de prestaties van de werk belasting.

Afhankelijk van de beschik bare wacht tijden, worden de gegevens in het logboek van de Intelligent Insights Diagnostics-pogingen om de hashes te controleren die invloed hebben op de prestaties en de betrokken query's, de metrische gegevens die ervoor zorgen dat query's worden uitgevoerd tijdens de uitvoering en de gemeten wacht tijd.

## <a name="errored-requests"></a>Aanvragen met fouten

Het model voor het degraderen van de fout aanvragen bewaakt afzonderlijke query's en detecteert een toename in het aantal query's dat is opgetreden in vergelijking met de basislijn periode. Dit model bewaakt ook kritieke uitzonde ringen die kruised absolute drempel waarden die worden beheerd door ingebouwde intelligentie. Het systeem houdt automatisch rekening met het aantal query aanvragen dat is gedaan aan de data base en de accounts voor eventuele wijzigingen in de werk belasting in de bewaakte periode.

Wanneer de gemeten toename in fout aanvragen ten opzichte van het totale aantal aanvragen wordt beschouwd als significant voor de prestaties van de werk belasting, worden de betrokken query's gemarkeerd als fouten bij het oplossen van problemen met de prestaties.

Het Intelligent Insights logboek voert een uitvoer uit van het aantal aanvragen dat is gefoutd. Hiermee wordt aangegeven of de prestaties afnemen van een verslechterde fout in aanvragen of de drempel waarde voor een bewaakte kritieke uitzonde ring en de gemeten tijd van de verslechtering van de prestaties.

Als een van de bewaakte kritieke uitzonde ringen de absolute drempel waarden overschrijdt die worden beheerd door het systeem, wordt een intelligent inzicht gegenereerd met kritieke uitzonderings Details.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [bewaken van data bases met behulp van SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Meer informatie over het [oplossen van prestatie problemen met intelligent Insights](intelligent-insights-troubleshoot-performance.md).