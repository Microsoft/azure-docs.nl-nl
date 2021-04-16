---
title: Metrische gegevens, waarschuwingen en diagnostische logboeken
description: Registreer en analyseer diagnostische logboekgebeurtenissen voor Azure Batch accountbronnen zoals pools en taken.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 61aaca84b609aaf7513c6de6f0f7e73aef5a5efe
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389312"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metrische gegevens, waarschuwingen en logboeken in Batch voor diagnostische evaluatie en bewaking

Azure Monitor verzamelt [metrische gegevens en](../azure-monitor/essentials/data-platform-metrics.md) [diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md) voor resources in uw Azure Batch account.

U kunt deze gegevens op verschillende manieren verzamelen en gebruiken om uw Batch-account te bewaken en problemen vast te stellen. U kunt ook waarschuwingen [voor metrische gegevens configureren,](../azure-monitor/alerts/alerts-overview.md) zodat u meldingen ontvangt wanneer een metrische waarde een opgegeven waarde bereikt.

## <a name="batch-metrics"></a>Metrische batchgegevens

[Metrische](../azure-monitor/essentials/data-platform-metrics.md)  gegevens zijn Azure-telemetriegegevens (ook wel prestatiemeters genoemd) die worden uitgezonden door uw Azure-resources en worden gebruikt door de Azure Monitor service. Voorbeelden van metrische gegevens in een Batch-account zijn gebeurtenissen voor het maken van een pool, Low-Priority aantal knooppunten en gebeurtenissen voor het voltooien van taken. Deze metrische gegevens kunnen helpen bij het identificeren van trends en kunnen worden gebruikt voor gegevensanalyse.

Zie de lijst [met ondersteunde metrische Batch-gegevens.](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)

Metrische gegevens zijn:

- Standaard ingeschakeld in elk Batch-account zonder aanvullende configuratie
- Elke minuut gegenereerd
- Niet automatisch persistent, maar wel een doorrolgeschiedenis van 30 dagen. U kunt metrische gegevens van activiteiten persistent maken als onderdeel van diagnostische logboekregistratie.

## <a name="view-batch-metrics"></a>Metrische gegevens van Batch weergeven

In de Azure Portal pagina **Overzicht** voor het Batch-account worden standaard belangrijke metrische gegevens voor knooppunten, kernen en taken weergegeven.

Aanvullende metrische gegevens voor een Batch-account weergeven:

1. Selecteer in Azure Portal **Batch-accounts alle services**  >  en selecteer vervolgens de naam van uw Batch-account.
1. Selecteer **Metrische gegevens** onder **Bewaking**.
1. Selecteer **Metrische waarde toevoegen** en kies vervolgens een metrische waarde in de vervolgkeuzelijst.
1. Selecteer een **aggregatieoptie** voor de metrische gegevens. Gebruik de aggregatie **Gem.** voor metrische gegevens op basis van count (zoals 'Dedicated Core Count' of 'Low-Priority Node Count'). Gebruik voor metrische gegevens op basis van gebeurtenissen (zoals 'Pool Resize Complete Events' )de aggregatie **Count**. Vermijd het gebruik **van de** aggregatie Som, waarmee de waarden worden opgeteld van alle gegevenspunten die gedurende de periode van de grafiek zijn ontvangen.
1. Herhaal stap 3 en 4 om aanvullende metrische gegevens toe te voegen.

U kunt metrische gegevens ook programmatisch ophalen met de Azure Monitor API's. Zie Metrische gegevens ophalen Azure Monitor [.NET voor een voorbeeld.](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/)

> [!NOTE]
> Metrische gegevens die in de afgelopen 3 minuten zijn ingediend, kunnen nog steeds worden aggregeren, zodat de waarden in deze periode mogelijk te kort worden gerapporteerd. Levering van metrische gegevens wordt niet gegarandeerd en kan worden beïnvloed door levering buiten de order, gegevensverlies of duplicatie.

## <a name="batch-metric-alerts"></a>Batchwaarschuwingen voor metrische gegevens

U kunt bijna realtime waarschuwingen voor metrische gegevens configureren die worden activeren wanneer de waarde van een opgegeven metrische waarde een drempelwaarde overschrijdt die u toewijst. De waarschuwing genereert een melding wanneer de waarschuwing wordt geactiveerd (wanneer de drempelwaarde wordt overschreden en aan de waarschuwingsvoorwaarde wordt voldaan) en wanneer deze is opgelost (wanneer de drempelwaarde opnieuw wordt overschreden en niet meer aan de voorwaarde wordt voldaan).

