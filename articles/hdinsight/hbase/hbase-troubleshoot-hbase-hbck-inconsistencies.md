---
title: hbase hbck retourneert inconsistenties in azure HDInsight
description: hbase hbck retourneert inconsistenties in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: cbe4231bbecdf279c637cd334336437a020188d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936986"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenario: `hbase hbck` opdracht retourneert inconsistenties in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue-region-is-not-in-hbasemeta"></a>Probleem: regio bevindt zich niet in `hbase:meta`

Regio xxx op HDFS, maar niet vermeld in `hbase:meta` of geïmplementeerd op een server met regio's.

### <a name="cause"></a>Oorzaak

Hangt.

### <a name="resolution"></a>Oplossing

1. Herstel de meta tabel door uit te voeren:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Regio's toewijzen aan RegionServers door uit te voeren:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Probleem: regio is offline

Regio xxx is niet geïmplementeerd op een wille keurige RegionServer. Dit betekent dat de regio zich in `hbase:meta` , maar offline bevindt.

### <a name="cause"></a>Oorzaak

Hangt.

### <a name="resolution"></a>Oplossing

Regio's online plaatsen door uit te voeren:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Probleem: regio's hebben dezelfde begin-en eind sleutels

### <a name="cause"></a>Oorzaak

Hangt.

### <a name="resolution"></a>Oplossing

Deze overlappende regio's hand matig samen voegen. Ga naar de sectie HBase HMaster Web UI Table en selecteer de tabel koppeling. Dit heeft het probleem. U ziet de start sleutel/eind sleutel van elke regio die deel uitmaakt van deze tabel. Voeg deze overlappende regio's vervolgens samen. Doe in HBase-shell `merge_region 'xxxxxxxx','yyyyyyy', true` . Bijvoorbeeld:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

In dit scenario moet u Regioa en RegionC samen voegen en met hetzelfde sleutel bereik worden geregiod als RegionB en vervolgens de RegionB en de regio samenvoegt. XXXXXXX en yyyyyy zijn de hash-teken reeks aan het einde van elke regio naam. Wees hier voorzichtig om twee niet-aaneengesloten regio's samen te voegen. Na elke samen voeging, zoals samen voegen A en C, HBase wordt een compressie gestart in de regio. Wacht totdat de compressie is voltooid voordat u een andere samenvoeg bewerking uitvoert. U kunt de status van de compressie vinden op de server pagina van de betreffende regio in de gebruikers interface van HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Probleem: kan niet laden `.regioninfo`

Kan de `.regioninfo` regio niet laden `/hbase/data/default/tablex/regiony` .

### <a name="cause"></a>Oorzaak

Dit komt waarschijnlijk doordat regio gedeeltelijk wordt verwijderd wanneer RegionServer vastloopt of het opnieuw opstarten van de VM. Op dit moment is het Azure Storage een plat bestands systeem voor blobs en sommige Bestands bewerkingen zijn niet atomisch.

### <a name="resolution"></a>Oplossing

Deze resterende bestanden en mappen hand matig opschonen:

1. Voer `hdfs dfs -ls /hbase/data/default/tablex/regiony` uit om te controleren welke mappen/bestanden er nog zijn.

1. Uitvoeren `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` om alle onderliggende bestanden/mappen te verwijderen

1. Voer uit `hdfs dfs -rmr /hbase/data/default/tablex/regiony` om de map Region te verwijderen.

---

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).