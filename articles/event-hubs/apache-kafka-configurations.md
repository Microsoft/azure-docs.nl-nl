---
title: Aanbevolen configuraties voor Apache Kafka-clients-Azure Event Hubs
description: In dit artikel vindt u aanbevolen Apache Kafka configuraties voor clients die communiceren met Azure Event Hubs voor Apache Kafka.
ms.topic: reference
ms.date: 01/07/2021
ms.openlocfilehash: 713900a3cc7e2b9f6f176edb21455faa577098d6
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028825"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Aanbevolen configuraties voor Apache Kafka-clients
Hier vindt u de aanbevolen configuraties voor het gebruik van Azure Event Hubs van Apache Kafka-client toepassingen. 

## <a name="java-client-configuration-properties"></a>Eigenschappen van de Java-client configuratie

### <a name="producer-and-consumer-configurations"></a>Producer-en consumenten configuraties

Eigenschap | Aanbevolen waarden | Toegestaan bereik | Opmerkingen
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (bij benadering) | < 240000 | Kan worden gereduceerd om meta gegevens wijzigingen sneller op te halen.
`connections.max.idle.ms`   | 180000 | < 240000 | Azure sluit binnenkomende TCP-> 240.000 MS, wat kan leiden tot het verzenden van Dead-verbindingen (weer gegeven als verlopen batches vanwege een time-out voor verzenden).

### <a name="producer-configurations-only"></a>Alleen producer-configuraties
U kunt de configuratie van de producent [hier](https://kafka.apache.org/documentation/#producerconfigs)vinden.

Eigenschap | Aanbevolen waarden | Toegestaan bereik | Opmerkingen
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | Er worden verbindingen met de service afgesloten als er aanvragen van meer dan 1.046.528 bytes worden verzonden.  *Deze waarde **moet** worden gewijzigd en zal problemen veroorzaken bij het produceren van hoge door voer.*
`retries` | > 0 | | Zie de documentatie voor meer delivery.timeout.ms-waarden.
`request.timeout.ms` | 30000.. 60000 | > 20000| EH wordt intern ingesteld op een minimum van 20.000 MS.  *Hoewel aanvragen met lagere time-outwaarden worden geaccepteerd, wordt het client gedrag niet gegarandeerd.*
`metadata.max.idle.ms` | 180000 | > 5000 | Hiermee wordt bepaald hoe lang de meta gegevens van een onderwerp in de cache worden opgeslagen. Als de verstreken tijd sinds een onderwerp voor het laatst is geproduceerd, de niet-actieve duur van de meta gegevens overschrijdt, wordt de meta gegevens van het onderwerp verg eten en de volgende toegang tot een aanvraag voor het ophalen van meta gegevens wordt afgedwongen.
`linger.ms` | > 0 | | Voor scenario's met hoge door voer moet de waarde voor de ondergeschiktheid gelijk zijn aan de hoogste toelaat bare waarde om te profiteren van batch verwerking.
`delivery.timeout.ms` | | | Instellen volgens de formule ( `request.timeout.ms`  +  `linger.ms` ) * `retries` .
`enable.idempotence` | onjuist | | Idempotentie wordt momenteel niet ondersteund.
`compression.type` | `none` | | Compressie wordt op dit moment niet ondersteund...

### <a name="consumer-configurations-only"></a>Alleen voor consumenten configuraties
Configuratie van de consument vindt u [hier](https://kafka.apache.org/documentation/#consumerconfigs).

Eigenschap | Aanbevolen waarden | Toegestaan bereik | Opmerkingen
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 is de standaard waarde en mag niet worden gewijzigd.
`session.timeout.ms` | 30.000 |6000.. 300000| Begin met 30000, verhoog als u regel matig herverdeling wilt zien vanwege gemiste heartbeats.


## <a name="librdkafka-configuration-properties"></a>eigenschappen van librdkafka-configuratie
Het hoofd `librdkafka` configuratie bestand ([koppeling](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) bevat uitgebreide beschrijvingen voor de onderstaande eigenschappen.

### <a name="producer-and-consumer-configurations"></a>Producer-en consumenten configuraties

Eigenschap | Aanbevolen waarden | Toegestaan bereik | Opmerkingen
---|---:|-----:|---
`socket.keepalive.enable` | true | | Vereist als de verbinding naar behoren inactief moet zijn.  Azure sluit binnenkomende TCP-inactiviteit > 240.000 MS.
`metadata.max.age.ms` | ~ 180000| < 240000 | Kan worden gereduceerd om meta gegevens wijzigingen sneller op te halen.

### <a name="producer-configurations-only"></a>Alleen producer-configuraties

Eigenschap | Aanbevolen waarden | Toegestaan bereik | Opmerkingen
---|---:|-----:|---
`retries` | > 0 | | De standaard waarde is 2. We raden u aan deze waarde te hand haven. 
`request.timeout.ms` | 30000.. 60000 | > 20000| EH wordt intern ingesteld op een minimum van 20.000 MS.  `librdkafka` de standaard waarde is 5000, wat problematisch kan zijn. *Hoewel aanvragen met lagere time-outwaarden worden geaccepteerd, wordt het client gedrag niet gegarandeerd.*
`partitioner` | `consistent_random` | Zie librdkafka-documentatie | `consistent_random` is standaard en beste.  Lege en null-sleutels worden in de meeste gevallen in het ideale geval afgehandeld.
`enable.idempotence` | onjuist | | Idempotentie wordt momenteel niet ondersteund.
`compression.codec` | `none` || Compressie wordt op dit moment niet ondersteund.

### <a name="consumer-configurations-only"></a>Alleen voor consumenten configuraties

Eigenschap | Aanbevolen waarden | Toegestaan bereik | Opmerkingen
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 is de standaard waarde en mag niet worden gewijzigd.
`session.timeout.ms` | 30.000 |6000.. 300000| Begin met 30000, verhoog als u regel matig herverdeling wilt zien vanwege gemiste heartbeats.


## <a name="further-notes"></a>Verdere opmerkingen

Raadpleeg de volgende tabel met veelvoorkomende fout scenario's met betrekking tot configuratie.

Symptomen | Probleem | Oplossing
----|---|-----
Fouten bij het door voeren van de offset vanwege herverdeling | Uw consument wacht te lang op het aanroepen van polling () en de service is bezig met het starten van de consument uit de groep. | U hebt verschillende mogelijkheden: <ul><li>Time-out van poll verwerking verhogen ( `max.poll.interval.ms` )</li><li>De omvang van de bericht batch verlagen om de verwerking te versnellen</li><li>Verbeter de verwerkings parallel Lise ring om te voor komen dat consumenten worden geblokkeerd. polling ()</li></ul> Het Toep assen van een combi natie van de drie is waarschijnlijk wisest.
Netwerk uitzondering bij hoge door Voer | Gebruikt u Java-client + standaard Max. aanvraag. grootte?  Uw aanvragen zijn mogelijk te groot. | Zie Java-configuraties hierboven.

## <a name="next-steps"></a>Volgende stappen
Zie [Azure-abonnement en service limieten, quota's en beperkingen](..//azure-resource-manager/management/azure-subscription-service-limits.md) voor quota en limieten van alle Azure-Services. 
