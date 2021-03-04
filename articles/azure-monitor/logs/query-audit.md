---
title: Query's controleren in Azure Monitor-logboek query's
description: Details van de audit logboeken van logboek query's die telemetrie over logboek query's uitvoeren in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: 28dfac7de8e73adf577b0a13e5fbd8740b1e3b06
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047414"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Query's controleren in Azure Monitor Logboeken (preview-versie)
Controle logboeken voor logboek query's bieden telemetrie over logboek query's die in Azure Monitor worden uitgevoerd. Dit omvat informatie zoals wanneer een query werd uitgevoerd, wie deze uitvoert, welk hulp programma is gebruikt, de query tekst en prestatie statistieken die de uitvoering van de query beschrijven.


## <a name="configure-query-auditing"></a>Query controle configureren
Het controleren van query's is ingeschakeld met een [Diagnostische instelling](../essentials/diagnostic-settings.md) voor de log Analytics-werk ruimte. Hierdoor kunt u controle gegevens verzenden naar de huidige werk ruimte of een andere werk ruimte in uw abonnement, naar Azure Event Hubs om buiten Azure te verzenden of naar Azure Storage voor archivering. 

### <a name="azure-portal"></a>Azure Portal
Open de diagnostische instelling voor een Log Analytics werk ruimte in de Azure Portal op een van de volgende locaties:

