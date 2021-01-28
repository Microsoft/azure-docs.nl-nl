---
title: Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0
description: Meer informatie over het migreren van Apache Hive-workloads op HDInsight 3,6 naar HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 93dc565055c6eb413a0c277a9891e5fcfab50345
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941361"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0

Dit document bevat informatie over het migreren van Apache Hive-en LLAP-workloads op HDInsight 3,6 naar HDInsight 4,0. HDInsight 4,0 biedt nieuwere Hive-en LLAP-functies, zoals gerealiseerde weer gaven en caches voor query resultaten. Wanneer u uw workloads migreert naar HDInsight 4,0, kunt u veel nieuwere functies van Hive 3 gebruiken die niet beschikbaar zijn op HDInsight 3,6.

In dit artikel komen de volgende onderwerpen aan bod:

* Migratie van Hive-meta gegevens naar HDInsight 4,0
* Veilige migratie van zuren en niet-zure tabellen
* Behoud van Hive-beveiligings beleid in HDInsight-versies
* Uitvoering van query's en fout opsporing van HDInsight 3,6 naar HDInsight 4,0

Een voor deel van het onderdeel is de mogelijkheid om meta gegevens te exporteren naar een externe data base (waarnaar wordt verwezen als het Hive-archief). De **Hive-meta Store** is verantwoordelijk voor het opslaan van tabel statistieken, met inbegrip van de tabel opslag locatie, kolom namen en tabel index informatie. Voor HDInsight 3,6 en HDInsight 4,0 zijn verschillende meta Store-schema's vereist en er kan geen enkele meta Store worden gedeeld. De aanbevolen manier om de Hive-metastore veilig bij te werken is het bijwerken van een kopie in plaats van het origineel in de huidige productie omgeving. Voor dit document moeten de oorspronkelijke en nieuwe clusters toegang hebben tot hetzelfde opslag account. Daarom heeft het geen betrekking op gegevens migratie naar een andere regio.

## <a name="migrate-from-external-metastore"></a>Migreren vanuit externe meta Store

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. grote compressie uitvoeren op zuren-tabellen in HDInsight 3,6

De tabellen HDInsight 3,6 en HDInsight 4,0 zuren begrijpen zuur verschillen. De enige vereiste actie voor de migratie is het uitvoeren van ' primaire ' compressie voor elke ACID-tabel op het 3,6-cluster. Raadpleeg de [hand leiding](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) van de Hive-taal voor meer informatie over compressie.

### <a name="2-copy-sql-database"></a>2. SQL database kopiëren
Maak een nieuwe kopie van uw externe meta Store. Als u een externe meta Store gebruikt, is een van de veilige en eenvoudige manieren om een kopie van de meta Store te maken, [het herstellen van de data base](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) met een andere naam met behulp van de `RESTORE` functie.  Zie [externe meta gegevensopslag plaatsen in azure HDInsight gebruiken](../hdinsight-use-external-metadata-stores.md) voor meer informatie over het koppelen van een externe metadata voor een HDInsight-cluster.

### <a name="3-upgrade-metastore-schema"></a>3. het meta Store-schema bijwerken
Zodra de meta Store- **kopie** is voltooid, voert u een schema-upgrade script uit in [script actie](../hdinsight-hadoop-customize-cluster-linux.md) op het bestaande HDInsight 3,6-cluster om de nieuwe Meta Store naar Hive 3-schema bij te werken. (Voor deze stap is het niet nodig om de nieuwe Meta Store te koppelen aan een cluster.) Hierdoor kan de Data Base worden gekoppeld als HDInsight 4,0-meta Store.

Gebruik de waarden in de tabel hieronder. Vervang door `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` de juiste waarden voor de Hive-metastore **kopie**, gescheiden door spaties. Neem '. database.windows.net ' niet op bij het opgeven van de naam van de SQL-Server.

|Eigenschap | Waarde |
|---|---|
|Scripttype|- Aangepast|
|Naam|Hive-upgrade|
|Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Knooppunttype(n)|Head|
|Parameters|GEBRUIKERS NAAM DATABASENAME DATA BASE WACHT WOORD|

> [!Warning]  
> De upgrade waarmee het meta gegevens schema van HDInsight 3,6 wordt geconverteerd naar het HDInsight 4,0-schema, kan niet worden omgekeerd.

