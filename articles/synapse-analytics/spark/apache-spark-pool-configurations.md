---
title: Concepten van Apache Spark-groep
description: Inleiding tot de grootte en configuratie van Apache Spark Pools in azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 6422c33f17879aa8ec4844cc6de63411528a388b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606154"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Groeps configuraties Apache Spark in azure Synapse Analytics

Een Spark-pool is een set meta gegevens die de vereisten voor de reken resource en de bijbehorende gedrags kenmerken definieert wanneer een Spark-exemplaar wordt geïnstantieerd. Deze kenmerken omvatten, maar zijn niet beperkt tot de naam, het aantal knoop punten, de knooppunt grootte, het gedrag van de schaal en de tijd tot Live. Een Spark-groep heeft in zichzelf geen resources verbruikt. Er zijn geen kosten verbonden aan het maken van Spark-groepen. Er worden alleen kosten in rekening gebracht wanneer een Spark-taak wordt uitgevoerd op de doel-Spark-pool en de Spark-instantie op aanvraag wordt geïnstantieerd.

In [Get started with Spark pools in Synapse Analytics](../quickstart-create-apache-spark-pool-portal.md) (Aan de slag met Spark-pools in Synapse Analytics) kunt u lezen hoe u een Spark-pool maakt en alle bijbehorende eigenschappen bekijkt

## <a name="nodes"></a>Knooppunten

Apache Spark pool-exemplaar bestaat uit één hoofd knooppunt en twee of meer werk knooppunten met mini maal drie knoop punten in een Spark-exemplaar.  Het hoofd knooppunt voert extra beheer services uit, zoals livy, garen Resource Manager, Zookeeper en het Spark-stuur programma.  Alle knoop punten voeren services uit, zoals knooppunt agent en garen knooppunt beheer. Alle worker-knoop punten voeren de Spark-uitvoerder service uit.

## <a name="node-sizes"></a>Knooppunt grootten

Een Spark-pool kan worden gedefinieerd met knooppunt grootten die variëren van een klein Compute-knoop punt met 8 vCore en 64 GB geheugen tot een XXLarge Compute-knoop punt met 64 vCore en 432 GB geheugen per knoop punt. De grootte van knoop punten kan worden gewijzigd nadat de groep is gemaakt, hoewel het exemplaar mogelijk opnieuw moet worden gestart.

|Grootte | vCore | Geheugen|
|-----|------|-------|
|Klein|4|32 GB|
|Normaal|8|64 GB|
|Groot|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Automatisch schalen

Apache Spark groepen bieden de mogelijkheid om de reken resources automatisch te schalen en neer te zetten op basis van de hoeveelheid activiteit.  Wanneer de functie voor automatisch schalen is ingeschakeld, kunt u het minimum en maximum aantal knoop punten instellen dat moet worden geschaald.
Wanneer de functie voor automatisch schalen is uitgeschakeld, blijft het aantal knoop punten dat is ingesteld vast.  Deze instelling kan worden gewijzigd nadat de groep is gemaakt, hoewel het exemplaar mogelijk opnieuw moet worden gestart.

## <a name="automatic-pause"></a>Automatisch onderbreken

Met de functie voor automatisch onderbreken worden resources vrijgegeven na een ingestelde periode van inactiviteit, waarbij de totale kosten van een Apache Spark groep worden verminderd.  Het aantal minuten van niet-actieve tijd kan worden ingesteld zodra deze functie is ingeschakeld.  De functie voor automatische onderbreking is onafhankelijk van de functie voor automatisch schalen. Resources kunnen worden onderbroken, of automatisch schalen is ingeschakeld of uitgeschakeld.  Deze instelling kan worden gewijzigd nadat de groep is gemaakt, hoewel het exemplaar mogelijk opnieuw moet worden gestart.

## <a name="next-steps"></a>Volgende stappen

* [Azure Synapse Analytics](../index.yml)
* [Documentatie voor Apache Spark](https://spark.apache.org/docs/2.4.5/)