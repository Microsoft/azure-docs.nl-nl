---
title: Synapse Analytics bewaken met Azure Monitor
description: Meer informatie over het bewaken van uw Synapse Analytics-werk ruimte met Azure Monitor metrische gegevens, waarschuwingen en Logboeken
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 6861fd7a92c26fad883f14fb430a03b237c90122
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609255"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Azure Monitor gebruiken met uw Azure Synapse Analytics-werk ruimte

Cloud toepassingen zijn complex en hebben veel bewegende onderdelen. Monitors bieden gegevens waarmee u ervoor kunt zorgen dat uw toepassingen in een goede staat blijven werken en worden uitgevoerd. Monitors helpen u om potentiële problemen te voor komen en eerdere problemen op te lossen. U kunt bewakings gegevens gebruiken om uitgebreid inzicht te krijgen in uw toepassingen. Deze kennis helpt u bij het verbeteren van de prestaties en het onderhoud van toepassingen. Daarnaast kunt u hiermee acties automatiseren die anders hand matig moeten worden uitgevoerd.

Azure Monitor biedt metrische gegevens van de infra structuur op basis niveau, waarschuwingen en logboeken voor de meeste Azure-Services. Diagnostische logboeken van Azure worden verzonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die resource. Met Azure Synapse Analytics kunt u Diagnostische logboeken in Azure Monitor schrijven.

Zie [Overzicht van Azure Monitor](../../azure-monitor/overview.md) voor meer informatie.

## <a name="metrics"></a>Metrische gegevens

Met monitor kunt u inzicht krijgen in de prestaties en status van uw Azure-workloads. Het belangrijkste type monitor gegevens is de metriek. dit wordt ook wel het prestatie meter item genoemd. Metrische gegevens worden verzonden door de meeste Azure-resources. Monitor biedt verschillende manieren om deze metrische gegevens te configureren en te gebruiken voor bewaking en probleem oplossing.

Als u toegang wilt krijgen tot deze metrische gegevens, voltooit u de instructies in [Azure monitor data platform](../../azure-monitor/data-platform.md).

### <a name="workspace-level-metrics"></a>Metrische gegevens op werkruimte niveau

Hier volgen enkele van de metrische gegevens die worden verzonden met werk ruimten:

| **Meting**                           | **Categorie meet waarde, weergave naam**                  | **Eenheid** | **Aggregatie typen** | **Beschrijving**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | De metrische gegevens voor uitvoering van activiteiten                     | Count    | Sum (standaard), Count                | Het totale aantal uitgevoerde activiteiten in een venster van één minuut. </br></br> Gebruik de dimensie resultaat van deze metriek om te filteren op geslaagd, mislukt of eind status geannuleerd.|
| IntegrationPipelineRunsEnded         | De metrische gegevens van de pijplijn uitvoering                     | Count    | Sum (standaard), Count                | Het totale aantal uitvoeringen van de pijp lijn dat in een venster van één minuut is opgetreden/beëindigd. </br></br> Gebruik de dimensie resultaat van deze metriek om te filteren op geslaagd, mislukt of eind status geannuleerd. |
| IntegrationTriggerRunsEnded          | Integratie, metrische gegevens voor triggers uitvoeren                      | Count    | Sum (standaard), Count                | Het totale aantal uitgevoerde triggers in een venster van één minuut. </br></br> Gebruik de dimensie resultaat van deze metriek om te filteren op geslaagd, mislukt of eind status geannuleerd. |
| BuiltinSqlPoolDataProcessedBytes     | Ingebouwde SQL-groep, verwerkte gegevens (bytes) | Byte | Sum (standaard) | De hoeveelheid gegevens die wordt verwerkt door de ingebouwde serverloze SQL-groep. |
| BuiltinSqlPoolLoginAttempts          | Ingebouwde SQL-groep, aanmeldings pogingen | Count | Sum (standaard) | Aantal aanmeldings pogingen voor de ingebouwde serverloze SQL-groep. |
| BuiltinSqlPoolDataRequestsEnded      | Ingebouwde SQL-groep, aanvragen beëindigd (bytes) | Count | Sum (standaard) | Aantal beëindigde SQL-aanvragen voor de ingebouwde serverloze SQL-groep. </br></br> Gebruik de dimensie resultaat van deze metriek om te filteren op eind status. |

### <a name="dedicated-sql-pool-metrics"></a>Metrische gegevens van toegewezen SQL-groep

Hier volgen enkele van de metrische gegevens die worden verzonden door toegewezen SQL-groepen:

