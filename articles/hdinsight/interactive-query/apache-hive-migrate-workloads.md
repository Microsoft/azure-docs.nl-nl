---
title: Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0
description: Meer informatie over het migreren van Apache Hive-workloads op HDInsight 3,6 naar HDInsight 4,0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 43d616bc82c608918f5e7ee51481a393dd55a284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566067"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0

HDInsight 4,0 heeft verschillende voor delen ten opzichte van HDInsight 3,6. Hier volgt een [overzicht van wat er nieuw is in HDInsight 4,0](../hdinsight-version-release.md).

In dit artikel worden de stappen beschreven voor het migreren van Hive-workloads van HDInsight 3,6 naar 4,0, met inbegrip van

* Upgrade kopie en schema Hive-metastore
* Veilige migratie voor ACID-compatibiliteit
* Behoud van Hive-beveiligings beleid

De nieuwe en oude HDInsight-clusters moeten toegang hebben tot dezelfde opslag accounts.

De migratie van Hive-tabellen naar een nieuw opslag account moet als afzonderlijke stap worden uitgevoerd. Zie [Hive-migratie tussen opslag accounts](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade

### <a name="1-prepare-the-data"></a>1. de gegevens voorbereiden

* HDInsight 3,6 biedt standaard geen ondersteuning voor ACID-tabellen. Als er zure tabellen aanwezig zijn, moet u echter ' grote ' compressie uitvoeren. Raadpleeg de [hand leiding](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) van de Hive-taal voor meer informatie over compressie.

* Als u [Azure data Lake Storage gen1](../overview-data-lake-storage-gen1.md)gebruikt, zijn de locaties van Hive-tabellen waarschijnlijk afhankelijk van de HDFS-configuraties van het cluster. Voer de volgende script actie uit om deze locaties te verplaatsen naar andere clusters. Zie [script actie voor een actief cluster](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Eigenschap | Waarde |
    |---|---|
    |Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Knooppunttype(n)|Head|
    |Parameters||

### <a name="2-copy-the-sql-database"></a>2. Kopieer de SQL database

* Als in het cluster een standaard Hive-metastore wordt gebruikt, volgt u deze [hand leiding](./hive-default-metastore-export-import.md) om meta gegevens te exporteren naar een extern archief. Maak vervolgens een kopie van de externe meta Store voor de upgrade.

* Als het cluster een externe Hive-metastore gebruikt, maakt u een kopie ervan. Opties zijn onder andere [exporteren/importeren](../../azure-sql/database/database-export.md) en [herstel naar](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)een bepaald tijdstip.

### <a name="3-upgrade-the-metastore-schema"></a>3. het meta Store-schema bijwerken

Deze stap maakt gebruik [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) van de van HDInsight 4,0 om het meta Store-schema bij te werken.

> [!Warning]
> Deze stap kan niet ongedaan worden gemaakt. Voer dit alleen uit op een kopie van de meta Store.

1. Maak een tijdelijk HDInsight 4,0-cluster om toegang te krijgen tot de component 4,0 `schematool` . U kunt de [standaard Hive-metastore](../hdinsight-use-external-metadata-stores.md#default-metastore) voor deze stap gebruiken.

1. Voer uit het HDInsight 4,0-cluster uit `schematool` om de doel-hdinsight 3,6-meta Store te upgraden:

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Dit hulp programma maakt gebruik `beeline` van client om SQL-scripts uit te voeren in `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` .
    >
    > De SQL-syntaxis in deze scripts is niet noodzakelijkerwijs compatibel met andere client hulpprogramma's. [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) en [query-editor in azure Portal](../../azure-sql/database/connect-query-portal.md) vereisen bijvoorbeeld een sleutel woord `GO` na elke opdracht.
    >
    > Als een script mislukt als gevolg van resource capaciteit of time-outs van trans acties, schaalt u de SQL Database.

1. Controleer de definitieve versie met de query `select schema_version from dbo.version` .

    De uitvoer moet overeenkomen met die van de volgende bash-opdracht uit het HDInsight 4,0-cluster.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Verwijder het tijdelijke HDInsight 4,0-cluster.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. een nieuw HDInsight 4,0-cluster implementeren

Maak een nieuw HDInsight 4,0-cluster en [Selecteer de geüpgradede Hive-metastore](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) en dezelfde opslag accounts.

* Het nieuwe cluster vereist niet hetzelfde standaard bestands systeem.

* Als de meta Store tabellen bevat die zich in meerdere opslag accounts bevinden, moet u deze opslag accounts toevoegen aan het nieuwe cluster voor toegang tot deze tabellen. Zie [extra opslag accounts toevoegen aan HDInsight](../hdinsight-hadoop-add-storage.md).

* Als Hive-taken mislukken vanwege opslag ontoegankelijkheid, controleert u of de tabel locatie zich in een opslag account bevindt dat is toegevoegd aan het cluster.

    Gebruik de volgende Hive-opdracht om de locatie van de tabel te identificeren:

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. tabellen voor ACID-naleving converteren

Beheerde tabellen moeten volgens zuur voldoen aan HDInsight 4,0. Voer uit `strictmanagedmigration` op HDInsight 4,0 om alle niet-zure Managed Tables te converteren naar externe tabellen met een eigenschap `'external.table.purge'='true'` . Uitvoeren vanuit de hoofd knooppunt:

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Component beveiligen in HDInsight-versies

HDInsight kan desgewenst worden geïntegreerd met Azure Active Directory met behulp van HDInsight Enterprise Security Package (ESP). ESP maakt gebruik van Kerberos en Apache zwerver voor het beheren van de machtigingen van specifieke bronnen in het cluster. Zwerver-beleid dat is geïmplementeerd op Hive in HDInsight 3,6 kan worden gemigreerd naar HDInsight 4,0 met de volgende stappen:

1. Navigeer naar het Service Manager paneel zwerver in uw HDInsight 3,6-cluster.
2. Navigeer naar het beleid met de naam **Hive** en exporteer het beleid naar een JSON-bestand.
3. Zorg ervoor dat alle gebruikers waarnaar wordt verwezen in de JSON van het geëxporteerde beleid aanwezig zijn in het nieuwe cluster. Als een gebruiker naar wordt verwezen in de JSON van het beleid, maar niet bestaat in het nieuwe cluster, voegt u de gebruiker toe aan het nieuwe cluster of verwijdert u de verwijzing van het beleid.
4. Navigeer naar het **Service Manager paneel zwerver** in uw HDInsight 4,0-cluster.
5. Navigeer naar het beleid met de naam **Hive** en importeer de JSON-beleids regel uit stap 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Hive-wijzigingen in HDInsight 4,0 waarvoor wijzigingen in de toepassing moeten worden aangebracht

* Zie [aanvullende configuratie met behulp van Hive Warehouse connector](./apache-hive-warehouse-connector.md) voor het delen van de meta Store tussen Spark en Hive voor Acid-tabellen.

* HDInsight 4,0 maakt gebruik [van autorisatie op basis van opslag](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Als u bestands machtigingen wijzigt of als u mappen maakt als een andere gebruiker dan Hive, raakt u waarschijnlijk Hive-fouten op basis van de opslag machtigingen. Verleen toegang tot de gebruiker om het probleem op te lossen `rw-` . Zie de [hand leiding voor HDFS-machtigingen](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` wordt vervangen door `Beeline` .

Raadpleeg de [aankondiging van HDInsight 4,0](../hdinsight-version-release.md) voor meer wijzigingen.

## <a name="further-reading"></a>Meer lezen

* [Aankondiging van HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0 dieper](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 zuren-tabellen](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)