U kunt de upgrade controleren door de volgende SQL-query uit te voeren op de Data Base:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. een nieuw HDInsight 4,0-cluster implementeren

1. Geef de bijgewerkte meta Store op als de Hive-metastore van het nieuwe cluster.

1. De werkelijke gegevens uit de tabellen zijn echter niet toegankelijk totdat het cluster toegang heeft tot de benodigde opslag accounts.
Zorg ervoor dat de opslag accounts van de Hive-tabellen in het HDInsight 3,6-cluster zijn opgegeven als de primaire of secundaire opslag accounts van het nieuwe HDInsight 4,0-cluster.
Zie [extra opslag accounts toevoegen aan hdinsight](../hdinsight-hadoop-add-storage.md)voor meer informatie over het toevoegen van opslag accounts aan hdinsight-clusters.

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. Voltooi de migratie met een hulp programma na de upgrade in HDInsight 4,0

Beheerde tabellen moeten standaard voldoen aan HDInsight 4,0. Nadat u de migratie van de meta Store hebt voltooid, voert u een hulp programma na de upgrade uit om eerder niet-zure beheerde tabellen compatibel te maken met het HDInsight 4,0-cluster. Met dit hulp programma wordt de volgende conversie toegepast:

|3,6 |4,0 |
|---|---|
|Externe tabellen|Externe tabellen|
|Niet-zure beheerde tabellen|Externe tabellen met de eigenschap external. table. leegmaak ' = ' True '|
|ACID Managed Tables|ACID Managed Tables|

Voer het hulp programma Hive post-upgrade uit van het HDInsight 4,0-cluster met behulp van de SSH-Shell:

1. Maak via SSH verbinding met uw cluster-hoofd knooppunt. Zie [verbinding maken met HDInsight via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) voor instructies.
1. Een aanmeldings shell openen als de Hive-gebruiker door uit te voeren `sudo su - hive`
1. Voer de volgende opdracht uit vanuit de shell.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Wanneer het hulp programma is voltooid, is het Hive-magazijn gereed voor HDInsight 4,0.

## <a name="migrate-from-internal-metastore"></a>Migreren vanuit interne meta Store

Als uw HDInsight 3,6-cluster gebruikmaakt van een interne Hive-metastore, volgt u de onderstaande stappen om een script uit te voeren waarmee Hive-query's worden gegenereerd voor het exporteren van object definities uit de meta Store.

De HDInsight 3,6-en 4,0-clusters moeten hetzelfde opslag account gebruiken.

