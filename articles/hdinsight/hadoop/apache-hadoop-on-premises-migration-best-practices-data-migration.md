---
title: 'Gegevens migratie: on-premises Apache Hadoop naar Azure HDInsight'
description: Meer informatie over de aanbevolen procedures voor gegevens migratie voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cdb028bcd608aa7a19a7c83e5e2c1129386928a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939785"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>On-premises Apache Hadoop clusters migreren naar de aanbevolen procedures voor het migreren van Azure HDInsight-gegevens

Dit artikel bevat aanbevelingen voor het migreren van gegevens naar Azure HDInsight. Het is onderdeel van een serie die aanbevolen procedures biedt voor het migreren van on-premises Apache Hadoop systemen naar Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>On-premises gegevens migreren naar Azure

Er zijn twee belang rijke opties voor het migreren van gegevens van on-premises naar Azure-omgeving:

* Gegevens overdragen via het netwerk met TLS
    * Via internet: u kunt gegevens overdragen naar Azure Storage via een reguliere Internet verbinding met een van de volgende hulpprogram ma's, zoals: Azure Storage Explorer, AzCopy, Azure Power shell en Azure CLI. Zie [gegevens verplaatsen van en naar Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md)voor meer informatie.

    * Express route-ExpressRoute is een Azure-service waarmee u particuliere verbindingen kunt maken tussen micro soft-data centers en-infra structuur op uw locatie of in een functie voor samen locatie. ExpressRoute-verbindingen gaan niet via het open bare Internet en bieden betere beveiliging, betrouw baarheid en snelheden met lagere latenties dan typische verbindingen via internet. Zie [een ExpressRoute-circuit maken en wijzigen](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)voor meer informatie.

    * Data Box Online gegevens overdracht: Data Box Edge en Data Box Gateway zijn online gegevens overdracht producten die fungeren als gateways voor netwerk opslag om gegevens te beheren tussen uw site en Azure. Data Box Edge, een on-premises netwerk apparaat, brengt gegevens over van en naar Azure en maakt gebruik van een Edge Compute met kunst matige intelligentie (AI) voor het verwerken van gegevens. Data Box Gateway is een virtueel apparaat met mogelijkheden voor opslag gateway. Zie [Azure data Box documentatie-online overdracht](../../databox-online/index.yml)voor meer informatie.

* Gegevens offline verzenden

    Data Box offline gegevens overdracht: Data Box-, Data Box Disk-en Data Box Heavy-apparaten kunt u grote hoeveel heden gegevens naar Azure overdragen wanneer het netwerk geen optie is. Deze apparaten voor offline gegevens overdracht worden verzonden tussen uw organisatie en het Azure-Data Center. Ze gebruiken AES-versleuteling om uw gegevens tijdens de overdracht te beschermen en ze ondervinden een verouderd proces voor het opschonen van de upload om uw gegevens van het apparaat te verwijderen. Zie [Azure data Box-documentatie-offline overdracht](../../databox/index.yml)voor meer informatie over de data Box apparaten voor offline overdracht. Zie [Azure data box gebruiken om te migreren van een on-premises HDFS-Store naar Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)voor meer informatie over de migratie van Hadoop-clusters.

De volgende tabel heeft geschatte duur van gegevens overdracht op basis van het gegevens volume en de netwerk bandbreedte. Gebruik een Data box als de gegevens migratie meer dan drie weken naar verwachting duurt.

|Hoeveelheid gegevens | Netwerk bandbreedte<br>van<br>**45 Mbps (T3)**|Netwerk bandbreedte<br>van<br>**100 Mbps**|Netwerk bandbreedte<br>van<br>**1 Gbps**|Netwerk bandbreedte<br>van<br>**10 Gbps**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 dagen|1 dag| 2 uur|14 minuten|
|10 TB|22 dagen|10 dagen|1 dag|2 uur|
|35 TB|76 dagen|34 dagen|3 dagen|8 uur|
|80 TB|173 dagen|78 dagen|8 dagen|19 uur|
|100 TB|216 dagen|97 dagen|10 dagen|1 dag|
|200 TB|1 jaar|194 dagen|19 dagen|2 dagen|
|500 TB|3 jaar|1 jaar|49 dagen|5 dagen|
|1 PB|6 jaar|3 jaar|97 dagen|10 dagen|
|2 PB|12 jaar|Vijf jaar|194 dagen|19 dagen|

Hulpprogram ma's systeem eigen naar Azure, zoals Apache Hadoop DistCp, Azure Data Factory en AzureCp, kunnen worden gebruikt voor het overdragen van gegevens via het netwerk. De WANDisco van het hulp programma van derden kunnen ook voor hetzelfde doel einde worden gebruikt. Apache Kafka MirrorMaker en Apache Sqoop kunnen worden gebruikt voor doorlopende gegevens overdracht van on-premises naar Azure-opslag systemen.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Prestatie overwegingen bij het gebruik van Apache Hadoop DistCp

DistCp is een Apache-project dat gebruikmaakt van een MapReduce-toewijzings taak voor het overdragen van gegevens, het afhandelen van fouten en het herstellen van deze fouten. Er wordt een lijst met bron bestanden aan elke kaart taak toegewezen. De toewijzings taak kopieert vervolgens alle toegewezen bestanden naar de bestemming. Er zijn verschillende technieken waarmee de prestaties van DistCp kunnen worden verbeterd.

### <a name="increase-the-number-of-mappers"></a>Verhoog het aantal mappers

