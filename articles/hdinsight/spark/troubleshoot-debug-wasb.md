---
title: Fouten opsporen in WASB-Bestands bewerkingen in azure HDInsight
description: Hierin worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f0f06c81906116dc278377cf9fd8871e8899a1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938744"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Fouten opsporen in WASB-Bestands bewerkingen in azure HDInsight

Het kan zijn dat u wilt weten welke bewerkingen het WASB-stuur programma heeft gestart met Azure Storage. Voor de client zijde produceert het WASB-stuur programma Logboeken voor elke bestandssysteem bewerking op het niveau van de **fout opsporing** . Het WASB-stuur programma gebruikt log4j voor het beheren van het registratie niveau en de standaard instelling is **info** niveau. Zie [Azure Storage Analytics-logboek registratie](../../storage/common/storage-analytics-logging.md)voor Azure Storage Analytics-logboeken aan de server zijde.

Een geproduceerd logboek ziet er ongeveer als volgt uit:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>WASB debug-logboek inschakelen voor bestands bewerkingen

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` , waarbij `CLUSTERNAME` de naam van uw Spark-cluster is.

1. Ga naar **Geavanceerde spark2-log4j-Properties**.

    1. Wijzigen `log4j.appender.console.Threshold=INFO` in `log4j.appender.console.Threshold=DEBUG` .

    1. Toevoegen `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Ga naar **Geavanceerde livy2-log4j-Properties**.

    Toevoegen `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG` .

1. Sla wijzigingen op.

## <a name="additional-logging"></a>Aanvullende logboekregistratie

De bovenstaande logboeken moeten informatie op hoog niveau van de bestandssysteem bewerkingen bieden. Als de bovenstaande logboeken nog steeds geen bruikbare informatie bieden of als u API-aanroepen voor Blob-opslag wilt onderzoeken, voegt u `fs.azure.storage.client.logging=true` deze toe aan `core-site` . Met deze instelling worden de Java SDK-logboeken ingeschakeld voor wasb-opslag stuur programma en worden alle aanroepen naar de Blob Storage-Server afgedrukt. Verwijder de instelling na onderzoek, omdat hiermee de schijf snel kan worden gevuld en het proces kan worden vertraagd.

Als de back-end Azure Data Lake is, gebruikt u de volgende log4j-instelling voor het onderdeel (bijvoorbeeld Spark/TEZ/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Zoek de logboeken in `/var/log/adl/adl.log` voor de logboeken.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).