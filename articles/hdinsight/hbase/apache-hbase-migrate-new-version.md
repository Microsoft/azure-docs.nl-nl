---
title: Een HBase-cluster migreren naar een nieuwe versie-Azure HDInsight
description: Apache HBase-clusters migreren naar een nieuwere versie in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 43b46d19503856f5eae38272299f73d9c80055b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868882"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Een Apache HBase-cluster migreren naar een nieuwe versie

In dit artikel worden de stappen beschreven die nodig zijn om uw Apache HBase-cluster in azure HDInsight bij te werken naar een nieuwere versie.

De downtime tijdens de upgrade moet mini maal zijn, in de volg orde van minuten. Deze downtime wordt veroorzaakt door de stappen voor het leegmaken van alle gegevens in het geheugen en vervolgens de tijd voor het configureren en opnieuw starten van de services op het nieuwe cluster. De resultaten zijn afhankelijk van het aantal knoop punten, de hoeveelheid gegevens en andere variabelen.

## <a name="review-apache-hbase-compatibility"></a>De Apache HBase-compatibiliteit controleren

Controleer voordat u Apache HBase upgradet of de HBase-versies op de bron-en doel clusters compatibel zijn. Zie [Apache Hadoop onderdelen en versies die beschikbaar zijn met HDInsight](../hdinsight-component-versioning.md)voor meer informatie.

