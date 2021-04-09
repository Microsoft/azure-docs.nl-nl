---
title: Veelgestelde vragen over Apache Kafka in azure HDInsight
description: Krijg antwoorden op veelgestelde vragen over Apache Kafka in azure HDInsight, een beheerde Hadoop-Cloud service.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939098"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Veelgestelde vragen over Apache Kafka in Azure HDInsight

In dit artikel worden enkele veelgestelde vragen over het gebruik van Apache Kafka op Azure HDInsight behandeld.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Welke Kafka-versies worden ondersteund door HDInsight?

Meer informatie over de versies van officieel ondersteunde onderdelen van HDInsight in [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn met HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). U wordt aangeraden altijd de nieuwste versie te gebruiken om de beste prestaties en gebruikers ervaring te garanderen.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Welke bronnen worden er in een HDInsight Kafka-cluster vermeld en voor welke resources worden er kosten in rekening gebracht?

Een HDInsight Kafka-cluster bevat de volgende bronnen:

* Hoofd knooppunten
* Zookeeper-knoop punten
* Knoop punten van Broker (worker) 
* Azure Managed Disks gekoppeld aan de Broker knooppunten
* Gateway knooppunten

Al deze resources worden in rekening gebracht op basis van ons [prijs model voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), met uitzonde ring van Gateway knooppunten. Er worden geen kosten in rekening gebracht voor gateway knooppunten.

Zie [Azure HDInsight Virtual Network-architectuur](../hdinsight-virtual-network-architecture.md)voor een gedetailleerde beschrijving van de verschillende typen knoop punten. Prijzen zijn gebaseerd op het gebruik van het knoop punt per minuut. De prijzen variëren, afhankelijk van de grootte van het knoop punt, het aantal knoop punten, het type beheerde schijf dat wordt gebruikt en de regio.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Werken Apache Kafka-Api's met HDInsight?

Ja, HDInsight maakt gebruik van systeem eigen Kafka-Api's. De code van uw client toepassing hoeft niet te worden gewijzigd. Zie [zelf studie: de Apache Kafka producer-en Consumer-Api's gebruiken](./apache-kafka-producer-consumer-api.md) om te zien hoe u op Java gebaseerde producer/Consumer-api's kunt gebruiken met uw cluster.

## <a name="can-i-change-cluster-configurations"></a>Kan ik cluster configuraties wijzigen?

Ja, via de Ambari-Portal. Elk onderdeel in de portal heeft een sectie **configuratie** , die kan worden gebruikt om onderdeel configuraties te wijzigen. Voor sommige wijzigingen is het vereist dat Broker opnieuw wordt gestart.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Welk type verificatie ondersteunt HDInsight voor Apache Kafka?

Met [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md)kunt u beveiliging op onderwerpniveau verkrijgen voor hun Kafka-clusters. Zie [zelf studie: Apache Kafka beleid in HDInsight configureren met Enterprise Security Package (preview)](../domain-joined/apache-domain-joined-run-kafka.md)voor meer informatie.

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Zijn mijn gegevens versleuteld? Kan ik mijn eigen sleutels gebruiken?

Alle Kafka-berichten op de beheerde schijven worden versleuteld met [Azure Storage-service versleuteling (SSE)](../../storage/common/storage-service-encryption.md). Data-in-transit (bijvoorbeeld gegevens die worden verzonden van clients naar makelaars en de andere manier rond) is niet standaard versleuteld. Het is mogelijk om dit verkeer te versleutelen door [TLS zelf in te stellen](./apache-kafka-ssl-encryption-authentication.md). Daarnaast kunt u met HDInsight hun eigen sleutels beheren om de gegevens in rust te versleutelen. Zie door de [klant beheerde sleutel schijf versleuteling](../disk-encryption.md)voor meer informatie.

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Hoe kan ik verbinding maken tussen clients en mijn cluster?

Om Kafka-clients te laten communiceren met Kafka-Brokers, moeten ze de Brokers via het netwerk kunnen bereiken. Voor HDInsight-clusters is de Virtual Network (VNet) de beveiligings grens. De eenvoudigste manier om clients te verbinden met uw HDInsight-cluster is daarom om clients te maken binnen hetzelfde VNet als het cluster. Andere scenario's zijn onder andere:

