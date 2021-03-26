---
title: Standaard Hive-metastore migreren naar externe meta Store in azure HDInsight
description: Standaard Hive-metastore migreren naar externe meta Store in azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566112"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Standaard Hive-metastore data base migreren naar externe meta store-data base

In dit artikel wordt beschreven hoe u meta gegevens migreert vanuit een [Data Base van het standaard archief](../hdinsight-use-external-metadata-stores.md#default-metastore) voor de Hive naar een extern SQL database op HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Waarom migreren naar externe meta store-data base

* De standaard-META store-data base is beperkt tot de basis-SKU en kan geen werk belastingen voor productie schalen verwerken.

* Met de externe meta store-data base kunnen de onderdelen van de Hive-reken resources worden geschaald door nieuwe HDInsight-clusters toe te voegen die dezelfde meta Store DB delen.

* Voor de migratie van HDInsight 3,6 tot 4,0 is het verplicht om meta gegevens te migreren naar de externe metadata-Data Base voordat de versie van het Hive-schema wordt bijgewerkt. Zie [workloads migreren van HDInsight 3,6 naar HDInsight 4,0](./apache-hive-migrate-workloads.md).

Omdat de standaard-META store-data base een beperkte reken capaciteit heeft, raden we u aan laag gebruik uit te voeren van andere taken in het cluster tijdens het migreren van meta gegevens.

De bron-en doel-Db's moeten dezelfde HDInsight-versie en dezelfde opslag accounts gebruiken. Als u HDInsight-versies wilt upgraden van 3,6 naar 4,0, voert u eerst de stappen in dit artikel uit. Volg de stappen in de officiële upgrade [hier](./apache-hive-migrate-workloads.md).

## <a name="prerequisites"></a>Vereisten

Als u [Azure data Lake Storage gen1](../overview-data-lake-storage-gen1.md)gebruikt, zijn de locaties van Hive-tabellen waarschijnlijk afhankelijk van de HDFS-configuraties van het cluster voor Azure data Lake Storage gen1. Voer de volgende script actie uit om deze locaties te verplaatsen naar andere clusters. Zie [script actie voor een actief cluster](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

De actie is vergelijkbaar met het vervangen van symlinks met hun volledige paden.

|Eigenschap | Waarde |
|---|---|
|Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Knooppunttype(n)|Head|
|Parameters|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migreren met exporteren/importeren met behulp van sqlpackage

An HDInsight cluster alleen is gemaakt nadat 2020-10-15 SQL-export/-import ondersteunt voor de Hive-standaard META store-data base met behulp van `sqlpackage` .

1. Installeer [sqlpackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) in het cluster.

2. Exporteer de standaard-BACPAC-data base door de volgende opdracht uit te voeren.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Sla het BACPAC-bestand op. Hieronder ziet u een optie.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importeer het BACPAC-bestand naar een nieuwe Data Base met de [hier](../../azure-sql/database/database-import.md)beschreven stappen.

5. De nieuwe Data Base is gereed om te worden [geconfigureerd als externe meta Store DB op een nieuw HDInsight-cluster](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migreren met hive-script

Clusters die zijn gemaakt vóór 2020-10-15, bieden geen ondersteuning voor het exporteren/importeren van de standaard-META store-data base.

Voor dergelijke clusters volgt u de instructies voor het [kopiëren van Hive-tabellen in opslag accounts](./hive-migration-across-storage-accounts.md)met behulp van een tweede cluster met een [externe Hive-metastore DB](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Het tweede cluster kan hetzelfde opslag account gebruiken, maar moet een nieuw standaard bestands systeem gebruiken.

### <a name="option-to-shallow-copy"></a>Optie voor ' bediepe ' kopie
Opslag verbruik zou dubbel zijn wanneer tabellen ' diep ' zijn gekopieerd met behulp van de bovenstaande hand leiding. U moet de gegevens in de bron opslag container hand matig opschonen.
We kunnen in plaats daarvan ' belopend ' de tabellen kopiëren als ze niet-transactioneel zijn. Alle Hive-tabellen in HDInsight 3,6 zijn standaard niet-transactioneel, maar alleen externe tabellen zijn niet-transactioneel in HDInsight 4,0. Transactionele tabellen moeten diep worden gekopieerd. Volg deze stappen om niet-transactionele tabellen te kopiëren:

1. Voer script [Hive-DDLs.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) uit op de primaire hoofd knooppunt van het bron cluster om de DDL voor elke Hive-tabel te genereren.
2. De DDL wordt geschreven naar een lokale Hive-script met de naam `/tmp/hdi_hive_ddls.hql` . Voer dit uit op het doel cluster dat gebruikmaakt van een externe Hive-metastore DB.

## <a name="verify-that-all-hive-tables-are-imported"></a>Controleren of alle Hive-tabellen zijn geïmporteerd

De volgende opdracht maakt gebruik van een SQL-query in de meta store-data base om alle Hive-tabellen en de bijbehorende gegevens locaties af te drukken. Vergelijk de uitvoer tussen nieuwe en oude clusters om te controleren of er geen tabellen ontbreken in de nieuwe Meta store-data base.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Meer lezen

* [Werk belastingen migreren van HDInsight 3,6 naar 4,0](./apache-hive-migrate-workloads.md)
* [Migratie van Hive-werk belasting tussen opslag accounts](./hive-migration-across-storage-accounts.md)
* [Verbinding maken met beeline in HDInsight](../hadoop/connect-install-beeline.md)
* [Problemen met machtigings fout maken tabel oplossen](./interactive-query-troubleshoot-permission-error-create-table.md)