---
title: Deelname aan Apache Hive leidt tot OutOfMemory-fout-Azure HDInsight
description: Omgaan met OutOfMemory-fouten ' de GC-overhead limiet is overschreden '
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0c396cde38d8cba8e1f3eaf8527429647868a0c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935959"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scenario: deelname aan Apache Hive leidt tot een OutOfMemory-fout in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het standaard gedrag voor Apache Hive verbindingen is het laden van de volledige inhoud van een tabel in het geheugen, zodat er een samen voeging kan worden uitgevoerd zonder dat u een stap voor het toewijzen of verlagen hoeft uit te voeren. Als de Hive-tabel te groot is om in het geheugen te passen, kan de query mislukken.

## <a name="cause"></a>Oorzaak

Wanneer samen voegingen in een Hive met voldoende grootte worden uitgevoerd, wordt de volgende fout aangetroffen:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Oplossing

Voor komen dat Hive-tabellen in het geheugen worden geladen voor samen voegingen (in plaats van een stap voor het toewijzen/verlagen van een kaart) door de volgende Hive-configuratie waarde in te stellen:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Volgende stappen

Als u dit probleem niet kunt oplossen met deze waarde, gaat u naar een van de volgende...

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](../../azure-portal/supportability/how-to-create-azure-support-request.md). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).