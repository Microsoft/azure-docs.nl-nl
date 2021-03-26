---
title: Apache Kafka workloads migreren naar Azure HDInsight 4,0
description: Meer informatie over het migreren van Apache Kafka-workloads op HDInsight 3,6 naar HDInsight 4,0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: e15ebb13aee0e5dd814688ae77edaded667d54ac
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864122"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Apache Kafka workloads migreren naar Azure HDInsight 4,0

Azure HDInsight 4,0 biedt de meest recente open source-onderdelen met belang rijke verbeteringen in prestaties, connectiviteit en beveiliging. In dit document wordt uitgelegd hoe u Apache Kafka workloads op HDInsight 3,6 migreert naar HDInsight 4,0. Nadat u uw workloads hebt gemigreerd naar HDInsight 4,0, kunt u veel van de nieuwe functies gebruiken die niet beschikbaar zijn op HDInsight 3,6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Migratie paden voor HDInsight 3,6 Kafka

HDInsight 3,6 ondersteunt twee versies van Kafka: 1.0.0 en 1.1.0. HDInsight 4,0 ondersteunt versies 1.1.0 en 2.1.0. Afhankelijk van welke versie van Kafka en welke versie van HDInsight u wilt uitvoeren, zijn er meerdere ondersteunde migratie paden. Deze paden worden hieronder beschreven en geïllustreerd in het volgende diagram.

* **Voer zowel Kafka als HDInsight uit op de nieuwste versies (aanbevolen)**: Migreer een hdinsight 3,6-en Kafka 1.0.0-of 1.1.0-toepassing naar HDInsight 4,0 met Kafka 2.1.0 (paden D en E hieronder).
* **Voer HDInsight uit op de nieuwste versie, maar Kafka alleen voor een recentere versie**: Migreer een hdinsight 3,6-en Kafka 1.0.0-toepassing naar HDInsight 4,0 met Kafka 1.1.0 (pad B hieronder).
* **Voer HDInsight uit op de nieuwste versie, behoud de Kafka-versie**: Migreer een hdinsight 3,6-en Kafka 1.1.0-toepassing naar HDInsight 4,0 met Kafka 1.1.0 (pad C hieronder).
* **Voer Kafka uit voor een recentere versie en behoud hdinsight-versie**: Migreer een Kafka 1.0.0-toepassing naar 1.1.0 en blijf op HDInsight 3,6 (pad a hieronder). Houd er rekening mee dat voor deze optie nog steeds een nieuw cluster moet worden geïmplementeerd. Het bijwerken van de Kafka-versie op een bestaand cluster wordt niet ondersteund. Nadat u een cluster hebt gemaakt met de versie die u wilt gebruiken, migreert u uw Kafka-clients naar het nieuwe cluster.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png" alt-text="Upgrade paden voor Apache Kafka op 3,6" border="false":::

## <a name="apache-kafka-versions"></a>Apache Kafka versies

### <a name="kafka-110"></a>Kafka 1.1.0
  
Als u migreert van Kafka 1.0.0 naar 1.1.0, kunt u profiteren van de volgende nieuwe functies:

