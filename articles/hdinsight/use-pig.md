---
title: Apache Pig gebruiken
titleSuffix: Azure HDInsight
description: Meer informatie over het gebruik van Pig met Apache Hadoop op HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7b74a41f7d6b636dddce0388d5ee0e0a12658d52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944618"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Apache Pig gebruiken met Apache Hadoop op HDInsight

Meer informatie over het gebruik van [Apache Pig](https://pig.apache.org/) met HDInsight.

Apache Pig is een platform voor het maken van Program ma's voor Apache Hadoop met behulp van een procedure taal die wordt aangeduid als *varken Latijn*. Pig is een alternatief voor Java voor het maken van *MapReduce* -oplossingen en is opgenomen in azure HDInsight. Gebruik de volgende tabel om de verschillende manieren te ontdekken waarop varken kan worden gebruikt met HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Waarom Apache Pig gebruiken

Een van de uitdagingen van het verwerken van gegevens met behulp van MapReduce in Hadoop is het implementeren van de verwerkings logica door alleen een kaart en een functie verminderen te gebruiken. Voor complexe verwerking moet u vaak de verwerking verstoren naar meerdere MapReduce-bewerkingen die samen worden gekoppeld om het gewenste resultaat te krijgen.

Met Pig kunt u een verwerking definiëren als een reeks trans formaties die de gegevens doorloopt om de gewenste uitvoer te produceren.

Met de Latijnse taal van het varken kunt u de gegevens stroom beschrijven van onbewerkte invoer, via een of meer trans formaties om de gewenste uitvoer te produceren. Latijnse Program ma's voor Pig volgen dit algemene patroon:

* **Laden**: gegevens lezen die moeten worden gemanipuleerd vanuit het bestands systeem.

* **Trans formatie**: gegevens bewerken.

* **Dump of Store**: uitvoer gegevens naar het scherm of sla het op voor verwerking.

### <a name="user-defined-functions"></a>Door de gebruiker gedefinieerde functies

Pig-Latijns biedt ook ondersteuning voor door de gebruiker gedefinieerde functies (UDF), waarmee u externe componenten kunt aanroepen die logica implementeren die moeilijk te model leren in de Latijnse Latijns.

Zie voor meer informatie over Pig-Latijns de hand [leiding voor varkens Latijns naslag handleiding 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) en de [referentie hand leiding voor varkens Latijns 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Voorbeeld gegevens

HDInsight biedt diverse voor beelden van gegevens sets, die worden opgeslagen in de `/example/data` `/HdiSamples` mappen en. Deze directory's bevinden zich in de standaard opslag voor uw cluster. Het Pig-voor beeld in dit document maakt gebruik van het *log4j* -bestand van `/example/data/sample.log` .

Elk logboek in het bestand bestaat uit een regel met velden die een `[LOG LEVEL]` veld bevat om het type en de ernst weer te geven, bijvoorbeeld:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

In het vorige voor beeld is het logboek niveau fout.

> [!NOTE]  
> U kunt ook een log4j-bestand genereren met behulp van het hulp programma [Apache log4j](https://en.wikipedia.org/wiki/Log4j) logging en dat bestand vervolgens uploaden naar uw blob. Zie [gegevens uploaden naar HDInsight](hdinsight-upload-data.md) voor instructies. Zie [Azure Blob Storage gebruiken met hdinsight](hdinsight-hadoop-use-blob-storage.md)voor meer informatie over hoe blobs in azure Storage worden gebruikt in combi natie met hdinsight.

## <a name="example-job"></a><a id="job"></a>Voorbeeld taak

Met de volgende waarde van de Latijnse taak wordt het `sample.log` bestand geladen uit de standaard opslag voor uw HDInsight-cluster. Vervolgens wordt een reeks trans formaties uitgevoerd die resulteren in een telling van het aantal keren dat elk logboek niveau is opgetreden in de invoer gegevens. De resultaten worden geschreven naar STDOUT.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

De volgende afbeelding toont een samen vatting van wat elke trans formatie naar de gegevens doet.

![Grafische weer gave van de trans formaties][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>De Latijnse taak van de Pig uitvoeren

HDInsight kan met behulp van verschillende methoden de Latijnse taken van Pig uitvoeren. Gebruik de volgende tabel om te bepalen welke methode het meest geschikt is voor u en volg de koppeling voor een overzicht.

## <a name="pig-and-sql-server-integration-services"></a>Varken en SQL Server Integration Services

U kunt SQL Server Integration Services (SSIS) gebruiken om een Pig-taak uit te voeren. Het Azure Feature Pack voor SSIS bevat de volgende onderdelen die werken met Pig-taken op HDInsight.

* [Azure HDInsight Pig-taak][pigtask]

* [Verbindings beheer voor Azure-abonnementen][connectionmanager]

Meer informatie over het Azure Feature Pack [voor SSIS.][ssispack]

## <a name="next-steps"></a><a id="nextsteps"></a>Volgende stappen

Nu u hebt geleerd hoe u Pig met HDInsight kunt gebruiken, kunt u de volgende koppelingen gebruiken om andere manieren te ontdekken om met Azure HDInsight te werken.

* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Apache Hive gebruiken met HDInsight](./hadoop/hdinsight-use-hive.md)
* [Apache Sqoop gebruiken met HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [MapReduce-taken gebruiken met HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif