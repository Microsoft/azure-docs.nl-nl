---
title: Vm's voor Azure HDInsight-clusters opnieuw opstarten
description: Meer informatie over het opnieuw opstarten van niet-reagerende Vm's voor Azure HDInsight-clusters.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: a61735dba60860459d007eb54d4655f41d5ae51a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946869"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>Vm's voor HDInsight-clusters opnieuw opstarten

Azure HDInsight-clusters bevatten groepen van virtuele machines (Vm's) als cluster knooppunten. Voor langlopende clusters kunnen deze knoop punten om verschillende redenen onreageerd worden. In dit artikel wordt beschreven hoe u niet-reagerende Vm's in een HDInsight-cluster opnieuw opstarten.

## <a name="when-to-reboot"></a>Wanneer opnieuw opstarten

> [!WARNING]
> Wanneer u Vm's opnieuw opstart in een cluster, is het knoop punt niet beschikbaar voor gebruik en moeten de services op het knoop punt opnieuw worden gestart.

Wanneer een knoop punt opnieuw wordt opgestart, wordt het cluster mogelijk beschadigd en kunnen taken vertragen of mislukken. Als u het actieve hoofd knooppunt opnieuw wilt opstarten, worden alle actieve taken gestopt. U kunt geen taken naar het cluster verzenden totdat de services weer actief zijn. Daarom moet u Vm's alleen opnieuw opstarten als dat nodig is. Overweeg het opnieuw opstarten van Vm's wanneer:

- U kunt SSH niet gebruiken om in het knoop punt op te halen, maar reageert wel op pings.
- Het worker-knoop punt is niet actief zonder heartbeat in de Ambari-gebruikers interface.
- De tijdelijke schijf is vol op het knoop punt.
- De proces tabel op de VM bevat veel vermeldingen waar het proces is voltooid, maar het wordt weer gegeven met de status afgesloten.

> [!NOTE]
> Het opnieuw opstarten van virtuele machines wordt niet ondersteund voor **HBase** -en **Kafka** -clusters, omdat het opnieuw opstarten mogelijk dat er gegevens verloren gaan.

## <a name="use-powershell-to-reboot-vms"></a>Power shell gebruiken voor het opnieuw opstarten van Vm's

Er zijn twee stappen vereist voor het opnieuw opstarten van het knoop punt: een lijst met knoop punten en opnieuw starten van knoop punten.

1. Knoop punten weer geven. U kunt de lijst met cluster knooppunten ophalen op [Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost).

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. Start hosts opnieuw op. Nadat u de namen van de knoop punten hebt opgehaald die u opnieuw wilt opstarten, start u de knoop punten opnieuw met behulp van [restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost).

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>Een REST API gebruiken om Vm's opnieuw op te starten

U kunt de functie **try it** in het API-document gebruiken om aanvragen te verzenden naar HDInsight. Er zijn twee stappen vereist voor het opnieuw opstarten van het knoop punt: een lijst met knoop punten en opnieuw starten van knoop punten.

1. Knoop punten weer geven. U kunt de lijst met cluster knooppunten ophalen uit de REST API of in Ambari. Zie voor meer informatie de [lijst met HDInsight-hosts rest API bewerking](/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. Start hosts opnieuw op. Nadat u de namen van de knoop punten hebt opgehaald die u opnieuw wilt opstarten, start u de knoop punten opnieuw met behulp van de REST API om de knoop punten opnieuw op te starten. De naam van het knoop punt volgt het patroon van *NodeType (wn/HN/ZK/GW)*  +  *x* de  +  *eerste zes tekens van de cluster naam*. Zie voor meer informatie [HDInsight-hosts opnieuw opstarten rest API bewerking](/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

De werkelijke namen van de knoop punten die u opnieuw wilt opstarten, worden opgegeven in een JSON-matrix in de hoofd tekst van de aanvraag.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Volgende stappen

* [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Virtuele HDInsight-machines REST API](/rest/api/hdinsight/virtualmachines)
* [HDInsight-REST API](/rest/api/hdinsight/)