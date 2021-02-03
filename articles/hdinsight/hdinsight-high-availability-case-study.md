---
title: Casestudy Azure HDInsight met hoge Beschik baarheid voor oplossings architectuur
description: Dit artikel is een fictieve casestudy van een mogelijke Azure HDInsight-oplossing met hoge Beschik baarheid.
keywords: Hadoop hoge Beschik baarheid
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6b995e2ab5ba663f6e33b009062859eb32928cc1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508588"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Casestudy Azure HDInsight met hoge Beschik baarheid voor oplossings architectuur

De replicatie mechanismen van Azure HDInsight kunnen worden geïntegreerd in een Maxi maal beschik bare oplossings architectuur. In dit artikel wordt een fictieve casestudy voor contoso Retail gebruikt om mogelijke oplossingen voor nood herstel met hoge Beschik baarheid, kosten overwegingen en de bijbehorende ontwerpen uit te leggen.

Aanbevelingen voor nood herstel met hoge Beschik baarheid kunnen veel permutaties en combi Naties hebben. Deze oplossingen moeten worden ontvangen wanneer u de voor-en nadelen van elke optie uitschakelt. In dit artikel wordt slechts één mogelijke oplossing besproken.

## <a name="customer-architecture"></a>Klant architectuur

In de volgende afbeelding ziet u de primaire architectuur van Contoso Retail. De architectuur bestaat uit een streaming-workload, batch-workload, voor het leveren van lagen, verbruiks lagen, opslag lagen en versie beheer.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Retail-architectuur voor contoso":::

### <a name="streaming-workload"></a>Streaming werk belasting

Apparaten en Sens oren produceren gegevens naar HDInsight Kafka, die het berichten raamwerk vormt. An HDInsight Spark-gebruikers lezen van de Kafka-onderwerpen. Spark transformeert de inkomende berichten en schrijft deze naar een HDInsight HBase-cluster op de leverende laag.

### <a name="batch-workload"></a>Batch-workload

An HDInsight Hadoop-cluster waarop Hive en MapReduce worden uitgevoerd, neemt gegevens op uit on-premises transactionele systemen. Onbewerkte gegevens die worden getransformeerd door Hive-en MapReduce, worden opgeslagen in Hive-tabellen op een logische partitie van de data Lake die wordt ondersteund door Azure Data Lake Storage Gen2. Gegevens die zijn opgeslagen in Hive-tabellen, worden ook beschikbaar gesteld voor Spark SQL, wat batch transformeert voordat de gegevens worden opgeslagen die worden gebruikt in HBase.

### <a name="serving-layer"></a>Ondersteunende laag

An HDInsight HBase-cluster met Apache Phoenix wordt gebruikt om gegevens te leveren aan webtoepassingen en visualisatie dashboards. An HDInsight LLAP-cluster wordt gebruikt om te voldoen aan interne rapportage vereisten.

### <a name="consumption-layer"></a>Laag verbruik

Een Azure-API Apps en API Management laag een open bare webpagina weer gegeven. Aan interne rapportage vereisten wordt voldaan door Power BI.

### <a name="storage-layer"></a>Opslaglaag

Logisch gepartitioneerde Azure Data Lake Storage Gen2 wordt gebruikt als ENTER prise data Lake. De HDInsight-meta Stores worden ondersteund door Azure SQL DB.

### <a name="version-control-system"></a>Versie beheersysteem

Een versie beheersysteem dat in een Azure-pijp lijn is geïntegreerd en buiten Azure wordt gehost.

## <a name="customer-business-continuity-requirements"></a>Vereisten voor zakelijke continuïteit van klant

Het is belang rijk om te bepalen wat de minimale bedrijfs functionaliteit is die u nodig hebt als er sprake is van een nood geval.

### <a name="contoso-retails-business-continuity-requirements"></a>Bedrijfs continuïteits vereisten voor contoso

* Wij moeten worden beschermd tegen een regionale fout of een regionale service status probleem.
* Mijn klanten mogen geen 404-fout weer geven. Open bare inhoud moet altijd worden geleverd. (RTO = 0)  
* Voor de meeste deel van het jaar kunnen we open bare inhoud weer geven die met 5 uur verouderd is. (RPO = 5 uur)
* Tijdens het kerst seizoen moet onze openbaar gerichte inhoud altijd up-to-date zijn. (RPO = 0)
* Mijn interne rapportage vereisten worden niet als kritiek beschouwd voor bedrijfs continuïteit.
* Optimaliseer de kosten voor bedrijfs continuïteit.

## <a name="proposed-solution"></a>Voorgestelde oplossing

In de volgende afbeelding ziet u de architectuur van de hoge Beschik baarheid voor nood herstel van contoso.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso-oplossing":::

**Kafka** maakt gebruik van [Active-passieve](hdinsight-business-continuity-architecture.md#apache-kafka) replicatie voor het spie gelen van Kafka-onderwerpen van de primaire regio naar de secundaire regio. Een alternatief voor Kafka-replicatie kan worden geproduceerd naar Kafka in beide regio's.

**Hive en Spark** gebruiken [actieve primaire en secundaire replicatie modellen op aanvraag](hdinsight-business-continuity-architecture.md#apache-spark) tijdens normale tijdstippen. Het Hive-replicatie proces wordt regel matig uitgevoerd en wordt geleverd met de Hive Azure SQL-meta Store en Hive-opslag account replicatie. Het Spark-opslag account wordt periodiek gerepliceerd met behulp van ADF DistCP. De tijdelijke aard van deze clusters helpt de kosten te optimaliseren. Replicaties worden elke 4 uur gepland om aan een RPO te komen die binnen de vereiste van vijf uur goed is.

**HBase** -replicatie maakt gebruik van het model van de [leider – follower](hdinsight-business-continuity-architecture.md#apache-hbase) gedurende een normale periode om ervoor te zorgen dat de gegevens altijd worden verwerkt, ongeacht de regio en de RPO zeer laag is.

Als er sprake is van een regionale fout in de primaire regio, worden de inhoud van de webpagina en de back-end gedurende 5 uur met een zekere mate van veroudering van de secundaire regio bediend. Als het Azure service Health-dash board geen herstel ETA in het venster van vijf uur aangeeft, maakt de retail-handels versie van Contoso de Hive-en Spark-transformatie laag in de secundaire regio en wijst vervolgens alle gegevens bronnen stroomopwaarts naar de secundaire regio. Het maken van de secundaire regio kan leiden tot een failbackproces waarbij replicatie weer wordt uitgevoerd naar de primaire.

Tijdens een seizoen voor piek uren is de volledige secundaire pijp lijn altijd actief en wordt uitgevoerd. Kafka producenten die in beide regio's produceren en de HBase-replicatie zouden worden gewijzigd van Leader-Follower naar Leader-Leader om ervoor te zorgen dat de open bare inhoud altijd up-to-date is.

Er moet geen failover-oplossing worden ontworpen voor interne rapportage omdat deze niet essentieel is voor bedrijfs continuïteit.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de items die in dit artikel worden besproken:

* [Azure HDInsight-bedrijfs continuïteit](./hdinsight-business-continuity.md)
* [Azure HDInsight-architectuur voor bedrijfs continuïteit](./hdinsight-business-continuity-architecture.md)
* [Wat is Apache Hive en HiveQL in azure HDInsight?](./hadoop/hdinsight-use-hive.md)
