---
title: Open-source software ondersteuning in azure HDInsight
description: Microsoft Azure biedt een algemeen ondersteunings niveau voor open-source technologieën.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fec4cff974031982c782c9265a7d3186d6bb0233
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942549"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Open-source software ondersteuning in azure HDInsight

De Microsoft Azure HDInsight-service gebruikt een omgeving van open-source technologieën die zijn gevormd rond Apache Hadoop. Microsoft Azure biedt een algemeen ondersteunings niveau voor open-source technologieën. Zie de sectie **support Scope** van [Veelgestelde vragen over ondersteuning van Azure](https://azure.microsoft.com/support/faq/)voor meer informatie. De HDInsight-service biedt een extra ondersteunings niveau voor ingebouwde componenten.

## <a name="components"></a>Onderdelen

Er zijn twee typen open source-onderdelen beschikbaar in de HDInsight-service:

### <a name="built-in-components"></a>Ingebouwde onderdelen

Deze onderdelen zijn vooraf geïnstalleerd op HDInsight-clusters en bieden kern functionaliteit van het cluster. De volgende onderdelen maken deel uit van deze categorie:

* [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager.
* De [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)van de Hive-query taal.
* [Apache mahout](https://mahout.apache.org/).

Er is een volledige lijst met cluster onderdelen beschikbaar in [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Aangepaste onderdelen

Als gebruiker van het cluster kunt u in uw workload elk onderdeel dat beschikbaar is in de Community installeren of gebruiken, of door u gemaakt.

> [!WARNING]  
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund. Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Microsoft Ondersteuning kunt het probleem mogelijk oplossen. Het kan ook zijn dat u beschik bare kanalen moet betrekken voor de open source technologieën waar diep gaande expertise voor die technologie wordt gevonden. Veel community-sites kunnen worden gebruikt. Voor beelden zijn [micro soft Q&een vraag pagina voor HDInsight](/answers/topics/azure-hdinsight.html) en [stack overflow](https://stackoverflow.com).
>
> Apache-projecten hebben ook project sites op de [Apache-website](https://apache.org). Een voor beeld is [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Onderdeel gebruik

De HDInsight-service biedt verschillende manieren om aangepaste onderdelen te gebruiken. Hetzelfde ondersteunings niveau geldt, ongeacht hoe een onderdeel wordt gebruikt of geïnstalleerd op het cluster. In de volgende tabel worden de meest voorkomende manieren beschreven waarop aangepaste onderdelen worden gebruikt in HDInsight-clusters:

|Gebruik |Beschrijving |
|---|---|
|Indienen van job|Hadoop of andere typen taken die aangepaste onderdelen uitvoeren of gebruiken, kunnen worden verzonden naar het cluster.|
|Cluster aanpassing|Tijdens het maken van het cluster kunt u aanvullende instellingen en aangepaste onderdelen opgeven die op de cluster knooppunten zijn geïnstalleerd.|
|Voorbeelden|Voor populaire aangepaste onderdelen kunnen micro soft en andere voor beelden bieden van hoe deze onderdelen kunnen worden gebruikt in HDInsight-clusters. Deze voor beelden worden zonder ondersteuning geboden.|

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight-clusters aanpassen met behulp van script acties](./hdinsight-hadoop-customize-cluster-linux.md)
* [Script actie scripts voor HDInsight ontwikkelen](hdinsight-hadoop-script-actions-linux.md)
* [Een Python-omgeving veilig beheren in Azure HDInsight met scriptactie](./spark/apache-spark-python-package-installation.md)