- Selecteer in het menu **Azure monitor** **Diagnostische instellingen**, zoek de werk ruimte en selecteer deze.

    [![Diagnostische instellingen Azure Monitor ](media/query-audit/diagnostic-setting-monitor.png)](media/query-audit/diagnostic-setting-monitor.png#lightbox) 

- Selecteer in het menu **log Analytics werk ruimten** de werk ruimte en selecteer vervolgens **Diagnostische instellingen**.

    [![Diagnostische instellingen Log Analytics werk ruimte ](media/query-audit/diagnostic-setting-workspace.png)](media/query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Resource Manager-sjabloon
U kunt een voor beeld van een resource manager-sjabloon ophalen uit de [Diagnostische instelling voor log Analytics werk ruimte](../essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Controledatabase
Telkens wanneer een query wordt uitgevoerd, wordt een controle record gemaakt. Als u de gegevens naar een Log Analytics-werk ruimte verzendt, wordt deze opgeslagen in een tabel met de naam *LAQueryLogs*. In de volgende tabel worden de eigenschappen in elke record van de controle gegevens beschreven.

| Veld | Beschrijving |
|:---|:---|
| TimeGenerated         | UTC-tijd waarop de query is verzonden. |
| CorrelationId         | Unieke ID voor het identificeren van de query. Kan worden gebruikt in scenario's voor het oplossen van problemen bij het contact opnemen met micro soft voor hulp. |
| AADObjectId           | Azure Active Directory ID van het gebruikers account dat de query heeft gestart.  |
| AADTenantId           | ID van de Tenant van het gebruikers account waarmee de query is gestart.  |
| AADEmail              | E-mail van de Tenant van het gebruikers account waarmee de query is gestart.  |
| AADClientId           | De ID en de naam van de toepassing die wordt gebruikt om de query te starten. |
| RequestClientApp      | De naam van de toepassing die wordt gebruikt om de query te starten. |
| QueryTimeRangeStart   | Het begin van het geselecteerde tijds bereik voor de query. Dit kan niet worden ingevuld in bepaalde scenario's, zoals wanneer de query wordt gestart vanuit Log Analytics en het tijds bereik is opgegeven in de query in plaats van de tijd kiezer. |
| QueryTimeRangeEnd     | Het einde van het geselecteerde tijds bereik voor de query. Dit kan niet worden ingevuld in bepaalde scenario's, zoals wanneer de query wordt gestart vanuit Log Analytics en het tijds bereik is opgegeven in de query in plaats van de tijd kiezer.  |
| QueryText             | De tekst van de query die is uitgevoerd. |
| RequestTarget         | API-URL is gebruikt om de query te verzenden.  |
| RequestContext        | Lijst met resources waarvoor de query is aangevraagd om uit te voeren. Bevat Maxi maal drie teken reeks matrices: werk ruimten, toepassingen en resources. De doel query's van een abonnement of resource groep worden als *resources* weer gegeven. Bevat het doel dat is geïmpliceerd door RequestTarget.<br>De resource-ID voor elke resource wordt opgenomen als deze kan worden omgezet. Het kan mogelijk niet worden opgelost als er een fout wordt geretourneerd bij het openen van de resource. In dit geval wordt de specifieke tekst van de query gebruikt.<br>Als de query een dubbel zinnige naam gebruikt, zoals de naam van een werk ruimte die in meerdere abonnementen voor komt, wordt deze dubbel zinnige naam gebruikt. |
| RequestContextFilters | Set filters die zijn opgegeven als onderdeel van de aanroep van de query. Bevat Maxi maal drie mogelijke teken reeks matrices:<br>-ResourceTypes-type resource om het bereik van de query te beperken<br>-Werk ruimten: lijst met werk ruimten om de query te beperken tot<br>-WorkspaceRegions-lijst met werkruimte regio's om de query te beperken |
| ResponseCode          | HTTP-antwoord code geretourneerd tijdens het verzenden van de query. |
| ResponseDurationMs    | Tijdstip waarop het antwoord wordt geretourneerd.  |
| ResponseRowCount     | Totaal aantal rijen dat door de query wordt geretourneerd. |
| StatsCPUTimeMs       | De totale reken tijd die wordt gebruikt voor het berekenen en ophalen van gegevens. Alleen ingevuld als de query wordt geretourneerd met de status code 200. |
| StatsDataProcessedKB | De hoeveelheid gegevens die is gebruikt voor het verwerken van de query. Beïnvloed door de grootte van de doel tabel, gebruikte tijds periode, toegepaste filters en het aantal kolommen waarnaar wordt verwezen. Alleen ingevuld als de query wordt geretourneerd met de status code 200. |
| StatsDataProcessedStart | Tijd van de oudste gegevens die zijn gebruikt voor het verwerken van de query. Beïnvloed door de query expliciete tijds duur en filters toegepast. Dit kan groter zijn dan de expliciete tijds Panne vanwege het partitioneren van gegevens. Alleen ingevuld als de query wordt geretourneerd met de status code 200. |
| StatsDataProcessedEnd  |Tijd van de nieuwste gegevens die zijn gebruikt voor het verwerken van de query. Beïnvloed door de query expliciete tijds duur en filters toegepast. Dit kan groter zijn dan de expliciete tijds Panne vanwege het partitioneren van gegevens. Alleen ingevuld als de query wordt geretourneerd met de status code 200. |
| StatsWorkspaceCount | Het aantal werk ruimten dat door de query wordt geopend. Alleen ingevuld als de query wordt geretourneerd met de status code 200. |
| StatsRegionCount | Aantal regio's dat door de query wordt gebruikt. Alleen ingevuld als de query wordt geretourneerd met de status code 200. |

## <a name="considerations"></a>Overwegingen

- Query's worden alleen geregistreerd wanneer ze worden uitgevoerd in een gebruikers context. Er wordt geen service-to-service in azure vastgelegd. De twee primaire sets van query's die deze uitsluiting omvat, zijn facturerings berekeningen en automatische waarschuwings uitvoeringen. In het geval van waarschuwingen wordt alleen de geplande waarschuwings query zelf vastgelegd. de eerste uitvoering van de waarschuwing in het scherm voor het maken van waarschuwingen wordt uitgevoerd in een gebruikers context, en is beschikbaar voor controle doeleinden. 
- Prestatie statistieken zijn niet beschikbaar voor query's die afkomstig zijn van de Azure Data Explorer-proxy. Alle andere gegevens voor deze query's worden nog steeds ingevuld.
- De *h* -hint van teken reeksen die [teken reeks-literals](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) vergelijkt, heeft geen invloed op de controle logboeken van de query. De query's worden precies zo vastgelegd als verzonden zonder dat de teken reeks is verborgen. U moet ervoor zorgen dat alleen gebruikers die nalevings rechten hebben om deze gegevens te zien, hiervoor de verschillende Kubernetes RBAC-of Azure RBAC-modi kunnen gebruiken die beschikbaar zijn in Log Analytics-werk ruimten.
- Voor query's die gegevens uit meerdere werk ruimten bevatten, wordt de query alleen vastgelegd in de werk ruimten waartoe de gebruiker toegang heeft.

## <a name="costs"></a>Kosten  
Er zijn geen kosten verbonden aan de diagnostische Azure-extensie, maar mogelijk worden er kosten in rekening gebracht voor de gegevens die zijn opgenomen. Controleer [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor de bestemming waar u gegevens verzamelt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Diagnostische instellingen](../essentials/diagnostic-settings.md).
- Meer informatie over het [optimaliseren van logboek query's](query-optimization.md).
