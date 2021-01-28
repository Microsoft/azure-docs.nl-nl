---
title: Apache Hadoop onderdelen en versies-Azure HDInsight
description: Meer informatie over de Apache Hadoop onderdelen en versies in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 07db57ee36d39454e8a247be11dac4a9c51af3af
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946101"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Apache-onderdelen en-versies die beschikbaar zijn voor Azure HDInsight

In dit artikel vindt u informatie over de onderdelen en versies van de [Apache Hadoop](https://hadoop.apache.org/) -omgeving in azure HDInsight en de Enterprise Security Package. U leert ook hoe u de versies van de Hadoop-onderdelen in HDInsight kunt controleren.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Apache-onderdelen die beschikbaar zijn met verschillende HDInsight-versies

Azure HDInsight ondersteunt meerdere Hadoop-cluster versies die op elk gewenst moment kunnen worden geïmplementeerd. Op 4 april 2017 is de standaard versie van het cluster die door Azure HDInsight wordt gebruikt 3,6.

De versies van de onderdelen die zijn gekoppeld aan HDInsight-cluster versies, worden weer gegeven in de volgende tabel.

> [!NOTE]
> De standaard versie voor de HDInsight-service kan zonder kennisgeving worden gewijzigd. Als u een versie afhankelijkheid hebt, geeft u de HDInsight-versie op wanneer u uw clusters maakt met de .NET SDK met Azure PowerShell en de klassieke Azure-CLI.

| Onderdeel              | HDInsight 4.0 | HDInsight 3,6 (standaard)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop en garen | 3.1.1         | 2.7.3                       |
| Apache TEZ             | 0.9.1         | 0.7.0                       |
| Apache-Pig             | 0.16.1        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 op ESP-interactieve query) |
| Apache TEZ Hive2       | -             | 0.8.4                       |
| Apache zwerver          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | verschijnsel.                      |
| Apache Livy            | 0,5           | 0,4.                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4,0: Spark 2,4 en Kafka 2,1 worden volledig ondersteund. Spark 2,3-en Kafka 1,1-cluster typen worden echter niet verwerkt. HDInsight 3,6: Spark 2,3 en Kafka 1,1 worden volledig ondersteund.  

## <a name="check-for-current-apache-component-version-information"></a>Controleren op huidige versie gegevens van Apache-onderdeel

De versie van de Hadoop-omgevings onderdelen die zijn gekoppeld aan HDInsight-cluster versies kunnen veranderen met updates voor HDInsight. Als u de Hadoop-onderdelen wilt controleren en wilt controleren welke versies worden gebruikt voor een cluster, gebruikt u de Ambari-REST API. De **GetComponentInformation** -opdracht haalt informatie op over service onderdelen. Zie de [Apache Ambari-documentatie](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)voor meer informatie.

### <a name="release-notes"></a>Releaseopmerkingen

Zie [release opmerkingen voor hdinsight](hdinsight-release-notes.md)voor aanvullende opmerkingen bij de release over de nieuwste versies van hdinsight.

## <a name="supported-hdinsight-versions"></a>Ondersteunde HDInsight-versies

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Verval en buiten gebruiks telling voor HDInsight-versies ondersteunen

**Verval** van de ondersteuning houdt in dat micro soft geen ondersteuning meer biedt voor de specifieke HDInsight-versie. En is niet meer beschikbaar via de Azure Portal voor het maken van een cluster. Deze versies kunnen nog steeds worden gemaakt met behulp van de Azure CLI of de verschillende Sdk's.

**Buiten** gebruik stellen wordt aangegeven dat bestaande clusters van een HDInsight-versie als zodanig blijven worden uitgevoerd. Nieuwe clusters van deze versie kunnen niet op een wille keurige manier worden gemaakt, met inbegrip van de CLI en Sdk's. Andere functies van het besturings systeem, zoals hand matig schalen en automatisch schalen, werken mogelijk niet na het buiten gebruik stellen van de versie. Ondersteuning is niet beschikbaar voor niet-verouderde versies.

De volgende tabellen geven een lijst van de versies van HDInsight. De ondersteunings vervaldatums en pensioen datums worden ook gegeven wanneer ze bekend zijn.

### <a name="available-versions"></a>Beschik bare versies

Deze tabel geeft een lijst van de versies van HDInsight die beschikbaar zijn in de Azure Portal en via andere implementatie methoden, zoals Power shell en de .NET SDK.

| HDInsight-versie | VM-besturingssysteem | Releasedatum | Verval datum ondersteuning | Buitengebruikstellings datum | Hoge beschikbaarheid |  Beschik baarheid in de Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |September 24, 2018 | | |Ja |Ja |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4 april 2017      | * 30 juni 2021 |30 juni 2021 |Ja |Ja |

* We verlengen de ondersteunings periode voor bepaalde cluster typen van HDInsight 3,6

