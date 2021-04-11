---
title: Cluster migreren naar een nieuwere versie
titleSuffix: Azure HDInsight
description: Meer informatie over richt lijnen voor het migreren van uw Azure HDInsight-cluster naar een nieuwere versie.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 4aa25368e156ce793e969f866490352e253559fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871721"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight-cluster migreren naar een nieuwere versie

Om te profiteren van de nieuwste HDInsight-functies, raden we aan dat HDInsight-clusters regel matig naar de meest recente versie worden gemigreerd. HDInsight biedt geen ondersteuning voor in-place Upgrades waarbij een bestaande cluster wordt bijgewerkt naar een nieuwere versie van het onderdeel. U moet een nieuw cluster maken met de gewenste onderdelen en platform versie en vervolgens uw toepassingen migreren om het nieuwe cluster te gebruiken. Volg de onderstaande richt lijnen voor het migreren van uw HDInsight-cluster versies.

> [!NOTE]  
> Zie [HDInsight-onderdeel versies](hdinsight-component-versioning.md#supported-hdinsight-versions)voor meer informatie over ondersteunde versies van hdinsight.

## <a name="migration-tasks"></a>Migratie taken

De werk stroom voor het upgraden van een HDInsight-cluster is als volgt.
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="Werk stroom diagram voor HDInsight-upgrade" border="false":::

1. Lees elke sectie van dit document voor meer informatie over wijzigingen die mogelijk vereist zijn bij het upgraden van uw HDInsight-cluster.
2. Maak een cluster als test-of kwaliteits borgings omgeving. Zie [informatie over het maken van HDInsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het maken van een cluster.
3. Kopieer bestaande taken, gegevens bronnen en sinks naar de nieuwe omgeving.
4. Voer validatie tests uit om ervoor te zorgen dat uw taken werken zoals verwacht op het nieuwe cluster.

Zodra u hebt gecontroleerd of alles werkt zoals verwacht, kunt u de downtime voor de migratie plannen. Voer tijdens deze downtime de volgende acties uit:

1. Maak een back-up van tijdelijke gegevens die lokaal zijn opgeslagen op de cluster knooppunten. Bijvoorbeeld als u gegevens rechtstreeks op een hoofd knooppunt hebt opgeslagen.
1. [Verwijder het bestaande cluster](./hdinsight-delete-cluster.md).
1. Maak een cluster in hetzelfde VNET-subnet met de nieuwste (of ondersteunde) HDI-versie met behulp van hetzelfde standaard gegevens archief dat het vorige cluster gebruikt. Hierdoor kan het nieuwe cluster blijven werken met uw bestaande productie gegevens.
1. Importeer alle tijdelijke gegevens waarvan u een back-up hebt gemaakt.
1. Start taken/Ga door met de verwerking met het nieuwe cluster.

## <a name="workload-specific-guidance"></a>Specifieke richt lijnen voor workload

De volgende documenten bevatten richt lijnen voor het migreren van specifieke workloads:

* [HBase migreren](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka migreren](./kafka/migrate-versions.md)
* [Hive/interactieve query migreren](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Back-ups en herstellen

Zie [een Data Base herstellen in Azure SQL database met behulp van automatische database back-ups](../azure-sql/database/recovery-using-backups.md)voor meer informatie over back-up en herstel van de data base.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het maken van HDInsight-clusters op basis van Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Verbinding maken met HDInsight via SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Een op Linux gebaseerd cluster beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Opmerkingen bij de release van HDInsight](./hdinsight-version-release.md)
