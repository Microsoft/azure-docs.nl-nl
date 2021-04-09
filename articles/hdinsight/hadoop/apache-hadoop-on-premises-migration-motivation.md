---
title: 'Voor delen: migratie van on-premises Apache Hadoop naar Azure HDInsight'
description: Meer informatie over de motivatie en voor delen voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 975d72df32027888e217d5da9171dba0ba61f257
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943251"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>On-premises Apache Hadoop clusters migreren naar Azure HDInsight-motivatie en voor delen

Dit artikel is de eerste in een reeks over de best practices voor het migreren van on-premises Apache Hadoop implementaties van het eco-systeem naar Azure HDInsight. Deze reeks artikelen is bedoeld voor mensen die verantwoordelijk zijn voor het ontwerp, de implementatie en de migratie van Apache Hadoop oplossingen in azure HDInsight. De rollen die kunnen profiteren van deze artikelen zijn onder andere Cloud Architects, Hadoop-beheerders en DevOps-technici. Software ontwikkelaars, gegevens technici en gegevens wetenschappers moeten ook profiteren van de uitleg over hoe verschillende typen clusters in de Cloud werken.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Waarom migreren naar Azure HDInsight

Azure HDInsight is een clouddistributie van Hadoop-onderdelen. Azure HDInsight maakt het eenvoudig, snel en kosteneffectief om enorme hoeveelheden gegevens te verwerken. HDInsight bevat de populairste open source-frameworks, zoals:

- Apache Hadoop
- Apache Spark
- Apache Hive met LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Voor delen van Azure HDInsight over on-premises Hadoop

- **Lage kosten** -kosten kunnen worden verminderd door [clusters op aanvraag te maken](../hdinsight-hadoop-create-linux-clusters-adf.md) en alleen te betalen voor wat u gebruikt. Ontkoppelde reken kracht en opslag bieden flexibiliteit door het gegevens volume onafhankelijk van de cluster grootte te bewaren.

- **Automatisch maken van clusters** : voor het automatisch maken van een cluster zijn minimale installatie en configuratie vereist. Automation kan worden gebruikt voor clusters op aanvraag.

- **Beheerde hardware en configuratie** : u hoeft zich geen zorgen te maken over de fysieke hardware of infra structuur met een HDInsight-cluster. U hoeft alleen de configuratie van het cluster op te geven. Azure stelt deze in.

- **Eenvoudig schaal bare** HDInsight biedt u de mogelijkheid om werk belastingen omhoog of omlaag te [schalen](../hdinsight-administer-use-portal-linux.md) . Azure zorgt ervoor dat de herverdeling van gegevens en de herverdeling van de werk belasting zonder onderbreking van de gegevensverwerkings taken worden uitgevoerd.

