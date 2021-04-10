---
title: Kan Apache-garen logboek niet lezen in azure HDInsight
description: Probleemoplossings stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943039"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: kan het Apache-garen logboek in azure HDInsight niet lezen

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache-garens logboeken die zijn gevonden vanuit het opslag account zijn niet leesbaar voor mensen. De parser van het bestand werkt niet en levert het volgende fout bericht op:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Oorzaak

Het Apache-garen logboek wordt samengevoegd in de `IndexFile` indeling. dit wordt niet ondersteund door de bestands-parser.

## <a name="resolution"></a>Oplossing

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Ga vanuit de Ambari-gebruikers interface naar **garens**  >    >  **Geavanceerd**  >  **Geavanceerd garen-site**.

1. Voor WASB-opslag: de standaard waarde voor `yarn.log-aggregation.file-formats` is `IndexedFormat,TFile` . Wijzig de waarde in `TFile` .

1. Voor ADLS-opslag: de standaard waarde voor `yarn.nodemanager.log-aggregation.compression-type` is `gz` . Wijzig de waarde in `none` .

1. Sla de wijziging op en start alle betrokken services opnieuw op.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).