* Verbeteringen aan de Kafka-controller kunnen worden uitgeschakeld, zodat u de Brokers opnieuw kunt opstarten en sneller probleem hoeft op te lossen. 
* Verbeteringen in de [FetchRequests Logic](https://issues.apache.org/jira/browse/KAFKA-6254) waarmee u meer partities (en dus meer onderwerpen) op het cluster kunt maken. 
* Kafka Connect ondersteunt [record koppen](https://issues.apache.org/jira/browse/KAFKA-5142) en [reguliere expressies](https://issues.apache.org/jira/browse/KAFKA-3073) voor onderwerpen. 

Zie [Apache Kafka 1,1 release opmerkingen](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)voor een volledige lijst met updates.

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Als u migreert naar Kafka 2,1, kunt u profiteren van de volgende functies:

* Betere Broker tolerantie vanwege een verbeterd replicatie protocol.
* Nieuwe functionaliteit in de KafkaAdminClient-API.
* Configureerbaar quotum beheer.
* Ondersteuning voor Zstandard-compressie.

Zie [Apache Kafka 2,0 release opmerkingen](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) en [Apache Kafka 2,1 release opmerkingen](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)voor een volledige lijst met updates.

## <a name="kafka-client-compatibility"></a>Kafka-client compatibiliteit

Nieuwe Kafka-Brokers ondersteunen oudere clients. [Kip-35-het ophalen van een Protocol versie](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) heeft een mechanisme geïntroduceerd voor het dynamisch bepalen van de functionaliteit van een Kafka Broker en [kip-97: verbeterd Kafka client RPC-compatibiliteits beleid](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) heeft een nieuw compatibiliteits beleid en garanties voor de Java-client geïntroduceerd. Voorheen moest een Kafka-client communiceren met een Broker van dezelfde versie of een nieuwere versie. Nieuwere versies van de Java-clients en andere clients die ondersteuning bieden voor KIP-35 zoals, `librdkafka` kunnen terugvallen op oudere aanvraag typen of geschikte fouten genereren als de functionaliteit niet beschikbaar is.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png" alt-text="Upgrade uitvoeren voor Kafka-client compatibiliteit" border="false":::

Houd er rekening mee dat de client oudere Brokers ondersteunt.  Zie [Compatibility Matrix](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)(Engelstalig) voor meer informatie.

## <a name="general-migration-process"></a>Algemeen migratie proces

Bij de volgende migratie richtlijnen wordt uitgegaan van een Apache Kafka 1.0.0-of 1.1.0-cluster dat is geïmplementeerd op HDInsight 3,6 in één virtueel netwerk. De bestaande Broker heeft enkele onderwerpen en wordt actief gebruikt door producenten en consumenten.

:::image type="content" source="./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png" alt-text="Huidige Kafka veronderstelde omgeving" border="false":::

Voer de volgende stappen uit om de migratie te volt ooien:

1. **Implementeer een nieuw HDInsight 4,0-cluster en-clients voor test doeleinden.** Implementeer een nieuw HDInsight 4,0 Kafka-cluster. Als meerdere Kafka-cluster versies kunnen worden geselecteerd, is het raadzaam om de meest recente versie te selecteren. Stel na de implementatie een aantal para meters in als dat nodig is en maak een onderwerp met dezelfde naam als uw bestaande omgeving. Stel ook TLS-en BYOK-code ring (your-eigen-sleutel) in als dat nodig is. Controleer vervolgens of het goed werkt met het nieuwe cluster.

    :::image type="content" source="./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png" alt-text="Nieuwe HDInsight 4,0-clusters implementeren" border="false":::

1. **Het cluster overschakelen voor de producer-toepassing en wachten totdat alle wachtrij gegevens door de huidige gebruikers worden gebruikt.** Wanneer het nieuwe HDInsight 4,0 Kafka-cluster gereed is, schakelt u de bestaande doel locatie van de producent over naar het nieuwe cluster. Zorg ervoor dat het is totdat de bestaande consument alle gegevens van het bestaande cluster heeft verbruikt.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-producer-app.png" alt-text="Cluster scha kelen voor producer-app" border="false":::

1. **Scha kelen tussen het cluster en de consument toepassing.** Nadat u hebt bevestigd dat de bestaande consumenten toepassing alle gegevens van het bestaande cluster heeft verbruikt, schakelt u de verbinding met het nieuwe cluster.

    :::image type="content" source="./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png" alt-text="Cluster overschakelen op consumenten-app" border="false":::

1. **Verwijder het oude cluster en test toepassingen als dat nodig is.** Als de switch is voltooid en correct werkt, verwijdert u het oude HDInsight 3,6 Kafka-cluster en de producenten en consumenten die in de test worden gebruikt, indien nodig.

## <a name="next-steps"></a>Volgende stappen

* [Prestaties van Apache Kafka HDInsight-clusters optimaliseren](apache-kafka-performance-tuning.md)
* [Quickstart: Een Apache Kafka-cluster maken in Azure HDInsight met Azure Portal](apache-kafka-get-started.md)
