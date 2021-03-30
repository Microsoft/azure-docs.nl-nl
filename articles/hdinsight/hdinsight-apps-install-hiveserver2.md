---
title: HiveServer2 schalen op Azure HDInsight
description: Schaal HiveServer2 horizon taal op Azure HDInsight-clusters met behulp van Edge-knoop punten om fout tolerantie en beschik baarheid te verhogen.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88227192"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>HiveServer2 schalen op Azure HDInsight-clusters voor hoge Beschik baarheid

Meer informatie over hoe u een extra HiveServer2 implementeert in uw cluster om de beschik baarheid en belasting distributie te verg Roten. Wanneer u de hoofd knooppunt-grootte niet wilt verhogen, kunt u ook Edge-knoop punten gebruiken om HiveServer2 te implementeren. 

> [!NOTE]
> Afhankelijk van uw gebruik kan het verhogen van het aantal HiveServer2 het aantal verbindingen met de Hive-metastore verg Roten. Zorg er ook voor dat uw Azure-SQL database op de juiste manier wordt aangepast.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt gebruiken, moet u het volgende artikel begrijpen:
- [Lege Edge-knoop punten op Apache Hadoop clusters in HDInsight gebruiken](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>HiveServer2 installeren

In deze sectie installeert u een extra HiveServer2 op de doel-hosts.

1. Open Ambari in uw browser en klik op uw doelhost.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Het menu hosts van Ambari.":::

2. Klik op de knop toevoegen en klik op HiveServer2

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="HiveServer2 paneel van host toevoegen.":::

3. Bevestig dat het proces wordt uitgevoerd. Herhaal 1-3 voor alle gewenste hosts.

4. Wanneer u klaar bent met installeren, start u alle services opnieuw met verlopen configuraties en start u HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Start het deel venster HiveServer2.":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u HiveServer2 kunt installeren op uw cluster. Raadpleeg de volgende artikelen voor meer informatie over Edge-knoop punten en-toepassingen:

* [Edge-knoop punt installeren](hdinsight-apps-use-edge-node.md): informatie over het installeren van een Edge-knoop punt op uw HDInsight-cluster.
* [HDInsight-toepassingen installeren](hdinsight-apps-install-applications.md): informatie over het installeren van een HDInsight-toepassing op uw clusters.
* [Azure SQL-DTU-verbindings limieten](../azure-sql/database/resource-limits-dtu-single-databases.md): meer informatie over Azure SQL database limieten met DTU.
* [Limieten voor Azure SQL vCore-verbinding](../azure-sql/database/resource-limits-vcore-elastic-pools.md): meer informatie over limieten voor Azure SQL database met behulp van vCores.
