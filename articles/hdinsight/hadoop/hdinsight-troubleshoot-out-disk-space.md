---
title: Er is onvoldoende schijf ruimte voor het cluster knooppunt in azure HDInsight
description: Problemen met de schijf ruimte van Apache Hadoop cluster knooppunten in azure HDInsight oplossen.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: c58ac1e65509299680f67cf17060ff3e9af4c817
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944356"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scenario: er is onvoldoende schijf ruimte beschikbaar voor het cluster knooppunt in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Een taak kan mislukken met een fout bericht van de volgende strekking: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Of u ontvangt een Apache Ambari-waarschuwing die vergelijkbaar is met: `local-dirs usable space is below configured utilization percentage` .

## <a name="cause"></a>Oorzaak

Apache garen-toepassings cache heeft mogelijk alle beschik bare schijf ruimte verbruikt. Uw Spark-toepassing wordt waarschijnlijk niet efficiënt uitgevoerd.

## <a name="resolution"></a>Oplossing

1. Gebruik de Ambari-gebruikers interface om te bepalen welk knoop punt bijna geen schijf ruimte meer heeft.

1. Bepaal welke map in het knoop punt zorgwekkend de meeste schijf ruimte draagt. SSH eerst naar het knoop punt en voer vervolgens uit `df` om het schijf gebruik voor alle koppels weer te geven. Meestal is dit `/mnt` een tijdelijke schijf die wordt gebruikt door OSS. U kunt een map invoeren en vervolgens typen `sudo du -hs` om de samenvatte bestands grootten in een map weer te geven. Als er een map met de volgende strekking wordt weer gegeven `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` , betekent dit dat de toepassing nog steeds wordt uitgevoerd. Dit kan het gevolg zijn van RDD-persistentie of tussenliggende bestanden met een wille keurige volg orde.

1. Als u het probleem wilt verhelpen, moet u de toepassing afbreken, waardoor de schijf ruimte die door de toepassing wordt gebruikt, wordt vrijgegeven.

1. Als het probleem regel matig optreedt op de worker-knoop punten, kunt u de instellingen voor de lokale cache van het garen afstemmen op het cluster.

    Open de Ambari-gebruikers interface navigeer naar GARENs--> configuraties--> Geavanceerd.  
    Voeg de volgende twee eigenschappen toe aan de sectie Custom yarn-site.xml en sla het bestand op:

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. Als het probleem hiermee niet permanent wordt opgelost, optimaliseert u uw toepassing.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]