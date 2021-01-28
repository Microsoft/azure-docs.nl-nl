---
title: Het account dat wordt geopend, ondersteunt geen HTTP-fout in azure HDInsight
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943086"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Het account dat wordt geopend, ondersteunt geen HTTP-fout in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het volgende fout bericht wordt ontvangen:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Oorzaak

Er zijn verschillende redenen waarom het fout bericht wordt ontvangen:

* Voor het opslag account is [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md) ingeschakeld en er wordt een onjuist [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) gebruikt.

* Er is een cluster gemaakt met een opslag account waarvoor beveiligde overdracht is *uitgeschakeld*. Daarna werd beveiligde overdracht ingeschakeld op het opslag account.

## <a name="resolution"></a>Oplossing

Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data Lake Storage Gen2, is de URI `wasbs://` `abfss://` respectievelijk.  Zie ook [beveiligde overdracht](../../storage/common/storage-require-secure-transfer.md).

Gebruik voor nieuwe clusters een opslag account dat de gewenste instelling voor beveiligde overdracht al heeft. Wijzig de instelling voor beveiligde overdracht niet voor een opslag account dat wordt gebruikt door een bestaand cluster.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).