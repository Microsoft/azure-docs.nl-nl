---
title: Versnelde schrijfbewerkingen van Azure HDInsight voor Apache HBase
description: Geeft een overzicht van de functie voor versneld schrijven van Azure HDInsight, die gebruikmaakt van Premium Managed disks om de prestaties van het Apache HBase write-Ahead logboek te verbeteren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: 1f5a9f05b94f43db9e1d883ef5e86f8621f39251
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943033"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Versnelde schrijfbewerkingen van Azure HDInsight voor Apache HBase

In dit artikel vindt u achtergrond informatie over de functie voor **Geversnellene schrijf bewerkingen** voor Apache HBase in azure HDInsight en hoe u deze kunt gebruiken om schrijf prestaties te verbeteren. **Versnelde schrijf bewerkingen** maakt gebruik van [Azure Premium SSD Managed disks](../../virtual-machines/disks-types.md#premium-ssd) om de prestaties te verbeteren van het Apache HBase write Ahead log (Wal). Zie [Wat is Apache HBase in HDInsight](apache-hbase-overview.md)voor meer informatie over Apache HBase.

## <a name="overview-of-hbase-architecture"></a>Overzicht van HBase-architectuur

In HBase bestaat een **rij** uit een of meer **kolommen** en wordt deze aangeduid met een **rij-sleutel**. Meerdere rijen vormen een **tabel**. Kolommen bevatten **cellen** die een tijds tempel hebben van de waarde in die kolom. Kolommen worden gegroepeerd in **kolom families** en alle kolommen in een kolom familie worden samen opgeslagen in opslag bestanden met de naam **HFiles**.

**Regio's** in HBase worden gebruikt om de belasting van gegevens verwerking te verdelen. HBase slaat eerst de rijen van een tabel op in één regio. De rijen worden verdeeld over meerdere regio's naarmate de hoeveelheid gegevens in de tabel toeneemt. **Regio servers** kunnen aanvragen voor meerdere regio's verwerken.

## <a name="write-ahead-log-for-apache-hbase"></a>Write-Ahead logboek voor Apache HBase

HBase schrijft eerst gegevens updates naar een type doorvoer logboek dat een write-Ahead logboek (WAL) wordt genoemd. Nadat de update is opgeslagen in de WAL, wordt deze geschreven naar de **geheugen opslag** in het geheugen. Als de maximale capaciteit van de gegevens in het geheugen is bereikt, wordt deze naar de schijf geschreven als een **HFile**.

Als een **RegionServer** vastloopt of niet meer beschikbaar is voordat de geheugen opslag wordt leeg gemaakt, kan het Write-Ahead logboek worden gebruikt voor het opnieuw afspelen van updates. Zonder de WAL, als een **RegionServer** vastloopt voordat updates naar een **HFile** worden verwijderd, gaan al deze updates verloren.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>De functie voor versnelde schrijf bewerkingen in azure HDInsight voor Apache HBase

Met de functie voor versnelde schrijf bewerkingen wordt het probleem opgelost dat wordt veroorzaakt door het gebruik van schrijf voorschrijvende Logboeken in de Cloud opslag.  De functie voor versnelde schrijf bewerkingen voor HDInsight Apache HBase-clusters, koppelt Premium SSD-Managed disks aan elke RegionServer (worker-knoop punt). Naar voren geschreven logboeken worden vervolgens naar het Hadoop-bestands systeem (HDFS) genoteerd dat is gekoppeld aan deze Premium beheerde schijven in plaats van in de Cloud opslag.  Premium beheerde schijven gebruiken Solid-State schijven (Ssd's) en bieden uitstekende I/O-prestaties met fout tolerantie.  In tegens telling tot niet-beheerde schijven, is het niet van invloed op andere opslag eenheden in dezelfde beschikbaarheidsset als de ene opslag eenheid uitvalt.  Als gevolg hiervan bieden beheerde schijven een lage schrijf latentie en een betere tolerantie voor uw toepassingen. Zie [Introduction to Azure Managed disks](../../virtual-machines/managed-disks-overview.md)(Engelstalig) voor meer informatie over Azure Managed disks.

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Versnelde schrijf bewerkingen inschakelen voor HBase in HDInsight

Als u een nieuw HBase-cluster wilt maken met de functie voor versnelde schrijf bewerkingen, volgt u de stappen in [clusters instellen in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Op het tabblad **basis beginselen** selecteert u cluster type als HBase, geeft u een onderdeel versie op en klikt u vervolgens op het selectie vakje naast **HBase versneld schrijven inschakelen**. Ga vervolgens door met de resterende stappen voor het maken van het cluster.

![Optie voor versneld schrijven inschakelen voor HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-create-hbase-wals.png)

## <a name="other-considerations"></a>Andere overwegingen

Als u de duurzaamheid van gegevens wilt behouden, maakt u een cluster met mini maal drie werk knooppunten. Nadat u het cluster hebt gemaakt, kunt u het niet omlaag schalen naar minder dan drie werk knooppunten.

Flush of schakel uw HBase-tabellen uit voordat u het cluster verwijdert, zodat er geen write-Ahead logboek gegevens verloren gaan.

```
flush 'mytable'
```

```
disable 'mytable'
```

Volg dezelfde stappen bij het schalen van het cluster: uw tabellen leegmaken en tabellen uitschakelen om binnenkomende gegevens te stoppen. U kunt uw cluster niet omlaag schalen naar minder dan drie knoop punten.

Als u deze stappen volgt, zorgt u ervoor dat er een succes volle schaal is en kunt u voor komen dat een namenode in de veilige modus wordt uitgevoerd vanwege gerepliceerde of tijdelijke bestanden.

Als uw namenode na een geschaald naar de veilige modus is, gebruikt u hdfs-opdrachten om de onder-gerepliceerde blokken opnieuw te repliceren en op basis van de veilige weer gave. Met deze nieuwe replicatie kunt u HBase opnieuw starten.

## <a name="next-steps"></a>Volgende stappen

* Officiële Apache HBase-documentatie over de [logboek functie Write Ahead](https://hbase.apache.org/book.html#wal)
* Zie [een Apache HBase-cluster migreren naar een nieuwe versie](apache-hbase-migrate-new-version.md)om uw HDInsight Apache HBase-cluster bij te werken om versneld schrijven te gebruiken.
