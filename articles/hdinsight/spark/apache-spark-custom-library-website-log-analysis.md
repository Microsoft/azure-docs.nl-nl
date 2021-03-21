---
title: Website logboeken analyseren met python-bibliotheken in Spark-Azure
description: Dit notitie blok laat zien hoe u logboek gegevens kunt analyseren met behulp van een aangepaste bibliotheek met Spark in azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 591804a31c100e5c8211ec6d73fc483c9ac85c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940301"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Website logboeken analyseren met behulp van een aangepaste python-bibliotheek met Apache Spark cluster op HDInsight

Dit notitie blok laat zien hoe u logboek gegevens kunt analyseren met behulp van een aangepaste bibliotheek met Apache Spark op HDInsight. De aangepaste bibliotheek die wordt gebruikt, is een python-bibliotheek met de naam **iislogparser.py**.

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="save-raw-data-as-an-rdd"></a>Onbewerkte gegevens opslaan als een RDD

In deze sectie gebruiken we het [Jupyter](https://jupyter.org) -notebook dat is gekoppeld aan een Apache Spark-cluster in HDInsight om taken uit te voeren die uw onbewerkte voorbeeld gegevens verwerken en deze op te slaan als een Hive-tabel. De voorbeeld gegevens zijn standaard een CSV-bestand (hvac.csv) dat beschikbaar is op alle clusters.

Zodra de gegevens zijn opgeslagen als een Apache Hive tabel, wordt in de volgende sectie verbinding gemaakt met de Hive-tabel met behulp van BI-hulpprogram ma's zoals Power BI en tableau.

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/jupyter`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Maak een nieuwe notebook. Selecteer **Nieuw** en vervolgens **PySpark**.

    ![Een nieuwe Apache-Jupyter Notebook maken](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Een nieuwe Jupyter Notebook maken")

1. Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled.pynb. Selecteer de naam van het notitie blok bovenaan en geef een beschrijvende naam op.

    ![Een naam opgeven voor de notebook](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Een naam opgeven voor de notebook")

1. Omdat u een notitie blok hebt gemaakt met behulp van de PySpark-kernel, hoeft u geen contexten expliciet te maken. De Spark- en Hive-contexten worden automatisch voor u gemaakt tijdens het uitvoeren van de eerste codecel. U kunt beginnen met het importeren van de typen die vereist zijn voor dit scenario. Plak het volgende code fragment in een lege cel en druk op **SHIFT + ENTER**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Maak een RDD met behulp van de voorbeeld logboek gegevens die al beschikbaar zijn op het cluster. U hebt toegang tot de gegevens in het standaard opslag account dat is gekoppeld aan het cluster op `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Voer de volgende code uit:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Haal een voor beeld van een logboekset op om te controleren of de vorige stap is voltooid.

    ```pyspark
    logs.take(5)
    ```

    U ziet een uitvoer die vergelijkbaar is met de volgende tekst:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Logboek gegevens analyseren met behulp van een aangepaste python-bibliotheek

1. In de bovenstaande uitvoer bevatten de eerste paar regels de header-informatie en wordt elke resterende regel overeenkomt met het schema dat in die header wordt beschreven. Het parseren van dergelijke logboeken kan gecompliceerd zijn. Daarom gebruiken we een aangepaste python-bibliotheek (**iislogparser.py**) die het parseren van dergelijke logboeken veel eenvoudiger maakt. Deze bibliotheek is standaard opgenomen in uw Spark-cluster op HDInsight op `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    Deze bibliotheek bevindt zich echter niet in de `PYTHONPATH` daarom kan niet worden gebruikt met behulp van een import instructie zoals `import iislogparser` . Als u deze bibliotheek wilt gebruiken, moeten we deze distribueren naar alle worker-knoop punten. Voer het volgende code fragment uit.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` biedt een functie `parse_log_line` die retourneert `None` of een logboek regel een koprij is en een exemplaar van de `LogLine` klasse retourneert als er een logboek regel wordt aangetroffen. Gebruik de- `LogLine` klasse om alleen de logboek regels uit de RDD te extra heren:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Haal een aantal geëxtraheerde logboek regels op om te controleren of de stap is voltooid.

    ```pyspark
    logLines.take(2)
    ```

   De uitvoer moet er ongeveer uitzien als de volgende tekst:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. De `LogLine` klasse heeft op zijn beurt een aantal handige methoden, zoals `is_error()` , die retourneert of een logboek vermelding een fout code heeft. Gebruik deze klasse voor het berekenen van het aantal fouten in de uitgepakte logboek regels en meld alle fouten vervolgens naar een ander bestand.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    De uitvoer moet de status hebben `There are 30 errors and 646 log entries` .

1. U kunt **matplotlib** ook gebruiken om een visualisatie van de gegevens te maken. Als u bijvoorbeeld de oorzaak van aanvragen wilt isoleren die gedurende een lange periode worden uitgevoerd, kunt u de bestanden die het meest het meest worden gebruikt, voor het eerst vinden. In het onderstaande fragment worden de belangrijkste 25 resources opgehaald die de meeste tijd nodig hebben om een aanvraag te doen.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   U ziet een uitvoer zoals de volgende tekst:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. U kunt deze informatie ook in de vorm van het plot presen teren. Als eerste stap voor het maken van een tekening maakt u eerst een tijdelijke tabel **AverageTime**. De tabel groepeert de logboeken op tijd om te zien of er ongebruikelijke latentie pieken op een bepaald moment zijn.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Vervolgens kunt u de volgende SQL-query uitvoeren om alle records in de **AverageTime** -tabel op te halen.

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   Het `%%sql` Magic wordt gevolgd door `-o averagetime` ervoor te zorgen dat de uitvoer van de query lokaal wordt opgeslagen op de Jupyter-server (doorgaans de hoofd knooppunt van het cluster). De uitvoer wordt persistent gemaakt als een [Panda](https://pandas.pydata.org/) data frame met de opgegeven naam **averagetime**.

   U ziet een uitvoer zoals de volgende afbeelding:

   ![uitvoer van hdinsight jupyter SQL-query](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-query-uitvoer")

   `%%sql`Zie [para meters die worden ondersteund met%% SQL Magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)voor meer informatie over het Magic.

1. U kunt nu matplotlib, een bibliotheek die wordt gebruikt om visualisatie van gegevens te maken, gebruiken om een plot te creëren. Omdat het waarnemings punt moet worden gemaakt van de lokaal blijvende persistente **averagetime** -data frame, moet het code fragment beginnen met het `%%local` Magic. Dit zorgt ervoor dat de code lokaal wordt uitgevoerd op de Jupyter-server.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   U ziet een uitvoer zoals de volgende afbeelding:

   ![analyse diagram voor Apache Spark-Weblogboeken](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib uitvoer")

1. Wanneer u klaar bent met het uitvoeren van de toepassing, moet u het notitie blok afsluiten om de resources vrij te geven. Selecteer hiervoor **Sluiten en stoppen** in het menu **Bestand** van het notebook. Deze actie wordt afgesloten en het notitie blok gesloten.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen:

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)
