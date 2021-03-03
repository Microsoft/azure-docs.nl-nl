---
title: Migratie van Hive-werk belasting naar nieuw account in Azure Storage
description: Migratie van Hive-werk belasting naar nieuw account in Azure Storage
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746103"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migratie van Hive-werk belasting naar nieuw account in Azure Storage

Meer informatie over het gebruik van script acties om Hive-tabellen te kopiëren over opslag accounts in HDInsight. Dit kan handig zijn bij het migreren naar [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) .

Als u hand matig een afzonderlijke Hive-tabel wilt kopiëren in HDInsight 4,0, raadpleegt u [Hive exporteren/importeren](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Vereisten

* Een nieuw HDInsight-cluster met de volgende configuraties:
  * Het standaard bestands systeem bevindt zich in het doel-opslag account. Zie [Azure Storage gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md).
  * De Cluster versie moet overeenkomen met de naam van het bron cluster.
  * Er wordt een nieuwe externe Hive-metastore DB gebruikt. Zie [externe meta gegevens archieven gebruiken](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Een opslag account dat toegankelijk is voor zowel oorspronkelijke als nieuwe clusters. Zie [extra opslag accounts toevoegen aan HDInsight](../hdinsight-hadoop-add-storage.md) en [opslag typen en functies](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) voor toegestane secundaire opslag typen.

    Hier volgen enkele opties:
  * Voeg het doel opslag account toe aan het oorspronkelijke cluster.
  * Voeg het oorspronkelijke opslag account toe aan het nieuwe cluster.
  * Voeg een tussenliggend opslag account toe aan zowel de oorspronkelijke als de nieuwe clusters.

## <a name="how-it-works"></a>Uitleg

Er wordt een script actie uitgevoerd om Hive-tabellen van het oorspronkelijke cluster naar een opgegeven HDFS-map te exporteren. Zie [script actie voor een actief cluster](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Vervolgens wordt er een andere script actie op het nieuwe cluster uitgevoerd om de Hive-tabellen te importeren uit de map HDFS.

Met het script worden de tabellen opnieuw gemaakt naar het standaard bestandssysteem van het nieuwe cluster. In systeem eigen tabellen worden ook de gegevens in de opslag gekopieerd. Niet-systeem eigen tabellen worden alleen per definitie gekopieerd. Zie [Hive Storage handlers](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) voor meer informatie over niet-systeem eigen tabellen.

Het pad van externe tabellen die zich niet in de map van het onderdeel Warehouse bevinden, blijft behouden. Andere tabellen worden gekopieerd naar het standaard Hive-pad van het doel cluster. Zie Hive-eigenschappen `hive.metastore.warehouse.external.dir` en `hive.metastore.warehouse.dir` .

De scripts behouden geen aangepaste bestands machtigingen in het doel cluster.

> [!NOTE]
>
> Deze hand leiding ondersteunt het kopiëren van meta gegevens objecten die betrekking hebben op Hive-data bases, tabellen en partities. Andere meta gegevens objecten moeten hand matig opnieuw worden gemaakt.
>
> * For `Views` , component ondersteunt `SHOW VIEWS` opdracht als hive 2.2.0 in HDInsight 4,0. Gebruiken `SHOW CREATE TABLE` voor weergave definitie. Voor eerdere versies van Hive query uitvoeren op de meta Store SQL-Data Base om weer gaven weer te geven.
> * Voor `Materialized Views` , gebruikt u opdrachten `SHOW MATERIALIZED VIEWS` , `DESCRIBE FORMATTED` en `CREATE MATERIALIZED VIEW` . Zie [gerealiseerde weer gaven](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) voor meer informatie.
> * Voor `Constraints` , ondersteund als hive 2.1.0 in HDInsight 4,0, gebruikt `DESCRIBE EXTENDED` om beperkingen voor een tabel weer te geven en `ALTER TABLE` om beperkingen toe te voegen. Zie [ALTER TABLE constraints](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) voor meer informatie.

## <a name="copy-hive-tables"></a>Hive-tabellen kopiëren

1. Pas de script actie ' exporteren ' op het oorspronkelijke cluster toe met de volgende velden.

    Hiermee worden de tussenliggende Hive-scripts gegenereerd en uitgevoerd. Ze worden opgeslagen naar de opgegeven `<hdfs-export-path>` .

    Gebruik `--run-script=false` deze optie om ze aan te passen voordat u deze hand matig uitvoert.

    |Eigenschap | Waarde |
    |---|---|
    |Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Knooppunttype(n)|Head|
    |Parameters|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Nadat het exporteren is voltooid, past u de script actie ' importeren ' op het nieuwe cluster toe met de volgende velden.

    |Eigenschap | Waarde |
    |---|---|
    |Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Knooppunttype(n)|Head|
    |Parameters|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Verificatie

Down load en voer het script uit als hoofd gebruiker [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) op het primaire knoop punt van elk cluster en vergelijk de inhoud van het uitvoer bestand `/tmp/hive_contents.out` . Zie [verbinding maken met HDInsight (Apache Hadoop) met SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="cleanup-additional-storage-usage"></a>Extra opslag gebruik opschonen

Nadat de opslag migratie is voltooid en geverifieerd, kunt u de gegevens in het opgegeven HDFS-exportpad verwijderen.

Optie is het gebruik van de opdracht HDFS `hdfs dfs -rm -R` .

## <a name="option-reduce-additional-storage-usage"></a>Optie: extra opslag gebruik verminderen

De actie voor het exporteren van het script verdubbelt waarschijnlijk het opslag gebruik als gevolg van Hive. Het is echter mogelijk om het extra opslag gebruik te beperken door hand matig te migreren, één data base of tabel tegelijk.

1. Geef `--run-script=false` op om de uitvoering van het gegenereerde Hive-script over te slaan. De Hive-scripts voor het exporteren en importeren worden nog steeds opgeslagen in het exportpad.

2. Voer fragmenten uit van de Hive-scripts data base-by-data base of tabel-by-Table uit, reinig het exportpad hand matig na elke gemigreerde data base of tabel.

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Externe metagegevensopslag gebruiken](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Opslag typen en-functies](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Script actie aan een actief cluster](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
