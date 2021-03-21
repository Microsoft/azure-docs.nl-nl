---
title: Backup &-replicatie voor Apache HBase, Phoenix-Azure HDInsight
description: Back-ups en replicatie instellen voor Apache HBase en Apache Phoenix in azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 1d5bcf9c04ad02eaf297f8971aa0f4ff599888c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942995"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Back-up en replicatie instellen voor Apache HBase en Apache Phoenix op HDInsight

Apache HBase ondersteunt verschillende benaderingen voor het beveiligen tegen gegevens verlies:

* De `hbase` map kopiëren
* Exporteren en vervolgens importeren
* Tabellen kopiëren
* Momentopnamen
* Replicatie

> [!NOTE]  
> Apache Phoenix slaat de meta gegevens op in HBase-tabellen, zodat er een back-up wordt gemaakt van meta gegevens wanneer u een back-up maakt van de HBase-systeem catalogus tabellen.

In de volgende secties wordt het gebruiks scenario voor elk van deze benaderingen beschreven.

## <a name="copy-the-hbase-folder"></a>De map hbase kopiëren

Met deze aanpak kopieert u alle HBase-gegevens, zonder dat u een subset van tabellen of kolom families kunt selecteren. Volgende benaderingen bieden meer controle.

HBase in HDInsight maakt gebruik van de standaard opslag die is geselecteerd bij het maken van het cluster, hetzij Azure Storage blobs of Azure Data Lake Storage. In beide gevallen slaat HBase de gegevens-en meta gegevens bestanden op onder het volgende pad:

`/hbase`

* De `hbase` map bevindt zich in een Azure Storage account in de hoofdmap van de BLOB-container:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* In Azure Data Lake Storage `hbase` bevindt de map zich onder het hoofdpad dat u hebt opgegeven bij het inrichten van een cluster. Dit hoofdpad heeft doorgaans een `clusters` map met een submap met de naam na uw HDInsight-cluster:

  `/clusters/<clusterName>/hbase`

In beide gevallen bevat de `hbase` map alle gegevens die HBase naar de schijf heeft leeg gemaakt, maar de gegevens in het geheugen kunnen niet voor komen. Voordat u deze map kunt gebruiken als een nauw keurige weer gave van de HBase-gegevens, moet u het cluster uitschakelen.

Nadat u het cluster hebt verwijderd, kunt u de gegevens op de gewenste plaats laten of de gegevens naar een nieuwe locatie kopiëren:

* Een nieuw HDInsight-exemplaar maken dat verwijst naar de huidige opslag locatie. Het nieuwe exemplaar wordt gemaakt met alle bestaande gegevens.

