---
title: Systeem variabelen in Azure Data Factory
description: In dit artikel worden de systeem variabelen beschreven die door Azure Data Factory worden ondersteund. U kunt deze variabelen gebruiken in expressies bij het definiëren van Data Factory entiteiten.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 119ecb3ec9c208340f09f513bf10b3ad24312cb5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201223"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systeem variabelen die door Azure Data Factory worden ondersteund

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden de systeem variabelen beschreven die door Azure Data Factory worden ondersteund. U kunt deze variabelen gebruiken in expressies bij het definiëren van Data Factory entiteiten.

## <a name="pipeline-scope"></a>Pijplijn bereik

Naar deze systeem variabelen kan ergens worden verwezen in de JSON van de pijp lijn.

| Naam variabele | Beschrijving |
| --- | --- |
| @pipeline(). DataFactory |De naam van de data factory de pijplijn uitvoering wordt uitgevoerd in |
| @pipeline(). Pijp lijn |Naam van de pijp lijn |
| @pipeline(). RunId |ID van de specifieke pijplijn uitvoering |
| @pipeline(). Trigger type |Het type trigger dat de pijp lijn heeft aangeroepen (bijvoorbeeld `ScheduleTrigger` , `BlobEventsTrigger` ). Voor een lijst met ondersteunde trigger typen raadpleegt u [pijp lijnen uitvoeren en triggers in azure Data Factory](concepts-pipeline-execution-triggers.md). Een trigger type `Manual` geeft aan dat de pijp lijn hand matig is geactiveerd. |
| @pipeline(). TriggerId|ID van de trigger die de pijp lijn heeft aangeroepen |
| @pipeline(). TriggerName|Naam van de trigger die de pijp lijn heeft aangeroepen |
| @pipeline(). TriggerTime|Tijdstip waarop de trigger wordt uitgevoerd en die de pijp lijn heeft aangeroepen. Dit is het tijdstip waarop de trigger **daad werkelijk** is geactiveerd om de pijp lijn uit te voeren en kan enigszins afwijken van de geplande tijd van de trigger.  |

>[!NOTE]
>Trigger-gerelateerde datum/tijd systeem variabelen (in zowel pijp lijn-als trigger bereik) retour neren UTC-datums in ISO 8601-notatie, bijvoorbeeld `2017-06-01T22:20:00.4061448Z` .

## <a name="schedule-trigger-scope"></a>Trigger bereik plannen

Naar deze systeem variabelen kan worden verwezen overal in de trigger JSON voor triggers van het type [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Naam variabele | Beschrijving |
| --- | --- |
| @trigger().scheduledTime |Tijdstip waarop de trigger is gepland voor het aanroepen van de pijplijn uitvoering. |
| @trigger(). startTime |Tijdstip waarop de trigger **daad werkelijk** is gestart om de pijplijn uitvoering aan te roepen. Dit kan enigszins verschillen van de geplande tijd van de trigger. |

## <a name="tumbling-window-trigger-scope"></a>Tumblingvenstertriggers-venster trigger bereik

Naar deze systeem variabelen kan worden verwezen overal in de trigger JSON voor triggers van het type [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Naam variabele | Beschrijving |
| --- | --- |
| @trigger(). outputs. windowStartTime |Begin van het venster dat is gekoppeld aan de uitvoering van de trigger. |
| @trigger(). outputs. windowEndTime |Einde van het venster dat is gekoppeld aan de uitvoering van de trigger. |
| @trigger().scheduledTime |Tijdstip waarop de trigger is gepland voor het aanroepen van de pijplijn uitvoering. |
| @trigger(). startTime |Tijdstip waarop de trigger **daad werkelijk** is gestart om de pijplijn uitvoering aan te roepen. Dit kan enigszins verschillen van de geplande tijd van de trigger. |

## <a name="storage-event-trigger-scope"></a>Bereik van opslag gebeurtenis trigger

Naar deze systeem variabelen kan worden verwezen overal in de trigger JSON voor triggers van het type [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Naam variabele | Beschrijving |
| --- | --- |
| @triggerBody(). fileName  |De naam van het bestand waarvan het maken of verwijderen heeft geleid tot het starten van de trigger.   |
| @triggerBody(). mapnaam  |Pad naar de map die het bestand bevat dat is opgegeven door `@triggerBody().fileName` . Het eerste segment van het mappad is de naam van de Azure Blob Storage-container.  |
| @trigger(). startTime |Tijdstip waarop de trigger is geactiveerd om de pijplijn uitvoering aan te roepen. |

## <a name="next-steps"></a>Volgende stappen

* Zie [expressie language & functions (Engelstalig](control-flow-expression-language-functions.md)) voor meer informatie over hoe deze variabelen worden gebruikt in expressies.
* Zie [Naslag informatie over het activeren van meta gegevens in](how-to-use-trigger-parameterization.md) een pijp lijn om het trigger bereik systeem variabelen in een pijp lijn te gebruiken
