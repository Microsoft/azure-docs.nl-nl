---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 03874f76772d8722c7161ef43a2297c2e01b7da9
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95748832"
---
# <a name="azure-hdinsight-release-notes"></a>Opmerkingen bij de release van Azure HDInsight

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

Als u zich wilt abonneren op release opmerkingen, kijkt u naar releases op [deze github-opslag plaats](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Release datum: 11/18/2020

Deze versie is van toepassing op zowel HDInsight 3,6 als HDInsight 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

## <a name="new-features"></a>Nieuwe functies
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Automatische rotatie van de sleutel voor door de klant beheerde sleutel versleuteling bij rest
Vanaf deze release kunnen klanten met de Azure KeyValut-versie versleutelings sleutel-Url's voor door de klant beheerde sleutel versleuteling in rust gebruiken. HDInsight roteert de sleutels automatisch wanneer deze verlopen of worden vervangen door nieuwe versies. Meer informatie [vindt u hier](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Mogelijkheid om verschillende Zookeeper van virtuele machines te selecteren voor Spark-, Hadoop-en ML-Services
HDInsight biedt nog geen ondersteuning voor het aanpassen van de Zookeeper-knooppunt grootte voor de cluster typen Spark, Hadoop en ML Services. De standaard instelling is dat de grootte van de virtuele machines van/a2 wordt A2_v2, die gratis worden meegeleverd. In deze versie kunt u een grootte van een virtuele Zookeeper-machine selecteren die het meest geschikt is voor uw scenario. Zookeeper-knoop punten met een andere grootte dan A2_v2/a2 worden in rekening gebracht. A2_v2 en a2 virtuele machines worden nog gratis beschikbaar gesteld.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. Vanaf deze release wordt de service geleidelijk naar [virtuele-machine schaal sets van Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)gemigreerd. Het hele proces kan maanden duren. Nadat uw regio's en abonnementen zijn gemigreerd, worden nieuw gemaakte HDInsight-clusters uitgevoerd op virtuele-machine schaal sets zonder klant acties. Er wordt geen breuk wijziging verwacht.

## <a name="deprecation"></a>Afschaffing
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Afschaffing van HDInsight 3,6 ML Services-cluster
Het cluster type van HDInsight 3,6 ML-Services is de eind datum van de ondersteuning van december 31 2020. Klanten maken geen nieuwe 3,6 ML-clusters na december 31 2020. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Controleer [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)het ondersteunings verloop voor HDInsight-versies en cluster typen.

### <a name="disabled-vm-sizes"></a>Uitgeschakelde VM-grootten
Vanaf november 16 2020 worden nieuwe klanten die clusters maken met behulp van standand_A8, standand_A9 standand_A10 en standand_A11 VM-grootten geblokkeerd door HDInsight. Bestaande klanten die deze VM-grootten in de afgelopen drie maanden hebben gebruikt, worden niet beïnvloed. Formulier wordt gestart 9 2021. HDInsight blokkeert alle klanten die clusters maken met behulp van standand_A8, standand_A9, standand_A10 en standand_A11 VM-grootten. Bestaande clusters worden uitgevoerd. Overweeg om over te stappen op HDInsight 4,0 om mogelijke onderbreking van systeem/ondersteuning te voor komen.

### <a name="behavior-changes"></a>Gedrags wijzigingen
Er is geen wijziging van het gedrag voor deze versie.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases.

### <a name="default-cluster-version-will-be-changed-to-40"></a>De standaard versie van het cluster wordt gewijzigd in 4,0
Vanaf februari 2021 wordt de standaard versie van het HDInsight-cluster gewijzigd van 3,6 in 4,0. Zie [beschik bare versies](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)voor meer informatie over de beschik bare versies. Meer informatie over wat er nieuw is in [HDInsight 4,0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release)

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6 end of support op 30 2021 juni
HDInsight 3,6 wordt beëindigd. Het formulier wordt gestart 30 2021, klanten kunnen geen nieuwe HDInsight 3,6-clusters maken. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om over te stappen op HDInsight 4,0 om mogelijke onderbreking van systeem/ondersteuning te voor komen.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. In [dit document](./hdinsight-component-versioning.md)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.
