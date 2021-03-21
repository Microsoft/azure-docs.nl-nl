---
title: REST API voor het opvragen van Apache HBase in azure HDInsight
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met Apache HBase-onderdelen in azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 636f84c4c6aa097288dc2fb5481dcedd6863409d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942875"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API voor het opvragen van Apache HBase in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache HBase REST-interface gebruiken om een tabel onder een andere naam ruimte dan de standaard resultaten in een runtime-fout (HTTP-status 500) op te vragen.

## <a name="cause"></a>Oorzaak

HBase REST API wordt alleen ondersteund als de standaard naam ruimte wordt gebruikt. Dit is een bekend probleem met betrekking tot het gebruik van HBase-naam ruimten of het maken van aanroepen die verwijzen naar specifieke ophalen van kolommen met kolom families met REST server op HDInsight. Dit komt door een beveiligings probleem met HDInsight gateway. Wanneer u de API gebruikt om een tabel met een naam ruimte te maken, toegang tot kolommen via kolom families, moet u het `:` teken opgeven. dit wordt beschouwd als een beveiligings probleem in de IIS-gateway module.

## <a name="mitigation"></a>Oplossing

De gateway/REST-server overs Laan door uw toepassing te implementeren op een virtuele machine die zich in dezelfde Azure-VNet bevindt als het HDInsight-cluster. Vervolgens kunt u rechtstreeks met HBase communiceren via RPC (overs laan van de REST server) of naar afzonderlijke REST-servers die worden uitgevoerd op worker-knoop punten die HDInsight-gateways overs Laan.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).