DistCp probeert toewijzings taken te maken zodat elk exemplaar ongeveer hetzelfde aantal bytes heeft. DistCp-taken gebruiken standaard 20 mappers. Het gebruik van meer mappers voor Distcp (met de para meter ' op de opdracht regel) verhoogt de parallellisme tijdens het proces van gegevens overdracht en vermindert de lengte van de gegevens overdracht. Er zijn echter twee dingen waarmee u rekening moet houden bij het verhogen van het aantal mappers:

* De laagste granulatie van DistCp is één bestand. Het opgeven van een aantal mappers is groter dan het aantal bron bestanden dat niet helpt en zal de beschik bare cluster bronnen verspillen.

* Houd rekening met het beschik bare garen geheugen op het cluster om het aantal Mapper te bepalen. Elke kaart taak wordt gestart als een garen-container. Ervan uitgaande dat er geen andere zware werk belastingen op het cluster worden uitgevoerd, kan het aantal mappers worden bepaald door de volgende formule: m = (aantal worker-knoop punten \* garen geheugen voor elk worker-knoop punt)/grootte van de garen-container. Als andere toepassingen echter gebruikmaken van geheugen, moet u ervoor kiezen om alleen een deel van het geheugen van de DistCp te gebruiken voor de taken van de werk ruimte.

### <a name="use-more-than-one-distcp-job"></a>Meer dan één DistCp-taak gebruiken

Wanneer de grootte van de gegevensset die moet worden verplaatst groter is dan 1 TB, gebruikt u meer dan één DistCp-taak. Het gebruik van meer dan één taak beperkt de gevolgen van fouten. Als een taak mislukt, hoeft u die specifieke taak alleen opnieuw te starten in plaats van alle taken.

### <a name="consider-splitting-files"></a>Overweeg om bestanden te splitsen

Als er sprake is van een klein aantal grote bestanden, kunt u overwegen deze te splitsen in bestands segmenten van 256 MB om meer potentiële gelijktijdigheid te verkrijgen met meer toewijzingen.

### <a name="use-the-strategy-command-line-parameter"></a>Gebruik de opdracht regel parameter ' strategie '

Overweeg `strategy = dynamic` het gebruik van para meters op de opdracht regel. De standaard waarde van de `strategy` para meter is `uniform size` , in welk geval elke toewijzing een kopie heeft van ongeveer hetzelfde aantal bytes. Als deze para meter wordt gewijzigd in `dynamic` , wordt het vermelding bestand gesplitst in meerdere ' chunk-files '. Het aantal chunk-bestanden is een veelvoud van het aantal toewijzingen. Aan elke toewijzings taak is een van de segment bestanden toegewezen. Nadat alle paden in een segment zijn verwerkt, wordt het huidige segment verwijderd en wordt er een nieuwe chunk verkregen. Het proces wordt voortgezet totdat er geen segmenten meer beschikbaar zijn. Met deze ' dynamische ' benadering kunt u snellere toewijzings taken gebruiken om meer paden te verbruiken dan tragere, waardoor de DistCp-taak in het algemeen sneller verloopt.

### <a name="increase-the-number-of-threads"></a>Het aantal threads verhogen

Bekijk of het verhogen van de `-numListstatusThreads` para meter de prestaties verbetert. Deze para meter bepaalt het aantal threads dat moet worden gebruikt voor het maken van bestands vermelding en 40 is de maximum waarde.

### <a name="use-the-output-committer-algorithm"></a>Het doorvoer algoritme voor uitvoer gebruiken

Bekijk of de para meter wordt door gegeven, `-Dmapreduce.fileoutputcommitter.algorithm.version=2` verbetert de prestaties van DistCp. Dit algoritme voor het door voeren van de uitvoer heeft optimalisaties rondom het schrijven van uitvoer bestanden naar het doel. De volgende opdracht is een voor beeld waarin het gebruik van verschillende para meters wordt weer gegeven:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migratie van meta gegevens

### <a name="apache-hive"></a>Apache Hive

De Hive-meta Store kan worden gemigreerd met behulp van de scripts of door gebruik te maken van de database replicatie.

#### <a name="hive-metastore-migration-using-scripts"></a>Hive-metastore migratie met behulp van scripts

1. Genereer de Hive DDLs van on-premises Hive-metastore. Deze stap kan worden uitgevoerd met behulp van een [wrapper bash-script](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Bewerk de gegenereerde DDL om HDFS-URL te vervangen door WASB/ADLS/ABFS-Url's.
1. Voer de bijgewerkte DDL uit op de meta Store vanuit het HDInsight-cluster.
1. Zorg ervoor dat de Hive-metastore-versie compatibel is tussen on-premises en de Cloud.

#### <a name="hive-metastore-migration-using-db-replication"></a>Hive-metastore migratie met behulp van DB-replicatie

- Database replicatie instellen tussen on-premises Hive-metastore DB en HDInsight meta Store DB.
- Gebruik het ' hive-hulp programma ' om de HDFS-URL te vervangen door WASB/ADLS/ABFS-url's, bijvoorbeeld:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache zwerver

- Een on-premises zwerver-beleid exporteren naar XML-bestanden.
- Transformeer specifieke, op HDFS gebaseerde paden naar WASB/ADLS met behulp van een hulp programma zoals XSLT.
- Importeer het beleid voor zwerver op HDInsight.

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie:

- [Best practices voor beveiliging en DevOps voor on-premises migratie van Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)