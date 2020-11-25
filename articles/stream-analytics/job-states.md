---
title: Azure Stream Analytics taakstatussen
description: In dit artikel worden de vier verschillende statussen van een Stream Analytics taak beschreven. uitvoeren, gestopt, gedegradeerd en mislukt.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 008493c6eb8840752be3815d30a5df5bad42aff2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023854"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics taakstatussen

Een Stream Analytics taak kan op elk gewenst moment een van de volgende vier statussen hebben: actief, gestopt, gedegradeerd of mislukt. U kunt de status van uw taak vinden op de overzichts pagina van uw Stream Analytics-taak in de Azure Portal. 

| Status | Beschrijving | Aanbevolen acties |
| --- | --- | --- |
| **Wordt uitgevoerd** | Uw taak wordt uitgevoerd op Azure-Lees gebeurtenissen die afkomstig zijn van de gedefinieerde invoer bronnen, de verwerking ervan en het schrijven van de resultaten naar de geconfigureerde uitvoer-Sinks. | Het is een best practice om de prestaties van uw taken te volgen door de [belangrijkste metrische gegevens](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)te controleren. |
| **Gestopt** | De taak is gestopt en verwerkt geen gebeurtenissen. | NA | 
| **Verminderd beschikbaar** | Er zijn mogelijk onregelmatige problemen met uw invoer-en uitvoer verbindingen. Deze fouten worden tijdelijke fouten genoemd, waardoor uw taak een gedegradeerde status kan geven. Stream Analytics probeert deze fouten onmiddellijk te herstellen en terug te keren naar een actieve status (binnen enkele minuten). Deze fouten kunnen zich voordoen als gevolg van netwerk problemen, Beschik baarheid van andere Azure-resources, fouten bij deserialisatie, enzovoort. De prestaties van uw taak kunnen van invloed zijn wanneer de taak een gedegradeerde status heeft.| U kunt de [diagnostische of activiteiten logboeken](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) bekijken voor meer informatie over de oorzaak van deze tijdelijke fouten. In gevallen zoals het deserialiseren van fouten, is het raadzaam om corrigerende maat regelen te nemen om ervoor te zorgen dat gebeurtenissen niet misvormd zijn. Als de taak de limiet voor het resource gebruik blijft bereiken, kunt u het SU-nummer of de [parallelliseren van uw taak](./stream-analytics-parallelization.md)verhogen. In andere gevallen waarin u geen actie kunt ondernemen, probeert Stream Analytics de status wordt *uitgevoerd* te herstellen. <br> U kunt de metrische gegevens van [watermerk vertraging](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) gebruiken om te begrijpen of deze tijdelijke fouten van invloed zijn op de prestaties van uw taak.|
| **Mislukt** | Er is een kritieke fout opgetreden in uw taak, wat resulteert in een mislukte status. Gebeurtenissen worden niet gelezen en verwerkt. Runtime-fouten zijn een veelvoorkomende oorzaak voor taken die eindigen op een mislukte status. | U kunt [waarschuwingen configureren](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) zodat u wordt gewaarschuwd wanneer de status van de taak wordt mislukt. <br> <br>U kunt fouten opsporen met behulp van [activiteiten en bron logboeken](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) om de hoofd oorzaak te bepalen en het probleem op te lossen.|

## <a name="next-steps"></a>Volgende stappen
* [Installatie waarschuwingen voor Azure Stream Analytics taken](stream-analytics-set-up-alerts.md)
* [Metrische gegevens die beschikbaar zijn in Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [Problemen oplossen met activiteiten-en resource logboeken](./stream-analytics-job-diagnostic-logs.md)