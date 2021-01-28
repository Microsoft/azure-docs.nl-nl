---
title: InvalidClassException-fout van Apache Spark-Azure HDInsight
description: Apache Spark taak mislukt met InvalidClassException, niet-overeenkomende klasse versies in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 0b0889ac1e71ce33406e89ead62370a0c0168763
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929262"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark taak mislukt met InvalidClassException, niet-overeenkomende klasse versies in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U probeert een Apache Spark-taak te maken in een Spark 2. x-cluster. Dit mislukt met een fout die vergelijkbaar is met de volgende:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een extra jar toe te voegen aan de `spark.yarn.jars` Configuratie, met name een geschakeerd jar dat een andere versie van het `commons-lang3` pakket bevat en een niet-overeenkomende klasse introduceert. Spark 2.1/2/3 maakt standaard gebruik van versie 3,5 van `commons-lang3` .

> [!TIP]
> Als u een bibliotheek wilt arceren, moet u de inhoud ervan in uw eigen jar plaatsen, waardoor het pakket wordt gewijzigd. Dit verschilt van het verpakken van de bibliotheek, die de bibliotheek in uw eigen jar plaatst zonder opnieuw te verpakken.

## <a name="resolution"></a>Oplossing

Verwijder het JAR of compileer het aangepaste JAR opnieuw (AzureLogAppender) en gebruik [maven-Shade-invoeg toepassing](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) om klassen te verplaatsen.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]