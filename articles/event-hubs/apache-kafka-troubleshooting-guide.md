---
title: Problemen met Azure Event Hubs voor Apache Kafka oplossen
description: In dit artikel wordt beschreven hoe u problemen kunt oplossen met Azure Event Hubs voor Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a9d4a93f0074f206cd4627913505c66eb6480cbd
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314079"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Gids voor het oplossen van problemen met Apache Kafka voor Event Hubs
In dit artikel vindt u tips voor het oplossen van problemen die kunnen optreden bij het gebruik van Event Hubs voor Apache Kafka. 

## <a name="server-busy-exception"></a>Uitzonde ring voor server bezet
U kunt uitzonde ring op de server ontvangen vanwege Kafka-beperking. Met AMQP-clients retourneert Event Hubs onmiddellijk een **Server** -uitzonde ring bij het beperken van services. Het is gelijk aan het bericht ' Probeer het later opnieuw '. In Kafka worden berichten uitgesteld voordat ze worden voltooid. De lengte van de vertraging wordt geretourneerd in milliseconden, zoals `throttle_time_ms` in het antwoord maken/ophalen. In de meeste gevallen worden deze vertraagde aanvragen niet geregistreerd als server-uitzonde ringen op Event Hubs Dash boards. In plaats daarvan moet de waarde van het antwoord `throttle_time_ms` worden gebruikt als een indicator die het ingerichte quotum heeft overschreden.

Als het verkeer buitensporig is, heeft de service het volgende gedrag:

- Als de uitstel vertraging van de aanvraag de time-out voor de aanvraag overschrijdt, wordt Event Hubs fout code voor het **beleids** fout geretourneerd.
- Als de vertraging van de aanvraag voor ophalen langer is dan de time-out van de aanvraag, wordt de aanvraag door Event Hubs geregistreerd als beperkt en wordt er met een lege set records en zonder fout code gereageerd.

[Toegewezen clusters](event-hubs-dedicated-overview.md) hebben geen beperkings mechanismen. U kunt al uw cluster bronnen gebruiken.

## <a name="no-records-received"></a>Er zijn geen records ontvangen
U ziet mogelijk dat consumenten geen records ontvangen en voortdurend worden gebalanceerd. In dit scenario krijgen consumenten geen records en worden ze voortdurend opnieuw gebalanceerd. Er is geen uitzonde ring of fout opgetreden wanneer deze zich voordoet, maar in de Kafka-Logboeken wordt aangegeven dat de gebruikers zijn vastgelopen om de groep opnieuw samen te voegen en partities toe te wijzen. Er zijn enkele mogelijke oorzaken:

- Zorg ervoor dat u ten `request.timeout.ms` minste de aanbevolen waarde 60000 hebt en dat u ten `session.timeout.ms` minste de aanbevolen waarde van 30000 hebt. Als deze instellingen te laag zijn, kunnen er time-outs voor de consumers optreden, waardoor er vervolgens herverdelingen worden ingesteld (waardoor vervolgens meer time-outs ontstaan, waardoor meer gespreiding wordt veroorzaakt, enzovoort) 
- Als uw configuratie overeenkomt met de aanbevolen waarden en u nog steeds constante herverdeling ziet, kunt u een probleem openen (zorg ervoor dat u uw volledige configuratie in het probleem opneemt, zodat u de fout opsporing kunt uitvoeren).

## <a name="compressionmessage-format-version-issue"></a>Versie probleem compressie/bericht indeling
Kafka ondersteunt compressie en Event Hubs voor Kafka momenteel niet. Fouten die een versie van een bericht indeling vermelden (bijvoorbeeld `The message format version on the broker does not support the request.` ), worden veroorzaakt wanneer een client gecomprimeerde Kafka-berichten naar onze Brokers probeert te verzenden.

Als gecomprimeerde gegevens nood zakelijk zijn, comprimeert u uw gegevens voordat u deze naar de Brokers verzendt en decomprimeren nadat de ontvangst een geldige tijdelijke oplossing is. De bericht tekst is alleen een byte matrix voor de service, waardoor compressie op de client geen problemen ondervindt.

## <a name="unknownserverexception"></a>UnknownServerException
U ontvangt mogelijk een UnknownServerException van Kafka-client bibliotheken die vergelijkbaar zijn met het volgende voor beeld: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Open een ticket met micro soft ondersteuning.  Logboek registratie voor fout opsporing en uitzonde ringen in UTC is handig bij het opsporen van fouten in het probleem. 

## <a name="other-issues"></a>Overige problemen
Controleer de volgende items als u problemen ziet wanneer u Kafka gebruikt op Event Hubs.

- **Firewall die verkeer blokkeert** : Zorg ervoor dat poort **9093** niet wordt geblokkeerd door uw firewall.
- **TopicAuthorizationException** -de meest voorkomende oorzaken van deze uitzonde ring zijn:
    - Een type fout in het connection string in uw configuratie bestand of
    - Er wordt geprobeerd Event Hubs te gebruiken voor Kafka in een naam ruimte van de Basic-laag. De functie Event Hubs voor Kafka wordt [alleen ondersteund voor standaard-en toegewezen laag naam ruimten](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Kafka versie komt niet overeen** -Event hubs voor Kafka-ecosystemen ondersteunt Kafka versie 1,0 en hoger. Sommige toepassingen die gebruikmaken van Kafka versie 0,10 en hoger, kunnen af en toe werken vanwege de neerwaartse compatibiliteit van het Kafka-protocol, maar we raden u ten zeerste aan om oude API-versies te gebruiken. Kafka-versies 0,9 en lager bieden geen ondersteuning voor de vereiste SASL-protocollen en er kan geen verbinding worden gemaakt met Event Hubs.
- **Vreemde code ringen voor AMQP-headers bij het gebruik van Kafka** -bij het verzenden van gebeurtenissen naar een event hub over AMQP, worden alle AMQP Payload-headers GESERIALISEERD in AMQP-code ring. Kafka-gebruikers deserialiseren de headers van AMQP niet. Als u koptekst waarden wilt lezen, decodeert u de AMQP-headers hand matig. U kunt ook AMQP-headers vermijden als u weet dat u gaat gebruiken via Kafka-protocol. Zie voor meer informatie [Dit github-probleem](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Sasl-verificatie** : als u uw Framework wilt laten samen werken met het SASL-verificatie protocol dat vereist is voor Event hubs, kan dat lastiger zijn dan aan het oog. Controleer of u problemen met de configuratie kunt oplossen met behulp van de resources van uw Framework op SASL-verificatie. 

## <a name="limits"></a>Limieten
Apache Kafka versus Event Hubs Kafka. Voor de meeste delen heeft de Kafka-interface van Azure Event Hubs dezelfde standaard instellingen, eigenschappen, fout codes en algemene werking Apache Kafka. De instanties die deze twee expliciet verschillen (of waarbij Event Hubs een limiet oplegt die niet Kafka) worden hieronder weer gegeven:

- De maximale lengte van de `group.id` eigenschap is 256 tekens
- De maximale grootte van `offset.metadata.max.bytes` is 1024 bytes
- Offset-doorvoer bewerkingen worden beperkt tot 4 aanroepen/seconde per partitie met een maximale interne logboek grootte van 1 MB


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Apache Kafka ontwikkelaars handleiding voor Event Hubs](apache-kafka-developer-guide.md)
- [Apache Kafka migratie handleiding voor Event Hubs](apache-kafka-migration-guide.md)
- [Veelgestelde vragen-Event Hubs voor Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [Aanbevolen configuraties](apache-kafka-configurations.md)