> [!NOTE]  
> We raden u ten zeerste aan de versie compatibiliteits matrix in het [HBase-boek](https://hbase.apache.org/book.html#upgrading)te controleren. Eventuele incompatibiliteits problemen moeten worden beschreven in de release opmerkingen van de HBase-versie.

Hier volgt een voorbeeld matrix voor de compatibiliteits versie. Y duidt op compatibiliteit en N duidt op een mogelijke incompatibiliteit:

| Compatibiliteits type | Primaire versie| Secundaire versie | Patch |
| --- | --- | --- | --- |
| Compatibiliteit met Client-Server bedrading | N | J | J |
| Server-Server compatibiliteit | N | J | J |
| Compatibiliteit van bestands indelingen | N | J | J |
| Compatibiliteit van client-API | N | J | J |
| Binaire client compatibiliteit | N | N | J |
| **Beperkte API-compatibiliteit aan de server zijde** |  |  |  |
| Stabiel | N | J | J |
| Spelen | N | N | J |
| Biel | N | N | N |
| Compatibiliteit van afhankelijkheden | N | J | J |
| Operationele compatibiliteit | N | N | J |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>Upgrade met dezelfde HBase primaire versie van Apache

Voer de volgende stappen uit om uw Apache HBase-cluster in azure HDInsight bij te werken:

1. Zorg ervoor dat uw toepassing compatibel is met de nieuwe versie, zoals wordt weer gegeven in de HBase-compatibiliteits matrix en opmerkingen bij de release. Test uw toepassing in een cluster waarop de doel versie van HDInsight en HBase wordt uitgevoerd.

1. [Stel een nieuw doel-HDInsight-cluster](../hdinsight-hadoop-provision-linux-clusters.md) in met hetzelfde opslag account, maar met een andere container naam:

   :::image type="content" source="./media/apache-hbase-migrate-new-version/same-storage-different-container.png" alt-text="Hetzelfde opslag account gebruiken, maar een andere container maken" border="true":::

1. Maak uw bron HBase-cluster leeg, het cluster dat u wilt upgraden. HBase schrijft inkomende gegevens naar een in-Memory Store, een _geheugen opslag_ genoemd. Nadat de geheugen opslag een bepaalde grootte heeft bereikt, wordt deze door HBase naar de schijf leeg gemaakt voor lange termijn opslag in het opslag account van het cluster. Wanneer u het oude cluster verwijdert, worden de memstores gerecycled, waardoor gegevens verloren kunnen gaan. Als u de geheugen opslag voor elke tabel hand matig wilt leegmaken, voert u het volgende script uit. De meest recente versie van dit script bevindt zich in de [github](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)van Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. Stop de opname naar het oude HBase-cluster.

1. Voer het vorige script opnieuw uit om ervoor te zorgen dat alle recente gegevens in de geheugen opslag worden leeg gemaakt.

1. Meld u aan bij [Apache Ambari](https://ambari.apache.org/) op het oude cluster ( `https://OLDCLUSTERNAME.azurehdidnsight.net` ) en stop de HBase-Services. Wanneer u wordt gevraagd om te bevestigen dat u de services wilt stoppen, schakelt u het selectie vakje onderhouds modus inschakelen voor HBase in. Zie [HDInsight-clusters beheren met behulp van de Ambari-webgebruikersinterface](../hdinsight-hadoop-manage-ambari.md)voor meer informatie over het maken van verbinding met en het gebruik van Ambari.

    :::image type="content" source="./media/apache-hbase-migrate-new-version/stop-hbase-services1.png" alt-text="Klik in Ambari op Services > HBase > stop onder service acties" border="true":::

    :::image type="content" source="./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png" alt-text="Schakel het selectie vakje onderhouds modus inschakelen voor HBase in en bevestig" border="true":::

1. Als u geen HBase-clusters gebruikt met de verbeterde functie voor schrijf bewerkingen, slaat u deze stap over. Het is alleen nodig voor HBase-clusters met verbeterde functie voor schrijf bewerkingen.

   Back-up maken van de WAL dir onder HDFS door de volgende opdrachten uit te voeren vanuit een SSH-sessie op een van de Zookeeper-knoop punten of werk knooppunten van het oorspronkelijke cluster.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup**
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup**
   ```
    
1. Meld u aan bij Ambari op het nieuwe HDInsight-cluster. Wijzig de `fs.defaultFS` instelling HDFS zodat deze verwijst naar de container naam die door het oorspronkelijke cluster wordt gebruikt. Deze instelling bevindt zich onder **HDFS > configs > advanced > Advanced core-site**.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="Klik in Ambari op Services > HDFS > configs > Advanced" border="true":::

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name.png" alt-text="Wijzig in Ambari de naam van de container" border="true":::

1. Als u geen HBase-clusters gebruikt met de verbeterde functie voor schrijf bewerkingen, slaat u deze stap over. Het is alleen nodig voor HBase-clusters met verbeterde functie voor schrijf bewerkingen.

   Wijzig het `hbase.rootdir` pad zodat dit verwijst naar de container van het oorspronkelijke cluster.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="Wijzig in Ambari de container naam voor HBase rootdir" border="true":::
    
1. Als u geen HBase-clusters gebruikt met de verbeterde functie voor schrijf bewerkingen, slaat u deze stap over. Het is alleen nodig voor HBase-clusters met verbeterde schrijf functies en alleen in gevallen waarin uw oorspronkelijke cluster een HBase-cluster was met verbeterde functie voor schrijf bewerkingen.

   Reinig de Zookeeper-en WAL-FS-gegevens voor dit nieuwe cluster. Geef de volgende opdrachten op in een van de Zookeeper-knoop punten of worker-knoop punten:

   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit

   hdfs dfs -rm -r hdfs://mycluster/hbasewal**
   ```

1. Als u geen HBase-clusters gebruikt met de verbeterde functie voor schrijf bewerkingen, slaat u deze stap over. Het is alleen nodig voor HBase-clusters met verbeterde functie voor schrijf bewerkingen.
   
   Zet de WAL dir terug naar de HDFS van het nieuwe cluster vanuit een SSH-sessie op een van de Zookeeper-knoop punten of werk knooppunten van het nieuwe cluster.
   
   ```bash
   hdfs dfs -cp /hbase-wal-backup/hbasewal hdfs://mycluster/**
   ```
   
1. Als u HDInsight 3,6 bijwerkt naar 4,0, volgt u de onderstaande stappen en gaat u verder met stap 13:

    1. Start alle vereiste services in Ambari opnieuw door **Services**  >  **opnieuw starten vereist** te selecteren.
    1. Stop de HBase-service.
    1. SSH naar het Zookeeper-knoop punt en voer de [zkCli](https://github.com/go-zkcli/zkcli) -opdracht `rmr /hbase-unsecure` uit om de HBase root znode van Zookeeper te verwijderen.
    1. Start HBase opnieuw.

1. Als u een upgrade uitvoert naar een andere HDInsight-versie dan 4,0, voert u de volgende stappen uit:
    1. Sla uw wijzigingen op.
    1. Start alle vereiste services opnieuw op zoals aangegeven door Ambari.

1. Wijs uw toepassing naar het nieuwe cluster.

    > [!NOTE]  
    > De statische DNS voor uw toepassing verandert wanneer u een upgrade uitvoert. In plaats van deze DNS-code ring uit te voeren, kunt u een CNAME in de DNS-instellingen van uw domein naam configureren die verwijst naar de naam van het cluster. Een andere optie is het gebruik van een configuratie bestand voor uw toepassing dat u kunt bijwerken zonder opnieuw te implementeren.

1. Start de opname om te controleren of alles werkt zoals verwacht.

1. Verwijder het oorspronkelijke cluster als het nieuwe cluster voldoende is.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over [Apache HBase](https://hbase.apache.org/) en het upgraden van HDInsight-clusters:

* [Een HDInsight-cluster upgraden naar een nieuwere versie](../hdinsight-upgrade-cluster.md)
* [Azure HDInsight bewaken en beheren met de Web-UI van Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop onderdelen en versies](../hdinsight-component-versioning.md)
* [Apache HBase optimaliseren](../optimize-hbase-ambari.md)