| **Meting**                           | **Weergavenaam**                  | **Eenheid** | **Aggregatie typen** | **Beschrijving**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | Limiet voor DWU                       | Count   | Max (standaard waarde), min, Gem | Geconfigureerde grootte van de SQL-groep |
| DWUUsed                             | DWU gebruikt                        | Count   | Max (standaard waarde), min, Gem | Vertegenwoordigt een weer gave op hoog niveau van het gebruik in de SQL-groep. Gemeten met DWU-limiet * DWU percentage |
| DWUUsedPercent                      | Percentage gebruikt DWU             | Percentage | Max (standaard waarde), min, Gem | Vertegenwoordigt een weer gave op hoog niveau van het gebruik in de SQL-groep. Gemeten door het maximum te nemen tussen het CPU-percentage en het IO-percentage voor gegevens |
| ConnectionsBlockedByFirewall        | Verbindingen geblokkeerd door Firewall | Count   | Sum (standaard)   | Het aantal verbindingen dat wordt geblokkeerd door firewall regels. Ga naar het toegangs beheer beleid voor uw SQL-groep en controleer deze verbindingen als het aantal hoog is |
| AdaptiveCacheHitPercent             | Treffer percentage van adaptieve cache   | Percentage | Max (standaard waarde), min, Gem | Meet hoe goed werk belastingen gebruikmaken van de adaptieve cache. Gebruik deze metriek met de metrische gegevens in het cache geheugen om te bepalen of u een extra capaciteit wilt schalen of de werk belasting opnieuw wilt uitvoeren om de cache te hydraten |
| AdaptiveCacheUsedPercent            | Percentage van adaptief cache gebruik  | Percentage | Max (standaard waarde), min, Gem | Meet hoe goed werk belastingen gebruikmaken van de adaptieve cache. Gebruik deze metriek met de waarde voor het gebruikte percentage van het cache geheugen om te bepalen of u wilt schalen op extra capaciteit of werk belastingen opnieuw wilt uitvoeren voor de cache |
| LocalTempDBUsedPercent              | Percentage lokaal gebruikte tempdb    | Percentage | Max (standaard waarde), min, Gem | Lokaal TempDB-gebruik voor alle reken knooppunten: de waarden worden elke vijf minuten verzonden |
| MemoryUsedPercent                   | Percentage gebruikt geheugen          | Percentage | Max (standaard waarde), min, Gem | Geheugen gebruik op alle knoop punten in de SQL-groep |
| CPUPercent                          | Percentage gebruikt CPU             | Percentage | Max (standaard waarde), min, Gem | CPU-gebruik in alle knoop punten in de SQL-groep |
| Verbindingen                         | Verbindingen                     | Count   | Sum (standaard)  | Totaal aantal aanmeldingen bij de SQL-groep |
| ActiveQueries                      | Actieve query's                 | Count   | Sum (standaard)   | De actieve query's. Als u deze metrische waarde onfilterd en ongesplitst gebruikt, worden alle actieve query's weer gegeven die worden uitgevoerd op het systeem |
| QueuedQueries                      | Query's in de wachtrij                  | Count   | Sum (standaard)   | Cumulatief aantal aanvragen dat in de wachtrij is geplaatst nadat de limiet voor het maximum aantal gelijktijdig is bereikt |
| WLGActiveQueries                   | Actieve query's voor werkbelasting groep   | Count   | Sum (standaard)   | De actieve query's binnen de werkbelasting groep. Als u deze metrische waarde onfilterd en ongesplitst gebruikt, worden alle actieve query's weer gegeven die worden uitgevoerd op het systeem |
| WLGActiveQueriesTimeouts           | Time-outs query werkbelasting groep   | Count   | Sum (standaard)   | Query's voor de werkbelasting groep waarvoor een time-out is opgetreden. Querytime-time-outs die door deze metrische gegevens worden gerapporteerd, worden slechts eenmaal gestart nadat de query is uitgevoerd (de wacht tijd is niet opgenomen als gevolg van vergren deling of wachten op een resource) |
| WLGQueuedQueries                   | Werkbelasting groep in wachtrij geplaatste query's   | Count   | Sum (standaard)   | Cumulatief aantal aanvragen dat in de wachtrij is geplaatst nadat de limiet voor het maximum aantal gelijktijdig is bereikt |
| WLGAllocationBySystemPercent        | Toewijzing van werkbelasting groep per systeem percentage | Percentage | Max (standaard), min, Gem, Sum | Het percentage toewijzing van resources ten opzichte van het hele systeem |
| WLGAllocationByEffectiveCapResourcePercent   | Toewijzing van werkbelasting groep op Maxi maal resource percentage | Percentage | Max (standaard waarde), min, Gem | Geeft de percentage toewijzing van resources ten opzichte van het resource percentage van de effectief cap per werkbelasting groep. Deze metrische gegevens bieden het effectief gebruik van de werkbelasting groep |
| WLGEffectiveCapResourcePercent      | Effectief cap-resource percentage  | Percentage | Max (standaard waarde), min, Gem | Het werkelijke Cap-resource percentage voor de werkbelasting groep. Als er andere werkbelasting groepen zijn met min_percentage_resource > 0, wordt de effective_cap_percentage_resource proportioneel verlaagd |
| WLGEffectiveMinResourcePercent      | Effectief min resource percentage  | Percentage | Max (standaard), min, Gem, Sum | De instelling van het werkelijke minimale resource percentage dat is toegestaan voor het beschouwen van het service niveau en de instellingen van de werkbelasting groep. De effectief min_percentage_resource kan hoger worden aangepast op lagere service niveaus |

