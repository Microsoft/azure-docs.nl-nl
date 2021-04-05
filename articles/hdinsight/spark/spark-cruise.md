---
title: SparkCruise gebruiken in azure HDInsight om Apache Spark query's te versnellen
description: Meer informatie over hoe u het SparkCruise-optimalisatie platform kunt gebruiken om de efficiëntie van Apache Spark query's te verbeteren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f07ea3007d2e72c179abe38cca56178b564bc231
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928273"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise op Azure HDInsight

In dit document wordt de functie *SparkCruise* van Azure HDInsight beschreven, waarmee automatisch Apache Spark berekeningen worden gebruikt om de efficiëntie van query's te verhogen.

## <a name="overview"></a>Overzicht

De query's die u uitvoert op een Analytics-platform, zoals Apache Spark, worden uitgesplitst in een query plan dat kleinere subquery's bevat. Deze subquery's kunnen herhaaldelijk worden weer gegeven in query plannen voor meerdere query's. Telkens wanneer ze optreden, worden ze opnieuw uitgevoerd om de resultaten te retour neren. Het opnieuw uitvoeren van dezelfde query kan echter inefficiënt zijn en leiden tot onnodige verwerkings kosten.

*SparkCruise* is een optimalisatie platform voor werk belastingen dat gemeen schappelijke berekeningen kan hergebruiken, de algehele uitvoerings tijd van query's en de kosten voor gegevens overdracht te verminderen. Het platform gebruikt het concept van een *gerealiseerde weer gave*. Dit is een query waarvan de resultaten worden opgeslagen in een vooraf berekende vorm. Deze resultaten kunnen vervolgens opnieuw worden gebruikt wanneer de query zelf opnieuw wordt weer gegeven, in plaats van de resultaten opnieuw te berekenen.

## <a name="setup-and-installation"></a>Instellingen en installatie

SparkCruise is beschikbaar op alle HDInsight 4,0-clusters met Spark 2,3 of 2,4. De SparkCruise-bibliotheek bestanden worden geïnstalleerd in de `/opt/peregrine/` map op uw HDInsight-cluster. Voor een goede werking moet *SparkCruise* de volgende configuratie-eigenschappen hebben, die standaard zijn ingesteld.

* `spark.sql.queryExecutionListeners` is ingesteld op `com.microsoft.peregrine.spark.listeners.PlanLogListener` , waarmee query-abonnementen kunnen worden vastgelegd.
* `spark.sql.extensions` is ingesteld op `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , waarmee de Optimizer regels voor online-materialisatie en hergebruik worden ingeschakeld.

## <a name="computation-reuse-in-spark-sql"></a>Berekeningen opnieuw gebruiken in Spark SQL

Het volgende voorbeeld scenario laat zien hoe u *SparkCruise* kunt gebruiken om Apache Spark query's te optimaliseren. 

1. SSH in het hoofd knooppunt van uw Spark-cluster.
1. Typ `spark-shell`.
1. Voer het volgende code fragment uit, dat enkele eenvoudige query's uitvoert met behulp van voorbeeld gegevens in het cluster.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Gebruik het analyse hulpprogramma *SparkCruise* -query om de query plannen van de vorige query's te analyseren, die zijn opgeslagen in de Spark-toepassings Logboeken. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. De uitvoer van het analyse proces weer geven. Dit is een feedback bestand. Dit feedback bestand bevat aantekeningen voor toekomstige Spark SQL-query's. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

De `analyze` opdracht parseert de query plannen en maakt een tabel weergave van de werk belasting. U kunt een query uitvoeren op de werkbelasting tabel met behulp van het *WorkloadInsights* -notitie blok dat is opgenomen in de opslag plaats voor SparkCruise-voor [beelden](https://github.com/Azure-Samples/azure-sparkcruise-samples) De `views` opdracht identificeert algemene subplan expressies en selecteert interessante subplan expressies voor toekomstige materialisatie en hergebruik. De uitvoer is een feedback bestand met aantekeningen voor toekomstige Spark SQL-query's. 

De `show` opdracht geeft een uitvoer als de volgende tekst:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

Het feedback bestand bevat vermeldingen met de volgende indeling: `subplan-identifier [Materialize|Reuse] input/path/to/action` . In dit voor beeld zijn er twee unieke hand tekeningen: een van de eerste twee herhaalde query's en de tweede die het filter predicaat in de laatste twee query's vertegenwoordigen. Met dit feedback bestand worden de volgende subabonnementen nu automatisch realiseren en opnieuw gebruikt. 

Als u de optimalisaties wilt testen, voert u een andere set voorbeeld query's uit.

1. Typ `spark-shell`.
1. Voer het volgende code fragment uit

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Bekijk het feedback bestand opnieuw om de hand tekeningen van de query's weer te geven die opnieuw zijn gebruikt.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

De `show` opdracht geeft een uitvoer die vergelijkbaar is met de volgende tekst:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Hoewel de letterlijke waarde in de query is gewijzigd van in `'11%'` `'12%'` , kan *SparkCruise* nog steeds overeenkomen met eerdere query's naar nieuwe query's met kleine variaties zoals de evolutie van letterlijke waarden en gegevensset-versies. Als er belang rijke wijzigingen zijn aangebracht in een query, kunt u het analyse hulpprogramma opnieuw uitvoeren om nieuwe query's te identificeren die opnieuw kunnen worden gebruikt.

Achter de schermen wordt met *SparkCruise* een subquery geactiveerd voor materialiseren van de geselecteerde subplan van de eerste query die het bevat. Latere query's kunnen de gerealiseerde subabonnementen rechtstreeks lezen in plaats van ze opnieuw te berekenen. In deze werk belasting worden de subabonnementen op een online manier gerealiseerd door de eerste en derde query's. U kunt de plannings wijziging van query's zien nadat de gemeen schappelijke subabonnementen zijn gerealiseerd.

U kunt zien dat er nu vier nieuwe gerealiseerde subexpressies beschikbaar zijn om opnieuw te worden gebruikt in volgende query's.

## <a name="clean-up"></a>Opschonen

De feedback bestanden, gerealiseerde subabonnementen en query logboeken worden bewaard in Spark-sessies. Als u deze bestanden wilt verwijderen, voert u de volgende opdracht uit:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Volgende stappen

* [De werk belasting Insights-notebook gebruiken om de voor delen van SparkCruise te bepalen](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Verbeter de prestaties van Apache Spark werk belastingen met behulp van de Azure HDInsight IO-cache](apache-spark-improve-performance-iocache.md)
* [Apache Spark-taken in HDInsight optimaliseren](./apache-spark-perf.md)
* [SparkCruise: handsfree berekenen hergebruik in Spark](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Apache Spark richtlijnen voor Azure HDInsight](./spark-best-practices.md)