Omdat de levering van metrische gegevens kan worden veroorzaakt door inconsistenties, zoals levering buiten de bestelling, gegevensverlies of duplicatie, raden we u aan waarschuwingen te vermijden die worden uitgevoerd op één gegevenspunt. Gebruik in plaats daarvan drempelwaarden om rekening te houden met inconsistenties, zoals levering buiten de bestelling, gegevensverlies en duplicatie gedurende een bepaalde periode.

U kunt bijvoorbeeld een waarschuwing voor metrische gegevens configureren wanneer het aantal kernen met lage prioriteit op een bepaald niveau valt, zodat u de samenstelling van uw pools kunt aanpassen. Voor de beste resultaten stelt u een periode van 10 of meer minuten in, waarbij de waarschuwing wordt geactiveerd als het gemiddelde aantal kernen met lage prioriteit onder de drempelwaarde voor de hele periode valt. Hierdoor kunnen metrische gegevens worden samengevoegd, zodat u nauwkeurigere resultaten krijgt.

Een waarschuwing voor metrische gegevens configureren in de Azure Portal:

1. Selecteer **Alle services** > **Batch-accounts** en selecteer vervolgens de naam van uw Batch-account.
1. Selecteer **onder Bewaking** de optie **Waarschuwingen** en selecteer vervolgens **Nieuwe waarschuwingsregel.**
1. Selecteer **Voorwaarde toevoegen** en kies vervolgens een metriek.
1. Selecteer de gewenste waarden **voor Grafiekperiode,** **Drempelwaarde,** **Operator** en **Aggregatietype.**
1. Voer een **drempelwaarde in** en selecteer de **eenheid** voor de drempelwaarde.  Selecteer vervolgens **Done**.
1. Voeg een [actiegroep toe](../azure-monitor/alerts/action-groups.md) aan de waarschuwing door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.
1. Voer in **de sectie Details waarschuwingsregel** de naam van **een waarschuwingsregel en** beschrijving **in.** Als u wilt dat de waarschuwing onmiddellijk wordt ingeschakeld, controleert u of het selectievakje Waarschuwingsregel **inschakelen** bij het maken is ingeschakeld.
1. Selecteer **Waarschuwingsregel maken**.

Zie Begrijpen hoe waarschuwingen voor metrische gegevens werken [in Azure Monitor](../azure-monitor/alerts/alerts-metric-overview.md) en Metrische waarschuwingen maken, weergeven en beheren met behulp van Azure Monitor voor meer informatie over het maken van waarschuwingen [voor metrische Azure Monitor.](../azure-monitor/alerts/alerts-metric.md)

U kunt ook een bijna realtime-waarschuwing configureren met behulp van [de Azure Monitor REST API](/rest/api/monitor/). Zie Overzicht van waarschuwingen [in](../azure-monitor/alerts/alerts-overview.md)Microsoft Azure. Zie Reageren op gebeurtenissen met Azure Monitor Waarschuwingen als u taak-, taak- of poolspecifieke informatie in uw waarschuwingen [wilt opnemen.](../azure-monitor/alerts/tutorial-response.md)

## <a name="batch-diagnostics"></a>Batch-diagnose

[Diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md) bevatten informatie die wordt uitgezonden door Azure-resources die de werking van elke resource beschrijven. Voor Batch kunt u de volgende logboeken verzamelen:

