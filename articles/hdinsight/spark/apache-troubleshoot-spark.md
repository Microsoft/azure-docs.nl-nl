---
title: Problemen met Apache Spark in azure HDInsight oplossen
description: Krijg antwoorden op veelgestelde vragen over het werken met Apache Spark en Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: b54b9d932505ada890ac21c1b8de3178ad2f0042
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867505"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Probleem met Apache Spark oplossen met behulp van Azure HDInsight

Meer informatie over de belangrijkste problemen en hun oplossingen bij het werken met Apache Spark Payloads in [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van Apache Ambari in clusters?

Spark-configuratie waarden kunnen worden afgestemd om een Apache Spark toepassings uitzondering te voor komen `OutofMemoryError` . In de volgende stappen worden standaard waarden voor Spark-configuratie in azure HDInsight weer gegeven:

1. Meld u aan bij Ambari `https://CLUSTERNAME.azurehdidnsight.net` met uw cluster referenties. In het eerste scherm wordt een overzichts dashboard weer gegeven. Er zijn geringe cosmetische verschillen tussen HDInsight 3,6 en 4,0.

1. Ga naar **Spark2**-  >  **configuraties**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png" alt-text="Selecteer het tabblad Configuratie." border="true":::

1. Selecteer in de lijst met configuraties de **Opties Custom-spark2-defaults**.

1. Zoek naar de waarde-instelling die u moet aanpassen, zoals **spark.executor. Memory**. In dit geval is de waarde van **9728m** te hoog.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png" alt-text="Aangepaste Spark-standaard selecteren" border="true":::

1. Stel de waarde in op de aanbevolen instelling. De waarde **2048m** wordt aanbevolen voor deze instelling.

1. Sla de waarde op en sla de configuratie op. Selecteer **Opslaan**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png" alt-text="Waarde wijzigen in 2048m" border="true":::

    Schrijf een opmerking over de configuratie wijzigingen en selecteer vervolgens **Opslaan**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png" alt-text="Voer een opmerking in over de wijzigingen die u hebt aangebracht" border="true":::

    U ontvangt een melding als er configuraties zijn die aandacht vereisen. Noteer de items en selecteer vervolgens **door gaan**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png" alt-text="Selecteer toch door gaan" border="true":::

1. Wanneer een configuratie wordt opgeslagen, wordt u gevraagd de service opnieuw te starten. Selecteer **opnieuw opstarten**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png" alt-text="Selecteer opnieuw opstarten" border="true":::

    Bevestig het opnieuw opstarten.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png" alt-text="Selecteer bevestig opnieuw opstarten" border="true":::

    U kunt de processen bekijken die worden uitgevoerd.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png" alt-text="Actieve processen controleren" border="true":::

1. U kunt configuraties toevoegen. Selecteer in de lijst met configuraties de **Opties aangepast-spark2-standaard** en selecteer vervolgens **eigenschap toevoegen**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png" alt-text="Eigenschap toevoegen selecteren" border="true":::

1. Definieer een nieuwe eigenschap. U kunt één eigenschap definiëren met behulp van een dialoog venster voor specifieke instellingen, zoals het gegevens type. U kunt ook meerdere eigenschappen definiëren door één definitie per regel te gebruiken.

    In dit voor beeld is de eigenschap **Spark. drivers. Memory** gedefinieerd met de waarde **4G**.

    :::image type="content" source="./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png" alt-text="Nieuwe eigenschap definiëren" border="true":::

1. Sla de configuratie op en start de service opnieuw zoals beschreven in stap 6 en 7.

Deze wijzigingen zijn het hele cluster, maar kunnen worden overschreven wanneer u de Spark-taak verzendt.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van een Jupyter Notebook in clusters?

In de eerste cel van de Jupyter Notebook, na de instructie **%% Configure** , geeft u de Spark-configuraties in een geldige JSON-indeling op. Wijzig de werkelijke waarden indien nodig:

:::image type="content" source="./media/apache-troubleshoot-spark/add-configuration-cell.png" alt-text="Een configuratie toevoegen" border="true":::

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van Apache Livy in clusters?

Dien de Spark-toepassing in op livy met behulp van een REST-client, zoals krul. Gebruik een opdracht die er ongeveer als volgt uitziet. Wijzig de werkelijke waarden indien nodig:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hoe kan ik een Apache Spark-toepassing configureren met behulp van spark-indiening in clusters?

Start Spark-shell met behulp van een opdracht zoals de volgende. Wijzig indien nodig de werkelijke waarde van de configuraties:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Meer artikelen

[Verzen ding van taken Apache Spark op HDInsight-clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* [Overzicht van Spark-geheugen beheer](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Fout opsporing van Spark-toepassing op HDInsight-clusters](/archive/blogs/azuredatalake/spark-debugging-101).

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).