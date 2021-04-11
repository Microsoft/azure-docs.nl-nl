---
title: Aangepaste headers voor de geleverde gebeurtenissen Azure Event Grid instellen
description: Hierin wordt beschreven hoe u aangepaste kopteksten (of bezorgings eigenschappen) kunt instellen voor de geleverde gebeurtenissen.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 6cc6874b7aba6e0696dec21de5b431ca18df3013
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967588"
---
# <a name="delivery-with-custom-headers"></a>Levering met aangepaste kopteksten
Met gebeurtenis abonnementen kunt u HTTP-headers instellen die in de geleverde gebeurtenissen zijn opgenomen. Met deze mogelijkheid kunt u aangepaste headers instellen die vereist zijn voor een doel. U kunt Maxi maal 10 kopteksten instellen bij het maken van een gebeurtenis abonnement. Elke header waarde mag niet groter zijn dan 4.096 bytes (4.000).

U kunt aangepaste kopteksten instellen voor de gebeurtenissen die worden geleverd aan de volgende bestemmingen:

- Webhooks
- Azure Service Bus onderwerpen en wacht rijen
- Azure Event Hubs
- Hybride verbindingen doorgeven

Wanneer u een gebeurtenis abonnement maakt in de Azure Portal, kunt u het tabblad **bezorgings eigenschappen** gebruiken om aangepaste HTTP-headers in te stellen. Op deze pagina kunt u vaste en dynamische koptekst waarden instellen.

## <a name="setting-static-header-values"></a>Statische header-waarden instellen
Als u koppen wilt instellen met een vaste waarde, geeft u de naam van de koptekst en de waarde ervan op in de bijbehorende velden:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Bezorgings eigenschappen-statisch":::

Mogelijk wilt u controleren of het een **geheim is?** wanneer u gevoelige gegevens opgeeft. Gevoelige gegevens worden niet weer gegeven op de Azure Portal. 

## <a name="setting-dynamic-header-values"></a>Dynamische header waarden instellen
U kunt de waarde van een koptekst instellen op basis van een eigenschap in een binnenkomende gebeurtenis. Gebruik de syntaxis JsonPath om te verwijzen naar de eigenschaps waarde van een binnenkomende gebeurtenis die moet worden gebruikt als de waarde voor een koptekst in uitgaande aanvragen. Als u bijvoorbeeld de waarde van een header met de naam **Channel** wilt instellen met behulp van de waarde van de eigenschap **systeem** van de inkomende gebeurtenis in de gebeurtenis gegevens, configureert u uw gebeurtenis abonnement op de volgende manier:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Bezorgings eigenschappen: dynamisch":::

## <a name="examples"></a>Voorbeelden
In deze sectie vindt u enkele voor beelden van het gebruik van bezorgings eigenschappen.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>De autorisatie-header instellen met een Bearer-token (niet-normatieve voor beeld)

Stel een waarde in op een autorisatie-header om de aanvraag te identificeren met uw webhook-handler. Een autorisatie-header kan worden ingesteld als u [uw webhook niet beveiligt met Azure Active Directory](secure-webhook-delivery.md).

| Headernaam   | Koptekst type | Headerwaarde |
| :--           | :--         | :--            |
|`Authorization` | Statisch | `BEARER SlAV32hkKG...`|

Uitgaande aanvragen moeten nu de header-set bevatten in het gebeurtenis abonnement:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Het definiëren van autorisatie headers is een praktische optie wanneer uw bestemming een webhook is. Het mag niet worden gebruikt voor [functies die zijn geabonneerd met een resource-id](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs en hybride verbindingen, aangezien die doelen hun eigen verificatie schema's ondersteunen wanneer ze worden gebruikt met Event grid.

### <a name="service-bus-example"></a>Service Bus-voor beeld
Azure Service Bus ondersteunt het gebruik van een [BrokerProperties HTTP-header](/rest/api/servicebus/message-headers-and-properties#message-headers) om bericht eigenschappen te definiëren bij het verzenden van afzonderlijke berichten. De waarde van de `BrokerProperties` header moet worden opgenomen in de JSON-indeling. Als u bijvoorbeeld de eigenschappen van een bericht wilt instellen bij het verzenden van één bericht naar Service Bus, stelt u de koptekst op de volgende manier in:

| Headernaam | Koptekst type | Headerwaarde |
| :-- | :-- | :-- |
|`BrokerProperties` | Statisch     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Event Hubs-voor beeld

Als u gebeurtenissen moet publiceren naar een specifieke partitie in een Event Hub, definieert u een [BrokerProperties HTTP-header](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) op uw gebeurtenis abonnement om de partitie sleutel op te geven waarmee de doel-event hub partitie wordt geïdentificeerd.

| Headernaam | Koptekst type | Headerwaarde                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Statisch | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Time-to-Live configureren voor uitgaande gebeurtenissen Azure Storage wachtrijen
Voor de Azure Storage wachtrij bestemming kunt u de time-to-Live alleen configureren als het uitgaande bericht eenmaal is bezorgd bij een Azure Storage wachtrij. Als u geen tijd opgeeft, is de standaard levens duur van het bericht 7 dagen. U kunt ook instellen dat de gebeurtenis nooit verloopt.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Bezorgings eigenschappen-opslag wachtrij":::

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over de levering van gebeurtenissen:

- [Leveren en opnieuw proberen](delivery-and-retry.md)
- [Gebeurtenisverzending voor de webhook](webhook-event-delivery.md)
- [Gebeurtenissen filteren](event-filtering.md)
