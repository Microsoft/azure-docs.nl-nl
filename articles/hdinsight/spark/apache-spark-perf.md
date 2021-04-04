---
title: Spark-taken optimaliseren voor prestaties-Azure HDInsight
description: Algemene strategieën weer geven voor de beste prestaties van Apache Spark clusters in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 567fc2637538408d9727d07d3185a5b0e3cf20c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929940"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark-taken in HDInsight optimaliseren

Dit artikel bevat een overzicht van strategieën voor het optimaliseren van Apache Spark taken in azure HDInsight.

## <a name="overview"></a>Overzicht

De prestaties van uw Apache Spark-taken zijn afhankelijk van meerdere factoren. Deze prestatie factoren zijn onder andere: hoe uw gegevens worden opgeslagen, hoe het cluster is geconfigureerd en welke bewerkingen worden gebruikt bij het verwerken van de gegevens.

Veelvoorkomende problemen zijn onder andere: geheugen beperkingen vanwege onjuist gemaate uitvoeringen, langlopende bewerkingen en taken die leiden tot Cartesische bewerkingen.

Er zijn ook veel optimalisaties die u kunnen helpen bij het oplossen van deze uitdagingen, zoals caching, en het toestaan van gegevens scheefheid.

In elk van de volgende artikelen vindt u informatie over verschillende aspecten van Spark-optimalisatie.

* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-data-processing.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)
* [HDInsight-cluster configuratie optimaliseren voor Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Volgende stappen

* [Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight](apache-spark-job-debugging.md)
* [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
* [Apache Spark-instellingen configureren](apache-spark-settings.md)
