---
title: Clusters optimaliseren met Apache Ambari in azure HDInsight
description: Gebruik de Apache Ambari Web-UI om Azure HDInsight-clusters te configureren en te optimaliseren.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 2146ccb0c4d7f263c3e1a69db9b172649fcd25ea
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863493"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Clusters optimaliseren met Apache Ambari in azure HDInsight

HDInsight biedt Apache Hadoop clusters voor grootschalige toepassingen voor gegevens verwerking. Het beheren, bewaken en optimaliseren van deze complexe clusters met meerdere knoop punten kan lastig zijn. Apache Ambari is een webinterface voor het beheren en controleren van HDInsight Linux-clusters.

Zie [HDInsight-clusters beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md) voor een inleiding tot het gebruik van de Ambari-webgebruikersinterface.

Meld u aan bij Ambari `https://CLUSTERNAME.azurehdidnsight.net` met uw cluster referenties. In het eerste scherm wordt een overzichts dashboard weer gegeven.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png" alt-text="Apache Ambari-gebruikers dashboard weer gegeven":::

De Ambari-webgebruikersinterface wordt gebruikt voor het beheren van hosts, services, waarschuwingen, configuraties en weer gaven. Ambari kan niet worden gebruikt om een HDInsight-cluster of upgrade services te maken. Kan ook geen stacks en versies, hosts buiten gebruik stellen of opnieuw in de handel worden gebracht, of services toevoegen aan het cluster.

## <a name="manage-your-clusters-configuration"></a>De configuratie van uw cluster beheren

Met configuratie-instellingen kunt u een bepaalde service afstemmen. Als u de configuratie-instellingen van een service wilt wijzigen, selecteert u de service in de zijbalk **Services** (aan de linkerkant). Ga vervolgens naar het tabblad **configuratie** op de pagina Service Details.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png" alt-text="Terzijde voor Apache Ambari Services":::

## <a name="modify-namenode-java-heap-size"></a>Grootte van NameNode Java-heap wijzigen

De grootte van de NameNode Java-heap is afhankelijk van veel factoren, zoals de belasting van het cluster. Ook het aantal bestanden en het aantal blokken. De standaard grootte van 1 GB werkt goed voor de meeste clusters, maar sommige werk belastingen kunnen meer of minder geheugen vereisen.

De grootte van de NameNode Java-heap wijzigen:

1. Selecteer **HDFS** in de services-zijbalk en navigeer naar het tabblad **configuratie** .

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png" alt-text="Apache Ambari HDFS-configuratie":::

1. Zoek de instelling **NameNode Java Heap-grootte**. U kunt ook het tekstvak **filteren** gebruiken om een bepaalde instelling te typen en te zoeken. Selecteer het pictogram met de **pen** naast de naam van de instelling.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png" alt-text="Ambari NameNode Java-Heap-grootte":::

1. Typ de nieuwe waarde in het tekstvak en druk vervolgens op **Enter** om de wijziging op te slaan.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png" alt-text="Ambari bewerken NameNode Java-heap size1":::

1. De grootte van de NameNode Java-heap is gewijzigd van 1 GB van 2 GB.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png" alt-text="Bewerkte NameNode Java-heap size2":::

1. Sla uw wijzigingen op door te klikken op de groene knop **Opslaan** boven aan het configuratie scherm.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png" alt-text="' Apache Ambari Save-configuraties '":::

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache HBase optimaliseren](./optimize-hbase-ambari.md)
* [Apache Hive optimaliseren](./optimize-hive-ambari.md)
* [Apache Pig optimaliseren](./optimize-pig-ambari.md)