* Kopieer de `hbase` map naar een andere Azure Storage BLOB-container of Data Lake Storage locatie en start vervolgens een nieuw cluster met die gegevens. Gebruik [AzCopy](../../storage/common/storage-use-azcopy-v10.md)voor Azure Storage en voor data Lake Storage gebruik van [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exporteren en vervolgens importeren

Gebruik op het bron HDInsight-cluster het [export programma](https://hbase.apache.org/book.html#export) (opgenomen in HBase) om gegevens uit een bron tabel te exporteren naar de standaard Attached Storage. U kunt de geëxporteerde map vervolgens kopiëren naar de locatie van de doel opslag en het [hulp programma importeren](https://hbase.apache.org/book.html#import) uitvoeren op het HDInsight-doel cluster.

Als u tabel gegevens wilt exporteren, moet u eerst SSH in het hoofd knooppunt van uw bron-HDInsight-cluster en vervolgens de volgende `hbase` opdracht uitvoeren:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

De exportmap mag niet al bestaan. De tabel naam is hoofdletter gevoelig.

Als u tabel gegevens wilt importeren, moet u SSH in het hoofd knooppunt van uw doel-HDInsight-cluster en vervolgens de volgende `hbase` opdracht uitvoeren:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

De tabel moet al bestaan.

Geef het volledige exportpad op naar de standaard opslag of naar een van de gekoppelde opslag opties. Bijvoorbeeld in Azure Storage:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

In Azure Data Lake Storage Gen2 is de syntaxis:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

In Azure Data Lake Storage Gen1 is de syntaxis:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Deze benadering biedt granulatie op tabel niveau. U kunt ook een datum bereik opgeven voor de rijen die u wilt toevoegen, zodat u het proces incrementeel kunt uitvoeren. Elke datum is in milliseconden sinds de UNIX-epoche.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Houd er rekening mee dat u het aantal versies van elke rij moet opgeven dat u wilt exporteren. Als u alle versies in het datum bereik wilt gebruiken, stelt `<numberOfVersions>` u in op een waarde die hoger is dan de Maxi maal mogelijke rij-versies, zoals 100000.

## <a name="copy-tables"></a>Tabellen kopiëren

Het [hulp programma CopyTable](https://hbase.apache.org/book.html#copy.table) kopieert gegevens van een bron tabel, rij voor rij, naar een bestaande doel tabel met hetzelfde schema als de bron. De doel tabel kan zich in hetzelfde cluster of een ander HBase-cluster bevindt. De tabel namen zijn hoofdletter gevoelig.

Als u CopyTable binnen een cluster wilt gebruiken, SSH dan in het hoofd knooppunt van uw bron-HDInsight-cluster en voert u deze `hbase` opdracht uit:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Als u CopyTable wilt gebruiken om te kopiëren naar een tabel in een ander cluster, voegt u de `peer` switch toe met het adres van het doel cluster:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

Het doel adres bestaat uit de volgende drie onderdelen:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` is een door komma's gescheiden lijst met Apache ZooKeeper knooppunten, bijvoorbeeld:

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, ZK4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` op HDInsight worden standaard ingesteld op 2181 en `<ZnodeParent>` is `/hbase-unsecure` de volledige waarde `<destinationAddress>` :

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, ZK4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net, zk3-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. internal. cloudapp. net: 2181:/hbase-UNSECURE

Zie [hand matig de Apache ZooKeeper-quorum lijst](#manually-collect-the-apache-zookeeper-quorum-list) in dit artikel verzamelen voor meer informatie over het ophalen van deze waarden voor uw HDInsight-cluster.

Het hulp programma CopyTable biedt ook ondersteuning voor para meters om het tijds bereik op te geven van rijen die moeten worden gekopieerd en om de subset van kolom families op te geven in een tabel die moet worden gekopieerd. Als u de volledige lijst met para meters wilt zien die worden ondersteund door CopyTable, voert u CopyTable zonder para meters uit:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

CopyTable scant de volledige inhoud van de bron tabel die wordt gekopieerd naar de doel tabel. Dit kan de prestaties van uw HBase-cluster verminderen terwijl CopyTable wordt uitgevoerd.

> [!NOTE]  
> Voor het automatiseren van het kopiëren van gegevens tussen tabellen, raadpleegt u het `hdi_copy_table.sh` script in de [Azure HBase utils](https://github.com/Azure/hbase-utils/tree/master/replication) -opslag plaats op github.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>De Apache ZooKeeper-quorum lijst hand matig verzamelen

Wanneer beide HDInsight-clusters zich in hetzelfde virtuele netwerk bevinden, zoals eerder beschreven, wordt de interne naam omzetting automatisch. Als u CopyTable voor HDInsight-clusters in twee afzonderlijke virtuele netwerken wilt gebruiken die zijn verbonden met een VPN Gateway, moet u de IP-adressen van de host van de Zookeeper-knoop punten in het quorum opgeven.

Voer de volgende krul opdracht uit om de namen van de quorum-hostnamen te verkrijgen:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

Met de krul opdracht wordt een JSON-document met HBase-configuratie gegevens opgehaald en de grep-opdracht retourneert alleen de vermelding ' HBase. Zookeeper. quorum ', bijvoorbeeld:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

De waarde van de quorum host is de volledige teken reeks rechts van de dubbele punt.

Als u de IP-adressen voor deze hosts wilt ophalen, gebruikt u de volgende krul opdracht voor elke host in de vorige lijst:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

In deze krul opdracht `<zookeeperHostFullName>` bevindt zich de volledige DNS-naam van een ZooKeeper-host, zoals het voor beeld `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` . De uitvoer van de opdracht bevat het IP-adres voor de opgegeven host, bijvoorbeeld:

`100    "ip" : "10.0.0.9",`

Nadat u de IP-adressen voor alle ZooKeeper-knoop punten in uw quorum hebt verzameld, bouwt u het doel adres opnieuw op:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

In ons voorbeeld:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Momentopnamen

Met [moment opnamen](https://hbase.apache.org/book.html#ops.snapshots) kunt u een tijdrovende back-up maken van gegevens in uw HBase-opslag plaats. Moment opnamen hebben minimale overhead en zijn binnen enkele seconden voltooid, omdat een momentopname bewerking in feite een meta gegevens bewerking is die de namen van alle bestanden in de opslag op dat moment vastlegt. Op het moment van een moment opname worden er geen werkelijke gegevens gekopieerd. Moment opnamen zijn afhankelijk van het onveranderbare karakter van de gegevens die zijn opgeslagen in HDFS, waarbij updates, verwijderingen en invoegingen worden weer gegeven als nieuwe gegevens. U kunt een moment opname op hetzelfde cluster herstellen (*klonen*) of een moment opname exporteren naar een ander cluster.

Als u een moment opname wilt maken, maakt u een SSH-verbinding met het hoofd knooppunt van uw HDInsight HBase-cluster en start u de `hbase` shell:

```console
hbase shell
```

Gebruik in de hbase-shell de opdracht snap shot met de namen van de tabel en van deze moment opname:

```console
snapshot '<tableName>', '<snapshotName>'
```

Als u een moment opname met de naam in de shell wilt herstellen `hbase` , schakelt u eerst de tabel uit, herstelt u de moment opname en schakelt u de tabel opnieuw in:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Als u een moment opname wilt herstellen naar een nieuwe tabel, gebruikt u clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Als u een moment opname naar HDFS wilt exporteren voor gebruik door een ander cluster, maakt u eerst de moment opname zoals eerder beschreven en gebruikt u vervolgens het hulp programma ExportSnapshot. Voer dit hulp programma uit vanuit de SSH-sessie naar het hoofd knooppunt, niet in de `hbase` shell:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

Dit `<hdfsHBaseLocation>` kan elk van de opslag locaties zijn die toegankelijk zijn voor uw bron cluster en moet verwijzen naar de map hbase die wordt gebruikt door uw doel cluster. Als u bijvoorbeeld een secundaire Azure Storage-account hebt gekoppeld aan uw bron cluster en dat account toegang biedt tot de container die wordt gebruikt door de standaard opslag van het doel cluster, kunt u de volgende opdracht gebruiken:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Als u geen secundair Azure Storage-account hebt gekoppeld aan uw bron cluster of als uw bron cluster een on-premises cluster is (of een niet-HDI cluster), kunt u autorisatie problemen ondervinden wanneer u probeert toegang te krijgen tot het opslag account van uw HDI-cluster. U kunt dit oplossen door de sleutel voor uw opslag account op te geven als een opdracht regel parameter, zoals wordt weer gegeven in het volgende voor beeld. U kunt de sleutel naar uw opslag account in de Azure Portal ophalen.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Als uw doel cluster een ADLS gen 2-cluster is, wijzigt u de voor gaande opdracht in aanpassen voor de configuraties die worden gebruikt door ADLS gen 2:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.<account_name>.dfs.core.windows.net=<key> -Dfs.azure.account.auth.type.<account_name>.dfs.core.windows.net=SharedKey -Dfs.azure.always.use.https.<account_name>.dfs.core.windows.net=false -Dfs.azure.account.keyprovider.<account_name>.dfs.core.windows.net=org.apache.hadoop.fs.azurebfs.services.SimpleKeyProvider -snapshot 'Snapshot1' -copy-to 'abfs://<container>@<account_name>.dfs.core.windows.net/hbase'
```

Nadat de moment opname is geëxporteerd, moet u SSH in het hoofd knooppunt van het doel cluster en de moment opname herstellen met behulp van de `restore_snapshot` opdracht zoals eerder beschreven.

Moment opnamen bieden een volledige back-up van een tabel op het moment van de `snapshot` opdracht. Met moment opnamen kunt u niet de mogelijkheid bieden om incrementele moment opnamen op te zetten en kunt u geen subsets van kolom families opgeven die moeten worden opgenomen in de moment opname.

## <a name="replication"></a>Replicatie

Met [HBase-replicatie](https://hbase.apache.org/book.html#_cluster_replication) worden automatisch trans acties van een bron cluster naar een doel cluster gepusht, met behulp van een asynchroon mechanisme met minimale overhead op het bron cluster. In HDInsight kunt u replicatie instellen tussen clusters waar:

* De bron-en doel clusters bevinden zich in hetzelfde virtuele netwerk.
* De bron-en doel clusters bevinden zich in verschillende virtuele netwerken die zijn verbonden via een VPN-gateway, maar beide clusters bestaan op dezelfde geografische locatie.
* Het bron cluster en de doel clusters bevinden zich in verschillende virtuele netwerken die zijn verbonden met een VPN-gateway en elk cluster bestaat op een andere geografische locatie.

De algemene stappen voor het instellen van de replicatie zijn:

1. Op het bron cluster maakt u de tabellen en vult u gegevens in.
2. Maak op het doel cluster lege doel tabellen met het schema van de bron tabel.
3. Registreer het doel cluster als een peer bij het bron cluster.
4. Schakel replicatie in op de gewenste bron tabellen.
5. Kopieer bestaande gegevens van de bron tabellen naar de doel tabellen.
6. Replicatie kopieert automatisch nieuwe gegevens wijzigingen naar de bron tabellen naar de doel tabellen.

Als u replicatie op HDInsight wilt inschakelen, moet u een script actie Toep assen op uw actieve HDInsight-bron-cluster. Zie [Apache HBase-replicatie configureren](apache-hbase-replication.md)voor een overzicht van het inschakelen van replicatie in uw cluster of als u wilt experimenteren met replicatie in voorbeeld clusters die zijn gemaakt in virtuele netwerken met Azure resource management-sjablonen. Dit artikel bevat ook instructies voor het inschakelen van replicatie van Bredae meta gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Apache HBase-replicatie configureren](apache-hbase-replication.md)
* [Werken met het HBase-hulp programma voor importeren en exporteren](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)
