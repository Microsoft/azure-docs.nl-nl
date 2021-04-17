---
title: Opmerkingen bij de release van Azure HDInsight
description: Meest recente releasenotities voor Azure HDInsight. Krijg tips voor ontwikkeling en details voor Hadoop, Spark, R Server, Hive en meer.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 838eb517697c0625139058a19c7def764e869ed5
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588170"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight opmerkingen bij de release

Dit artikel bevat informatie over de **meest recente Azure HDInsight** release-updates. Zie [HdInsight Release Notes Archive](hdinsight-release-notes-archive.md)voor meer informatie over eerdere releases.

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services onder zakelijke klanten voor opensource-analyses in Azure.

Als u zich wilt abonneren op opmerkingen bij de release, bekijkt u releases op [deze GitHub-opslagplaats.](https://github.com/hdinsight/release-notes/releases)

## <a name="release-date-03242021"></a>Releasedatum: 24-03-2021

Deze release is van toepassing op zowel HDInsight 3.6 als HDInsight 4.0. De HDInsight-release wordt gedurende enkele dagen beschikbaar gesteld voor alle regio's. De releasedatum hier geeft de releasedatum van de eerste regio aan. Als u onderstaande wijzigingen niet ziet, wacht u enkele dagen tot de release live is in uw regio.

## <a name="new-features"></a>Nieuwe functies
### <a name="spark-30-preview"></a>Spark 3.0 Preview
HDInsight heeft [Spark 3.0.0-ondersteuning](https://spark.apache.org/docs/3.0.0/) toegevoegd aan HDInsight 4.0 als preview-functie. 

### <a name="kafka-24-preview"></a>Kafka 2.4 Preview
HDInsight heeft [Kafka 2.4.1-ondersteuning](http://kafka.apache.org/24/documentation.html) toegevoegd aan HDInsight 4.0 als preview-functie.

### <a name="eav4-series-support"></a>Ondersteuning voor Eav4-serie
HdInsight heeft ondersteuning voor de Eav4-serie toegevoegd in deze release. Meer informatie over [de Dav4-serie kunt u hier vinden.](../virtual-machines/eav4-easv4-series.md) De reeks is beschikbaar gesteld in de volgende regio's: 

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

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Overschalen naar virtuele-machineschaalsets van Azure
HDInsight maakt nu gebruik van virtuele Azure-machines om het cluster in terichten. De service wordt geleidelijk gemigreert naar [virtuele-machineschaalsets van Azure.](../virtual-machine-scale-sets/overview.md) Het hele proces kan maanden duren. Nadat uw regio's en abonnementen zijn gemigreerd, worden nieuw gemaakte HDInsight-clusters uitgevoerd op virtuele-machineschaalsets zonder acties van de klant. Er wordt geen wijziging verwacht die een wijziging onderbreekt.

## <a name="deprecation"></a>Afschaffing
Geen afschaffing in deze release.

## <a name="behavior-changes"></a>Gedragswijzigingen
### <a name="default-cluster-version-is-changed-to-40"></a>De standaardversie van het cluster is gewijzigd in 4.0
De standaardversie van het HDInsight-cluster is gewijzigd van 3.6 in 4.0. Zie beschikbare versies voor meer informatie [over beschikbare versies.](./hdinsight-component-versioning.md) Meer informatie over wat er nieuw is in [HDInsight 4.0.](./hdinsight-version-release.md)

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Standaardgrootten van cluster-VM's zijn gewijzigd in Ev3-serie 
Standaardgrootten van cluster-VM's zijn gewijzigd van D-serie in Ev3-serie. Deze wijziging is van toepassing op hoofdknooppunten en werkknooppunten. Geef de VM-grootten op die u wilt gebruiken in de ARM-sjabloon om te voorkomen dat deze wijziging van invloed is op uw geteste werkstromen.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Netwerkinterfaceresource niet zichtbaar voor clusters die worden uitgevoerd op virtuele-machineschaalsets van Azure
HDInsight wordt geleidelijk gemigraliseerd naar virtuele-machineschaalsets van Azure. Netwerkinterfaces voor virtuele machines zijn niet langer zichtbaar voor klanten voor clusters die gebruikmaken van virtuele-machineschaalsets van Azure.

## <a name="upcoming-changes"></a>Toekomstige wijzigingen
De volgende wijzigingen worden doorgevoerd in toekomstige releases.

### <a name="os-version-upgrade"></a>Upgrade van versie van besturingssysteem
HDInsight-clusters worden momenteel uitgevoerd op Ubuntu 16.04 LTS. Zoals wordt verwezen in de releasecyclus van [Ubuntu,](https://ubuntu.com/about/release-cycle)bereikt de Ubuntu 16.04-kernel in april 2021 End of Life (EOL). We beginnen met het uitrollen van de nieuwe HDInsight 4.0-clusterafbeelding die in mei 2021 wordt uitgevoerd op Ubuntu 18.04. Nieuw gemaakte HDInsight 4.0-clusters worden standaard uitgevoerd op Ubuntu 18.04 zodra deze beschikbaar zijn. Bestaande clusters op Ubuntu 16.04 worden zonder volledige ondersteuning uitgevoerd.

HDInsight 3.6 blijft worden uitgevoerd op Ubuntu 16.04. De standaardondersteuning wordt op 30 juni 2021 aan het einde bereikt en wordt Basic Support 1 juli 2021 gewijzigd. Zie voor meer informatie over datums en ondersteuningsopties [Azure HDInsight versies.](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions) Ubuntu 18.04 wordt niet ondersteund voor HDInsight 3.6. Als u Ubuntu 18.04 wilt gebruiken, moet u uw clusters migreren naar HDInsight 4.0. 

U moet uw clusters neerzetten en opnieuw maken als u bestaande clusters wilt verplaatsen naar Ubuntu 18.04. Plan het maken of opnieuw maken van uw cluster nadat Ubuntu 18.04-ondersteuning beschikbaar is. We sturen nog een melding nadat de nieuwe afbeelding beschikbaar is in alle regio's.

Het is raadzaam om uw scriptacties en aangepaste toepassingen die zijn geïmplementeerd op edge-knooppunten op een Ubuntu 18.04 virtuele machine (VM) vooraf te testen. U kunt een eenvoudige Ubuntu Linux-VM maken op [18.04-LTS](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)en vervolgens een [SSH-sleutelpaar (Secure Shell)](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys#ssh-into-your-vm) maken en gebruiken op uw VM om uw scriptacties en aangepaste toepassingen die zijn geïmplementeerd op edge-knooppunten uit te voeren en te testen.

### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsgiht-40"></a>Schakel Stardard_A5 VM-grootte uit als hoofd-knooppunt voor HDInsgiht 4.0
Het hoofdknooppunt van het HDInsight-cluster is verantwoordelijk voor het initialiseren en beheren van het cluster. Standard_A5 VM-grootte heeft betrouwbaarheidsproblemen als hoofd-knooppunt voor HDInsight 4.0. Vanaf de volgende release in mei 2021 kunnen klanten geen nieuwe clusters maken met Standard_A5 VM-grootte als hoofd-knooppunt. U kunt andere virtuele 2-core-VM's gebruiken, E2_v3 of E2s_v3. Bestaande clusters worden uitgevoerd zoals ze zijn. Een VM met vier kernen wordt ten zeerste aanbevolen voor Head Node om de hoge beschikbaarheid en betrouwbaarheid van uw HDInsight-productieclusters te garanderen.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Basic Support 1 juli 2021 voor HDInsight 3.6
Vanaf 1 juli 2021 biedt [](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) Microsoft een Basic Support voor bepaalde HDInsight 3.6-clustertypen. Het Basic Support is beschikbaar tot 3 april 2022. U wordt automatisch ingeschreven bij Basic Support 1 juli 2021. U hoeft geen actie te ondernemen om u aan te kunnen. Zie [onze documentatie](hdinsight-36-component-versioning.md) over welke clustertypen zijn opgenomen onder Basic Support. 

Het is niet raadzaam om nieuwe oplossingen te bouwen in HDInsight 3.6 en wijzigingen in bestaande 3.6-omgevingen te blokkeren. We raden u aan om [uw clusters te migreren naar HDInsight 4.0.](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40) Meer informatie [over wat er nieuw is in HDInsight 4.0.](hdinsight-version-release.md#whats-new-in-hdinsight-40)

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft verbeteringen in de betrouwbaarheid en prestaties van clusters aanbrengen. 

## <a name="component-version-change"></a>Versiewijziging van onderdeel
Ondersteuning toegevoegd voor Spark 3.0.0 en Kafka 2.4.1 als preview. U vindt de huidige onderdeelversies voor HDInsight 4.0 en HDInsight 3.6 in [dit document](./hdinsight-component-versioning.md).

## <a name="recommanded-features"></a>Nieuwe functies
### <a name="service-tags"></a>Servicetags
Servicetags vereenvoudigen het beperken van de netwerktoegang tot de Azure-services voor virtuele Azure-machines en virtuele Azure-netwerken. Met servicetags in uw NSG-regels (netwerkbeveiligingsgroep) wordt verkeer naar een specifieke Azure-service toegestaan of weigert. De regel kan globaal of per Azure-regio worden ingesteld. Azure biedt het onderhoud van IP-adressen die onderliggend zijn voor elke tag. HDInsight-servicetags voor netwerkbeveiligingsgroepen (NSG's) zijn groepen IP-adressen voor status- en beheerservices. Deze groepen helpen de complexiteit voor het maken van beveiligingsregel te minimaliseren. HDInsight-klanten kunnen servicetags inschakelen via Azure Portal, PowerShell en REST API. Zie Servicetags voor [netwerkbeveiligingsgroep (NSG)](./hdinsight-service-tags.md)voor meer Azure HDInsight.
