---
title: Poort conflict bij het starten van services in azure HDInsight
description: Probleemoplossings stappen en mogelijke oplossingen voor problemen met poort conflicten bij interactie met Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f42e84d5d9c1dd49d9bf5604fe2f967eae0b6276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943097"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Scenario: poort conflict bij het starten van services in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Een service kan niet worden gestart.

## <a name="cause"></a>Oorzaak

Er bestaat een poort conflict.

## <a name="resolution"></a>Oplossing

### <a name="method-1"></a>Methode 1

Gebruik onderstaande opdrachten om alle actieve processen op te halen/af te breken, die worden beïnvloed door het poort probleem.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Start vervolgens service.

### <a name="method-2"></a>Methode 2

Start het knoop punt opnieuw op.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).