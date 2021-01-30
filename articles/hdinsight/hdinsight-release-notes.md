---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 88e2161cfddf95f7f250b8b76c067d045f1529da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092231"
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
Vanaf deze release kunnen klanten met de Azure KeyValut-versie versleutelings sleutel-Url's voor door de klant beheerde sleutel versleuteling in rust gebruiken. HDInsight roteert de sleutels automatisch wanneer deze verlopen of worden vervangen door nieuwe versies. Meer informatie [vindt u hier](./disk-encryption.md).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Mogelijkheid om verschillende Zookeeper van virtuele machines te selecteren voor Spark-, Hadoop-en ML-Services
HDInsight biedt nog geen ondersteuning voor het aanpassen van de Zookeeper-knooppunt grootte voor de cluster typen Spark, Hadoop en ML Services. De standaard instelling is dat de grootte van de virtuele machines van/a2 wordt A2_v2, die gratis worden meegeleverd. In deze versie kunt u een grootte van een virtuele Zookeeper-machine selecteren die het meest geschikt is voor uw scenario. Zookeeper-knoop punten met een andere grootte dan A2_v2/a2 worden in rekening gebracht. A2_v2 en a2 virtuele machines worden nog gratis beschikbaar gesteld.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. Vanaf deze release wordt de service geleidelijk naar [virtuele-machine schaal sets van Azure](../virtual-machine-scale-sets/overview.md)gemigreerd. Het hele proces kan maanden duren. Nadat uw regio's en abonnementen zijn gemigreerd, worden nieuw gemaakte HDInsight-clusters uitgevoerd op virtuele-machine schaal sets zonder klant acties. Er wordt geen breuk wijziging verwacht.

## <a name="deprecation"></a>Afschaffing
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Afschaffing van HDInsight 3,6 ML Services-cluster
Het cluster type van HDInsight 3,6 ML-Services is de eind datum van de ondersteuning van december 31 2020. Klanten kunnen na december 31 2020 geen nieuwe clusters van 3,6 ML-services meer maken. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Controleer [hier](./hdinsight-component-versioning.md#available-versions)het ondersteunings verloop voor HDInsight-versies en cluster typen.

### <a name="disabled-vm-sizes"></a>Uitgeschakelde VM-grootten
Vanaf november 16 2020 worden nieuwe klanten die clusters maken met behulp van standand_A8, standand_A9 standand_A10 en standand_A11 VM-grootten geblokkeerd door HDInsight. Bestaande klanten die deze VM-grootten in de afgelopen drie maanden hebben gebruikt, worden niet beïnvloed. Formulier wordt gestart 9 2021. HDInsight blokkeert alle klanten die clusters maken met behulp van standand_A8, standand_A9, standand_A10 en standand_A11 VM-grootten. Bestaande clusters worden uitgevoerd. Overweeg om over te stappen op HDInsight 4,0 om mogelijke onderbreking van systeem/ondersteuning te voor komen.

## <a name="behavior-changes"></a>Gedrags wijzigingen
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>Controle van NSG-regel toevoegen vóór schaal bewerking
Met HDInsight zijn netwerk beveiligings groepen (Nsg's) en Udr's-controle (User-defined routes) toegevoegd met een schaal bewerking. Dezelfde validatie wordt uitgevoerd voor het schalen van clusters behalve bij het maken van het cluster. Deze validatie helpt onvoorspelbare fouten te voor komen. Als de validatie niet is geslaagd, mislukt het schalen. Zie [IP-adressen voor HDInsight-beheer](./hdinsight-management-ip-addresses.md)voor meer informatie over het correct configureren van Nsg's en udr's.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases.

### <a name="breaking-change-for-net-for-apache-spark-100"></a>Belang rijke wijziging voor .NET voor Apache Spark 1.0.0
HDInsight introduceert de eerste belang rijke officiële versie van .NET voor Apache Spark in de volgende release. Het biedt data frame API-volledigheid voor Spark 2.4. x en Spark 3.0. x, samen met andere functies. Als er wijzigingen worden opgesplitst voor deze primaire versie, raadpleegt u [deze migratie-GUID](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) om de stappen te begrijpen die nodig zijn om uw code en pijp lijnen bij te werken. Klik [hier](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight) voor meer informatie.

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>De standaard grootte van een cluster-VM wordt gewijzigd in de Ev3-serie
Vanaf de volgende release (rond einde januari) worden standaard cluster-VM-grootten gewijzigd van D-serie naar Ev3-familie. Deze wijziging is van toepassing op hoofd knooppunten en worker-knoop punten. U kunt deze wijziging vermijden door de VM-grootten op te geven die u wilt gebruiken in de ARM-sjabloon.

### <a name="default-cluster-version-will-be-changed-to-40"></a>De standaard versie van het cluster wordt gewijzigd in 4,0
Vanaf februari 2021 wordt de standaard versie van het HDInsight-cluster gewijzigd van 3,6 in 4,0. Zie [beschik bare versies](./hdinsight-component-versioning.md#available-versions)voor meer informatie over de beschik bare versies. Meer informatie over wat er nieuw is in [HDInsight 4,0](./hdinsight-version-release.md)

### <a name="os-version-upgrade"></a>Upgrade van besturingssysteem versie
Bij HDInsight wordt de versie van het besturings systeem bijgewerkt van 16,04 naar 18,04. De upgrade wordt voltooid vóór 2021 april.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6 end of support op 30 2021 juni
HDInsight 3,6 wordt beëindigd. Het formulier wordt gestart 30 2021, klanten kunnen geen nieuwe HDInsight 3,6-clusters maken. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om over te stappen op HDInsight 4,0 om mogelijke onderbreking van systeem/ondersteuning te voor komen.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. In [dit document](./hdinsight-component-versioning.md)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.

## <a name="known-issues"></a>Bekende problemen
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>Voorkomen dat VM's in HDInsight-cluster periodiek opnieuw opstarten

Vanaf medio november 2020 hebt u mogelijk gedetecteerd dat Vm's van het HDInsight-cluster regel matig opnieuw worden opgestart. Dit kan worden veroorzaakt door:

1.  Clamav is ingeschakeld in uw cluster. Het nieuwe azsec-clamav-pakket verbruikt een grote hoeveelheid geheugen die het opnieuw opstarten van knoop punten activeert. 
2.  Een CRON-taak is dagelijks gepland, waarmee wordt gecontroleerd op wijzigingen in de lijst met certificerings instanties (Ca's) die worden gebruikt door Azure-Services. Wanneer een nieuw CA-certificaat beschikbaar is, voegt het script het certificaat toe aan het vertrouwens archief van de JDK en wordt opnieuw opgestart gepland.

HDInsight is het implementeren van oplossingen en het Toep assen van patch voor alle actieve clusters voor beide problemen. Als u de correctie onmiddellijk wilt Toep assen en wilt voor komen dat er onverwachte Vm's opnieuw worden opgestart, kunt u onderstaande script acties op alle cluster knooppunten uitvoeren als een permanente script actie. Er wordt nog een bericht weer gegeven na de oplossing en de patch is voltooid.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```