* Clients verbinden in een andere Azure VNet: peer het cluster-VNet en het client-VNet en configureer het cluster voor het [adverteren van IP-adressen](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Wanneer u IP-reclame gebruikt, moeten Kafka-clients Broker IP-adressen gebruiken om verbinding te maken met de Brokers, in plaats van FQDN-namen (Fully Qualified Domain Name).

* Verbinding maken met on-premises clients: met behulp van een VPN-netwerk en aangepaste DNS-servers instellen zoals beschreven in [een virtueel netwerk voor Azure HDInsight plannen](../hdinsight-plan-virtual-network-deployment.md).

* Een openbaar eind punt maken voor uw Kafka-service: als de beveiligings vereisten van uw bedrijf dit toestaan, kunt u een openbaar eind punt voor uw Kafka-Brokers implementeren of een door de Self beheerde open-source REST-eind punt met een openbaar eind punt.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Kan ik meer schijf ruimte aan een bestaand cluster toevoegen?

Als u de hoeveelheid beschik bare ruimte voor Kafka-berichten wilt verg Roten, kunt u het aantal knoop punten verg Roten. Het is momenteel niet mogelijk om meer schijven toe te voegen aan een bestaand cluster.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kan een Kafka-cluster werken met Databricks? 

Ja, Kafka-clusters kunnen samen werken met Databricks, zolang ze zich in hetzelfde VNet bevinden. Als u een Kafka-cluster met Databricks wilt gebruiken, maakt u een VNet met een HDInsight Kafka-cluster. vervolgens geeft u het VNet op wanneer u de werk ruimte Databricks maakt en VNet-injectie gebruikt. Zie [Deploy Azure Databricks in uw Azure Virtual Network (VNet-injectie)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)voor meer informatie. U moet de namen van de Boots trap-Broker van het Kafka-cluster opgeven bij het maken van de Databricks-werk ruimte. Zie [Get the Apache Zookeeper and Broker host information](./apache-kafka-get-started.md#getkafkainfo)(Engelstalig) voor meer informatie over het ophalen van de namen van de Kafka-Broker.

## <a name="how-can-i-have-maximum-data-durability"></a>Hoe kan ik maximale duurzaamheid van gegevens hebben?

Met de duurzaamheid van gegevens kunt u het laagste risico op bericht verlies behaalt. Voor maximale duurzaamheid van gegevens kunt u het beste de volgende instellingen instellen:

* Gebruik een minimale replicatie factor van 3 in de meeste regio's
* een minimale replicatie factor van 4 gebruiken in regio's met slechts twee fout domeinen
* verkiezingen voor onduidelijke leider uitschakelen
* Stel **min. Insync. replica's** in op 2 of meer: Hiermee wijzigt u het aantal replica's dat volledig moet worden gesynchroniseerd met de leider voordat een schrijf bewerking kan worden voortgezet.
* de eigenschap **bevestigingen** instellen op **all** -voor deze eigenschap moeten alle replica's alle berichten bevestigen

Het configureren van Kafka voor een hogere consistentie van gegevens is van invloed op de beschik baarheid van makelaars om aanvragen te produceren.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Kan ik mijn gegevens repliceren naar meerdere clusters?

Ja, gegevens kunnen naar meerdere clusters worden gerepliceerd met behulp van Kafka MirrorMaker. Meer informatie over het instellen van MirrorMaker vindt u in de [onderwerpen over spiegel Apache Kafka](apache-kafka-mirroring.md). Daarnaast zijn er andere zelf-beheerde open-source technologieën en leveranciers die u kunnen helpen bij de replicatie naar meerdere clusters, zoals [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Kan ik een upgrade uitvoeren voor mijn cluster? Hoe moet ik een upgrade uitvoeren voor mijn cluster?

Momenteel wordt er geen ondersteuning geboden voor in-place cluster versie-upgrades. Als u uw cluster wilt bijwerken naar een hogere versie van Kafka, maakt u een nieuw cluster met de gewenste versie en migreert u uw Kafka-clients voor gebruik van het nieuwe cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Wilt u mijn Kafka-cluster Hoe kan ik controleren?

Gebruik Azure monitor om uw [Kafka-logboeken](./apache-kafka-log-analytics-operations-management.md)te analyseren.

## <a name="next-steps"></a>Volgende stappen

* [TLS-versleuteling en-verificatie instellen voor Apache Kafka in azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [MirrorMaker gebruiken om Apache Kafka-onderwerpen te repliceren met Kafka in HDInsight](./apache-kafka-mirroring.md)