### <a name="apache-spark-pool-metrics"></a>Metrische gegevens van Apache Spark-groep

Hier volgen enkele van de metrische gegevens die worden verzonden door Apache Spark Pools:

| **Meting**                           | **Categorie meet waarde, weergave naam**                  | **Eenheid** | **Aggregatie typen** | **Beschrijving**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Beëindigde Apache Spark toepassingen  | Count | Sum (standaard) | Aantal beëindigde Apache Spark groeps toepassingen |
| BigDataPoolAllocatedCores     | Aantal vCores dat aan de Apache Spark pool is toegewezen                 | Count | Max (standaard waarde), min, Gem | Toegewezen vCores voor een Apache Spark groep |
| BigDataPoolAllocatedMemory    | Hoeveelheid geheugen (GB) die aan de Apache Spark pool is toegewezen            | Count | Max (standaard waarde), min, Gem | Toegewezen geheugen voor Apache Spark pool (GB) |
| BigDataPoolApplicationsActive | Actieve Apache Spark-toepassingen | Count | Max (standaard waarde), min, Gem | Aantal actieve Apache Spark groeps toepassingen |

## <a name="alerts"></a>Waarschuwingen

Meld u aan bij de Azure Portal en selecteer **monitor**  >  **waarschuwingen** om waarschuwingen te maken.

### <a name="create-alerts"></a>Waarschuwingen maken

1. Selecteer **+ nieuwe waarschuwings regel** om een nieuwe waarschuwing te maken.

1. Definieer de **waarschuwings voorwaarde** die moet worden opgegeven wanneer de waarschuwing moet worden geactiveerd.

    > [!NOTE]
    > Zorg ervoor dat u **alle** selecteert in de vervolg keuzelijst **filteren op resource type** .

1. Definieer de **Details** van de waarschuwing om op te geven hoe de waarschuwing moet worden geconfigureerd.

1. Definieer de **actie groep** om te bepalen wie de waarschuwingen moet ontvangen (en hoe).

## <a name="logs"></a>Logboeken

### <a name="workspace-level-logs"></a>Logboeken op werkruimte niveau

Dit zijn de logboeken die worden gegenereerd door Azure Synapse Analytics-werk ruimten:

| Naam van Log Analytics tabel | Naam van logboek categorie                 | Description |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | API-aanvragen van Azure Synapse gateway. |
| SynapseRbacOperations         | SynapseRbacOperations          | Azure Synapse (SRBAC)-bewerkingen op basis van op rollen gebaseerde toegangs beheer. |

### <a name="dedicated-sql-pool-logs"></a>Exclusieve logboeken van de SQL-groep

Dit zijn de logboeken die worden gegenereerd door toegewezen SQL-groepen:

| Naam van Log Analytics tabel        | Naam van logboek categorie             | Description |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Informatie over SQL-aanvragen/-query's in een door Azure Synapse toegewezen SQL-groep.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Informatie over werk nemers die de DMS-stappen in een door Azure Synapse toegewezen SQL-pool volt ooien.
| SynapseSqlPoolRequestSteps  | RequestSteps | Informatie over de stappen van een bepaalde SQL-aanvraag/-query in een door Azure Synapse toegewezen SQL-groep.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Informatie over query distributies van de stappen van SQL-aanvragen/-query's in een door Azure Synapse toegewezen SQL-groep.
| SynapseSqlPoolWaits         | Wacht        | Informatie over de wachtende statussen tijdens het uitvoeren van een SQL-aanvraag/-query in een door Azure Synapse toegewezen SQL-pool, met inbegrip van vergren delingen en wacht rijen op verzen ding.

Raadpleeg de volgende informatie voor meer informatie over deze logboeken:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Groeps logboek Apache Spark

Hier volgt het logboek dat wordt verzonden door Apache Spark Pools:

| Naam van Log Analytics tabel               | Naam van logboek categorie              | Description                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Informatie over beëindigde Apache Spark-toepassingen |

### <a name="diagnostic-settings"></a>Diagnostische instellingen

Diagnostische instellingen gebruiken voor het configureren van Diagnostische logboeken voor niet-reken resources. De instellingen voor een resource-besturings element hebben de volgende kenmerken:

