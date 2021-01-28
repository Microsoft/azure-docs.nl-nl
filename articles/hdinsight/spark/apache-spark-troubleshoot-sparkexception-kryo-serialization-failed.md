---
title: Problemen met JDBC/ODBC & Apache Thrift Framework-Azure HDInsight
description: Kan geen grote gegevens sets downloaden met behulp van JDBC/ODBC en Apache Thrift software framework in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: 0b96bc9f6a78d07e091e791b769056859183da0d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945124"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Kan geen grote gegevens sets downloaden met behulp van JDBC/ODBC en Apache Thrift software framework in HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u probeert grote gegevens sets te downloaden met behulp van JDBC/ODBC en het Apache Thrift-software raamwerk in azure HDInsight, wordt een fout bericht als volgt weer gegeven:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Oorzaak

Deze uitzonde ring wordt veroorzaakt door het serialisatie proces en probeert meer buffer ruimte te gebruiken dan is toegestaan. In Spark 2.0.0 wordt de klasse `org.apache.spark.serializer.KryoSerializer` gebruikt voor het serialiseren van objecten wanneer gegevens worden geopend via het Apache Thrift software Framework. Een andere klasse wordt gebruikt voor gegevens die via het netwerk worden verzonden of in de cache worden opgeslagen.

## <a name="resolution"></a>Oplossing

Verhoog de `Kryoserializer` buffer waarde. Voeg een sleutel toe `spark.kryoserializer.buffer.max` met de naam en stel deze `2047` in op spark2 config onder `Custom spark2-thrift-sparkconf` . Start alle betrokken onderdelen opnieuw op.

> [!IMPORTANT]
> De waarde voor `spark.kryoserializer.buffer.max` moet kleiner zijn dan 2048. Fractionele waarden worden niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).