- **ServiceLog:** gebeurtenissen die tijdens de levensduur van een afzonderlijke resource, zoals een pool of taak, door de [Batch-service](#service-log-events) worden ingediend.
- **AllMetrics:** Metrische gegevens op Batch-accountniveau.

U moet expliciet diagnostische instellingen inschakelen voor elk Batch-account dat u wilt bewaken.

### <a name="log-destination-options"></a>Opties voor logboekbestemming

Een veelvoorkomende situatie is het selecteren van Azure Storage account als de logboekbestemming. Als u logboeken wilt opslaan in Azure Storage, maakt u het account voordat u het verzamelen van logboeken inschakelen. Als u een opslagaccount aan uw Batch-account hebt gekoppeld, kunt u dat account als logboekbestemming kiezen.

U kunt ook het volgende doen:

- Diagnostische logboekgebeurtenissen van Batch streamen naar [een Azure Event Hub](../event-hubs/event-hubs-about.md). Event Hubs kunt miljoenen gebeurtenissen per seconde opnemen, die u vervolgens kunt transformeren en opslaan met behulp van elke realtime analyseprovider.
- Verzend diagnostische [logboeken naar Azure Monitor logboeken,](../azure-monitor/logs/log-query-overview.md)waar u ze kunt analyseren of exporteren voor analyse in Power BI of Excel.

> [!NOTE]
> Er kunnen extra kosten in rekening worden brengen voor het opslaan of verwerken van diagnostische logboekgegevens met Azure-services.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Het verzamelen van diagnostische Batch-logboeken inschakelen

Volg de onderstaande stappen om een nieuwe diagnostische Azure Portal te maken in de lijst met diagnostische gegevens.

1. Selecteer in Azure Portal Alle **services**  >  **Batch-accounts** en selecteer vervolgens de naam van uw Batch-account.
2. Selecteer **Diagnostische instellingen** onder **Controle**.
3. Selecteer **diagnostische instelling** toevoegen **in Diagnostische instellingen.**
4. Voer een naam in voor de instelling.
5. Selecteer een doel: **Verzenden naar Log Analytics,** **Archiveren naar een opslagaccount** of **Streamen naar een Event Hub.** Als u een opslagaccount selecteert, kunt u desgewenst het aantal dagen selecteren dat gegevens voor elk logboek moeten worden bewaard. Als u geen aantal dagen voor retentie opgeeft, worden gegevens bewaard tijdens de levensduur van het opslagaccount.
6. Selecteer **ServiceLog,** **AllMetrics** of beide.
7. Selecteer **Opslaan om** de diagnostische instelling te maken.

U kunt logboekverzameling ook inschakelen door diagnostische instellingen te maken in de [Azure Portal,](../azure-monitor/essentials/diagnostic-settings.md)met behulp van een [Resource Manager-sjabloon](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)of met behulp van Azure PowerShell of de Azure CLI. Zie Overzicht van [Azure-platformlogboeken voor meer informatie.](../azure-monitor/essentials/platform-logs-overview.md)

### <a name="access-diagnostics-logs-in-storage"></a>Diagnostische logboeken openen in opslag

Als u [diagnostische Batch-logboeken in een opslagaccount](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)archiveren, wordt er een opslagcontainer gemaakt in het opslagaccount zodra er een gerelateerde gebeurtenis optreedt. Blobs worden gemaakt volgens het volgende naamgevingspatroon:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Bijvoorbeeld:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Elk blobbestand bevat gebeurtenissen in JSON-indeling die hebben plaatsgevonden binnen het uur dat is opgegeven in de `PT1H.json` blob-URL (bijvoorbeeld `h=12` ). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het `PT1H.json` bestand wanneer ze optreden. De minuutwaarde ( `m=00` ) is altijd , omdat `00` diagnostische logboekgebeurtenissen worden opgesplitst in afzonderlijke blobs per uur. (Alle tijden zijn in UTC.)

Hieronder vindt u een voorbeeld van `PoolResizeCompleteEvent` een vermelding in een `PT1H.json` logboekbestand. Het bevat informatie over het huidige en doelaantal toegewezen knooppunten en knooppunten met lage prioriteit, evenals de begin- en eindtijd van de bewerking:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Gebruik de Storage-API's om programmatisch toegang te krijgen tot de logboeken in uw opslagaccount.

### <a name="service-log-events"></a>Servicelogboekgebeurtenissen

Azure Batch-servicelogboeken bevatten gebeurtenissen die tijdens de levensduur van een afzonderlijke Batch-resource, zoals een pool of taak, door de Batch-service worden ingediend. Elke gebeurtenis die door Batch wordt uitgezonden, wordt geregistreerd in JSON-indeling. Dit is bijvoorbeeld de body van een gebeurtenis voor het maken **van een voorbeeldpool:**

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Servicelogboekgebeurtenissen die door de Batch-service worden ingediend, zijn onder andere:

- [Pool maken](batch-pool-create-event.md)
- [Begin pool verwijderen](batch-pool-delete-start-event.md)
- [Pool verwijderen voltooid](batch-pool-delete-complete-event.md)
- [Het begin van het pool-resize](batch-pool-resize-start-event.md)
- [Het poolize is voltooid](batch-pool-resize-complete-event.md)
- [Automatisch schalen van pool](batch-pool-autoscale-event.md)
- [Taak starten](batch-task-start-event.md)
- [Taak voltooid](batch-task-complete-event.md)
- [Taak mislukt](batch-task-fail-event.md)
- [Taakplanning mislukt](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
- Meer informatie over het [bewaken van Batch-oplossingen.](monitoring-overview.md)