* Ze geven aan waar Diagnostische logboeken worden verzonden. Voor beelden zijn onder andere een Azure-opslag account, een Azure Event Hub of controle Logboeken.
* Ze geven aan welke logboek categorieën worden verzonden.
* Ze geven op hoe lang elke logboek categorie moet worden bewaard in een opslag account.
* Een waarde van nul voor de optie Retentie houdt in dat logboeken permanent worden bewaard. Anders kan de waarde een wille keurig aantal dagen van 1 tot en met 2.147.483.647 zijn.
* Als het Bewaar beleid is ingesteld, maar logboeken opslaat in een opslag account is uitgeschakeld, heeft het Bewaar beleid geen effect. Dit probleem kan bijvoorbeeld optreden wanneer alleen de opties Event Hubs of controle logboeken zijn geselecteerd.
* Bewaar beleid wordt per dag toegepast. De grens tussen dagen vindt plaats om middernacht Coordinated Universal Time (UTC). Aan het einde van de dag worden logboeken van dagen die het Bewaar beleid overschrijden, verwijderd. Als u bijvoorbeeld een Bewaar beleid van één dag hebt, wordt aan het begin van de huidige datum de logboeken van vóór gisteren verwijderd.

Met Azure Monitor Diagnostische instellingen kunt u Diagnostische logboeken voor analyse naar meerdere verschillende doelen routeren.

* **Opslag account**: Sla uw Diagnostische logboeken op in een opslag account voor controle of hand matige inspectie. U kunt de diagnostische instellingen gebruiken om de Bewaar tijd in dagen op te geven.
* **Event hub**: de logboeken naar Azure Event hubs streamen. De logboeken worden ingevoerd in een partner service/aangepaste analyse oplossing, zoals Power BI.
* **Log Analytics-werk ruimte**: Analyseer de logboeken met log Analytics. De Azure Synapse-integratie met Log Analytics is handig in de volgende scenario's:
  * U wilt complexe query's schrijven naar een uitgebreide set metrische gegevens die worden gepubliceerd door Azure Synapse naar Log Analytics. U kunt aangepaste waarschuwingen voor deze query's maken via Azure Monitor.
  * U wilt bewaken in werk ruimten. U kunt gegevens van meerdere werk ruimten naar een enkele Log Analytics-werk ruimte routeren.

U kunt ook een opslag account of event hub-naam ruimte gebruiken die niet voor komt in het abonnement van de resource waarmee logboeken worden verzonden. De gebruiker die de instelling configureert, moet beschikken over het juiste toegangs beheer (Azure RBAC) voor op rollen gebaseerde toegang tot beide abonnementen.

#### <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

Diagnostische instellingen voor uw werk ruimte, toegewezen SQL-groep of Apache Spark groep maken of toevoegen.

1. Ga in de portal naar monitor. Instellingen **voor**  >  **Diagnostische** instellingen selecteren.

1. Selecteer de Synapse-werk ruimte, de exclusieve SQL-groep of de Apache Spark groep waarvoor u een diagnostische instelling wilt maken.

1. Als er geen diagnostische instellingen in de geselecteerde werk ruimte voor komen, wordt u gevraagd een instelling te maken. Selecteer **Diagnostische gegevens inschakelen**.

   Als er bestaande Diagnostische instellingen in de werk ruimte aanwezig zijn, ziet u een lijst met instellingen die al zijn geconfigureerd voor de resource. Selecteer **Diagnostische instellingen toevoegen**.

1. Geef een naam op voor de instelling, selecteer **verzenden naar log Analytics** en selecteer een werk ruimte in **log Analytics werk ruimte**.

    > [!NOTE]
    > Omdat een Azure-logboek tabel niet meer dan 500 kolommen kan bevatten, raden we u **ten zeerste** aan de _resource-specifieke modus_ te selecteren. Zie [AzureDiagnostics-logboeken](/azure/azure-monitor/reference/tables/azurediagnostics)voor meer informatie.

1. Selecteer **Opslaan**.

Na enkele ogen blikken wordt de nieuwe instelling weer gegeven in de lijst met instellingen voor uw werk ruimte, toegewezen SQL-groep of Apache Spark groep. Diagnostische logboeken worden gestreamd naar die werk ruimte zodra er nieuwe gebeurtenis gegevens worden gegenereerd. Maxi maal 15 minuten kan verstrijken tussen het moment dat een gebeurtenis wordt verzonden en wanneer deze wordt weer gegeven in Log Analytics.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken van pijplijn uitvoeringen de [pipeline-uitvoeringen in Synapse Studio](how-to-monitor-pipeline-runs.md) -artikel. 

Zie voor meer informatie over het bewaken van Apache Spark toepassingen het artikel [Apache Spark toepassingen bewaken in Synapse Studio](apache-spark-applications.md) .

Zie het artikel [SQL-aanvragen bewaken in Synapse Studio](how-to-monitor-sql-requests.md) voor meer informatie over het bewaken van SQL-aanvragen.
