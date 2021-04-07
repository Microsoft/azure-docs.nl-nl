---
title: Time-outs bij gebruik van de opdracht 'hbase hbck' in Azure HDInsight
description: Er is een time-outprobleem opgetreden met de opdracht ' hbase hbck ' bij het herstellen van regio toewijzingen
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: acf4d1237841f8c20c014598e00a5e8961e2a012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936700"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Scenario: time-outs met de opdracht ' hbase hbck ' in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Er worden time-outs gevonden met de `hbase hbck` opdracht bij het herstellen van regio toewijzingen.

## <a name="cause"></a>Oorzaak

Een mogelijke oorzaak van problemen met de time-out wanneer u de `hbck` opdracht gebruikt, kan het zijn dat verschillende regio's in de status ' in transitie ' lang zijn. U kunt deze regio's als offline weer geven in de gebruikers interface van HBase Master. Omdat er een groot aantal regio's probeert te overstappen, HBase Master mogelijk een time-out opgetreden en kan deze regio's niet weer online worden gezet.

## <a name="resolution"></a>Oplossing

1. Meld u met SSH aan bij het HDInsight HBase-cluster.

1. Voer de `hbase zkcli` opdracht uit om verbinding te maken met Apache ZooKeeper shell.

1. Uitvoeren `rmr /hbase/regions-in-transition` of `rmr /hbase-unsecure/regions-in-transition` opdracht.

1. Sluit de `hbase zkcli` Shell af met behulp van de `exit` opdracht.

1. Start de Active HBase Master-service opnieuw vanuit de Apache Ambari-gebruikers interface.

1. Voer de opdracht `hbase hbck -fixAssignments` uit.

1. Bewaak de HBase Master gebruikers interface ' regio in overgang ' die sectie om ervoor te zorgen dat er geen regio achterblijft.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

- Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).