| Clustertype                    | Framework-versie | Huidige ondersteunings termijn        | Nieuwe verval datum van ondersteuning |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 31 december 2020                      | 30 juni 2021               |
| HDInsight 3,6 Spark             | 2.3               | 31 december 2020                      | 30 juni 2021               |
| HDInsight 3,6 Spark             | 2.2               | Ingetrokken op 30 juni 2020          |                             |
| HDInsight 3,6 Spark             | 2.1               | Ingetrokken op 30 juni 2020          |                             |
| HDInsight 3,6 Kafka             | 1.1               | 31 december 2020                      | 30 juni 2021               |
| HDInsight 3,6 Kafka             | 1.0               | Ingetrokken op 30 juni 2020.         |                             |
| HDInsight 3,6 HBase             | 1.1               | 31 december 2020                      | 30 juni 2021               |
| HDInsight 3,6 Interactive-query | 2.1               | 31 december 2020                      | 30 juni 2021               |
| HDInsight 3,6 Storm             | 1.1               | 31 december 2020                      | 30 juni 2021               |
| HDInsight 3,6 ML-Services      | 9.3               | 31 december 2020                      | 31 december 2020                |

> [!NOTE]
> Nadat de ondersteuning voor een cluster versie is verlopen, is deze mogelijk niet meer beschikbaar via de Azure Portal. In sommige gevallen blijven cluster versies beschikbaar met behulp van de para meter **Version** in de Windows Power shell [New-AzHDInsightCluster-](/powershell/module/az.hdinsight/new-azhdinsightcluster) opdracht en de .NET SDK tot de datum waarop de versie is uitgevallen.

### <a name="retired-versions"></a>Buiten gebruik gestelde versies

Deze tabel geeft een lijst van de versies van HDInsight die niet beschikbaar zijn in de Azure Portal.

| HDInsight-versie | HDP-versie | VM-besturingssysteem | Releasedatum | Verval datum ondersteuning | Buitengebruikstellings datum | Hoge beschikbaarheid |  Beschik baarheid in de Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |30 september 2016 |5 september 2017 |28 juni 2018 |Ja |Nee |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |29 maart 2016 |29 december 2016 |9 januari 2018 |Ja |Nee |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2 december 2015 |27 juni 2016 |31 juli 2018 |Ja |Nee |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2 december 2015 |27 juni 2016 |31 juli 2017 |Ja |Nee |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS of Windows Server 2012 R2 |18 februari 2015 |1 maart 2016 |1 april 2017 |Ja |Nee |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |24 juni 2014 |18 mei 2015 |30 juni 2016 |Ja |Nee |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |11 februari 2014 |17 september 2014 |30 juni 2015 |Ja |Nee |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |28 oktober 2013 |12 mei 2014 |31 mei 2015 |Ja |Nee |
| HDInsight 1,6 |HDP 1,1 | |28 oktober 2013 |26 april 2014 |31 mei 2015 |Nee |Nee |

> [!NOTE]
> Maxi maal beschik bare clusters met twee hoofd knooppunten worden standaard geïmplementeerd voor HDInsight-versie 2,1 en hoger. Ze zijn niet beschikbaar voor HDInsight-versie 1,6-clusters.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Service Level overeenkomst voor HDInsight-cluster versies

De Service Level Agreement wordt gedefinieerd als een _ondersteunings venster_. Een ondersteunings venster is de periode waarin een HDInsight-versie wordt ondersteund door de klanten service en ondersteuning van micro soft. Als de versie de _verval datum_ van de ondersteuning heeft door gegeven, bevindt het HDInsight-cluster zich buiten het ondersteunings venster. Ondersteuning voor verloop tijd van HDInsight-versie X (nadat een nieuwere X + 1-versie beschikbaar is), is de volgende:

- **Formule 1:** 180 dagen toevoegen aan de datum waarop de versie X van het HDInsight-cluster is uitgebracht.
- **Formule 2:** Voeg 90 dagen toe aan de datum waarop de versie van het HDInsight-cluster, X + 1, beschikbaar wordt gemaakt in de Azure Portal.

De _datum van pensionering_ is de datum waarna de Cluster versie niet kan worden gemaakt op HDInsight. Vanaf 31 juli 2017 kunt u de grootte van een HDInsight-cluster niet wijzigen na de aftredings datum.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standaard knooppunt configuratie en grootte van virtuele machines voor clusters

Zie [Configuratie Details van Azure HDInsight-cluster](hdinsight-supported-node-configuration.md)voor meer informatie over de virtuele-machine-sku's die u voor uw cluster wilt selecteren.

## <a name="next-steps"></a>Volgende stappen

- [Setup van het cluster voor Apache Hadoop, Spark en meer op HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Release opmerkingen voor Hortonworks die zijn gekoppeld aan Azure HDInsight-versies](./hortonworks-release-notes.md)
- [Werken in Apache Hadoop op HDInsight vanaf een Windows-computer](hdinsight-hadoop-windows-tools.md)
