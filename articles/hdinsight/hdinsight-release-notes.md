---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607215"
---
# <a name="azure-hdinsight-release-notes"></a>Opmerkingen bij de release van Azure HDInsight

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

Als u zich wilt abonneren op release opmerkingen, kijkt u naar releases op [deze github-opslag plaats](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Release datum: 03/24/2021

Deze versie is van toepassing op zowel HDInsight 3,6 als HDInsight 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

## <a name="new-features"></a>Nieuwe functies
### <a name="spark-30-preview"></a>Spark 3,0-Preview
HDInsight heeft [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) -ondersteuning toegevoegd aan HDInsight 4,0 als preview-functie. 

### <a name="kafka-24-preview"></a>Kafka 2,4 preview
HDInsight heeft [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) -ondersteuning toegevoegd aan HDInsight 4,0 als preview-functie.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. De service wordt geleidelijk naar virtuele- [machine schaal sets van Azure](../virtual-machine-scale-sets/overview.md)gemigreerd. Het hele proces kan maanden duren. Nadat uw regio's en abonnementen zijn gemigreerd, worden nieuw gemaakte HDInsight-clusters uitgevoerd op virtuele-machine schaal sets zonder klant acties. Er wordt geen breuk wijziging verwacht.

## <a name="deprecation"></a>Afschaffing
Geen afschaffing in deze release.

## <a name="behavior-changes"></a>Gedrags wijzigingen
### <a name="default-cluster-version-is-changed-to-40"></a>De standaard versie van het cluster is gewijzigd in 4,0
De standaard versie van het HDInsight-cluster is gewijzigd van 3,6 naar 4,0. Zie [beschik bare versies](./hdinsight-component-versioning.md)voor meer informatie over de beschik bare versies. Meer informatie over wat er nieuw is in [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>De standaard grootte van de VM-cluster is gewijzigd in Ev3-serie 
De standaard grootte van virtuele cluster-vm's wordt gewijzigd van D-serie naar Ev3-Series. Deze wijziging is van toepassing op hoofd knooppunten en worker-knoop punten. Geef de VM-grootten op die u wilt gebruiken in de ARM-sjabloon om te voor komen dat deze wijziging invloed heeft op uw geteste werk stromen.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>De netwerk interface bron is niet zichtbaar voor clusters die worden uitgevoerd op virtuele-machine schaal sets van Azure
HDInsight wordt geleidelijk gemigreerd naar virtuele-machine schaal sets van Azure. Netwerk interfaces voor virtuele machines zijn niet meer zichtbaar voor klanten voor clusters die gebruikmaken van virtuele-machine schaal sets van Azure.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases.

### <a name="os-version-upgrade"></a>Upgrade van besturingssysteem versie
Bij HDInsight wordt de versie van het besturings systeem bijgewerkt van Ubuntu 16,04 naar 18,04. De upgrade wordt voltooid vóór 2021 april.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6 end of support op 30 2021 juni
HDInsight 3,6 wordt beëindigd. Het formulier wordt gestart 30 2021, klanten kunnen geen nieuwe HDInsight 3,6-clusters maken. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om over te stappen op HDInsight 4,0 om mogelijke onderbreking van systeem/ondersteuning te voor komen.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is ondersteuning toegevoegd voor Spark 3.0.0 en Kafka 2.4.1 als preview. In [dit document](./hdinsight-component-versioning.md)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.
