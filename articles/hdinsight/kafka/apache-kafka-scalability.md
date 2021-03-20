---
title: Schaal Apache Kafka verg Roten-Azure HDInsight
description: Leer hoe u beheerde schijven voor een Apache Kafka-cluster configureert in Azure HDInsight om de schaalbaarheid te verhogen.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: f22642ae94ea01a798b1eab639c93fda31f87581
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944061"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Opslag en schaalbaarheid configureren voor Apache Kafka in HDInsight

Meer informatie over het configureren van het aantal Managed disks dat wordt gebruikt door [Apache Kafka](https://kafka.apache.org/) op HDInsight.

Kafka in HDInsight maakt gebruik van de lokale schijf van de virtuele machines in het HDInsight-cluster. Aangezien Kafka veel gebruikmaakt van invoer/uitvoer, wordt [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md) gebruikt voor een hoge doorvoer en meer opslag per knooppunt. Als u de traditionele VHD (virtuele harde schijven) gebruikt voor Kafka, heeft elk knooppunt een limiet van 1 TB. Met beheerde schijven kunt u meerdere schijven gebruiken en zodat elk knooppunt in het cluster een limiet heeft van 16 TB.

In het volgende diagram ziet u een vergelijking tussen Kafka in HDInsight voordat beheerde schijven werden gebruikt, en Kafka in HDInsight met beheerde schijven:

![architectuur van Kafka met Managed disks](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Beheerde schijven configureren: Azure Portal

1. Volg de stappen in [Een HDInsight-cluster maken](../hdinsight-hadoop-create-linux-clusters-portal.md) voor de algemene stappen voor het maken van een cluster met behulp van de portal. Voltooi het proces voor het maken van de portal niet.

2. Gebruik in de sectie **configuratie-& prijzen** het veld __aantal knoop punten__ om het aantal schijven te configureren.

    > [!NOTE]  
    > Het type beheerde schijf is __Standaard__ (HDD) of __Premium__ (SSD). Premium-schijven worden gebruikt met virtuele machines uit de DS- en GS-reeks. Alle andere VM-typen gebruiken standaardschijven.

    ![sectie cluster grootte met de gemarkeerde schijven per werk knooppunt](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Beheerde schijven configureren: Resource Manager-sjabloon

Als u het aantal schijven wilt beheren dat wordt gebruikt in de werkknooppunten van een Kafka-cluster, gebruikt u de volgende sectie van de sjabloon:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

U vindt een volledige sjabloon die laat zien hoe u beheerde schijven kunt configureren op [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) .

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor meer informatie over het werken met Apache Kafka op HDInsight:

* [MirrorMaker gebruiken voor het maken van een replica van Apache Kafka in HDInsight](apache-kafka-mirroring.md)
* [Apache Storm gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Verbinding maken met Apache Kafka via een Azure-Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [HDInsight-blog op beheerde schijven met Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
