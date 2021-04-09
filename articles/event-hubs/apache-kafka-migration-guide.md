---
title: Migreren naar Azure Event Hubs voor Apache Kafka
description: In dit artikel wordt uitgelegd hoe u clients migreert van Apache Kafka naar Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d9f3775a85df5a881c2c38566628e4e1d4d8c40e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90061441"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migreren naar Azure Event Hubs voor Apache Kafka-ecosystemen
Azure Event Hubs geeft een Apache Kafka eind punt weer, waarmee u verbinding kunt maken met Event Hubs met behulp van het Kafka-protocol. Door minimale wijzigingen aan te brengen in uw bestaande Kafka-toepassing, kunt u verbinding maken met Azure Event Hubs en profiteren van de voor delen van het Azure-ecosysteem. Event Hubs werkt met veel van uw bestaande Kafka-toepassingen, waaronder MirrorMaker. Zie [Event hubs voor Apache Kafka voor](event-hubs-for-kafka-ecosystem-overview.md) meer informatie.

## <a name="pre-migration"></a>Premigratie 

### <a name="create-an-azure-account"></a>Een Azure-account maken
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

### <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Volg de stapsgewijze instructies in het artikel Create a [Event hub](event-hubs-create.md) om een event hubs naam ruimte en een event hub te maken. 

### <a name="connection-string"></a>Verbindingsreeks
Volg de stappen in het artikel [Connection String ophalen uit de portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) . En noteer de connection string voor later gebruik. 

### <a name="fully-qualified-domain-name-fqdn"></a>Fully Qualified Domain Name (FQDN)
Mogelijk hebt u ook de FQDN nodig die naar uw event hub-naam ruimte wijst. U kunt de FQDN als volgt in uw connection string vinden:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Als uw Event Hubs naam ruimte op een niet-open bare Cloud is geïmplementeerd, kan de domein naam verschillen (bijvoorbeeld \* . servicebus.chinacloudapi.cn, \* . servicebus.usgovcloudapi.net of \* . servicebus.cloudapi.de).

## <a name="migration"></a>Migratie 

### <a name="update-your-kafka-client-configuration"></a>Uw Kafka-client configuratie bijwerken

Als u verbinding wilt maken met een event hub met Kafka-functionaliteit, moet u de Kafka-client configuraties bijwerken. Als u geen ervaring kunt vinden, kunt u zoeken naar waar `bootstrap.servers` in uw toepassing is ingesteld.

Voer de volgende configuraties in, waar u zich in de toepassing kunt bedenken. Zorg ervoor dat u de `bootstrap.servers` waarden en bijwerkt `sasl.jaas.config` om de client naar uw event hubs Kafka-eind punt met de juiste verificatie te sturen. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Als de `sasl.jaas.config` configuratie niet wordt ondersteund in uw Framework, zoekt u de configuraties die worden gebruikt voor het instellen van de sasl gebruikers naam en het wacht woord en gebruiken ze in plaats daarvan. Stel de gebruikers naam `$ConnectionString` en het wacht woord in op uw Event Hubs Connection String.

## <a name="post-migration"></a>Postmigratie
Voer uw Kafka-toepassing uit die gebeurtenissen naar de Event Hub verzendt. Controleer vervolgens of de Event Hub de gebeurtenissen ontvangt met behulp van de Azure Portal. Ga op de pagina **overzicht** van uw event hubs naam ruimte naar de weer gave **berichten** in het gedeelte **metrische gegevens** . U moet de pagina vernieuwen om de grafiek bij te werken. Het kan een paar seconden duren voordat wordt weergegeven dat de berichten zijn ontvangen. 

[![Controleren of de Event Hub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Gids voor het oplossen van problemen met Apache Kafka voor Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Veelgestelde vragen-Event Hubs voor Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka ontwikkelaars handleiding voor Azure Event Hubs](apache-kafka-developer-guide.md)
- [Aanbevolen configuraties](apache-kafka-configurations.md)