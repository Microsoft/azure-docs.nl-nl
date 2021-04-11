---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065494"
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

### <a name="eav4-series-support"></a>Ondersteuning voor Eav4-Series
HDInsight heeft Eav4-Series-ondersteuning toegevoegd in deze versie. Meer informatie over de [Dav4-serie](../virtual-machines/eav4-easv4-series.md). De reeks is beschikbaar gesteld in de volgende regio's: 

* Australië - oost
* Brazilië - zuid
* Central US
* Azië - oost
* VS - oost
* Japan - oost
* Azië - zuidoost
* Verenigd Koninkrijk Zuid
* Europa -west
* VS - west 2

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

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Basic Support voor HDInsight 3,6 vanaf 1 juli 2021
Vanaf 1 juli 2021 biedt micro soft [Basic Support](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) voor bepaalde cluster typen van HDInsight 3,6. Het Basic Support-abonnement is beschikbaar tot 3 april 2022. U wordt automatisch Inge schreven bij Basic Support vanaf 1 juli 2021. U hoeft geen actie te ondernemen om u aan te melden. Zie de [documentatie](hdinsight-36-component-versioning.md) waarvoor cluster typen onder Basic Support worden opgenomen. 

Het is niet raadzaam nieuwe oplossingen te bouwen op HDInsight 3,6, wijzigingen in bestaande 3,6-omgevingen te blok keren. U wordt aangeraden [uw clusters te migreren naar HDInsight 4,0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Meer informatie over [wat er nieuw is in HDInsight 4,0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is ondersteuning toegevoegd voor Spark 3.0.0 en Kafka 2.4.1 als preview. In [dit document](./hdinsight-component-versioning.md)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.

## <a name="recommanded-features"></a>Opnieuw ingeopdrachtde onderdelen
### <a name="service-tags"></a>Servicetags
Service Tags vereenvoudigen het beperken van de netwerk toegang tot de Azure-Services voor virtuele Azure-machines en virtuele netwerken van Azure. Met Service tags in uw NSG-regels (netwerk beveiligings groep) wordt verkeer naar een specifieke Azure-service toegestaan of geweigerd. De regel kan globaal of per Azure-regio worden ingesteld. Azure biedt het onderhoud van IP-adressen die onder elke tag liggen. HDInsight-service tags voor netwerk beveiligings groepen (Nsg's) zijn groepen met IP-adressen voor status-en beheer Services. Deze groepen helpen de complexiteit te minimaliseren voor het maken van de beveiligings regel. HDInsight-klanten kunnen een servicetag inschakelen via Azure Portal, Power shell en REST API. Zie [NSG-service tags (netwerk beveiligings groep) voor Azure HDInsight](./hdinsight-service-tags.md)voor meer informatie.
