---
title: Apache Spark instanties automatisch schalen
description: Gebruik de functie voor automatisch schalen van Azure Synapse om Apache Spark-exemplaren te schalen
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: f34bcfa8b743fbee6ee3b78fc1a042d1df0abfde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93313636"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Automatisch schalen van Azure Synapse Analytics Apache Spark Pools

Met de functie voor automatische schaalaanpassing van Apache Spark for Azure Synapse Analytics-pool wordt het aantal knooppunten in een clusterexemplaar automatisch omhoog en omlaag geschaald. Tijdens het maken van een nieuwe Apache Spark for Azure Synapse Analytics-pool kan een minimum-en maximum aantal knooppunten worden ingesteld wanneer Automatische schaalaanpassing is geselecteerd. Vervolgens worden de resourcevereisten van de belasting bewaakt en wordt het aantal knooppunten automatisch omhoog of omlaag geschaald. Er worden geen extra kosten in rekening gebracht voor deze functie.

## <a name="metrics-monitoring"></a>Bewaking van metrische gegevens

Automatisch schalen bewaakt het Spark-exemplaar voortdurend en verzamelt de volgende metrische gegevens:

|Metrisch|Beschrijving|
|---|---|
|Totale CPU in behandeling|Het totale aantal kern geheugens dat nodig is om de uitvoering van alle knoop punten in behandeling te starten.|
|Totaal geheugen in behandeling|Het totale geheugen (in MB) dat is vereist om de uitvoering van alle knoop punten in behandeling te starten.|
|Totale vrije CPU|De som van alle ongebruikte kernen op de actieve knoop punten.|
|Totaal beschikbaar geheugen|De som van het ongebruikte geheugen (in MB) op de actieve knoop punten.|
|Gebruikt geheugen per knoop punt|De belasting van een knoop punt. Een knoop punt waarop 10 GB geheugen wordt gebruikt, wordt beschouwd als meer belasting dan een werk nemer met 2 GB gebruikt geheugen.|

De bovenstaande metrische gegevens worden elke 30 seconden gecontroleerd. Met automatisch schalen kunt u op basis van deze metrische gegevens schalen en omlaag schalen.

## <a name="load-based-scale-conditions"></a>Schaal voorwaarden op basis van een belasting

Wanneer aan de volgende voor waarden wordt voldaan, wordt door automatisch schalen een schaal aanvraag uitgegeven:

|Omhoog schalen|Omlaag schalen|
|---|---|
|De totale CPU in behandeling is groter dan de totale vrije CPU voor meer dan 1 minuut.|De totale beschik bare CPU is minder dan de totale vrije CPU voor meer dan twee minuten.|
|Totaal geheugen in behandeling is groter dan het totale beschik bare geheugen voor meer dan 1 minuut.|Totaal geheugen in behandeling is minder dan het totale beschik bare geheugen gedurende meer dan twee minuten.|

Voor omhoog schalen berekent de Azure Synapse AutoScale-service hoeveel nieuwe knoop punten er nodig zijn om aan de huidige CPU-en geheugen vereisten te voldoen en geeft vervolgens een opschaal aanvraag uit om het vereiste aantal knoop punten toe te voegen.

Voor opschalen, op basis van het aantal uitvoerende modules, toepassings Masters per knoop punt en de huidige CPU-en geheugen vereisten, wordt door automatisch schalen een aanvraag voor het verwijderen van een bepaald aantal knoop punten uitgegeven. De service detecteert ook welke knoop punten kandidaten zijn voor verwijdering op basis van de huidige taak uitvoering. Met de bewerking omlaag schalen worden de knoop punten eerst buiten gebruik gesteld en vervolgens uit het cluster verwijderd.

## <a name="get-started"></a>Aan de slag

### <a name="create-a-serverless-apache-spark-pool-with-autoscaling"></a>Een Apache Spark groep zonder server maken met automatisch schalen

Als u de functie voor automatisch schalen wilt inschakelen, voert u de volgende stappen uit als onderdeel van het normale proces voor het maken van een pool:

1. Schakel op het tabblad **basis beginselen** het selectie vakje **automatisch schalen inschakelen** in.
1. Voer de gewenste waarden in voor de volgende eigenschappen:  

    * **Min** . aantal knoop punten.
    * Het **maximum** aantal knoop punten.

Het eerste aantal knoop punten is het minimum. Met deze waarde wordt de oorspronkelijke grootte van het exemplaar gedefinieerd wanneer het wordt gemaakt. Het minimum aantal knoop punten kan niet minder dan drie zijn.

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Houd rekening met de latentie van omhoog of omlaag schalen van bewerkingen

Het kan 1 tot vijf minuten duren voordat een schaal bewerking is voltooid.

### <a name="prepare-for-scaling-down"></a>Voorbereiden op omlaag schalen

Tijdens het proces voor het schalen van de instantie worden de knoop punten in de buiten gebruik stellen, zodat er geen nieuwe uitbreiers kunnen worden gestart op het knoop punt.

De actieve taken blijven worden uitgevoerd en voltooid. De taken die in behandeling zijn, wachten om te worden gepland als normaal met minder beschik bare knoop punten.

## <a name="next-steps"></a>Volgende stappen

Snelstartgids voor het instellen van een nieuwe Spark-groep [een Spark-groep maken](../quickstart-create-apache-spark-pool-portal.md)