> [!NOTE]
>
> * In het geval van zuur tabellen wordt een nieuwe kopie van de gegevens onder de tabel gemaakt.
>
> * Dit script ondersteunt alleen de migratie van Hive-data bases,-tabellen en-partities. Andere meta gegevens objecten, zoals weer gaven, Udf's en tabel beperkingen, worden naar verwachting hand matig gekopieerd.
>
> * Als dit script is voltooid, wordt ervan uitgegaan dat het oude cluster niet meer wordt gebruikt voor toegang tot een van de tabellen of data bases waarnaar wordt verwezen in het script.
>
> * Alle beheerde tabellen worden Transactioneel in HDInsight 4,0. U kunt de tabel eventueel niet transactioneel laten door de gegevens te exporteren naar een externe tabel met de eigenschap external. table. leegmaak actie ' = ' True '. Bijvoorbeeld:
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Verbinding maken met het HDInsight 3,6-cluster met behulp van een [SSH-client (Secure Shell)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Down load vanuit de open SSH-sessie het volgende script bestand om een bestand met de naam **AllTables. HQL** te genereren.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Voor een gewoon HDInsight-cluster zonder ESP voert u gewoon uit `exporthive_hdi_3_6.sh` .

    * Voor een cluster met ESP, kinit en wijzig de argumenten in beeline: Voer het volgende uit en definieer de gebruikers en het domein voor de Azure AD-gebruiker met volledige Hive-machtigingen.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Sluit uw SSH-sessie af. Voer vervolgens een SCP-opdracht in om **AllTables. HQL** lokaal te downloaden.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Upload **AllTables. HQL** naar het *nieuwe* HDInsight-cluster.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Gebruik vervolgens SSH om verbinding te maken met het *nieuwe* HDInsight 4,0-cluster. Voer de volgende code uit vanuit een SSH-sessie naar dit cluster:

    Zonder ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Met ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

Het hulp programma post-upgrade voor externe migratie van meta Store is hier niet van toepassing, omdat niet-zure beheerde tabellen van HDInsight 3,6 worden geconverteerd naar zure beheerde tabellen in HDInsight 4,0.

> [!Important]  
> Beheerde tabellen in HDInsight 4,0 (inclusief tabellen die zijn gemigreerd vanuit 3,6) mogen niet worden gebruikt door andere services of toepassingen, waaronder HDInsight 3,6-clusters.

## <a name="secure-hive-across-hdinsight-versions"></a>Component beveiligen in HDInsight-versies

Sinds HDInsight 3,6 wordt HDInsight geïntegreerd met Azure Active Directory met HDInsight Enterprise Security Package (ESP). ESP maakt gebruik van Kerberos en Apache zwerver voor het beheren van de machtigingen van specifieke bronnen in het cluster. Zwerver-beleid dat is geïmplementeerd op Hive in HDInsight 3,6 kan worden gemigreerd naar HDInsight 4,0 met de volgende stappen:

1. Navigeer naar het Service Manager paneel zwerver in uw HDInsight 3,6-cluster.
2. Navigeer naar het beleid met de naam **Hive** en exporteer het beleid naar een JSON-bestand.
3. Zorg ervoor dat alle gebruikers waarnaar wordt verwezen in de JSON van het geëxporteerde beleid aanwezig zijn in het nieuwe cluster. Als een gebruiker naar wordt verwezen in de JSON van het beleid, maar niet bestaat in het nieuwe cluster, voegt u de gebruiker toe aan het nieuwe cluster of verwijdert u de verwijzing van het beleid.
4. Navigeer naar het **Service Manager paneel zwerver** in uw HDInsight 4,0-cluster.
5. Navigeer naar het beleid met de naam **Hive** en importeer de JSON-beleids regel uit stap 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Controleer de compatibiliteit en Wijzig codes naar wens in de app testen

Bij het migreren van werk belastingen, zoals bestaande Program ma's en query's, raadpleegt u de opmerkingen bij de release en documentatie voor wijzigingen en past u waar nodig wijzigingen toe. Als uw HDInsight 3,6-cluster gebruikmaakt van een gedeelde Spark en Hive-metastore, is [aanvullende configuratie vereist met hive Warehouse connector](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Nieuwe app voor productie implementeren

Als u wilt overschakelen naar het nieuwe cluster, bijvoorbeeld door een nieuwe client toepassing te installeren en deze als een nieuwe productie omgeving te gebruiken, of u kunt uw bestaande client toepassing upgraden en overschakelen naar HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4,0 overschakelen naar de productie omgeving

Als er tijdens het testen verschillen zijn gemaakt in de meta Store, moet u de wijzigingen direct bijwerken voordat u overschakelt. In dit geval kunt u de meta Store exporteren & importeren en opnieuw upgraden.

## <a name="remove-the-old-production"></a>De oude productie verwijderen

Nadat u hebt bevestigd dat de release volledig en operationeel is, kunt u versie 3,6 en de vorige meta Store verwijderen. Zorg ervoor dat alles wordt gemigreerd voordat u de omgeving verwijdert.

## <a name="query-execution-across-hdinsight-versions"></a>Uitvoering van query's in HDInsight-versies

Er zijn twee manieren voor het uitvoeren en opsporen van fouten in Hive/LLAP-query's binnen een HDInsight 3,6-cluster. HiveCLI biedt een opdracht regel ervaring en de weer gave [TEZ View/Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md) biedt een op GUI gebaseerde werk stroom.

In HDInsight 4,0 is HiveCLI vervangen door Beeline. De weer gave TEZ/Hive bevat een werk stroom op basis van een GUI. HiveCLI is een thrift-client voor Hiveserver 1 en Beeline is een JDBC-client die toegang biedt tot Hiveserver 2. Beeline kan ook worden gebruikt om verbinding te maken met een ander data base-eind punt dat compatibel is met JDBC. Beeline is out-of-box beschikbaar op HDInsight 4,0 zonder dat hiervoor een installatie nodig is.

## <a name="next-steps"></a>Volgende stappen

* [Aankondiging van HDInsight 4,0](../hdinsight-version-release.md)
* [HDInsight 4,0 dieper](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 zuren-tabellen](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)