- **Wereld wijde Beschik baarheid** -HDInsight is beschikbaar in meer [regio's](https://azure.microsoft.com/regions/services/) dan enige andere Big data analyse-aanbieding. Azure HDInsight is ook beschikbaar in Azure Government, China en Duitsland, waarmee u kunt voldoen aan de behoeften van uw bedrijf in belangrijke soevereine gebieden.

- Met **beveiligen en compatibel** -HDInsight kunt u uw zakelijke gegevensassets beveiligen met [Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md), [versleuteling](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)en integratie met [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight voldoet aan de meeste populaire [nalevingsstandaarden](https://azure.microsoft.com/overview/trusted-cloud) van de industrie en de overheid.

- **Vereenvoudigd versie beheer** : Azure HDInsight beheert de versie van de Hadoop-eco-systeem onderdelen en houdt deze up-to-date. Software-updates zijn doorgaans een complex proces voor on-premises implementaties.

- **Kleinere clusters die zijn geoptimaliseerd voor specifieke werk belastingen met minder afhankelijkheden tussen onderdelen** : een typische on-premises Hadoop-installatie maakt gebruik van één cluster dat veel doelen speelt. Met Azure HDInsight kunnen specifieke werkbelasting clusters worden gemaakt. Het maken van clusters voor specifieke werk belastingen verwijdert de complexiteit van het onderhoud van één cluster met toenemende complexiteit.

- **Productiviteit** : u kunt verschillende hulpprogram Ma's voor Hadoop en Spark gebruiken in uw favoriete ontwikkel omgeving.

- **Uitbreid baarheid met aangepaste hulpprogram ma's of toepassingen** van derden-HDInsight-clusters kunnen worden uitgebreid met geïnstalleerde onderdelen en kunnen ook worden geïntegreerd met de andere oplossingen van Big data met [één klik op](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) implementaties van de Azure-markt plaats.

- **Eenvoudig beheer, beheer en controle** : Azure HDInsight kan worden geïntegreerd met [Azure monitor logboeken](../hdinsight-hadoop-oms-log-analytics-tutorial.md) om een enkele interface te bieden waarmee u al uw clusters kunt bewaken.

- **Integratie met andere Azure-Services** -HDInsight kan eenvoudig worden geïntegreerd met andere populaire Azure-Services, zoals de volgende:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- Zelf herstellende **processen en onderdelen** : met HDInsight worden de infra structuur en open source-onderdelen voortdurend gecontroleerd met behulp van een eigen bewakings infrastructuur. Daarnaast worden kritieke fouten, zoals niet-beschik baarheid van open-source onderdelen en knoop punten, automatisch hersteld. Waarschuwingen worden in Ambari geactiveerd als een OSS-onderdeel is mislukt.

Zie het artikel [Wat is Azure HDInsight en de technologie stack van Apache Hadoop](../hadoop/apache-hadoop-introduction.md)voor meer informatie.

## <a name="migration-planning-process"></a>Migratie plannings proces

De volgende stappen worden aanbevolen voor het plannen van een migratie van on-premises Hadoop-clusters naar Azure HDInsight:

1. Inzicht in de huidige on-premises implementatie en topologieën.
2. Inzicht in de huidige project omvang, tijd lijnen en team expertise.
3. Meer informatie over de vereisten van Azure.
4. Bouw een gedetailleerd plan op basis van best practices.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Details verzamelen om een migratie voor te bereiden

Deze sectie bevat sjabloon vragenlijsten waarmee u belang rijke informatie kunt verzamelen over:

- De on-premises implementatie
- Projectgegevens
- Azure-vereisten

### <a name="on-premises-deployment-questionnaire"></a>Vragen lijst on-premises implementatie

| **Vraag** | **Voorbeeld** | **Antwoord** |
|---|---|---|
|**Onderwerp**: **omgeving**|||
|Distributie versie van cluster|HDP 2.6.5, CDH 5,7|
|Systeem onderdelen van Big Data|HDFS, garens, Hive, LLAP, Impala, kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, solr, Sqoop, Oozie, zwerver, Atlas, Falcon, Zeppelin, R|
|Cluster typen|Hadoop, Spark, Confluent Kafka, Storm, solr|
|Aantal clusters|4|
|Aantal hoofd knooppunten|2|
|Aantal worker-knoop punten|100|
|Aantal Edge-knoop punten| 5|
|Totale schijf ruimte|100 TB|
|Configuratie van hoofd knooppunt|m/y, CPU, schijf, etc.|
|Configuratie van gegevens knooppunten|m/y, CPU, schijf, etc.|
|Configuratie van Edge-knoop punten|m/y, CPU, schijf, etc.|
|HDFS-versleuteling?|Yes|
|Hoge beschikbaarheid|HDFS HA, meta Store HA|
|Herstel na nood geval/back-up|Back-upcluster?|  
|Systemen die afhankelijk zijn van het cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integraties van derden|Tableau, GridGain, Qubole, informatica, Splunk|
|**Onderwerp**: **beveiliging**|||
|Perimeterbeveiliging|Firewalls|
|Autorisatie voor cluster verificatie &|Active Directory, Ambari, Cloudera Manager, geen verificatie|
|HDFS Access Control|  Hand matig, SSH-gebruikers|
|Autorisatie van Hive-verificatie &|Sentry, LDAP, AD met Kerberos, zwerver|
|Controleren|Ambari, Cloudera Navigator, zwerver|
|Bewaking|Grafiet, verzamelde, statistieken, telegrafie, InfluxDB|
|Waarschuwingen|Kapacitor, Prometheus, Datadog|
|Duur van gegevens retentie| 3 jaar, 5 jaar|
|Cluster beheerders|Eén beheerder, meerdere beheerders|

### <a name="project-details-questionnaire"></a>Project details vragen lijst

|**Vraag**|**Voorbeeld**|**Antwoord**|
|---|---|---|
|**Onderwerp**: **werk belastingen en frequentie**|||
|MapReduce-taken|10 taken-twee keer per dag||
|Hive-taken|100-taken--elk uur||
|Spark-batch taken|50 taken: elke 15 minuten||
|Spark-streaming-taken|5 taken--elke 3 minuten||
|Structured streaming-taken|5 taken--elke minuut||
|Trainings taken voor ML model|2 taken: één keer per week||
|Programmeer talen|Python, scala, java||
|Uitvoeren van scripts|Shell, python||
|**Onderwerp**: **gegevens**|||
|Gegevensbronnen|Platte bestanden, JSON, Kafka, RDBMS||
|Gegevens indeling|Oozie werk stromen, lucht stroom||
|Zoek opdrachten in het geheugen|Apache Ignite, redis||
|Gegevens bestemmingen|HDFS, RDBMS, Kafka, MPP ||
|**Onderwerp**: **meta gegevens**|||
|DATABASE type van Hive|MySQL, post gres||
|Aantal Hive-meta Stores|2||
|Aantal Hive-tabellen|100||
|Aantal beleids regels voor Zwerver|20||
|Aantal Oozie-werk stromen|100||
|**Onderwerp**: **schalen**|||
|Gegevens volume inclusief replicatie|100 TB||
|Dagelijks opname volume|50 GB||
|Groei tempo van gegevens|10% per jaar||
|Groei tempo van cluster knooppunten|5% per jaar
|**Onderwerp**: **cluster gebruik**|||
|Gemiddeld CPU-percentage gebruikt|60%||
|Gemiddeld geheugen gebruikt%|75%||
|Gebruikte schijf ruimte|75%||
|Gemiddeld netwerk gebruikt%|25%
|**Onderwerp**: **personeel**|||
|Aantal beheerders|2||
|Aantal ontwikkel aars|10||
|Aantal eind gebruikers|100||
|Vaardigheden|Hadoop, Spark||
|Aantal beschik bare resources voor migratie taken|2||
|**Onderwerp**: **beperkingen**|||
|Huidige beperkingen|Latentie is hoog||
|Huidige uitdagingen|Probleem met gelijktijdigheid||

### <a name="azure-requirements-questionnaire"></a>Vragen lijst Azure-vereisten

|**Vraag**|**Voorbeeld**|**Antwoord**|
|---|---|---|
|**Onderwerp**: **infra structuur** |||
| Voorkeurs regio|US - oost||
|Voor keur voor VNet?|Yes||
|HA/DR nodig?|Yes||
|Integratie met andere Cloud Services?|ADF, CosmosDB||
|**Onderwerp**:   **gegevens verplaatsing**  |||
|Initiële laad voorkeur|DistCp, data box, ADF, WANDisco||
|Delta voor gegevens overdracht|DistCp, AzCopy||
|Voortdurende incrementele gegevens overdracht|DistCp, Sqoop||
|**Onderwerp**:   **bewaking & waarschuwingen** |||
|Azure monitoring &-waarschuwingen gebruiken versus bewaking van derden integreren|Azure monitoring &-waarschuwingen gebruiken||
|**Onderwerp**:   **beveiligings voorkeuren** |||
|Persoonlijke en beveiligde gegevens pijplijn?|Yes||
|Aan het domein toegevoegd cluster (ESP)?|     Yes||
|On-premises AD Sync naar de Cloud?|     Yes||
|Aantal AD-gebruikers dat moet worden gesynchroniseerd?|          100||
|Wilt u wacht woorden synchroniseren met de Cloud?|    Yes||
|Alleen gebruikers in de Cloud?|                 Yes||
|MFA vereist?|                       No|| 
|Vereisten voor gegevens autorisatie?|  Yes||
|Op rollen gebaseerd toegangs beheer?|        Yes||
|Controle vereist?|                  Yes||
|Gegevens versleuteling in rust?|          Yes||
|Gegevens versleuteling in transit?|       Yes||
|**Onderwerp**:   **voor keuren voor opnieuw bearchitectuur** |||
|Eén cluster versus specifieke cluster typen|Specifieke cluster typen||
|Opgeslagen opslag versus externe opslag?|Externe opslag||
|Kleinere cluster grootte naarmate gegevens extern worden opgeslagen?|Kleinere cluster grootte||
|Gebruikt u meerdere kleinere clusters in plaats van één groot cluster?|Meerdere kleinere clusters gebruiken||
|Een externe meta Store gebruiken?|Yes||
|Wilt u de meta Stores delen tussen verschillende clusters?|Yes||
|Werk belastingen ontconstrueren?|Hive-taken vervangen door Spark-taken||
|ADF gebruiken voor gegevens indeling?|Nee||

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie:

- [Aanbevolen procedures voor architectuur voor on-premises migratie van Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
